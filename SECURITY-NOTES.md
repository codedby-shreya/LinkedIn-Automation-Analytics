# Security checklist

- The original content workflow contained a hardcoded Cloudflare bearer token. Revoke/rotate it before sharing anything publicly.
- The JSON files in `workflows/` are sanitized copies; the originals are unchanged.
- Reconnect credentials inside n8n after importing. Credential references were removed from the public copies.
- Confirm no secrets, private sheet data, execution data, or personal identifiers remain before pushing.
- If a secret was ever pushed to Git, rotate it and clean the repository history; a new commit deleting the secret is not sufficient.
