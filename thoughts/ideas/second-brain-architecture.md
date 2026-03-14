---
type: idea
title: Second Brain — Complete Architecture v3
date: 2026-03-15
tags: [second-brain, architecture, telegram-bot, obsidian, vault, skills, feedback-loop]
last_accessed: 2026-03-15
relevance: 1.0
tier: core
---
# Second Brain — Complete Architecture v3

Дата: 2026-03-15
Статус: v3.0 — все компоненты работают + vault-skill feedback loop + расширенные категории

## Системная диаграмма

```
                    +------------------------------+
                    |     ВХОДНЫЕ КАНАЛЫ            |
                    +------------------------------+
                    |  Голос -> Deepgram -> текст    |
                    |  Текст -> напрямую             |
                    |  Фото -> Claude описание       |
                    |  Пересланные -> с атрибуцией   |
                    |  /do -> произвольная задача     |
                    |  /chat -> Anthropic SDK         |
                    |  /code -> Claude CLI + git      |
                    |  Web query -> Perplexity API    |
                    +--------------+---------------+
                                   |
                    +--------------v---------------+
                    |  TELEGRAM BOT (VPS 24/7)      |
                    |  aiogram 3.0 + Deepgram        |
                    |  systemd: d-brain-bot.service   |
                    |  IP: 23.94.252.50, user: dbrain |
                    +--------------+---------------+
                                   |
              +--------------------+--------------------+
              |                    |                     |
    +---------v--------+ +--------v--------+ +---------v--------+
    | /process         | | /do, /chat      | | Perplexity       |
    | Daily Pipeline   | | Claude CLI/SDK  | | sonar-pro/deep   |
    | 3 phases:        | | + MCP tools     | | Stream -> TG     |
    | capture ->       | | Todoist, GitHub  | | [Save|Deep|NLM]  |
    | execute ->       | | NotebookLM      | |                  |
    | reflect          | | Filesystem      | |                  |
    +--------+---------+ +--------+--------+ +--------+---------+
             |                    |                     |
             +--------------------+---------------------+
                                  |
                    +-------------v----------------+
                    |  OBSIDIAN VAULT (dbrain-vault) |
                    |  GitHub: giftcollection-ui/    |
                    |          dbrain-vault           |
                    +-------------+----------------+
                                  |
         +------------------------+----------------------------+
         |                        |                            |
+--------v---------+  +---------v----------+  +---------------v---------+
| VPS -> GitHub    |  | GitHub ->          |  | Claude Code (Desktop)   |
| cron */10 push   |  | Obsidian Git       |  | Skills read VAULT       |
|                  |  | auto-pull 10m      |  | CONTEXT on load         |
|                  |  |                    |  | Write lessons/decisions |
|                  |  |                    |  | Feedback loop           |
+------------------+  +--------------------+  +-------------------------+
```

## v3.0 Нововведения

### 1. Vault-Skill Feedback Loop
Скиллы GiftMixer проекта теперь имеют секцию VAULT CONTEXT.
При загрузке скилла Claude читает указанные vault файлы для живого контекста.

```
Skill loads -> reads vault learnings/decisions -> better context
  |
Work done -> new lesson discovered
  |
Write to vault SSOT files
  |
Next session -> skill reads updated vault
```

**Скиллы с VAULT CONTEXT:** prompt-architect, craft-engineer, quality-guardian, security-ops, video-pipeline-engineer

### 2. SSOT Консолидация
- **Lessons/Decisions SSOT** = vault файлы (не Claude memory)
- Claude memory `lessons.md` и `decisions.md` = указатели на vault
- Одна запись = одно место. Нет дублирования.

### 3. Расширенные vault категории

| Категория | Путь | Скиллы-потребители |
|-----------|------|--------------------|
| Уроки | `thoughts/learnings/` | prompt-architect, craft-engineer, quality-guardian |
| Решения | `thoughts/ideas/` | все скиллы |
| Задачи | `thoughts/tasks/` | quality-guardian, video-pipeline-engineer |
| Проекты | `thoughts/projects/` | growth-marketer |
| Рефлексии | `thoughts/reflections/` | agent-memory |
| **Исследования** | `thoughts/research/` | prompt-architect, model-scout |
| **Безопасность** | `thoughts/security/` | security-ops |
| **Экономика** | `thoughts/economics/` | unit-economist |

### 4. NotebookLM стратегия

| Notebook | Назначение | Источники |
|----------|-----------|-----------|
| GiftMixer Platform | Продуктовый контекст | Vault notes, GitHub docs |
| AI Models & Providers | Модели и цены | FAL.ai docs, blogs |
| Telegram Mini Apps | Платформенные знания | TG docs, case studies |
| Competitor Intelligence | Конкуренты | Сайты, обзоры |
| Agent Engineering | Best practices | Papers, tutorials |

Pipeline: Research -> NLM query -> Distill -> vault `thoughts/research/` -> cross-link

### 5. Рекомендованные Obsidian плагины

| Плагин | Что даст |
|--------|----------|
| **Dataview** | SQL-запросы по метаданным (tier, type, tags) |
| **Templater** | Автозаполнение frontmatter, динамические daily notes |
| **Periodic Notes** | Структурированные weekly/monthly reviews |
| **Calendar** | Визуальная навигация по daily notes |
| **Tasks** | Агрегация всех `- [ ]` из vault |
| **DB Folder** | Табличный вид thoughts/ с сортировкой |

## Компоненты и статус

| Компонент | Статус | Где |
|-----------|--------|-----|
| Telegram Bot | DONE | VPS, systemd 24/7 |
| Deepgram Voice | DONE | Nova-3, транскрипция |
| Claude CLI | DONE | VPS, /do + /code + /chat |
| Perplexity API | DONE | sonar-pro + sonar-deep-research |
| MCP: Todoist | DONE | add-tasks, find-completed |
| Memory Engine | DONE | Ebbinghaus decay, 5 tiers |
| Vault Sync | DONE | Git-based, */10 |
| Obsidian REST API | DONE | port 27124 |
| Smart Connections | DONE | bge-micro-v2 embeddings |
| Vault-Skill Loop | **v3.0** | 5 скиллов с VAULT CONTEXT |
| SSOT Consolidation | **v3.0** | Lessons/decisions = vault only |
| Extended Categories | **v3.0** | +research, +security, +economics |
| NotebookLM CLI | BLOCKED | Windows port 9223 |

## Автоматические процессы

| Процесс | Расписание | Что делает |
|---------|-----------|------------|
| Vault push | */10 мин (cron) | git add + commit + push на VPS |
| Obsidian pull | */10 мин (Git plugin) | git pull на десктопе |
| Daily processing | 21:00 (systemd) | Classify -> Todoist -> Report |
| Weekly digest | Sun 20:00 (systemd) | Анализ недели -> summary |
| Memory decay | После /process | Обновление relevance и tiers |
| Graph rebuild | После /process | Vault topology analysis |
| Claude Code session | По запросу | Startup ritual + creative recall |

## Как пользоваться

### Ежедневно
1. **Голос/текст в бота** — записывай мысли, идеи, задачи в любое время
2. **Web-запросы** — бот автоматически маршрутизирует в Perplexity
3. **В 21:00** — автообработка: задачи -> Todoist, идеи -> vault
4. **Отчёт в Telegram** — HTML-отчёт о том что обработано

### По запросу
- `/do переместить задачи на понедельник` — через Todoist MCP
- `/chat` — разговор с Claude (Anthropic SDK, история)
- `/code` — работа с gift-combo-creator через Claude CLI
- `/search запрос` — поиск по vault
- `/today` — что захвачено сегодня
- `/reflect` — рефлексия дня

### В Obsidian
- **Smart Connections** — семантически связанные заметки
- **Gemini Scribe** — AI-ассистент прямо в Obsidian
- **Граф** — визуализация связей
- **Daily notes** — автоматически из бота

### В Claude Code
- Скиллы с VAULT CONTEXT читают vault при загрузке
- Startup ritual: decay + creative recall + daily context
- Lessons/decisions записываются в vault как SSOT

## Ближайшие улучшения

### Высокий приоритет
- [ ] Заполнить goals cascade (vision, weekly)
- [ ] Активировать reflections (cron в 22:00)
- [ ] Настроить SSH ключ для VPS с десктопа
- [ ] Фикс WEB_KEYWORDS collision в text.py
- [ ] Установить Dataview + Templater + Periodic Notes

### Средний приоритет
- [ ] Smart Connections API для Claude Code
- [ ] Унифицировать vault-скиллы и проект-скиллы
- [ ] Decay-aware skill loading
- [ ] NLM на VPS (Linux)

### Долгосрочные
- [ ] Cross-vault knowledge graph
- [ ] Voice morning briefing (TTS)
- [ ] Multi-vault support
- [ ] Agent routing в боте

## Связанные заметки
- [[giftmixer-overview]] — Обзор GiftMixer
- [[giftmixer-decisions]] — Архитектурные решения
- [[giftmixer-lessons]] — Уроки
- [[giftmixer-roadmap]] — Roadmap
