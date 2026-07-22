---
name: decker-setup
description: Connect this agent to the user's Decker account through a one-time browser device-authorization flow, store the issued API key in .env, and verify it. Use when the user says connect, set up, initialize, or log in to Decker, after installing the Decker skills, or when a Decker API request returns 401.
---

# Connect this agent to Decker

Run this handshake yourself. Request a device code, send the user to the Google-authenticated approval page, poll for approval, and save the issued key. Never ask the user to copy a credential.

## 1. Check the existing connection

Use `DECKER_URL` from `.env`, defaulting to `https://decker.site`. Load `DECKER_API_KEY` without printing it and call:

```bash
curl -fsS "$DECKER_URL/api/auth/whoami" \
  -H "Authorization: Bearer $DECKER_API_KEY"
```

If it succeeds, report which account is connected and stop. If the key is missing or the response is 401, continue.

## 2. Request a device code

```bash
curl -fsS -X POST "$DECKER_URL/device/code" \
  -H "content-type: application/json" \
  -d '{"client_name":"<agent and project name>"}'
```

Use a recognizable name such as `Codex @ pitch-deck`. The response contains `device_code`, `user_code`, `verification_url`, `verification_uri_complete`, `expires_in`, and `interval`.

## 3. Ask the user to approve

Open `verification_uri_complete` in the user's browser when possible. Otherwise show it as a clickable link and include the short `user_code`. Tell the user that the code expires in 15 minutes. Wait for their approval while continuing to poll; they do not need to paste anything back.

## 4. Poll at the instructed interval

Never poll faster than `interval` seconds:

```bash
curl -fsS -X POST "$DECKER_URL/device/token" \
  -H "content-type: application/json" \
  -d '{"device_code":"<device_code>"}'
```

- `pending`: keep polling.
- `ok`: immediately store the returned `api_key`.
- `denied`: stop and tell the user the connection was denied.
- `expired`: restart from step 2.

The API key is returned only once. Keep the device code and API key out of chat and logs.

## 5. Store and verify

Write or replace these lines in the project-root `.env` without duplicating them:

```text
DECKER_URL=https://decker.site
DECKER_API_KEY=dkr_live_...
```

If `.env` is not ignored, add it to `.gitignore` before writing the key. Call `GET /api/auth/whoami` again with the stored key. On success, tell the user the connected account and that the agent can now publish, update, delete, inspect analytics, and upload images for Decker decks.

For a self-hosted Decker instance, use the base URL supplied by the user throughout.
