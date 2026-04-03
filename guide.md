# Hermes Agent セットアップウィザード解説書

このガイドでは、Hermes Agentをセットアップするための手順をステップバイステップで解説します。

---

## 目次

1. [Hermes Agentのインストール](#1-hermes-agentのインストール)
2. [Kilo Codeプロバイダ設定](#2-kilo-codeプロバイダ設定)
3. [Telegramボット設定](#3-telegramボット設定)
4. [Gatewayのsystemdサービス設定](#4-gatewayのsystemdサービス設定)
5. [モデル設定の変更](#5-モデル設定の変更)
6. [OpenClawのフォールバック設定](#6-openclawのフォールバック設定)
7. [トラブルシューティング](#トラブルシューティング)

---

## 1. Hermes Agentのインストール

### 前提条件

- Node.js v18以降がインストールされていること
- npmが使用可能であること

### インストール手順

#### 1.1 プロジェクトディレクトリの作成

```bash
mkdir -p ~/clawd
cd ~/clawd
```

#### 1.2 OpenClawのインストール

```bash
npm install -g @openclaw/openclaw
```

#### 1.3 インストールの確認

```bash
openclaw --version
```

正常にインストールされていれば、バージョン番号が表示されます。

---

## 2. Kilo Codeプロバイダ設定

Kilo Codeは、Hermes Agentが使用するAIモデルを提供するプロバイダです。

### 2.1 Kilo Codeアカウントの作成

1. [Kilo Code](https://kilocode.ai) にアクセスし、アカウントを作成します
2. APIキーを取得します

### 2.2 環境変数の設定

```bash
export KILO_CODE_API_KEY="your-api-key-here"
```

永続的な設定には、以下のコマンドを使用します：

```bash
echo 'export KILO_CODE_API_KEY="your-api-key-here"' >> ~/.bashrc
source ~/.bashrc
```

### 2.3 設定ファイルの作成

```bash
mkdir -p ~/.config/openclaw
cat > ~/.config/openclaw/providers.json << EOF
{
  "kilo": {
    "apiKey": "your-api-key-here",
    "baseUrl": "https://api.kilocode.ai"
  }
}
EOF
```

---

## 3. Telegramボット設定

### 3.1 Telegramボットの作成

1. Telegramで [@BotFather](https://t.me/botfather) を開きます
2. `/newbot` コマンドを入力します
3. ボット名とユーザー名を設定します
4. APIトークンを取得します

### 3.2 OpenClawでのボット設定

```bash
openclaw telegram:setup
```

このコマンドを実行すると、以下の情報を求められます：

```
? ボットトークンを入力してください: [Telegram Bot Tokenを入力]
? ペアリングしたいユーザーのIDを入力してください: [あなたのTelegram User ID]
```

### 3.3 Telegram User IDの確認方法

1. [@userinfobot](https://t.me/userinfobot) を開きます
2. `/start` コマンドを入力します
3. User IDが表示されます

### 3.4 設定の確認

```bash
openclaw telegram:status
```

以下のように表示されれば成功です：

```
✅ Telegramボットがアクティブです
ボット名: your_bot_name
ペアリング済みユーザー: Your Name (ID: 123456789)
```

---

## 4. Gatewayのsystemdサービス設定

Hermes Agentを常時実行するために、systemdサービスを設定します。

### 4.1 サービスファイルの作成

```bash
sudo nano /etc/systemd/system/openclaw-gateway.service
```

以下の内容を貼り付けます：

```ini
[Unit]
Description=OpenClaw Gateway Service
After=network.target

[Service]
Type=simple
User=jun
WorkingDirectory=/home/jun/.openclaw
ExecStart=/home/linuxbrew/.linuxbrew/bin/openclaw gateway start
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

> **注意**: `User` フィールドは、適切なユーザー名に変更してください。

### 4.2 サービスの有効化と開始

```bash
sudo systemctl daemon-reload
sudo systemctl enable openclaw-gateway
sudo systemctl start openclaw-gateway
```

### 4.3 サービスのステータス確認

```bash
sudo systemctl status openclaw-gateway
```

以下のように表示されれば成功です：

```
● openclaw-gateway.service - OpenClaw Gateway Service
     Loaded: loaded (/etc/systemd/system/openclaw-gateway.service; enabled; preset: enabled)
     Active: active (running) since ...
```

### 4.4 ログの確認

```bash
sudo journalctl -u openclaw-gateway -f
```

---

## 5. モデル設定の変更

### 5.1 デフォルトモデルの設定

```bash
mkdir -p ~/.config/openclaw
cat > ~/.config/openclaw/config.json << EOF
{
  "defaults": {
    "model": "kilo/gpt-4o"
  }
}
EOF
```

### 5.2 プロバイダ別のモデル設定

```bash
cat > ~/.config/openclaw/providers.json << EOF
{
  "kilo": {
    "apiKey": "your-api-key-here",
    "models": {
      "gpt-4o": {
        "name": "kilo/gpt-4o",
        "description": "GPT-4o via Kilo Code"
      },
      "gpt-4-turbo": {
        "name": "kilo/gpt-4-turbo",
        "description": "GPT-4 Turbo via Kilo Code"
      }
    }
  }
}
EOF
```

### 5.3 設定の確認

```bash
openclaw config:show
```

---

## 6. OpenClawのフォールバック設定

メインプロバイダが利用できない場合に、フォールバック先を設定します。

### 6.1 フォールバック設定の追加

```bash
cat > ~/.config/openclaw/fallback.json << EOF
{
  "fallbackProviders": [
    "openai",
    "anthropic"
  ],
  "fallbackConfig": {
    "openai": {
      "apiKey": "your-openai-api-key"
    },
    "anthropic": {
      "apiKey": "your-anthropic-api-key"
    }
  }
}
EOF
```

### 6.2 優先順位の設定

複数のフォールバック先がある場合、優先順位を設定できます：

```bash
cat > ~/.config/openclaw/fallback.json << EOF
{
  "fallbackProviders": [
    "kilo",
    "openai",
    "anthropic"
  ],
  "priority": {
    "kilo": 1,
    "openai": 2,
    "anthropic": 3
  }
}
EOF
```

### 6.3 フォールバック設定の確認

```bash
openclaw fallback:test
```

---

## トラブルシューティング

### Gatewayが起動しない

#### 症状
`sudo systemctl status openclaw-gateway` で `failed` と表示される

#### 解決策
```bash
# ログを確認
sudo journalctl -u openclaw-gateway -n 50

# 手動で起動してエラーを確認
openclaw gateway start
```

### Telegramボットが応答しない

#### 症状
メッセージを送ってもボットが応答しない

#### 解決策
```bash
# Gatewayのステータスを確認
openclaw gateway status

# Telegram設定を確認
openclaw telegram:status

# Gatewayを再起動
sudo systemctl restart openclaw-gateway
```

### Kilo Code APIキーのエラー

#### 症状
`401 Unauthorized` エラーが表示される

#### 解決策
```bash
# APIキーを再設定
openclaw config:set kilo.apiKey your-new-api-key

# 環境変数を確認
echo $KILO_CODE_API_KEY
```

### モデルの切り替えが効かない

#### 症状
デフォルトモデルが変更されない

#### 解決策
```bash
# 設定を再読み込み
openclaw config:reload

# 設定ファイルを確認
cat ~/.config/openclaw/config.json
```

---

## よくある質問

### Q: Hermes Agentとは何ですか？

A: Hermes Agentは、Telegramボットを通じてAIアシスタントと対話するためのツールです。自然言語処理を使用して、様々なタスクを自動化できます。

### Q: 複数のユーザーを登録できますか？

A: はい、複数のユーザーを登録できます。各ユーザーは、個別のTelegram IDを持つ必要があります。

### Q: 無料で使用できますか？

A: 基本的な機能は無料で使用できますが、APIコストはかかります。各プロバイダの料金プランを確認してください。

### Q: VPSにインストールできますか？

A: はい、UbuntuやDebianなどのLinuxディストリビューションが動作するVPSにインストールできます。

### Q: ボットの応答速度を改善する方法は？

A: 高速なモデルを選択する、APIエンドポイントを近いリージョンに設定する、またはローカルLLMを使用することで改善できます。

---

## 追加リソース

- [OpenClaw公式ドキュメント](https://github.com/openclaw/openclaw)
- [Kilo Codeドキュメント](https://docs.kilocode.ai)
- [Telegram Bot API](https://core.telegram.org/bots/api)

---

## サポート

問題が解決しない場合は、以下のリソースを活用してください：

- GitHub Issues: https://github.com/JunSuzuki1973/hermes-agent-setup-guide/issues
- 公式ドキュメントを確認
- コミュニティフォーラムで質問

---

*最終更新日: 2026-04-03*
