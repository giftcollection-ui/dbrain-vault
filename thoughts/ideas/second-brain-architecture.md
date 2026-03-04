---
type: architecture
title: Second Brain Architecture
last_accessed: 2026-03-04
relevance: 1.0
tier: core
---
# Second Brain — Complete Architecture

Дата: 2026-03-04
Статус: v2.0, все основные компоненты работают

## Схема системы

```
                    ┌──────────────────────────────┐
                    │     ВХОДНЫЕ КАНАЛЫ            │
                    ├──────────────────────────────┤
                    │  Голос → Deepgram → текст     │
                    │  Текст → напрямую             │
                    │  Фото → Claude описание       │
                    │  Пересланные → с атрибуцией   │
                    │  /do → произвольная задача     │
                    └──────────┬───────────────────┘
                               │
                    ┌──────────▼───────────────────┐
                    │  TELEGRAM BOT (VPS 24/7)      │
                    │  aiogram 3.0 + Deepgram       │
                    │  systemd: d-brain-bot.service  │
                    │  Hetzner CX11, user: dbrain    │
                    └──────────┬───────────────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
    ┌─────────▼──────┐ ┌──────▼──────┐ ┌───────▼──────┐
    │ /process       │ │ /do         │ │ /weekly      │
    │ Daily Pipeline │ │ Free-form   │ │ Digest       │
    │ 3 phases:      │ │ Claude CLI  │ │ Goals check  │
    │ capture→       │ │ + MCP tools │ │ + summary    │
    │ execute→       │ │ Any request │ │              │
    │ reflect        │ │             │ │              │
    └────────┬───────┘ └──────┬──────┘ └───────┬──────┘
             │                │                │
             └────────────────┼────────────────┘
                              │
                    ┌─────────▼────────────────────┐
                    │  CLAUDE CLI (VPS)              │
                    │  claude --print --mcp-config   │
                    │  ANTHROPIC_API_KEY в .bashrc    │
                    │  MCP: Todoist, файловая система │
                    └─────────┬────────────────────┘
                              │
                    ┌─────────▼────────────────────┐
                    │  OBSIDIAN VAULT (dbrain-vault) │
                    │  GitHub: giftcollection-ui/    │
                    │          dbrain-vault           │
                    └─────────┬────────────────────┘
                              │
         ┌────────────────────┼──────────────────────┐
         │                    │                      │
┌────────▼───────┐  ┌────────▼───────┐  ┌───────────▼──────┐
│ VPS → GitHub   │  │ GitHub →       │  │ Claude Code      │
│ cron */10 push │  │ Obsidian Git   │  │ Read/Write       │
│                │  │ auto-pull 10m  │  │ прямой доступ    │
│                │  │                │  │ + REST API 27124 │
└────────────────┘  └────────────────┘  └──────────────────┘
```

## Компоненты и статус

| Компонент | Статус | Где | Примечание |
|-----------|--------|-----|------------|
| Telegram Bot | DONE | VPS, systemd 24/7 | aiogram 3.0, /start /help /status /do /process /weekly |
| Deepgram Voice | DONE | API key в .env | Nova-3, транскрипция голосовых |
| Claude CLI | DONE | VPS | claude --print --dangerously-skip-permissions --mcp-config |
| MCP: Todoist | DONE | VPS | add-tasks, find-completed, user-info |
| Memory Engine | DONE | Оба проекта | Ebbinghaus decay, 5 tiers, Python скрипт |
| Vault Sync | DONE | Git-based | VPS push */10 → GitHub → Obsidian Git pull |
| Obsidian REST API | DONE | Desktop, port 27124 | Claude Code читает/пишет |
| Obsidian MCP | DONE | .mcp.json | mcp-obsidian + vault path |
| Smart Connections | DONE | Obsidian plugin | Семантический поиск, TaylorAI/bge-micro-v2 |
| Gemini Scribe | DONE | Obsidian plugin | AI-агент в Obsidian, Gemini API |
| Daily Processing | DONE | systemd timer 21:00 | 3-фазный pipeline + graph + decay |
| Weekly Digest | DONE | systemd timer Sun 20:00 | Анализ целей + summary |
| NotebookLM CLI | BLOCKED | Windows port 9223 | Доступен через браузер |

## Автоматические процессы

| Процесс | Расписание | Что делает |
|---------|-----------|------------|
| Vault push | */10 мин (cron) | git add + commit + push на VPS |
| Obsidian pull | */10 мин (Git plugin) | git pull на десктопе |
| Daily processing | 21:00 (systemd) | Classify entries → Todoist tasks → Report в Telegram |
| Weekly digest | Sun 20:00 (systemd) | Анализ недели → summary → Telegram |
| Memory decay | После каждого /process | Обновление relevance и tiers |
| Graph rebuild | После каждого /process | Vault topology analysis |

## Как пользоваться

### Ежедневно
1. **Голос/текст в бота** — записывай мысли, идеи, задачи в любое время
2. **В 21:00 автоматически** — Claude обработает: задачи → Todoist, идеи → vault, CRM → карточки
3. **Отчёт в Telegram** — получишь HTML-отчёт о том что обработано

### По запросу
- `/do переместить задачи на понедельник` — Claude выполнит через Todoist MCP
- `/do проанализируй мои цели за неделю` — Claude прочитает vault и ответит
- `/process` — запустить обработку вручную (не ждать 21:00)
- `/weekly` — запустить недельный дайджест вручную
- `/status` — сколько записей сегодня

### В Obsidian
- **Smart Connections** — открой заметку, справа покажутся семантически связанные
- **Gemini Scribe** — AI-ассистент прямо в Obsidian, работает с vault
- **Граф** — визуализация связей между заметками
- **Daily notes** — автоматически создаются из бота

### В Claude Code
- Прямое чтение/запись vault через файловую систему
- REST API на порту 27124 для программного доступа
- MCP server для инструментов vault (после рестарта)

## Структура vault

```
dbrain-vault/
├── daily/               ← Дневные записи (YYYY-MM-DD.md)
├── goals/               ← Цели (3y → yearly → monthly → weekly)
├── thoughts/
│   ├── ideas/           ← Идеи и архитектурные решения
│   ├── learnings/       ← Уроки и паттерны
│   ├── projects/        ← Проекты
│   ├── reflections/     ← Рефлексии
│   └── tasks/           ← Задачи
├── business/            ← CRM, контакты
├── projects/            ← Клиенты, лиды
├── blog/                ← Статьи
├── MOC/                 ← Maps of Content (автогенерация)
├── summaries/           ← Недельные сводки
├── templates/           ← Шаблоны заметок
├── .claude/             ← AI-агенты, скиллы, правила
│   ├── skills/          ← dbrain-processor, graph-builder, todoist-ai, vault-health
│   ├── agents/          ← inbox-processor, goal-aligner, note-organizer, weekly-digest
│   └── rules/           ← daily-format, thoughts-format, weekly-reflection
├── MEMORY.md            ← Долгосрочная память агента
└── .memory-config.json  ← Настройки decay (Ebbinghaus)
```

## Развитие системы

### Ближайшее (можно сделать сейчас)
- Заполнить vault информацией о GiftMixer (архитектура, решения, уроки)
- Настроить Smart Connections embeddings с Anthropic/OpenAI моделью (качественнее чем bge-micro)
- Добавить больше MCP серверов (GitHub, Notion, Calendar)

### Среднесрок
- Параллельные агенты: категоризация входящих → маршрутизация по специализированным Claude instances
- Vault health sweep: автоматический поиск orphans, broken links, стагнирующих заметок
- Telegram inline bot: быстрый поиск по vault прямо из любого чата
- NotebookLM интеграция (когда nlm CLI починят / через VPS)

### Долгосрок
- Multi-vault: отдельные vault для разных проектов, cross-vault search
- Voice-first dashboard: голосовой дайджест утром (TTS из weekly summary)
- Autonomous research agent: Claude сам ищет информацию в интернете, добавляет в vault
- Knowledge graph visualization: 3D граф связей в реальном времени