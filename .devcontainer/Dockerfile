# 使用輕量級的 Node.js 環境
FROM mcr.microsoft.com/devcontainers/javascript-node:18

# 更新系統並安裝 Go 語言 (Docsy 需要 Go 來下載模組)
RUN apt-get update && apt-get install -y golang-go

# 手動下載並安裝 Hugo Extended v0.135.0 (注意版號必須精確)
RUN wget -q -O hugo.tar.gz https://github.com/gohugoio/hugo/releases/download/v0.135.0/hugo_extended_0.135.0_linux-amd64.tar.gz \
    && tar -xf hugo.tar.gz \
    && mv hugo /usr/local/bin/hugo \
    && rm hugo.tar.gz \
    && echo "Hugo installed successfully!"