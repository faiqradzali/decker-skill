# Decker plugin

Publish agent-authored HTML presentations to Decker from Codex or another agent that supports the open Agent Skills format.

## What it teaches your agent

- The Decker HTML slide contract
- Presenter-note formatting
- Image upload and deck publishing endpoints
- A browser device-authorization flow that the agent runs itself
- Safe handling of agent credentials and private edit links

## Codex

Install this repository as a plugin, or copy `skills/decker` into `$HOME/.agents/skills/decker` for personal use. For a project-scoped installation, place it at `.agents/skills/decker` in the repository.

After installation, invoke it with `$decker` or ask Codex to create and publish a Decker presentation.

## Connect

Ask the agent to “Connect Decker.” It requests a short-lived device code, opens a Google-authenticated approval page, polls until you approve, and stores these environment values itself:

```text
DECKER_URL=https://your-decker-host.example
DECKER_API_KEY=dkr_live_your_agent_key
```

Never commit the token to a repository.
