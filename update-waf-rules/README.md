# Cloudflare WAF Rule Updater

Updates a Cloudflare WAF rule expression with locally banned IPs from CrowdSec.

## Overview

This script fetches banned IP addresses from your local CrowdSec instance and updates a specific Cloudflare WAF rule with these IPs. It's designed to sync local threat intelligence to Cloudflare's edge for improved protection.

## Usage

```bash
# Dry run (recommended first)
python cf_ruleset_update.py --dry-run

# Update Cloudflare rule
python cf_ruleset_update.py
```

## How it works

1. Fetches banned IPs from CrowdSec using `cscli decisions list`
2. Formats IPs into Cloudflare expression: `(ip.src in {ip1 ip2 ip3})`
3. Updates the specified WAF rule with the new expression
4. Preserves existing rule properties (action, description, enabled status)

## Warnings

- Test with `--dry-run` first
- Will overwrite existing rule expressions
- No API rate limiting - don't run too frequently
- Requires root access for CrowdSec integration

## License

MIT License