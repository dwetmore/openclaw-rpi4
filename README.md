# Openclaw RPi4

Project record for the Openclaw deployment running on the Raspberry Pi 4 host `Garagely`.

This project documents the configuration and operational commands for the live Openclaw gateway, Telegram channel, Codex-backed main agent, and user service. It intentionally does not store secrets.

## Current Deployment

- Host: Raspberry Pi 4, `linux 6.12.75+rpt-rpi-v8`, `arm64`
- Node: `24.16.0`
- Openclaw: `2026.5.28`
- Gateway service: user systemd service `openclaw-gateway.service`
- Gateway command: `/usr/bin/node /home/dwetmore/.npm-global/lib/node_modules/openclaw/dist/index.js gateway --port 18789`
- Dashboard: `http://192.168.0.168:18789/`
- State directory: `/home/dwetmore/.openclaw`
- Config file: `/home/dwetmore/.openclaw/openclaw.json`
- Service file: `/home/dwetmore/.config/systemd/user/openclaw-gateway.service`

## Enabled Capabilities

- Gateway mode: local
- Gateway bind: LAN
- Gateway auth: token auth, stored only in the live Openclaw config
- Control UI allowed origins:
  - `http://localhost:18789`
  - `http://127.0.0.1:18789`
  - `https://garagely.tailc5b7e8.ts.net`
- Telegram channel:
  - enabled
  - token loaded from `/home/dwetmore/.openclaw/secrets/telegram-bot-token`
  - DM policy: pairing
  - group policy: allowlist
  - group messages require mention
- Command owner:
  - `telegram:8670716857`
- Default model:
  - `openai-codex/gpt-5.5`
- OpenAI Codex auth:
  - OAuth profile for `davida.wetmore@gmail.com`
- Memory:
  - Openclaw memory state at `/home/dwetmore/.openclaw/memory/main.sqlite`

## Operational Commands

Run commands with the deployed home, state, and config paths pinned:

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw status
```

Check Telegram:

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw channels status
```

Restart the gateway:

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw gateway restart
```

Tail gateway logs:

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw logs --plain --limit 120
```

Check the process:

```bash
ps aux | rg -i 'openclaw|telegram|agent'
```

## Recovery Notes

On June 5, 2026, Telegram stopped responding even though the gateway health endpoint was live. Diagnosis showed:

- Telegram transport was connected.
- Telegram channel worker was `stopped` with health `not-running`.
- Incoming Telegram messages were present in `/home/dwetmore/.openclaw/telegram/ingress-spool-default`.
- Restarting `openclaw-gateway.service` recovered the channel.
- Logs confirmed successful outbound Telegram sends after restart.

Primary recovery action:

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw gateway restart
```

## Non-Goals

- Do not commit live secrets.
- Do not copy Telegram bot token values.
- Do not copy gateway auth token values.
- Do not copy OAuth credentials.
- Do not treat generated session logs as source files.
