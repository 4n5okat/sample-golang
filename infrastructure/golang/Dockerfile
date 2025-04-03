# Golang 1.23.5 + Alpine Linux 3.21の軽量なイメージをベースにする
FROM golang:1.24.1-alpine3.21 AS dev

# コンテナ内の作業ディレクトリを設定
WORKDIR /app

# 依存関係の管理ファイル (go.mod, go.sum) をコンテナにコピー
COPY ./app/go.mod ./app/go.sum ./
# 依存関係をダウンロード
RUN go mod download

# アプリケーションのソースコードをすべてコンテナにコピー
COPY ./app .

# Air（ホットリロードツール）をインストール
RUN go install github.com/air-verse/air@latest

CMD ["air", "-c", ".air.toml"]

# === ビルド用ステージ ===
FROM golang:1.24.1-alpine3.21 AS builder
WORKDIR /app

# 依存関係の管理ファイルをコピー
COPY /app/go.mod /app/go.sum ./
# 依存関係をダウンロード
RUN go mod download

# アプリケーションのソースコードをコピー
COPY /app .

# 静的バイナリを作成（軽量化）
RUN CGO_ENABLED=0 GOOS=linux go build -o build ./cmd/server

# === 実行用ステージ ===
# 実行時の軽量な Alpine Linux イメージ
FROM alpine:3.21
# ビルド済みバイナリのみをコピー
COPY --from=builder /app/build .
# アプリケーションを起動
CMD ["./build"]
