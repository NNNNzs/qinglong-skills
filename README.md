# 🐉 qinglong-skills

OpenClaw skill for controlling [QingLong (青龙)](https://github.com/whyour/qinglong) panel.

## Features

- **Cron Jobs** — list, create, update, delete, run, stop, enable/disable, pin/unpin, view logs
- **Environment Variables** — list, create, update, delete, enable/disable
- **Scripts** — list, read, save, run, stop, delete
- **Dependencies** — list, install, reinstall, cancel, delete (node/linux/python3)
- **Subscriptions** — list, run, stop, enable/disable, delete, view logs
- **Logs** — list, read, delete
- **System** — info, config, update, reload, command run/stop, auth reset
- **Config Files** — list, read, save

## Setup

### 1. Get QingLong API Credentials

1. Open your QingLong web UI
2. Go to **Configuration** → **Application**
3. Click **Create Application**
4. Select the scopes you need (e.g. crons, envs, scripts, logs, system)
5. Copy the **Client ID** and **Client Secret**

### 2. Configure in OpenClaw Gateway UI

The skill defines three environment variables that can be configured in the Gateway UI:

| Variable | Description | Required |
|----------|-------------|----------|
| `QINGLONG_URL` | Panel base URL (e.g. `http://192.168.1.100:5700`) | ✅ |
| `QINGLONG_CLIENT_ID` | Open API Client ID | ✅ |
| `QINGLONG_CLIENT_SECRET` | Open API Client Secret | ✅ |

### 3. Install the Skill

Copy the `qinglong-skills` folder to your OpenClaw skills directory:

```bash
cp -r qinglong-skills ~/.openclaw/workspace/skills/qinglong
```

## Usage

### Via OpenClaw Agent

The agent can use the `scripts/ql.sh` wrapper script for all operations:

```bash
# List cron jobs
scripts/ql.sh cron list

# Create a cron job
scripts/ql.sh cron create --command "task test.js" --schedule "0 0 * * *" --name "My Task"

# Run a cron
scripts/ql.sh cron run 1

# List envs
scripts/ql.sh env list

# Create env
scripts/ql.sh env create --name "JD_COOKIE" --value "pt_key=xxx;pt_pin=xxx"

# Run a script
scripts/ql.sh script run --file "test.js"

# System info
scripts/ql.sh system info
```

### Direct API Calls

```bash
# Get token
curl "http://your-panel:5700/open/auth/token?client_id=YOUR_ID&client_secret=YOUR_SECRET"

# List crons
curl -H "Authorization: Bearer TOKEN" "http://your-panel:5700/api/crons"
```

## Documentation

- [SKILL.md](SKILL.md) — Full skill documentation
- [references/api.md](references/api.md) — Complete QingLong API reference

## License

MIT
