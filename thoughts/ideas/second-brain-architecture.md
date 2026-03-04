---
type: note
title: Second Brain Architecture
last_accessed: 2026-03-04
relevance: 1.0
tier: active
---
# Second Brain Architecture
Дата: 2026-03-04
Контекст: Полная настройка Agent Second Brain v2 + интеграция с Claude Code
---

## Текущая архитектура

```
ВХОДЫ:
  Голос/текст в Telegram --> Бот (VPS) --> vault
  Claude Code --> прямая запись --> vault
  yt-dlp --> транскрипты --> NotebookLM (pending)

ХРАНИЛИЩЕ:
  dbrain-vault (GitHub: giftcollection-ui/dbrain-vault)
  Sync: VPS cron */10 push --> GitHub --> Obsidian Git pull (desktop)

ВЫХОД:
  Claude Code читает vault (файловая система + REST API)
  NotebookLM синтезирует (pending)
  Obsidian: поиск, граф, backlinks
```

## Компоненты

| Компонент | Статус | Где |
|-----------|--------|-----|
| Telegram Bot (Second Brain) | DONE | VPS, systemd 24/7 |
| Deepgram Voice | DONE | API key в .env |
| Memory Engine (Ebbinghaus) | DONE | .claude/skills/agent-memory/ |
| Obsidian Local REST API | DONE | port 27124 |
| Vault Sync (Git) | DONE | cron + Obsidian Git |
| Obsidian MCP | WIP | .mcp.json создан |
| Smart Connections | TODO | Obsidian plugin |
| NotebookLM | TODO | nlm login needed |
| Claude API remote tasks | PLANNED | Phase 2 |
| Vault Intelligence | PLANNED | Phase 3 |

## Planned: Claude API Remote Tasks (Phase 2)
- Telegram бот как фронтенд: голос/текст --> задача
- Claude CLI на VPS как бэкенд: выполняет задачу автономно
- Категоризация: входящие задачи маршрутизируются по агентам
- Результат: в vault + обратно в Telegram

## Planned: Vault Intelligence (Phase 3)
- Периодический sweep: Claude анализирует все заметки
- Автоматические связи: backlinks, tags, паттерны
- Insights: противоречия, пробелы, рекомендации
- NotebookLM Audio Overview: недельный дайджест голосом
