# Sync Crowdsec CAPI blocklist to Cloudflare list

Automatically sync CrowdSec Community API (CAPI) IP blocklist to a Cloudflare IP list for edge-level protection.

## Overview

This service fetches threat intelligence from CrowdSec's Community API and updates a Cloudflare IP list to block malicious traffic at the edge. It prioritizes high-severity threats (exploits > bruteforce > scans) and handles Cloudflare's 10,000 item limit by selecting the most critical IPs.

**NOTE**: due to the Cloudflares limit on list length the script does not send the entire CAPI list to Cloudflare. You will not
get the full protection that the list offers. 

## Requirements

- CrowdSec installed with CAPI enabled
- Cloudflare account with API access
- An existing Cloudflare IP List - the script doesn't create it
- Python 3.7+
- Root access (for cscli command)

## Configuration

### Environment Variables (.env)
Don't forget to change the name of .env-EDITME to .env and
update all the settings in the file with your information. 

## Usage

### Manual execution:
```bash
cd /opt/cloudflare-sync
sudo venv/bin/python cf_blocklist_update.py
```

## How it works

1. **Fetch** - Retrieves CAPI decisions from CrowdSec
2. **Prioritize** - Sorts by threat level (exploit > bruteforce > scan)
3. **Limit** - Truncates to 9 900 IPs for Cloudflare limits
4. **Verify** - Checks list name matches configuration
5. **Update** - Replaces entire Cloudflare IP list

## Troubleshooting

- **Permission errors**: Ensure script runs as root for cscli access
- **API errors**: Verify Cloudflare credentials and that API token has list edit permissions
- **No IPs found**: Check CrowdSec CAPI enrollment status
- **No traffic dropped**: For the list to be effective you need a Cloudflare WAF Rule that uses it to drop traffic