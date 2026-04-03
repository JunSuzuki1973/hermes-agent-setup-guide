# Hermes Agent セットアップ実践ガイド - Q&A形式

このガイドでは、実際のセットアッププロセスで交わされる質問と回答をベースに、Hermes Agentをセットアップするための手順をQ&A形式で解説します。

---

## 目次

1. [インストール編](#1-インストール編)
2. [Kilo Codeプロバイダ設定編](#2-kilo-codeプロバイダ設定編)
3. [Telegramボット設定編](#3-telegramボット設定編)
4. [Gateway常時実行設定編](#4-gateway常時実行設定編)
5. [モデル設定編](#5-モデル設定編)
6. [トラブルシューティング編](#トラブルシューティング編)

---

## 1. インストール編

### Q1: Node.jsは必要ですか？

**A:** はい、Node.js v18以降が必要です。

```bash
# Node.jsのバージョン確認
node --version
```

v18以上が表示されない場合は、Node.jsをインストールしてください。

---

### Q2: OpenClawはどこにインストールすればいいですか？

**A:** グローバルにインストールすることを推奨します。

```bash
# グローバルインストール
npm install -g @openclaw/openclaw

# インストール確認
openclaw --version
```

**推奨理由:** グローバルインストールすると、どのディレクトリからでも`openclaw`コマンドが使用できます。

---

### Q3: プロジェクト用ディレクトリは必要ですか？

**A:** はい、作業用のディレクトリを作成することを推奨します。

```bash
# プロジェクトディレクトリの作成
mkdir -p ~/clawd
cd ~/clawd
```

**推奨理由:** OpenClawの作業ファイルを一箇所にまとめて管理できます。

---

## 2. Kilo Codeプロバイダ設定編

### Q4: Kilo Codeとは何ですか？

**A:** Kilo Codeは、AIモデルを提供するプロバイダです。Hermes Agentが使用するGPT-4などのモデルにアクセスするために使用します。

---

### Q5: どうやってKilo Codeのアカウントを作成しますか？

**A:** 以下の手順で作成します：

1. https://kilocode.ai にアクセス
2. サインアップまたはログイン
3. ダッシュボードからAPIキーを取得

---

### Q6: APIキーはどこに設定すればいいですか？

**A:** 2箇所に設定することを推奨します。

**方法1: 環境変数（一時的）**
```bash
export KILO_CODE_API_KEY="your-api-key-here"
```

**方法2: ~/.bashrc に追加（永続的・推奨）**
```bash
echo 'export KILO_CODE_API_KEY="your-api-key-here"' >> ~/.bashrc
source ~/.bashrc
```

**推奨理由:** ~/.bashrcに追加すると、ターミナルを再起動しても環境変数が保持されます。

---

### Q7: providers.jsonも必要ですか？

**A:** はい、設定ファイルを作成することを推奨します。

```bash
# 設定ディレクトリの作成
mkdir -p ~/.config/openclaw

# providers.jsonの作成
cat > ~/.config/openclaw/providers.json << EOF
{
  "kilo": {
    "apiKey": "your-api-key-here",
    "baseUrl": "https://api.kilocode.ai"
  }
}
EOF
```

**推奨理由:** 設定ファイルに保存すると、複数のプロバイダを一元管理できます。

---

### Q8: APIキーのセキュリティは大丈夫ですか？

**A:** APIキーは機密情報です。以下の点に注意してください：

- ✅ ~/.bashrc や ~/.config/openclaw/ に保存（パーミッションで保護）
- ❌ GitHubなどのパブリックリポジトリにコミットしない
- ❌ チャットやメールに貼り付けない

**確認方法:**
```bash
# ファイルのパーミッション確認
ls -la ~/.bashrc ~/.config/openclaw/providers.json
```

---

## 3. Telegramボット設定編

### Q9: Telegramボットを作るにはどうすればいいですか？

**A:** 以下の手順で作成します：

1. Telegramで [@BotFather](https://t.me/botfather) を開く
2. `/newbot` コマンドを入力
3. ボット名（例: My Assistant Bot）を入力
4. ユーザー名（例: my_assistant_bot、最後は_botで終わる必要あり）を入力
5. APIトークンをコピー

**例:**
```
BotFather: Alright, a new bot. How are we going to call it? Please choose a name for your bot.
あなた: My Assistant Bot

BotFather: Good. Now let's choose a username for your bot. It must end in `bot`. Like this, for example: TetrisBot or tetris_bot.
あなた: my_assistant_bot

BotFather: Done! Congratulations on your new bot. You will find it at t.me/my_assistant_bot. You can now add a description, about section and profile picture for your bot, see /help for a list of commands.
Use this token to access the HTTP API:
1234567890:ABCdefGHIjklMNOpqrsTUVwxyz
Keep your token secure and store it safely, it can be used by anyone to control your bot.
```

---

### Q10: 自分のTelegram User IDはどうやって確認しますか？

**A:** [@userinfobot](https://t.me/userinfobot) を使用します。

1. [@userinfobot](https://t.me/userinfobot) を開く
2. `/start` コマンドを入力
3. Id: 123456789 のような形式で表示される

**例:**
```
userinfobot: Id: 8535316477
Firstname: JUN
Language code: ja
```

---

### Q11: OpenClawにボットを登録するにはどうすればいいですか？

**A:** `openclaw telegram:setup` コマンドを使用します。

```bash
openclaw telegram:setup
```

**対話形式の例:**
```
? ボットトークンを入力してください: 1234567890:ABCdefGHIjklMNOpqrsTUVwxyz
? ペアリングしたいユーザーのIDを入力してください: 8535316477
```

**設定の確認:**
```bash
openclaw telegram:status
```

**成功した場合の出力:**
```
✅ Telegramボットがアクティブです
ボット名: my_assistant_bot
ペアリング済みユーザー: JUN (ID: 8535316477)
```

---

### Q12: 複数のユーザーを登録できますか？

**A:** はい、複数のユーザーを登録できます。

```bash
# 追加のユーザーをペアリング
openclaw telegram:pair --user-id 1234567890
```

または、設定ファイルを直接編集して複数のユーザーIDを追加することもできます。

---

### Q13: ボットが応答しない場合はどうすればいいですか？

**A:** 以下の手順でトラブルシューティングしてください：

```bash
# Gatewayのステータス確認
openclaw gateway status

# Gatewayが停止している場合は起動
openclaw gateway start

# Telegram設定の確認
openclaw telegram:status

# Gatewayのログを確認
sudo journalctl -u openclaw-gateway -f
```

---

## 4. Gateway常時実行設定編

### Q14: Gatewayを常時実行させるにはどうすればいいですか？

**A:** systemdサービスを設定することを推奨します。

```bash
# サービスファイルの作成
sudo nano /etc/systemd/system/openclaw-gateway.service
```

**サービスファイルの内容:**
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

**重要:**
- `User` フィールドはあなたのユーザー名に変更してください（例: `jun`）
- `ExecStart` のパスは `which openclaw` で確認できます

---

### Q15: openclawのパスを確認するには？

**A:** 以下のコマンドで確認できます。

```bash
which openclaw
```

**例:**
```
/home/linuxbrew/.linuxbrew/bin/openclaw
```

---

### Q16: サービスを有効化して起動するには？

**A:** 以下のコマンドを実行します。

```bash
# systemdの設定を再読み込み
sudo systemctl daemon-reload

# サービスを有効化（自動起動設定）
sudo systemctl enable openclaw-gateway

# サービスを起動
sudo systemctl start openclaw-gateway
```

---

### Q17: サービスが正常に動いているかどうか確認するには？

**A:** 以下のコマンドでステータスを確認できます。

```bash
sudo systemctl status openclaw-gateway
```

**成功している場合の出力:**
```
● openclaw-gateway.service - OpenClaw Gateway Service
     Loaded: loaded (/etc/systemd/system/openclaw-gateway.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-04-03 14:30:00 JST; 5min ago
   Main PID: 12345 (openclaw)
      Tasks: 5 (limit: 4915)
     Memory: 45.2M
        CPU: 123ms
     CGroup: /system.slice/openclaw-gateway.service
             └─12345 /home/linuxbrew/.linuxbrew/bin/node /home/linuxbrew/.linuxbrew/bin/openclaw gateway start
```

**キーポイント:**
- `Active: active (running)` → 正常に動作中
- `enabled` → システム起動時に自動起動

---

### Q18: サービスのログを確認するには？

**A:** 以下のコマンドでログを確認できます。

```bash
# 最新のログを表示（追従モード）
sudo journalctl -u openclaw-gateway -f

# 最新50行を表示
sudo journalctl -u openclaw-gateway -n 50

# 今日のログを表示
sudo journalctl -u openclaw-gateway --since today
```

---

### Q19: サービスを再起動するには？

**A:** 以下のコマンドを実行します。

```bash
# 再起動
sudo systemctl restart openclaw-gateway

# 設定変更後は必ず再起動が必要です
```

---

### Q20: サービスを停止するには？

**A:** 以下のコマンドを実行します。

```bash
# 停止
sudo systemctl stop openclaw-gateway

# 自動起動を無効化
sudo systemctl disable openclaw-gateway
```

---

## 5. モデル設定編

### Q21: デフォルトのモデルを変更するには？

**A:** config.jsonファイルで設定します。

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

**推奨モデル:**
- `kilo/gpt-4o` - 最新の高性能モデル（推奨）
- `kilo/gpt-4-turbo` - 高速なモデル
- `kilo/gpt-3.5-turbo` - 低コストなモデル

---

### Q22: 複数のモデルを設定できますか？

**A:** はい、providers.jsonで複数のモデルを定義できます。

```bash
cat > ~/.config/openclaw/providers.json << EOF
{
  "kilo": {
    "apiKey": "your-api-key-here",
    "baseUrl": "https://api.kilocode.ai",
    "models": {
      "gpt-4o": {
        "name": "kilo/gpt-4o",
        "description": "GPT-4o via Kilo Code - 最新の高性能モデル"
      },
      "gpt-4-turbo": {
        "name": "kilo/gpt-4-turbo",
        "description": "GPT-4 Turbo via Kilo Code - 高速なモデル"
      },
      "gpt-3.5-turbo": {
        "name": "kilo/gpt-3.5-turbo",
        "description": "GPT-3.5 Turbo via Kilo Code - 低コストなモデル"
      }
    }
  }
}
EOF
```

---

### Q23: 設定が正しく反映されているか確認するには？

**A:** 以下のコマンドで確認できます。

```bash
# 設定の表示
openclaw config:show

# 設定の再読み込み
openclaw config:reload
```

---

### Q24: コストを抑えたい場合は？

**A:** 以下の方法でコストを抑えることができます：

**方法1: 低コストなモデルを使用**
```json
{
  "defaults": {
    "model": "kilo/gpt-3.5-turbo"
  }
}
```

**方法2: フォールバック設定を活用**
```json
{
  "fallbackProviders": [
    "kilo",
    "openai"
  ]
}
```

**方法3: レート制限を設定**
```json
{
  "rateLimit": {
    "requestsPerMinute": 30
  }
}
```

---

### Q25: モデルを切り替えたい場合は？

**A:** メッセージでモデルを指定して切り替えることができます。

Telegramボットでの例:
```
@gpt-4o このタスクをやって
```

または、設定ファイルを編集してデフォルトを変更します。

---

## トラブルシューティング編

### Q26: Gatewayが起動しない場合は？

**A:** 以下の手順で確認してください。

**ステップ1: エラーログの確認**
```bash
sudo journalctl -u openclaw-gateway -n 50
```

**ステップ2: 手動起動でエラーを確認**
```bash
openclaw gateway start
```

**ステップ3: よくあるエラーと解決策**

| エラー | 原因 | 解決策 |
|--------|------|--------|
| `Permission denied` | パーミッションの問題 | `sudo chmod +x /home/linuxbrew/.linuxbrew/bin/openclaw` |
| `openclaw: command not found` | パスが通っていない | `which openclaw` でパスを確認し、サービスファイルを修正 |
| `API key not found` | APIキーが設定されていない | `export KILO_CODE_API_KEY="your-key"` を実行 |

---

### Q27: Telegramボットがメッセージに応答しない場合は？

**A:** 以下の手順で確認してください。

**ステップ1: Gatewayのステータス確認**
```bash
openclaw gateway status
```

**ステップ2: ボット設定の確認**
```bash
openclaw telegram:status
```

**ステップ3: Gatewayを再起動**
```bash
sudo systemctl restart openclaw-gateway
```

**ステップ4: ログでエラーを確認**
```bash
sudo journalctl -u openclaw-gateway -f
```

---

### Q28: APIキーが無効と表示される場合は？

**A:** 以下の手順で確認してください。

**ステップ1: APIキーの確認**
```bash
echo $KILO_CODE_API_KEY
```

**ステップ2: Kilo CodeダッシュボードでAPIキーを確認**
- https://kilocode.ai にログイン
- API Keysセクションでキーが有効か確認

**ステップ3: APIキーの再設定**
```bash
export KILO_CODE_API_KEY="your-new-api-key"
```

---

### Q29: モデルの切り替えが反映されない場合は？

**A:** 以下の手順で確認してください。

**ステップ1: 設定ファイルの確認**
```bash
cat ~/.config/openclaw/config.json
```

**ステップ2: 設定の再読み込み**
```bash
openclaw config:reload
```

**ステップ3: Gatewayを再起動**
```bash
sudo systemctl restart openclaw-gateway
```

---

### Q30: メモリ不足のエラーが出る場合は？

**A:** 以下の方法で対処できます。

**方法1: スワップを追加**
```bash
# 2GBのスワップファイルを作成
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# 永続化
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**方法2: Node.jsのメモリ制限を緩和**
```bash
export NODE_OPTIONS="--max-old-space-size=2048"
```

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
