
---

```markdown
# NDTwin Website (Network Digital Twin)

![Hugo](https://img.shields.io/badge/Built%20with-Hugo-ff4088?style=flat-square&logo=hugo)
![Docsy](https://img.shields.io/badge/Theme-Docsy-0055AA?style=flat-square)
![License](https://img.shields.io/github/license/joemou/NDTwin-Website?style=flat-square)

這是 **NDTwin (Network Digital Twin)** 的官方文件網站原始碼。
NDTwin 是一套專為 OpenFlow SDN 網路設計的數位孿生系統，此網站用於託管專案的介紹、使用文件與開發者指南。

本網站使用 [Hugo](https://gohugo.io/) 靜態網站產生器，並搭配 [Docsy](https://www.docsy.dev/) 主題建置。

---

## 🚀 快速開始 (推薦：使用 Codespaces)

本專案已設定完整的雲端開發環境，**您不需要在電腦安裝任何軟體**即可修改網站。

1. 點擊上方綠色的 **Code** 按鈕。
2. 切換到 **Codespaces** 分頁。
3. 點擊 **Create codespace on main**。
4. 等待環境建立完成後，在下方終端機 (Terminal) 輸入：

   ```bash
   hugo server

```

5. 點擊右下角跳出的 **Open in Browser** 按鈕即可預覽網站。

---

## 🛠️ 本地開發 (Local Development)

如果您堅持要在自己的電腦上執行，請確保安裝以下工具：

* **Hugo Extended** (v0.135.0+): 必須是 Extended 版本。
* **Go Language**: 用於下載 Docsy 主題模組。
* **Node.js & npm**: 用於處理 PostCSS 和網頁樣式。

**安裝步驟：**

```bash
# 1. 下載專案
git clone --recurse-submodules [https://github.com/joemou/NDTwin-Website.git](https://github.com/joemou/NDTwin-Website.git)
cd NDTwin-Website

# 2. 安裝 npm 依賴
npm install

# 3. 啟動伺服器
hugo server

```

---

## 📂 專案結構說明

以下是您在維護網站時主要會用到的資料夾：

| 資料夾 | 說明 |
| --- | --- |
| **`content/`** | **最重要！** 所有的文章、頁面內容 (`.md`) 都放在這裡。 |
| **`static/`** | **靜態資源**。圖片請放在 `static/images/`，編譯後會直接複製到網站根目錄。 |
| **`hugo.yaml`** | **網站設定檔**。修改網站標題、選單、語言設定、Logo 路徑等。 |
| `.devcontainer/` | Codespaces 的設定檔 (包含 Dockerfile)，定義了雲端開發環境。 |
| `layouts/` | 自定義的排版與短代碼 (Shortcodes) 存放處。 |

---

## 📝 編輯指南

### 1. 修改文章

前往 `content/en/` 資料夾，找到對應的 `.md` 檔案進行編輯。

* **首頁**: `content/en/_index.md`
* **文件**: `content/en/docs/...`

### 2. 插入圖片

請將圖片檔案放入 `static/images/` 資料夾中。

**一般圖片 (Markdown):**

```markdown
![圖片說明](/images/your-image.png)

```

**右側繞圖 (使用自訂 Shortcode):**
我們建立了一個特殊的指令，可以讓圖片靠右、文字環繞：

```markdown
{{< img-right src="/images/your-image.png" alt="圖片說明" width="40%" >}}

```

---

## 🚢 部署 (Deployment)

本專案使用 GitHub Actions 自動部署。
只要將修改 **Push** 到 `main` 分支，GitHub 會自動建置靜態網頁並發布到 GitHub Pages。

---

## 📄 License

此網站內容採用 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) 授權。
原始碼部分採用 MIT License。


```