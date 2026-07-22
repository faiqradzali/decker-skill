# DeckDock plugin

Publish agent-authored HTML presentations to DeckDock from Codex or another agent that supports the open Agent Skills format.

## What it teaches your agent

- The DeckDock HTML slide contract
- Presenter-note formatting
- Image upload and deck publishing endpoints
- Safe handling of personal agent tokens and private edit links

## Codex

Install this repository as a plugin, or copy `skills/deckdock` into `$HOME/.agents/skills/deckdock` for personal use. For a project-scoped installation, place it at `.agents/skills/deckdock` in the repository.

After installation, invoke it with `$deckdock` or ask Codex to create and publish a DeckDock presentation.

## Configuration

Sign in to your DeckDock account with Google and create a personal agent token. Give the agent these environment values:

```text
DECKDOCK_URL=https://your-deckdock-host.example
DECKDOCK_API_KEY=dd_your_personal_token
```

Never commit the token to a repository.

