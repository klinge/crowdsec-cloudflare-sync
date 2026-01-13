# CrowdSec to Cloudflare Sync

Automatically sync CrowdSec threat intelligence to Cloudflare. 

## Overview

The Crowdsec has two bouncers for Cloudflare. The original one is deprecated due to new Cloudflare API rate 
limits. The Worker based bouncer is complex and not very suitable for a free Cloudflare plan.

Using both Crowdsec and Cloudflare I still wanted the edvantage of being able to off-load some of the blockling from 
my local server to the edge servers on Cloudflare. So this project contains two small python scripts 
that cover my main needs. 

1. **update_blocklist**: contains a script that pulls data from the Crowdsec community blocklist (CAPI) and updates
a Cloudflare IP List
2. **update-waf-rules** contains a script that lists locally banned IPs from the Crowdsec engine and updates 
a Cloudflare WAF ruleset 

## Requirements

- CrowdSec installed with CAPI enabled
- Cloudflare account with API access
- An existing Cloudflare IP List - the script doesn't create it
- An existing Cloudflare Security rule - again the script doesn't create it
- Python 3.7+

## Fair warning
The scripts has been tested and developed on a linux server. They should work on other OSes but that's nothing
that's been verified. 

The scripts (especially) the one that updates the ruleset can ruin your existing Cloudflare rules if something 
goes wrong, don't forget to make a backup of your ruleset. 

## License

MIT License