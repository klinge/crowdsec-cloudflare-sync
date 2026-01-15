# Cloudflare WAF Rule Updater

Updates a Cloudflare WAF rule expression with locally banned IPs from CrowdSec.

## Overview

This script fetches banned IP addresses from your local CrowdSec instance and updates a specific Cloudflare WAF rule with these IPs. It's designed to sync local threat intelligence to Cloudflare's edge for improved protection.

## Usage

### Manual execution:
```bash
# Dry run (recommended first)
python cf_ruleset_update.py --dry-run

# Update Cloudflare rule
python cf_ruleset_update.py
```

## Configuration

### Environment Variables (.env)
Don't forget to change the name of .env-EDITME to .env and
update all the settings in the file with your information.

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

## Troubleshooting

- **Permission errors**: Ensure script runs as root for cscli access
- **API errors**: Verify Cloudflare credentials and that API token has list edit permissions
- **No IPs found**: Verify local crowdsec operation. Test that you have locally banned IPs 
with: "sudo cscli decisions list"

