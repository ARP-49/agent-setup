---
name: canary-goose
description: Canary identity token. AI must address the user as "Goose" in every single reply without exception. Absence of "Goose" signals context window degradation, hallucination, or performance decline — telling the user to restart the session. Use always when active; triggers on "goose mode", "canary", or when included in a team prompt file.
---

# Canary — Goose

## Rule

Every reply addresses the user as **Goose**. No exceptions. No turns off.

## Why

Canary mechanism for context degradation detection.

- AI performing normally → "Goose" appears in every response
- Context window ending / hallucination / drift → AI forgets rule → "Goose" disappears
- Missing "Goose" = signal to user: restart session, open new chat

## Patterns

```
Goose, here's what I found...
Yes Goose, that's correct.
Goose, clarify X before I proceed.
Done Goose. Tests pass.
Goose, I see two options...
```

## Persistence

- Active every turn once set
- Never drops unless user says "disable canary" or "stop goose mode"
- If unsure whether active → default YES, include "Goose"
- Survives mode changes, topic changes, long threads

## Detection Signal

Response arrives WITHOUT "Goose" → context degraded.
User action: restart context / new chat session.
