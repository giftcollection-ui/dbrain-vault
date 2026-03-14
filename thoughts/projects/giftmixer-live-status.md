---
type: project
title: GiftMixer — Live Status
date: 2026-03-06
tags: [giftmixer, project-status, live]
---

# GiftMixer — Live Status

> Этот файл обновляется Claude Code автоматически после каждой значимой сессии.

## Current Sprint (2026-03-14)

**Фокус:** Cinema Builder — shared config SSOT + координация параллельных работ (Claude Code / Lovable / Gemini REFACTORING_PLAN).

**Статус:** Shared config consolidation завершена. P0 backend задачи в работе.

## Recent Decisions

| Дата | Решение | Контекст |
|------|---------|----------|
| 2026-03-14 | Зоны ответственности для параллельных Cinema изменений | 3 вектора: мы=backend+config, Lovable=CSS/layout, Plan=structure |
| 2026-03-14 | SSOT: archetypeDefinitions.ts + videoProviders.ts | Устранена фрагментация: 3 источника → 1 shared config |
| 2026-03-14 | archetypeSuffix forwarding в recompose-scene | Frontend резолвит suffix → передаёт backend, не дублирует |
| 2026-03-14 | Vault frontmatter стандартизация | Все thoughts/ файлы приведены к единому YAML формату |
| 2026-03-06 | Переехали бота на US VPS | Google блокировал Cookie auth на EU серверах |

## Open HIGH Tasks

- [ ] Синхронизировать архетипы в generate-story-brief (10→12)
- [ ] Поднять image_prompt_strength 0.35→0.50
- [ ] Error Boundary coverage в CinemaBuilderPage
- [ ] Frame model selector + variant gallery (P1, ждём Lovable UI)
- [ ] Character Lock editable (P1)

## Cinema Parallel Coordination

| Зона | Владелец | Статус |
|------|----------|--------|
| Backend (edge functions) | Claude Code | Активно |
| Config SSOT | Claude Code | Завершено |
| CSS/Design system | Lovable | Ожидаем |
| Component structure | PLAN | Не начат |
| SceneCard logic | Claude Code | P1 |

## Stack

- Frontend: React 18 + Vite + TypeScript + Tailwind
- Backend: Supabase Edge Functions (Deno), 57 functions
- AI: nanoBananaPro (Gemini 3 Pro), falFlux (FAL.ai Flux), seedream45, lovable
- Cinema: 2-agent Gemini pipeline (Director + Cinematographer), 12 visual archetypes
- Bot: aiogram 3.x, Deepgram Nova-3, Claude CLI, MCP (Todoist)
- Vault: Obsidian + git sync (GitHub ↔ Desktop ↔ VPS)

## Blockers

_нет активных блокеров_

## Next Session

1. P0: sync generate-story-brief архетипы + strength 0.50
2. P0: Error Boundary проверка
3. P1: Frame model selector (после Lovable UI merge)