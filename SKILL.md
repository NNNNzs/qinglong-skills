---
name: qinglong
description: Manage QingLong (青龙) panel — cron jobs, environment variables, scripts, dependencies, logs and system operations. Supports OpenClaw Gateway UI configuration for panel URL, client_id and client_secret.
metadata:
  {
    "openclaw":
      {
        "emoji": "🐉",
        "requires": { "bins": ["curl", "jq"] },
        "env":
          {
            "QINGLONG_URL":
              {
                "label": "青龙面板地址",
                "description": "QingLong panel base URL, e.g. http://192.168.1.100:5700",
                "required": true,
                "secret": false,
              },
            "QINGLONG_CLIENT_ID":
              {
                "label": "Client ID",
                "description": "Open API Client ID from QingLong panel → Configuration → Application",
                "required": true,
                "secret": false,
              },
            "QINGLONG_CLIENT_SECRET":
              {
                "label": "Client Secret",
                "description": "Open API Client Secret from QingLong panel → Configuration → Application",
                "required": true,
                "secret": true,
              },
          },
      },
  }
---

# QingLong Panel Skill

Control your [QingLong (青龙)](https://github.com/whyour/qinglong) scheduled task panel via REST API.

## Setup

1. Open QingLong web UI → **Configuration** → **Application**
2. Create a new application, select the scopes you need (crons / envs / scripts / logs / system)
3. Copy the **Client ID** and **Client Secret**
4. Set environment variables in OpenClaw Gateway UI:
   - `QINGLONG_URL` — panel address (e.g. `http://192.168.1.100:5700`)
   - `QINGLONG_CLIENT_ID` — the Client ID
   - `QINGLONG_CLIENT_SECRET` — the Client Secret

## Quick Start

Use the wrapper script for all operations:

```bash
# List all cron jobs
scripts/ql.sh cron list

# Get a specific cron job
scripts/ql.sh cron get <id>

# Create a cron job
scripts/ql.sh cron create --command "task test.js" --schedule "0 0 * * *" --name "My Task"

# Run a cron job
scripts/ql.sh cron run <id>

# Stop a running cron
scripts/ql.sh cron stop <id>

# Enable / Disable crons
scripts/ql.sh cron enable <id>
scripts/ql.sh cron disable <id>

# Delete crons
scripts/ql.sh cron delete <id>

# Pin / Unpin
scripts/ql.sh cron pin <id>
scripts/ql.sh cron unpin <id>

# Get cron log
scripts/ql.sh cron log <id>
```

### Environment Variables

```bash
# List all envs
scripts/ql.sh env list

# Create an env
scripts/ql.sh env create --name "JD_COOKIE" --value "pt_key=xxx;pt_pin=xxx" --remarks "备注"

# Update an env
scripts/ql.sh env update --id <id> --name "JD_COOKIE" --value "new_value"

# Delete envs
scripts/ql.sh env delete <id1> <id2>

# Enable / Disable envs
scripts/ql.sh env enable <id>
scripts/ql.sh env disable <id>
```

### Scripts

```bash
# List scripts
scripts/ql.sh script list

# Get script content
scripts/ql.sh script get --file "test.js"

# Create / Update script
scripts/ql.sh script save --file "test.js" --content "console.log('hello')"

# Run a script
scripts/ql.sh script run --file "test.js"

# Stop a running script
scripts/ql.sh script stop --file "test.js"

# Delete a script
scripts/ql.sh script delete --file "test.js"
```

### Dependencies

```bash
# List dependencies
scripts/ql.sh dep list

# Install dependencies
scripts/ql.sh dep install --name "axios" --type 0

# Reinstall dependencies
scripts/ql.sh dep reinstall <id>

# Delete dependencies
scripts/ql.sh dep delete <id>
```

### Subscriptions

```bash
# List subscriptions
scripts/ql.sh sub list

# Run / Stop subscription
scripts/ql.sh sub run <id>
scripts/ql.sh sub stop <id>

# Enable / Disable subscription
scripts/ql.sh sub enable <id>
scripts/ql.sh sub disable <id>
```

### Logs

```bash
# List log files
scripts/ql.sh log list

# Get log content
scripts/ql.sh log get --file "task.log" [--path "subfolder"]
```

### System

```bash
# Get system info
scripts/ql.sh system info

# Get system config
scripts/ql.sh system config

# Check for updates
scripts/ql.sh system check-update

# Reload system
scripts/ql.sh system reload

# Run a command
scripts/ql.sh system command-run --command "task test.js"
```

## Common Patterns

### Check all tasks status
```bash
scripts/ql.sh cron list | jq '.data[] | {id, name, status, schedule}'
```

### Find disabled tasks
```bash
scripts/ql.sh cron list | jq '.data[] | select(.isDisabled == 1) | {id, name}'
```

### Batch enable tasks
```bash
scripts/ql.sh cron enable 1 2 3 4 5
```

### Export envs
```bash
scripts/ql.sh env list | jq '.data[] | {name, value}'
```

## Troubleshooting

- **401 Unauthorized**: Check client_id and client_secret are correct
- **Connection refused**: Verify QINGLONG_URL is accessible from this machine
- **Token expired**: The script auto-refreshes tokens; no manual action needed
- **Scope error**: Ensure the application in QingLong has the required scopes enabled

## API Reference

The skill wraps the QingLong Open API. Key endpoints:

| Resource | Endpoints |
|----------|-----------|
| Cron Jobs | GET/POST/PUT/DELETE `/crons`, run/stop/enable/disable/pin/unpin |
| Envs | GET/POST/PUT/DELETE `/envs`, enable/disable |
| Scripts | GET/POST/PUT/DELETE `/scripts`, run/stop |
| Dependencies | GET/POST/PUT/DELETE `/dependencies`, reinstall |
| Subscriptions | GET/POST/PUT/DELETE `/subscriptions`, run/stop/enable/disable |
| Logs | GET/DELETE `/logs` |
| System | GET `/system`, config, reload, update, command-run |
| Config Files | GET/POST `/configs` |

See [references/api.md](references/api.md) for full API documentation.
