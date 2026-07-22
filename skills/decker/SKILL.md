---
name: decker
description: Create, publish, update, and upload images for HTML slide decks on Decker. Use when a user asks an agent to make, host, publish, revise, or remove a Decker presentation.
---

# Decker publishing

Create slide decks as an HTML fragment. Each slide must be one top-level element:

```html
<section class="slide" data-notes="Introduce the key idea and pause.">
  <p class="eyebrow">Quarterly review</p>
  <h1>Momentum compounds.</h1>
</section>
```

## Contract

- Use one `<section class="slide">` per slide.
- Send an HTML fragment only. Do not include `<!doctype>`, `<html>`, `<head>`, or `<body>`.
- Keep every `.slide` as a top-level element; do not put slides inside an app shell or JavaScript mount point.
- Put presenter notes in the optional `data-notes` attribute.
- Include all deck styles in one `<style>` element in the fragment.
- Design for a 16:9 viewport. Each `.slide` should use `width: 100%; height: 100%`.
- Do not leave slides hidden with `opacity: 0`, `visibility: hidden`, off-screen transforms, or `content-visibility: hidden`. Decker controls visibility with `.active`.
- If you add transitions, `.slide.active` must still render visibly without JavaScript.
- Use absolute image URLs. Upload local images to `/api/images` first.
- Do not include navigation; Decker supplies it.
- Scripts are allowed, but the public player runs the deck in a sandboxed iframe.

Before publishing, verify that the fragment contains at least one literal `<section class="slide">` and that adding `active` to a slide makes its content visible immediately.

## API

Use `DECKER_URL` (default `https://decker.site`) and `DECKER_API_KEY`, then send `Authorization: Bearer $DECKER_API_KEY`. Before any API work, verify the connection with `GET /api/auth/whoami`. If the key is missing or the request returns 401, run the `$decker-setup` skill. Do not ask the user to create or paste a token manually.

Never print the API key or commit it to source control.

- `POST /api/decks` with `{title, slug, description, html, listed}`
- `PUT /api/decks/{id}` with any fields to change
- `GET /api/decks/{id}` to fetch the source and metadata
- `DELETE /api/decks/{id}` to remove a deck
- `POST /api/images` as multipart form-data with field `file`

After `POST /api/decks` or `PUT /api/decks/{id}`, inspect `markup_warnings`. If it is non-empty, apply every `agent_action` whose `auto_fixed` value is `false`, update the HTML, and retry. A `422` response contains `detail.issues`; fix every issue using its `agent_action` and retry. Do not report the deck as complete while unresolved warnings or validation issues remain.

Publishing returns `share_url`, `present_url`, and a private `edit_url`. Show those links to the user. Treat `edit_url` as sensitive because it permits visual changes to the deck.
