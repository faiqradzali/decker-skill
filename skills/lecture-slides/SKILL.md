---
name: lecture-slides
description: Create complete, practical educational lecture decks as Decker-compatible HTML slides, with clear learning progression, speaker notes, worked examples, section separators, diagrams, evidence, citations, and accessible visual design. Use for lectures, lessons, course modules, teaching presentations, classroom slides, workshop instruction, or converting source materials into independently reviewable educational slide notes for Decker.
---

# Create educational lecture slides for Decker

Read [references/lecture-design.md](references/lecture-design.md) completely before planning or authoring the deck. Treat it as the lecture-design specification. Follow the Decker HTML contract below whenever it differs from the generic HTML behavior in that reference.

## Establish the brief

Extract or reasonably infer:

- subject and lecture title;
- audience and assumed knowledge;
- learning outcome;
- topics, framework, examples, and source materials;
- language and visual identity;
- whether whiteboard, demonstration, or discussion segments are genuinely part of the lesson.

Ask only for missing information that would materially change the teaching structure. Do not invent an unrelated framework or exercise merely to fill a gap.

## Plan the learning progression

Create a slide-by-slide outline before writing HTML. Start with a minimal title, roadmap, central model or question, and an end-to-end overview when appropriate. Teach each major topic in sequence and add a minimal separator at each major transition.

Prefer 35–50 focused slides for a full lecture. Split dense explanations and derivations rather than shrinking text. Give every slide one teaching purpose and a direct title whose sequence alone communicates the lesson.

Use visible content as durable learner notes. Put presenter guidance, transitions, caveats, likely questions, facilitation, and demonstration instructions in speaker notes.

## Use the Decker HTML contract

Return one HTML fragment, not a complete document. Follow these rules exactly:

- Represent every slide with one top-level `<section class="slide">`.
- Put presenter notes in that section's `data-notes` attribute. HTML-escape quotation marks and special characters in notes.
- Put all deck CSS in one `<style>` element within the fragment.
- Design every slide for 16:9 with `.slide { position: relative; width: 100%; height: 100%; }` so recurring absolute-positioned elements remain anchored to their slide.
- Keep recurring labels and progress indicators inside each slide when needed.
- Use semantic HTML, useful `alt` text, strong contrast, and print-readable sizing.
- Use absolute image URLs. Upload local images through `POST /api/images` before embedding them.
- Do not include document tags, an outer app shell, navigation buttons, keyboard handlers, a slide counter, fullscreen controls, a notes toggle, or print orchestration. Decker supplies all presentation behavior.
- Avoid scripts unless the teaching content genuinely requires them. The public player uses a sandboxed iframe.

Minimal structure:

```html
<style>
  .slide { position: relative; width: 100%; height: 100%; box-sizing: border-box; }
</style>

<section class="slide" data-notes="Introduce the objective and connect it to prior knowledge.">
  <p class="topic-label">Course · Lecture 04</p>
  <div class="teaching-content">
    <h1>Why the mechanism produces this result</h1>
    <p>A concise description of the learning outcome.</p>
  </div>
</section>
```

## Build explanatory visuals

Choose formats based on the relationship being taught:

- process diagrams for sequences;
- timelines for change over time;
- trees or mind maps for categories;
- tables for exact comparisons;
- charts for quantitative patterns;
- annotated images when the visual itself is evidence;
- staged worked examples for reasoning and calculation.

Keep reading direction clear and labels short. Avoid decorative diagrams, repeated card grids, dashboard layouts, crossing arrows, and boxes without semantic purpose.

For sourced facts, quotations, data, and research, use credible references and place a discreet source on the relevant slide. Never invent evidence. Explain the takeaway instead of displaying data without interpretation.

## Connect and publish

Use `DECKER_URL` (default `https://decker.site`) and `DECKER_API_KEY`. Verify the connection with `GET /api/auth/whoami`. If the key is missing or rejected, run `$decker-setup`; never ask the user to paste a token.

When the user asks to publish, send the fragment to:

```text
POST /api/decks
Authorization: Bearer $DECKER_API_KEY
Content-Type: application/json

{"title":"...","slug":"...","description":"...","html":"...","listed":true}
```

Upload local images first with multipart field `file` to `POST /api/images`, then replace local paths with returned absolute URLs. Show the returned `share_url`, `present_url`, and private `edit_url`; treat the edit URL as sensitive.

## Verify before delivery

Review the rendered deck at presentation size. Check every item in the reference's final quality-assurance section, plus:

- every slide is a top-level `.slide` section;
- the HTML contains exactly one shared style block;
- every presenter-only instruction is in `data-notes`;
- no Decker player controls are duplicated;
- all image URLs are absolute and load successfully;
- no text, table, equation, arrow, citation, label, or footer clips or overlaps;
- the final slide resolves the opening learning outcome.

Deliver or publish only after these checks pass.
