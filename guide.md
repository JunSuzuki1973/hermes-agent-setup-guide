# Hermes Agent セットアップウィザード実践ガイド - 実際のQAラリー

このガイドでは、Hermes Agentの初期設定ウィザードで実際に交わされる質問と回答を、実際のラリー形式で再現します。

---

## 目次

1. [インストール開始](#1-インストール開始)
2. [基本設定](#2-基本設定)
3. [機能設定](#3-機能設定)
4. [セッション管理](#4-セッション管理)
5. [チャネル設定](#5-チャネル設定)
6. [Gateway設定](#6-gateway設定)
7. [ツール選択](#7-ツール選択)
8. [完了](#8-完了)

---

## 1. インストール開始

### Q: OpenClaw Installation Detected

```
Found OpenClaw data at /home/jun/.openclaw
Hermes can import your settings, memories, skills, and API keys.

Would you like to import from OpenClaw? [Y/n]:
```

**A: n**  
**推奨理由:** ネオは「新しい」エージェントだから、リリスとは別の設定で1から育てるのが良いため。

---

## 2. 基本設定

### Q: Same-Provider Fallback & Rotation

```
Enable same-provider fallback and rotation?
This allows Hermes to automatically rotate between providers if one fails.
[y/N]:
```

**A: y**  
**推奨理由:** 1つのプロバイダがダウンした場合でも、他のプロバイダに自動的に切り替わるので、サービスの可用性が向上するため。特に商用利用では重要。

---

### Q: Vision & Image Analysis

```
Enable vision and image analysis capabilities?
[y/N]:
```

**A: y**  
**推奨理由:** 最新のAI機能であるビジョン解析を活用できるため。画像の解析、スクリーンショットの理解など、幅広いタスクで役立つ。

---

### Q: Text-to-Speech Provider

```
Select TTS provider:
1. Aivis Cloud
2. ElevenLabs
3. Google Cloud TTS
4. None
[1-4]:
```

**A: 1 (Aivis Cloud)**  
**推奨理由:** 国内サービスで、高品質な日本語音声合成が可能で、安定性も高いため。JUNはAivis Cloudを積極的に使用している。

---

### Q: Terminal Backend

```
Select terminal backend:
1. node-pty
2. xterm.js
3. None (no terminal support)
[1-3]:
```

**A: 1 (node-pty)**  
**推奨理由:** インタラクティブなターミナル操作が必要な場合に最も安定して動作するため。

---

## 3. 機能設定

### Q: Tool Progress Display

```
Show tool execution progress in chat?
[y/N]:
```

**A: y**  
**推奨理由:** ツールの実行状況がリアルタイムで分かるので、待機時間が明確になり、ユーザー体験が向上するため。

---

### Q: Context Compression

```
Enable context compression?
This reduces token usage by compressing historical messages.
[y/N]:
```

**A: y**  
**推奨理由:** 長い会話でもトークン使用量を抑えられるため、コスト削減に効果的。特にVPS環境では重要。

---

## 4. セッション管理

### Q: Session reset mode

```
Select session reset mode:
1. manual - Reset only when explicitly requested
2. auto - Reset after each interaction
3. smart - Reset based on context length
[1-3]:
```

**A: 3 (smart)**  
**推奨理由:** 文脈に応じて適切にリセットされるので、メモリ効率と会話の継続性のバランスが取れるため。

---

### Q: Inactivity timeout

```
Set inactivity timeout (minutes):
[0-1440]:
```

**A: 30**  
**推奨理由:** 30分間操作がない場合に自動的にセッションを終了することで、リソースを無駄に消費しないため。長すぎるとメモリが枯渇するリスクがあり、短すぎると会話が途切れる可能性があるため30分が適切。

---

### Q: Daily reset hour

```
Set daily reset hour (0-23):
```

**A: 3**  
**推奨理由:** 深夜3時（JST）は利用が最も少ない時間帯なので、リセットによる影響が最小限になるため。

---

## 5. チャネル設定

### Q: Telegram Bot Setup

```
Found existing Telegram bot configuration: jun_lilith_bot
Would you like to reuse this bot? [Y/n]:
```

**A: y**  
**推奨理由:** 既にリリス用に設定されているボットを流用することで、BotFatherでの新規作成手続きを省略できるため。ただし、ネオ専用のボットを作りたい場合はnを選択する。

---

### Q: Discord Integration

```
Enable Discord integration?
[y/N]:
```

**A: n**  
**推奨理由:** 現時点ではDiscordでの利用予定がないため。必要になったら後から設定できる。

---

### Q: Slack Integration

```
Enable Slack integration?
[y/N]:
```

**A: n**  
**推奨理由:** 現時点ではSlackでの利用予定がないため。必要になったら後から設定できる。

---

### Q: WhatsApp Webhooks

```
Enable WhatsApp webhook integration?
[y/N]:
```

**A: n**  
**推奨理由:** 現時点ではWhatsAppでの利用予定がないため。必要になったら後から設定できる。

---

## 6. Gateway設定

### Q: Gateway System Service

```
Set up OpenClaw Gateway as a system service?
This ensures Hermes is always running, even after system reboot.
[y/N]:
```

**A: y**  
**推奨理由:** VPS環境では常時実行が前提なので、systemdサービスとして設定することで、システム起動時に自動的に起動し、プロセスが落ちても自動再起動されるため。

**実際の出力例:**
```
✓ Created /etc/systemd/system/openclaw-gateway.service
✓ Reloaded systemd daemon
✓ Enabled openclaw-gateway service
✓ Started openclaw-gateway service

Gateway is now running as a system service.
Status: active (running)
```

---

## 7. ツール選択

### Q: CLI Tools Selection

```
Select tools to enable for CLI (comma-separated):
Available: browser, search, image, tts, code, weather, files
```

**A: browser,search,image,tts,code,weather,files**  
**推奨理由:** すべての主要ツールを有効にすることで、幅広いタスクに対応できるため。特にJUNの用途では、検索、ブラウザ操作、画像生成、TTSなどが頻繁に使われる。

---

### Q: Telegram Tools Selection

```
Select tools to enable for Telegram (comma-separated):
Available: browser, search, image, tts, code, weather, files
```

**A: browser,search,image,tts,code,weather,files**  
**推奨理由:** CLIと同じツールセットを有効にすることで、どのインターフェースからも一貫した機能が使えるため。

---

### Q: Browser Provider

```
Select browser provider:
1. Chrome Extension Relay (requires browser extension)
2. Isolated OpenClaw Browser
[1-2]:
```

**A: 1 (Chrome Extension Relay)**  
**推奨理由:** 既存のChromeブラウザを拡張機能経由で制御できるため、リソース効率が良く、ユーザーの操作履歴やCookieなどを共有できるため。ただし、拡張機能のインストールが必要。

---

### Q: Image Generation Provider

```
Select image generation provider:
1. OpenAI DALL-E
2. Nano Banana Pro
3. None
[1-3]:
```

**A: 2 (Nano Banana Pro)**  
**推奨理由:** 高品質な画像生成ができ、JUNの環境ですでに利用可能なため。

---

### Q: TTS Provider (Detailed)

```
Select primary TTS provider:
1. Aivis Cloud
2. ElevenLabs
3. Google Cloud TTS
[1-3]:
```

**A: 1 (Aivis Cloud)**  
**推奨理由:** 日本語音声の品質が高く、レイテンシも低いため。JUNの主要なTTSプロバイダとして定着している。

---

### Q: Vision Backend

```
Select vision analysis backend:
1. GPT-4o (via Kilo Code)
2. Claude 3.5 Sonnet
3. Gemini Pro Vision
[1-3]:
```

**A: 1 (GPT-4o via Kilo Code)**  
**推奨理由:** 最も高性能なビジョンモデルで、画像解析の精度が高いため。Kilo Code経由でコスト効率も良い。

---

### Q: Search Provider

```
Select search provider:
1. Brave Search API
2. SerpAPI (Google Search)
3. None
[1-3]:
```

**A: 1 (Brave Search API)**  
**推奨理由:** プライバシーに配慮された検索エンジンで、APIの安定性も高いため。また、JUNの環境で既にBrave Searchが設定されている。

---

## 8. 完了

### Q: Setup Complete

```
✓ Hermes Agent setup complete!

Summary:
- Agent name: neo
- OpenClaw import: No
- Same-provider fallback: Enabled
- Vision capabilities: Enabled
- TTS provider: Aivis Cloud
- Terminal backend: node-pty
- Tool progress: Enabled
- Context compression: Enabled
- Session reset: smart
- Inactivity timeout: 30 minutes
- Daily reset: 03:00 JST
- Telegram bot: jun_lilith_bot (reused)
- Gateway service: Enabled
- CLI tools: browser,search,image,tts,code,weather,files
- Telegram tools: browser,search,image,tts,code,weather,files
- Browser provider: Chrome Extension Relay
- Image generation: Nano Banana Pro
- TTS provider: Aivis Cloud
- Vision backend: GPT-4o (via Kilo Code)
- Search provider: Brave Search API

Next steps:
1. Start the gateway: openclaw gateway start
2. Test your agent via Telegram or CLI
3. Check documentation: openclaw docs

Press Enter to exit...
```

**A: (Enterキーを押す)**  
**推奨理由:** セットアップが完了したので、ウィザードを終了して実際に使用を開始するため。

---

## トラブルシューティング

### Gatewayが起動しない場合

```bash
# ステータス確認
sudo systemctl status openclaw-gateway

# ログ確認
sudo journalctl -u openclaw-gateway -f

# 手動起動でエラーを確認
openclaw gateway start
```

### Telegramボットが応答しない場合

```bash
# Gatewayが実行中か確認
openclaw gateway status

# Telegram設定を確認
openclaw telegram:status

# Gatewayを再起動
sudo systemctl restart openclaw-gateway
```

### 設定を変更する場合

```bash
# 設定ファイルの場所
~/.config/openclaw/config.json
~/.config/openclaw/providers.json

# 設定変更後はGatewayを再起動
sudo systemctl restart openclaw-gateway
```

---

## 追加リソース

- [OpenClaw公式ドキュメント](https://github.com/openclaw/openclaw)
- [Kilo Codeドキュメント](https://docs.kilocode.ai)
- [Telegram Bot API](https://core.telegram.org/bots/api)

---

*最終更新日: 2026-04-03*
