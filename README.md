# Docker container for Rclone Sync

A minimal Alpine Linux container that mirrors a source rclone remote to a target rclone remote on a cron schedule using `rclone sync`.

## How it works

On startup, the container snapshots its environment to a file (so cron jobs can access it), installs a crontab from the `SYNC_SCHEDULE` variable (defaulting to midnight daily), and runs `crond` in the foreground. Each scheduled run executes `rclone sync` to mirror the source to the target.

A lockfile (`flock`) prevents overlapping runs, and failed syncs can optionally send notifications to a webhook (e.g. Discord).

## Configuration

### Environment variables

| Variable | Default | Description |
|---|---|---|
| `SYNC_SCHEDULE` | `0 0 * * *` | Cron schedule for the sync job |
| `RCLONE_SOURCE` | | Source rclone remote and path (e.g. `b2:my-source-bucket`) |
| `RCLONE_TARGET` | | Target rclone remote and path (e.g. `b2:my-target-bucket`) |
| `RCLONE_CONFIG` | `/root/.config/rclone/rclone.conf` | *(Optional)* Path to the rclone configuration file |
| `NOTIFY_FAILURE_WEBHOOK_URL` | | *(Optional)* Webhook URL for failure notifications |
| `NOTIFY_FAILURE_USERNAME` | | *(Optional)* Username for failure notifications |

### Rclone configuration

To configure a remote interactively:

```sh
docker exec -it <container> rclone config
```

Mount a volume at the config file's parent directory to persist the configuration across container restarts. Set `RCLONE_CONFIG` to change the config file location.

## Running a manual sync

```sh
docker exec <container> /usr/local/bin/sync
```

## License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.
