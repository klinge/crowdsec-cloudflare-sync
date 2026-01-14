# Systemd Deployment Files

Systemd service and timer files for automated CrowdSec to Cloudflare synchronization.

## Files

- `cloudflare-list-sync.service` + `.timer` - Syncs CAPI community blocklist (runs twice daily)
- `cloudflare-rule-sync.service` + `.timer` - Syncs local CrowdSec decisions (runs every 3 hours)

## Installation

1. **Edit the service files** to match your installation paths:
   ```bash
   WorkingDirectory=/opt/cloudflare-sync
   ExecStart=/opt/cloudflare-sync/venv/bin/python /opt/cloudflare-sync/cf_blocklist_update.py
   ```

2. **Copy files to systemd directory**:
   ```bash
   sudo cp cloudflare-*.service /etc/systemd/system/
   sudo cp cloudflare-*.timer /etc/systemd/system/
   ```

3. **Reload systemd**:
   ```bash
   sudo systemctl daemon-reload
   ```

4. **Enable and start timers**:
   ```bash
   sudo systemctl enable --now cloudflare-list-sync.timer
   sudo systemctl enable --now cloudflare-rule-sync.timer
   ```

## Management

```bash
# Check timer status
sudo systemctl status cloudflare-list-sync.timer
sudo systemctl list-timers cloudflare-*

# Run service manually
sudo systemctl start cloudflare-list-sync.service

# View logs
sudo journalctl -u cloudflare-list-sync.service -f
```

## Schedule

- **list-sync**: Runs at 01:00 and 13:00 daily
- **rule-sync**: Runs every 3 hours starting at 01:00

Both have 15-minute randomized delay to avoid load spikes.
