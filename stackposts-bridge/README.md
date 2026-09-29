# Stackposts GitHub Bridge

This folder is the queue/result bridge between ChatGPT and the Someguys Stackposts Automation API.

## One-time setup

Add this GitHub Actions repository secret:

- Name: `STACKPOSTS_API_KEY`
- Value: the Stackposts Automation API key

Repository: `pr-pavlenko/Someguys-signature`

Path in GitHub:
Settings → Secrets and variables → Actions → New repository secret

The secret is never stored in queue files or committed to the repository.

## Queue format

Create one JSON file in:

`stackposts-bridge/queue/`

Example:

```json
{
  "idempotency_key": "sg-example-20260929-001",
  "account_ids": [3],
  "caption": "Example caption",
  "media_urls": ["https://raw.githubusercontent.com/.../image.jpg"],
  "mode": "draft",
  "network_options": {
    "facebook": {"post_to": "feed"}
  }
}
```

Scheduled example:

```json
{
  "idempotency_key": "sg-example-20260929-002",
  "account_ids": [3, 4, 5],
  "caption": "Example scheduled caption",
  "media_urls": ["https://raw.githubusercontent.com/.../image.jpg"],
  "mode": "scheduled",
  "schedule_at": "2026-09-29T14:00:00-04:00",
  "network_options": {
    "facebook": {"post_to": "feed"},
    "instagram": {"post_to": "feed"},
    "linkedin_page": {"linkedin_post_type": "auto"}
  }
}
```

## Results

The workflow writes API responses to:

`stackposts-bridge/results/<idempotency_key>.json`

A successful request has:

- `ok: true`
- `http_code: 201`
- Stackposts post IDs under `response.data`

The result file makes the operation auditable and lets ChatGPT verify success through GitHub alone.

## Important

Do not place the Stackposts API key inside queue JSON or workflow source.

The current repository is public. Queue files therefore contain public post copy, schedule metadata, account IDs, and media URLs. Use a private bridge repository instead if future queued content must stay private before publication.
