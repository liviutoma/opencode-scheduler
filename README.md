# opencode-scheduler

Schedule recurring jobs in OpenCode using native OS schedulers (launchd on macOS, systemd on Linux).

## Features

- **Native scheduling** - Uses launchd (Mac) or systemd (Linux) for reliable execution
- **Survives reboots** - Jobs persist and continue running after system restarts
- **Catches up on missed runs** - If your computer was asleep, jobs run when it wakes
- **Working directory support** - Run jobs from specific directories to pick up MCP configs
- **Environment variables** - Automatically includes PATH for node/npx access

## Installation

Add to your `opencode.json`:

```json
{
  "plugin": ["opencode-scheduler"]
}
```

Or install manually:

```bash
cd ~/.config/opencode
bun add opencode-scheduler
```

Then add to your `opencode.json`:

```json
{
  "plugin": ["opencode-scheduler"]
}
```

## Usage

### Schedule a job

```
Schedule a daily job at 9am to search for standing desks under $300
```

The plugin will create a job using cron syntax and install it in your OS scheduler.

### List jobs

```
Show my scheduled jobs
```

### Delete a job

```
Delete the standing-desk job
```

### Run a job immediately

```
Run the standing-desk job now
```

### View job logs

```
Show logs for standing-desk
```

## Tools

| Tool | Description |
|------|-------------|
| `schedule_job` | Create a new scheduled job with cron expression |
| `list_jobs` | List all scheduled jobs, optionally filter by source |
| `delete_job` | Remove a scheduled job |
| `run_job` | Execute a job immediately |
| `job_logs` | View the latest logs from a job |

## Cron Syntax

Jobs are scheduled using standard 5-field cron expressions:

```
┌───────────── minute (0-59)
│ ┌───────────── hour (0-23)
│ │ ┌───────────── day of month (1-31)
│ │ │ ┌───────────── month (1-12)
│ │ │ │ ┌───────────── day of week (0-6, Sunday=0)
│ │ │ │ │
* * * * *
```

Examples:
- `0 9 * * *` - Daily at 9:00 AM
- `0 */6 * * *` - Every 6 hours
- `30 8 * * 1` - Mondays at 8:30 AM
- `0 9,17 * * *` - At 9 AM and 5 PM daily

## Working Directory

Jobs run from a working directory, which is important for picking up `opencode.json` and MCP configurations:

```
Schedule a daily job at 9am from /path/to/project to run my-task
```

By default, jobs use the current working directory when created.

## Storage

- Jobs: `~/.config/opencode/jobs/*.json`
- Logs: `~/.config/opencode/logs/*.log`
- launchd plists: `~/Library/LaunchAgents/com.opencode.job.*.plist`
- systemd units: `~/.config/systemd/user/opencode-job-*.{service,timer}`

## Example: Facebook Marketplace Deal Finder

```
Schedule a daily job at 9am to:
1. Search Facebook Marketplace for posters under $100
2. Send the top 5 deals to my Telegram group
```

The plugin will:
1. Create a cron job for 9 AM daily
2. Install it via launchd/systemd
3. Run `opencode run "..."` with your prompt at the scheduled time
4. Log output to `~/.config/opencode/logs/`

## Troubleshooting

### Jobs not running

1. Check if the job is installed:
   - Mac: `launchctl list | grep opencode`
   - Linux: `systemctl --user list-timers | grep opencode`

2. Check logs:
   ```
   Show logs for my-job
   ```

3. Verify working directory has the right `opencode.json` with MCP configs

### MCP tools not available

Make sure the job's working directory contains an `opencode.json` with your MCP server configurations.

## License

MIT
