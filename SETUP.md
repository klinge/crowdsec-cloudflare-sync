# Setup Guide

This guide helps you find the Cloudflare IDs needed for configuration.

## Prerequisites

- Cloudflare account with API token
- `curl` or similar HTTP client
- `jq` for JSON parsing (optional but recommended)

## Getting Your Cloudflare IDs

### 1. Account ID and Zone ID

Both are visible in the Cloudflare dashboard by going to:
- **Dashboard → Select your domain → Overview** (right sidebar, under "API")

### 2. API Token

Create a token with these permissions:
- Account: Account Rulesets:Edit, Account Filter Lists:Edit, Account Firewall Access Rules:Edit
- Zone: Zone:Read, Firewall Services:Edit

Dashboard → My Profile → API Tokens → Create Token

### 3. List ID (for update-blocklist script)

First, create an IP List in Cloudflare dashboard:
- Dashboard → Manage Account → Configurations → Lists → Create new list

Then get the List ID via API:

```bash
curl -X GET "https://api.cloudflare.com/client/v4/accounts/{ACCOUNT_ID}/rules/lists" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -H "Content-Type: application/json" | jq '.result[] | {id, name}'
```

Find your list by name and copy the `id`.

### 4. Ruleset ID and Rule ID (for update-waf-rules script)

First, create a WAF rule in Cloudflare dashboard:
- Dashboard → Select domain → Security → WAF → Create rule
- Set expression to: `(ip.src in {})`
- Set action (e.g., "Block")
- Deploy the rule

Then get the Ruleset ID:

```bash
curl -X GET "https://api.cloudflare.com/client/v4/zones/{ZONE_ID}/rulesets" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -H "Content-Type: application/json" | jq '.result[] | select(.phase=="http_request_firewall_custom") | {id, name}'
```

Copy the ruleset `id`.

Then get the Rule ID:

```bash
curl -X GET "https://api.cloudflare.com/client/v4/zones/{ZONE_ID}/rulesets/{RULESET_ID}" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -H "Content-Type: application/json" | jq '.result.rules[] | {id, description}'
```

Find your rule by description and copy the `id`.

## Configuration

Copy `.env-EDITME` to `.env` and fill in all the values:

```bash
cp .env-EDITME .env
nano .env  # or your preferred editor
```

## Testing

Always test with dry-run first:

```bash
# Test blocklist sync
cd update-blocklist
python cf_blocklist_update.py --dry-run

# Test WAF rule sync
cd update-waf-rules
python cf_ruleset_update.py --dry-run
```

## Troubleshooting

**"Rule with ID not found"**
- Verify the RULESET_ID and RULE_ID are correct
- Ensure the rule exists in the specified zone

