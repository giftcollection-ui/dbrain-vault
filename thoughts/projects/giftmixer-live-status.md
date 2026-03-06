---
type: project
description: >-
  GiftMixer live project status — current sprint, decisions, open tasks, blockers. Updated by Claude Code after each significant session.
tags: [giftmixer, project-status, live]
status: active
created: 2026-03-06
updated: 2026-03-06
last_accessed: 2026-03-06
relevance: 1.0
tier: active
---

# GiftMixer — Live Status

> Этот файл обновляется Claude Code автоматически после каждой значимой сессии.

## Current Sprint

**Фокус:** Knowledge Circulation System — замкнуть петлю между Claude Code, Obsidian vault, NotebookLM и Telegram-ботом.

**Статус:** В процессе. Phase 1-2 завершены, Phase 3-4 в работе.

## Recent Decisions

| Дата | Решение | Контекст |
|------|---------|----------|
| 2026-03-06 | Переехали бота на US VPS (23.94.252.50) | Google блокировал Cookie auth на EU серверах |
| 2026-03-06 | process_message — умная классификация в боте | text.py + voice.py теперь роутят через Claude |
| 2026-03-06 | mcp-obsidian удалён | Кириллица битая, Write tool надёжнее |

## Open HIGH Tasks

- [ ] Vault search в боте (Phase 3) — контекст из заметок при ответах
- [ ] Conversation mode `/chat` (Phase 4) — персистентный диалог с памятью
- [ ] GiftMixer Architecture ноутбук в NotebookLM
- [ ] Streaming ответов в боте (UX)

## Architecture

```
Telegram Bot (aiogram 3.x, VPS 23.94.252.50)
  ↓ text/voice
process_message() → Claude CLI subprocess
  → [IDEA/LEARNING/DECISION] → vault/thoughts/
  → [TASK] → Todoist MCP
  → [RESEARCH] → NotebookLM MCP
  → [DAILY] → vault/daily/YYYY-MM-DD.md
  → [QUESTION] → direct answer

Claude Code (Desktop) → vault/daily/ → git push → VPS pull (cron */10)
```

## Stack

- Frontend: React 18 + Vite + TypeScript + Tailwind
- Backend: Supabase Edge Functions (Deno), 57 functions
- AI: nanoBananaPro (Gemini 3 Pro), falFlux (FAL.ai Flux), seedream45, lovable
- Bot: aiogram 3.x, Deepgram Nova-3, Claude CLI, MCP (Todoist + NotebookLM)
- Vault: Obsidian + git sync (GitHub ↔ Desktop ↔ VPS)

## Blockers

_нет активных блокеров_

## Next Session

1. Реализовать vault search (читать релевантные заметки в промпт)
2. Conversation mode с сохранением треда
3. Тест полного цикла: идея → vault → /process → Todoist