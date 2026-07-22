---
name: deckdock
description: Create, publish, update, and upload images for HTML slide decks on DeckDock. Use when a user asks an agent to make, host, publish, revise, or remove a DeckDock presentation.
---

# DeckDock publishing

Create slide decks as an HTML fragment. Each slide must be one top-level element:

```html
<section class="slide" data-notes="Introduce the key idea and pause.">
  <p class="eyebrow">Quarterly review</p>
  <h1>Momentum compounds.</h1>
</section>
```

## Contract

- Use one `<section class="slide">` per slide.
- Put presenter notes in the optional `data-notes` attribute.
- Include all deck styles in one `<style>` element in the fragment.
- Design for a 16:9 viewport. Each `.slide` should use `width: 100%; height: 100%`.
- Use absolute image URLs. Upload local images to `/api/images` first.
- Do not include navigation; DeckDock supplies it.
- Scripts are allowed, but the public player runs the deck in a sandboxed iframe.

## API

Ask the user for their DeckDock URL and personal agent token. Store them as `DECKDOCK_URL` and `DECKDOCK_API_KEY`, then send `Authorization: Bearer $DECKDOCK_API_KEY`. Users create or rotate this token after signing in to DeckDock with Google. Never print the token or commit it to source control.

- `POST /api/decks` with `{title, slug, description, html, listed}`
- `PUT /api/decks/{id}` with any fields to change
- `GET /api/decks/{id}` to fetch the source and metadata
- `DELETE /api/decks/{id}` to remove a deck
- `POST /api/images` as multipart form-data with field `file`

Publishing returns `share_url`, `present_url`, and a private `edit_url`. Show those links to the user. Treat `edit_url` as sensitive because it permits visual changes to the deck.

