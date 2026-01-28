---
title: AI/ML Model Training and Inference
description: Explain how an NDTwin application trains and inferences an AI/ML model for prediction and optimal control of the network.

date: 2017-01-05
weight: 4
---

AI/ML model training and inference involve data, training, and inferecne. Here, we explain how an NDTwin application gets data from the NDTwin Kernel for trainning a model and how it uses the trained model for inference. Performing inferences can be used to classify a packet/flow/situation, etc. or to predict the future outcome of a flow/switch/link/network, etc. The purposes of using AI/ML models in an NDTwin application can be various. 

Nowadays, most people use the Python language and Python-based platforms (e.g., scikit-learn, PyTorch,TensorFlow, etc.) to write programs for training and inferencing an AI/ML model. If the NDTwin application program is written in Python, it can use these Python-based platforms for training and inferencing a model as any non-NDTwin application program. After inferencing a model to get the classification result or the predicted future outcome, the NDTwin application can issue appropriate NDTwin Kernel APIs to optimally control the network.

Regarding obtaining appropriate data to train an AI/ML model, what data need to be collected to train the model depends on how an NDTwin application will use the trained model. For network data, the NDTwin application can (maybe periodically) issue appropriate NDTwin Kernel APIs to get the data. The network state recorder (NSR) tool of NDTwin can also be used to automatically and periodically record the network data during a specified period into files, which can later be opend by the NDTwin application for model trainning.

AI/ML model trainning can be performed offline. Thus, there is no need to perform model trainning on the NDTwin framework. NDTwin application developers can train their models on any machine they want. If the model trainning requires a considerable amount of GPU or CPU time, the developers can facilitate their model trainning on a powerful machine that is not part of NDTwin.

After an AI/ML model has been trained on a machine that is not part of NDTwin, the NDTwin application can use the trained model for inference and optimal control of the network. Normally, the trained model needs to be exported into a file (e.g., exported to a .pt file if the PyTorch platform is used for trainning). If the NDTwin application program is written in Python, it can import the trained model from the exported model file and then calls appropriate prediction functions to make inferences. This usage is the same as that for non-NDTwin application in the real world. 

To make an inference, depending on the model used, a set of feature data should be fed into the trained model. An NDTwin application can call appropriate NDTwin Kernel API functions to get a set of real-time network data (e.g., the current bandwidth usage of all links). Then, it can feed the retrieved real-time data into the model as the feature input to make an inference. For time-series models, an NDTwin application can keep the past retrieved data inside itself to form a sequence of time-series data for making an inference.

If the NDTwin application program is not written in Python but in a different language such as C++, the NDTwin application needs to perform some operations to import a trained model and call its prediction function for inference.

# Example

Below, we use an example to explain the training phase, deployment preparation phase, and inference phase of an NDTwin application that uses LSTNet to train a time-series model and uses the trained model to predict the network traffic volume transmitted in ten minutes for the next interval. In this example, the LSTNet model is trained in PyTorch and exported as a .pt file and the NDTwin application is a C++ program. 

# Training Phase
## 1) Prerequisites
### Python
* Python 3.9+ (recommended: 3.10)
### Required dependencies (actually used in the code)
* PyTorch (torch)
* scikit-learn (for Standard scaler)
* joblib (for saving the scaler)

```python
pip install torch scikit-learn joblib
```
## 2) Data Preparation and File Structure (Required)
Before training a network traffic forecasting model, users must ensure that their time series dataset satisfies the following requirements:

1. Fixed Time Granularity:

The dataset must be collected at a fixed time interval, such as:                    One record every 10 minutes.

2. Explicit Target Variable:

* The dataset must include a clearly defined traffic-related target variable to be predicted by the model.
* This variable serves as the label in the supervised learning process.

3. Chronologically Ordered Data:

Shuffling the data is not allowed, as it breaks temporal dependencies.

## 3) Feature Engineering Recommendations
To improve the model's ability to learn temporal and periodic patterns, you are encouraged to include time-related and cyclical features during the feature engineering stage, such as:

### 1. Calendar-Based Features:

The following features can be extracted directly from timestamps and used as model inputs:
* Month
* Day of month

### 2. Cyclical Features:
For traffic data exhibiting strong periodic behavior, it is recommended to include cyclical encodings, such as:
* Hourly cycles
* Weekly cycles

### 3. Target Value Normalization:
Network traffic values may span multiple orders of magnitude (e.g., hundreds of MB to tens of GB).
* Normalizing or standardizing the target variable is therefore recommended, for example using ```StandardScaler```.

## 4) Normalization (Optional)
### 1. Applies ```StandardScaler``` only to flow (bytes)
* ```fit``` on the training set
* ```transform``` on the test set

### 2. Saves the scaler as ```.joblib```
### Note
* The same scaler must be used during both training and deployment (for inverse normalization)
* The scaler must be fitted only on the training set, not the full dataset, to avoid data leakage

## 5) Training Outputs and Deployment Artifacts (Required)
When LSTNet is used as the time series forecasting model, the training process produces the following deployment artifacts:

* ```your_model_name.pt```:

   A TorchScript traced model used for deployment and inference
* ```your_scaler_name.joblib```:
    
    The data scaler fitted during training, which must be reused during inference and inverse normalization

#### During inference, the input tensor shape must exactly match the example input used during model tracing.
For example:     
```(batch_size, 168, 7)```

Where:
* ```168```: length of the input time sequence (e.g., 168 time steps)
* ```7```: number of features per time step

# Deployment Preparation Phase (Python Inference Bridge Module Specification)
* This phase packages the trained TorchScript model (```.pt```)and scaler artifact (```.joblib```) into a Python interface callable from a C++ application.
* Python handles feature engineering, normalization, and inference, then returns predictions back to C++.

### Python Interface (3 Pseudo Function)
1. ```matrix_to_df```

Input Matrix and convert it into a time series data table.
``` 
FUNCTION matrix_to_df(input_matrix):
    REQUIRE input_matrix length == WINDOW (168 timesteps)

    CREATE table with columns:
        - time
        - n_bytes

    FOR each row in input_matrix:
        PARSE time string into datetime
        PARSE n_bytes into numeric value

    SET time as index
    SORT rows by time in ascending order

    RETURN time-indexed table
```
2. ```build_features```

Feature engineering (consistent with the training phase)
```
FUNCTION build_features(time_series_table):
    CONVERT n_bytes from Bytes to MB

    LOAD scaler used during training

    STANDARDIZE n_bytes using the scaler

    EXTRACT calendar features from time index:
        - month
        - day

    COMPUTE cyclical features:
        - sin(hour), cos(hour)
        - sin(weekday), cos(weekday)

    ARRANGE features in fixed order:
        [month, day, n_bytes, sin_hour, cos_hour, sin_week, cos_week]

    RETURN feature_matrix with shape (168, 7)
```
3.```predict_from_matrix```

External inference interface (C++ call entry point)
```
FUNCTION predict_from_matrix(input_matrix):
    df <-matrix_to_df(input_matrix)

    features <-build_features(df)

    model_input <-reshape features to (1, WINDOW, FEATURES)

    LOAD TorchScript model

    DISABLE gradient computation

    prediction_std <-model(model_input)

    LOAD training scaler

    prediction_mb <-inverse-transform prediction_std using scaler

    RETURN prediction_mb 
```
* This function serves as the primary inference interface between Python and C++.
* The Python side is responsible for:
   * Input validation
   * Feature engineering
   * Model inference
   * Inverse normalization
* The C++ side only needs to provide the raw time series input data and receive the prediction results.

# Inference Phase (C++ Integration with Python Prediction Model)
## 1. System Architecture Overview
The inference framework adopts a hybrid deployment architecture using C++ and Python, in which responsibilities are clearly separated:
### Python Side (Deployment Preparation)

Responsible for:

* Loading the trained TorchScript model (```.pt```)

* Loading the trained scaler (```.joblib```)

* Performing feature engineering

* Executing model inference

* Performing inverse normalization

* Providing an external callable inference interface:```predict_from_matrix()```
### C++ Side
Responsible for:

* Preparing time series input data

* Calling Python inference functions via pybind11

* Receiving and integrating prediction outputs into the C++ system workflow
### Data Flow Diagram
```
C++ input sequence
         |
pybind11 calls Python module
         |
Python feature engineering + inference
         |
Prediction result returned
         |
C++ receives and processes result
```
## 2. Python Inference Module Loading
Users must place the Python inference module generated(```your_python_interface.py```) during the Deployment Preparation Phase together with the following files in the same directory:```your_model_name.pt```, ```your_scaler_name.joblib```, ```your_c/c++.exe```
# 3. C++ Loading Mechanism Design
* Use ```pybind11::embed```to initialize an embedded Python interpreter

* Modify ```sys.path``` to allow Python to locate the module

* Import the Python inference module using ```py::module::import("your_python_interface_name")```

* Call the Python inference function using ```module.attr("predict_from_matrix")(data);```

For example:
```c++
#include <pybind11/embed.h>
#include <pybind11/numpy.h>
#include <pybind11/stl.h>
namespace py = pybind11;
py::initialize_interpreter();
module = py::module::import("your_python_interface_name");
py::object predicted_result = module.attr("predict_from_matrix")(data);
```
# 4. C++ - Python Interface Design
## Python External Inference Function Specification
### Input Format
```
list of [time_string, n_bytes]
Total length: 168
```
### Example
```
[
  ["2024-01-01 00:00", 50000000000],
  ["2024-01-01 01:00", 52000000000],
  ...
  (168 samples in total)
]
```
### C++ Invocation Logic
```
1. Generate 168-hour time series input
2. Pack data as vector<pair<string, double>>
3. Call module.attr("predict_from_matrix") to predict
```
# 5. Runtime Inference Execution Flow
```
Create Predictor object
        |
Initialize embedded Python interpreter
        |
Import Python inference module
        |
Construct 168-hour input sequence
        |
Call predict_from_matrix()
        |
Receive prediction output
        |
Display or integrate into system
```