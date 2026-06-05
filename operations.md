# Operations

## Health Check

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw health
```

## Telegram Status

Expected healthy line:

```text
Telegram default: enabled, configured, running, connected
```

Command:

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw channels status
```

## Restart Gateway

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw gateway restart
```

## Inspect Service

```bash
systemctl --user status openclaw-gateway.service --no-pager
```

If the user bus is not available from the current shell, use Openclaw's gateway command instead:

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw gateway status
```

## Logs

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw logs --plain --limit 120
```

Direct log path observed on June 5, 2026:

```text
/tmp/openclaw-1000/openclaw-2026-06-05.log
```

## Telegram Recovery Pattern

Symptoms seen on June 5, 2026:

- Openclaw HTTP health endpoint returned live.
- Telegram transport still polled inbound messages.
- Channel status reported `stopped` and `health:not-running`.
- Incoming messages remained in `~/.openclaw/telegram/ingress-spool-default`.

Fix:

```bash
env HOME=/home/dwetmore \
  OPENCLAW_STATE_DIR=/home/dwetmore/.openclaw \
  OPENCLAW_CONFIG_PATH=/home/dwetmore/.openclaw/openclaw.json \
  /home/dwetmore/.npm-global/bin/openclaw gateway restart
```

Verification:

- `openclaw channels status` reports `running, connected`.
- Gateway logs include `telegram outbound send ok`.
