# MarkItDown MCP Docker Image

MarkItDown MCPをDockerコンテナとして実行するためのイメージです。

## 概要

MarkItDown MCPは、様々なファイル形式をMarkdownに変換するツールです。MCP (Model Context Protocol) サーバーとして動作し、以下の機能を提供します：

- 複数のファイル形式からMarkdownへの変換
- メディアファイルの処理（ffmpeg使用）
- メタデータの抽出（exiftool使用）
- プラグインシステムによる拡張性

## インストール

### DockerHubから取得

```bash
docker pull katzkawai/markitdown-mcp:latest
```

### ローカルでビルド

```bash
git clone https://github.com/katzkawai/markitdown-mcp.git
cd markitdown-mcp
docker build -t markitdown-mcp .
```

## 使い方

### 基本的な使用方法

```bash
# カレントディレクトリをマウントして実行
docker run -v $(pwd):/workdir katzkawai/markitdown-mcp:latest [引数]
```

### ユーザー権限を指定して実行

ホストのユーザー権限と合わせる場合：

```bash
# ビルド時にユーザーIDを指定
docker build --build-arg USERID=$(id -u) --build-arg GROUPID=$(id -g) -t markitdown-mcp .

# 実行
docker run -v $(pwd):/workdir markitdown-mcp [引数]
```

### 実行モード

MarkItDown MCPは以下のモードで実行できます：

1. **STDIO モード**（デフォルト）
   ```bash
   docker run -v $(pwd):/workdir katzkawai/markitdown-mcp:latest
   ```
   
   Claude Desktopで使用する場合の`.mcp.json`設定例：
   ```json
   {
     "mcpServers": {
       "markitdown-mcp": {
         "type": "stdio",
         "command": "docker",
         "args": ["run", "-i", "--rm", "-v", "${cwd}:/workdir", "katzkawai/markitdown-mcp:latest"]
       }
     }
   }
   ```

2. **HTTP サーバーモード**
   ```bash
   docker run -p 5173:5173 -v $(pwd):/workdir katzkawai/markitdown-mcp:latest http
   ```

3. **SSE (Server-Sent Events) モード**
   ```bash
   docker run -p 5173:5173 -v $(pwd):/workdir katzkawai/markitdown-mcp:latest sse
   ```

### ファイル変換の例

```bash
# PDFファイルをMarkdownに変換
docker run -v $(pwd):/workdir katzkawai/markitdown-mcp:latest convert file:///workdir/document.pdf

# HTTPSのURLから変換
docker run katzkawai/markitdown-mcp:latest convert https://example.com/document.pdf

# データURIから変換
docker run katzkawai/markitdown-mcp:latest convert "data:text/plain;base64,SGVsbG8gV29ybGQ="
```

## サポートされているファイル形式

- PDF文書
- Microsoft Office文書（Word、Excel、PowerPoint）
- 画像ファイル（メタデータ抽出を含む）
- 音声・動画ファイル（文字起こし機能）
- HTMLファイル
- その他多数の形式

## 環境変数

- `MARKITDOWN_ENABLE_PLUGINS`: プラグインを有効化（デフォルト: True）
- `EXIFTOOL_PATH`: exiftoolのパス（デフォルト: /usr/bin/exiftool）
- `FFMPEG_PATH`: ffmpegのパス（デフォルト: /usr/bin/ffmpeg）

## セキュリティに関する注意

- HTTPモードやSSEモードで実行する場合は、セキュリティのためlocalhostにバインドすることを推奨
- 信頼できないファイルを処理する場合は、適切なセキュリティ対策を実施してください

## トラブルシューティング

### 権限エラーが発生する場合

ファイルの読み書き権限でエラーが発生する場合は、ユーザーIDを指定してビルド・実行してください：

```bash
docker build --build-arg USERID=$(id -u) --build-arg GROUPID=$(id -g) -t markitdown-mcp .
```

### メモリ不足エラー

大きなファイルを処理する場合は、Dockerのメモリ制限を増やしてください：

```bash
docker run -m 4g -v $(pwd):/workdir katzkawai/markitdown-mcp:latest [引数]
```

## ライセンス

このDockerイメージは、MarkItDown MCPのライセンスに従います。詳細は[MarkItDown MCPのリポジトリ](https://github.com/markitdown/markitdown-mcp)を参照してください。

## 関連リンク

- [DockerHub](https://hub.docker.com/r/katzkawai/markitdown-mcp)
- [MarkItDown MCP PyPI](https://pypi.org/project/markitdown-mcp/)
- [MarkItDown公式リポジトリ](https://github.com/microsoft/markitdown)