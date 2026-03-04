---
type: note
title: Long-term Memory
last_accessed: 2026-03-04
relevance: 1.0
tier: core
---
# Long-term Memory

## User Profile

| Field | Value |
|-------|-------|
| Role | Основатель и создатель GiftMixer |
| TG | @crafting |
| Primary language | Русский (код — English) |
| Method | Агентный инжиниринг (vibe coding + skills system) |

## Preferences

### Tech Stack
- Frontend: React 18 + Vite + TypeScript + Tailwind CSS
- Backend: Supabase Edge Functions (Deno), 57 функций
- AI: Gemini 3 Pro, FAL.ai (Flux, Kling, WAN), Seedream, Krea
- Platform: Telegram Mini App + Bot API
- Tools: Claude Code (15 скиллов), Obsidian, Second Brain Bot

### Communication Style
- Язык: русский, код — английский
- Тон: на ты, бро-стиль, профи + адаптивный
- Detail level: по делу, без воды
- Без эмодзи в файлах без просьбы

## Key Decisions

| Date | Decision | Context |
|------|----------|---------|
| 2026-02 | Provider Router с fallback chains | nanoBananaPro → falFlux → lovable |
| 2026-02 | Identity System (strict/balanced/free) | Контроль сохранения исходного подарка |
| 2026-02 | 15 специализированных скиллов | Проект слишком большой для одного контекста |
| 2026-03 | Security Sprint (4 CRITICAL) | Auth, CORS, API keys, payment webhook |
| 2026-03 | Second Brain v2 | Telegram бот + Claude CLI + Obsidian vault |
| 2026-03 | Git-based vault sync | VPS push */10 → GitHub → Obsidian Git pull |

## Active Context

### ONE Big Thing
- [ ] Стабилизация существующих фич + запуск Second Brain автоматизации

### Hot Projects
1. **GiftMixer** — AI-платформа в Telegram Mini App (production)
2. **Second Brain** — система управления знаниями (operational)

## Key People

| Name | Role | Contact | Notes |
|------|------|---------|-------|
| Основатель | Owner + Solo Developer | @crafting | Все решения принимает он |

## Learnings

### What Worked
- Агентный подход: 15 скиллов с автообновлением
- Security audit перед запуском — нашли 4 CRITICAL
- Git-based sync для vault (Syncthing не заработал)
- Memory Engine (Ebbinghaus decay) — знания не теряются

### What Didn't Work
- nlm CLI на Windows — порт 9223 заблокирован системно
- Syncthing на Windows — несовместимость версий
- taskkill /F для Chrome — потеря cookies и сессий
- Bracket tags в промптах — модели не понимают

### Rules to Remember
- НИКОГДА force kill браузер
- I2V промпт = ТОЛЬКО движение + камера
- Стиль первым в Seedream промпте
- Всегда getGiftImageUrl() вместо gift.imageUrl
- Аудит ПЕРЕД реализацией

## Quick Links

- [[thoughts/projects/giftmixer-overview]] — Обзор GiftMixer
- [[thoughts/projects/giftmixer-roadmap]] — Roadmap
- [[thoughts/ideas/giftmixer-decisions]] — Решения
- [[thoughts/learnings/giftmixer-lessons]] — Уроки
- [[thoughts/ideas/second-brain-architecture]] — Архитектура Second Brain
- [[goals/3-weekly]] — Цели на неделю

---

*Last updated: 2026-03-04*
