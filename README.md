# OpenAI Realtime Webhook Server

OpenAI Realtime APIのWebhookリクエストを受信し、音声通話を処理するFlaskアプリケーションです。日本語対応のサポートエージェントとして動作します。

## 機能

- OpenAI Realtime APIからのWebhookリクエストを受信
- 着信通話の自動受付
- WebSocket接続による双方向通信
- 日本語での音声応答（「もしもし、本日のご要件はなんですか？」）
- カスタマイズ可能な音声応答メッセージ

## 必要要件

- Python 3.8以上
- OpenAI APIキー
- OpenAI Webhook Secret

## セットアップ

### 1. リポジトリのクローン

```bash
git clone <repository-url>
cd realtimeapi-sip-webhook-server
```

### 2. 仮想環境の作成とアクティベート

```bash
# 仮想環境を作成
python3 -m venv venv

# 仮想環境をアクティベート（macOS/Linux）
source venv/bin/activate

# 仮想環境をアクティベート（Windows）
# venv\Scripts\activate
```

### 3. 依存パッケージのインストール

```bash
pip install -r requirements.txt
```

## 設定方法

### 方法1: ハードコードされた認証情報を使用（main.py）

`main.py`ファイル内に直接APIキーとWebhook Secretが記載されています。
テスト環境での使用に適していますが、本番環境では推奨されません。

### 方法2: 環境変数を使用（main_with_env.py）

`.env`ファイルを使用して認証情報を管理します。

1. `.env`ファイルが既に作成されています。必要に応じて値を更新してください：

```env
OPENAI_WEBHOOK_SECRET=your_webhook_secret_here
OPENAI_API_KEY=your_api_key_here
```

2. `main_with_env.py`を使用して起動します。

## 実行方法

### 開発サーバーの起動

```bash
# 方法1: ハードコードされた認証情報を使用
python main.py

# 方法2: 環境変数を使用（推奨）
python main_with_env.py
```

サーバーはデフォルトでポート8000で起動します。

### Webhookエンドポイント

```
POST http://localhost:8000/
```

## プロジェクト構成

```
SIP-Webhook/
├── README.md           # このファイル
├── main_with_env.py   # 環境変数版のアプリケーション（日本語対応）
├── .env               # 環境変数ファイル（gitignoreに追加）
├── .gitignore         # Git除外設定ファイル
├── requirements.txt   # 依存パッケージリスト
└── venv/             # 仮想環境ディレクトリ（gitignoreに追加）
```

## 主要な依存パッケージ

- **Flask** (3.0.0): Webフレームワーク
- **OpenAI** (1.102.0): OpenAI APIクライアント
- **websockets** (13.1): WebSocket通信（additional_headers対応）
- **requests** (2.31.0): HTTPリクエスト処理
- **python-dotenv** (1.0.0): 環境変数管理

## 動作フロー

1. OpenAI Realtime APIからWebhookリクエストを受信
2. `realtime.call.incoming`イベントを検知
3. 通話を自動的に受付（accept）
4. WebSocket接続を確立
5. 日本語で応答（「もしもし、本日のご要件はなんですか？」）
6. WebSocket経由でリアルタイム通信を継続

## カスタマイズ

### 応答メッセージの変更

`response_create`オブジェクト内の`instructions`を編集：

```python
response_create = {
    "type": "response.create",
    "response": {
        "instructions": "カスタムメッセージをここに記載"
    },
}
```

### サポートエージェントの設定変更

`call_accept`オブジェクト内の`instructions`を編集（現在は日本語専用エージェントとして設定）：

```python
call_accept = {
    "type": "realtime",
    "instructions": "You are a support agent for Japanese. Please speak Japanese only.",
    "model": "gpt-4o-realtime-preview-2024-12-17",
}
```

## トラブルシューティング

### WebSocketエラー: additional_headers

websocketsライブラリのバージョンが古い場合に発生します。バージョン13.1を使用してください：

```bash
pip install websockets==13.1
```

### ポート8000が既に使用中

`main.py`または`main_with_env.py`の最終行でポート番号を変更：

```python
app.run(port=8001)  # 別のポート番号に変更
```

### 仮想環境の終了

作業が終わったら、仮想環境を終了：

```bash
deactivate
```

## セキュリティに関する注意

- 本番環境では必ず環境変数または安全な秘密管理システムを使用してください
- APIキーやWebhook Secretをコードに直接記載しないでください
- `.env`ファイルは`.gitignore`に追加してください
- HTTPSを使用してWebhookエンドポイントを保護してください

