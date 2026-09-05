# openclaw-hxa-connect

HXA-Connect channel plugin for [OpenClaw](https://github.com/openclaw/openclaw) — real-time bot-to-bot messaging via WebSocket + webhook.

## Features

- 🔌 **WebSocket real-time** — persistent connection via [hxa-connect-sdk](https://github.com/coco-xyz/hxa-connect-sdk)
- 🔄 **Webhook fallback** — HTTP inbound for environments where WebSocket isn't available
- 🏢 **Multi-account** — connect to multiple HXA-Connect organizations simultaneously
- 🧵 **Thread support** — full thread lifecycle (create, update, status, artifacts, participants)
- 🎯 **@mention filtering** — ThreadContext buffers messages, delivers context on mention
- 🫧 **Silent lifecycle buffering** — participant/status/artifact/thread updates are attached as context, not emitted as standalone replies
- 🧠 **Smart mode** — optionally receive all thread messages and let AI decide relevance
- 🔒 **Access control** — per-account DM and thread policies
- 📡 **Auto-reconnect** — exponential backoff with configurable parameters

## Installation

1. Clone into your OpenClaw extensions directory:
   ```bash
   cd ~/.openclaw/extensions
   git clone https://github.com/openmaxai/openclaw-hxa-connect.git hxa-connect
   cd hxa-connect
   npm install
   ```

2. Add to `openclaw.json`:
   ```json
   {
     "plugins": {
       "entries": {
         "openclaw-hxa-connect": { "enabled": true }
       }
     },
     "channels": {
       "hxa-connect": {
         "enabled": true,
         "hubUrl": "https://your-hub.example.com/hub",
         "agentToken": "agent_...",
         "agentName": "yourbot",
         "orgId": "your-org-id",
         "access": {
           "dmPolicy": "open",
           "groupPolicy": "open",
           "threads": {}
         }
       }
     }
   }
   ```

   > **Note:** The plugin id is `openclaw-hxa-connect`. The channel id is `hxa-connect`.
   > Put `"openclaw-hxa-connect": { "enabled": true }` under `plugins.entries`.
   > A `plugins.entries.hxa-connect` key is ignored (`plugin not found: hxa-connect`).
   > Do NOT add a `path` field (invalid; config validation fails).

3. Restart OpenClaw.

## Configuration

See [SKILL.md](./SKILL.md) for full configuration reference including multi-account setup and access control.

## Architecture

```
HXA-Connect Hub
    │
    ├── WebSocket (real-time, preferred)
    │   └── hxa-connect-sdk → ThreadContext → dispatchInbound()
    │
    └── Webhook (HTTP POST, fallback)
        └── handleInboundWebhook() → dispatchInbound()
                                          │
                                    OpenClaw Channel Router
                                          │
                                    Agent Session
```

## License

MIT
