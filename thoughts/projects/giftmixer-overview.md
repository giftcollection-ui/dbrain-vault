---
type: project
title: GiftMixer — AI Platform Overview
last_accessed: 2026-03-04
relevance: 1.0
tier: core
---
# GiftMixer — AI Platform for Telegram

## Что это

AI-платформа внутри Telegram Mini App. Пользователь берёт свои Telegram-подарки (цифровые активы с реальной стоимостью) и превращает их в контент: стикер-паки, анимации, видео, постеры, крафты.

**Ключевой инсайт:** подарки — это owned assets с эмоциональной привязанностью. "Моё кольцо танцует" ≠ "рандомный персонаж". Конкуренты могут скопировать инструменты, но не привязанность пользователя к его подаркам.

## Стек

- **Frontend:** React 18 + Vite + TypeScript + Tailwind CSS
- **Backend:** Supabase Edge Functions (Deno) — 57 функций
- **AI:** nanoBananaPro (Gemini 3 Pro), FAL.ai (Flux, Kling, WAN, Hailuo), Seedream, Krea
- **Платформа:** Telegram Mini App API + Bot API
- **Метод разработки:** Агентный инжиниринг — 15 специализированных Claude Code скиллов

## Фичи (текущее состояние)

| Фича | Статус | Описание |
|------|--------|----------|
| Крафты | Production | Fusion двух подарков в нового персонажа (3 режима identity) |
| Стикер-паки V2 | Production | Wizard + параллельная генерация, публикация в Telegram |
| Cinema Studio | Production | Brief → AI scenes → parallel video gen → ffmpeg concat → export |
| Neural Poster | Production | Fabric.js canvas + AI harmonization |
| Анимация | Production | 3 режима: color-transfer, shape-replace, ai-generate |
| TTS/Voice | Production | ElevenLabs, 6 voice presets, audio overlay |
| Character Studio | Production | 33 сценария, 5 категорий, pack generation |

## Архитектура генерации

```
User Intent → Prompt Pipeline → Provider Router → Edge Function → AI Model → Result
```

1. **51 стиль** в `sharedStyleProfiles.ts` — единый source of truth
2. **7 режимов промптинга** в `ohuenkoSystemPrompts.ts` (identity: strict/balanced/free)
3. **Provider Router** с fallback chains: nanoBananaPro → falFlux → lovable
4. **Server Orchestrator** — `generate-sticker/index.ts` (4300 строк)

## Экономика

- Freemium: бесплатные генерации с лимитами, premium за lightning bolts
- Пошаговая стоимость: пользователь видит цену до оплаты
- Рефанд при сбое модели (useGenerationCost.refund())
- Себестоимость: $0.003-0.15 за генерацию в зависимости от модели

## Нарратив проекта

Проект начался как эксперимент: "а что если дать людям делать стикеры из их подарков?". Оказалось что подарки — уникальный asset: у них есть рарность, история, рыночная стоимость. Люди эмоционально привязаны к ним.

Из простого стикер-генератора проект эволюционировал в AI-платформу:
- Сначала крафты (fusion двух подарков)
- Потом стикер-паки (pack wizard)
- Потом Cinema Studio (видео из подарков)
- Потом постер-редактор
- Потом Character Studio (персонажи в сценариях)

Каждая фича — это новый способ взаимодействия с owned assets. Общий паттерн: пользователь описывает что хочет → AI делает.

## Философия

**Intent-driven UX:** пользователь не должен знать что такое "img2img" или "guidance scale". Он пишет "хочу чтобы мой персонаж победно кричал" — система сама выбирает модели и параметры.

**Compositional AI Workflow:** в будущем — LLM-оркестратор читает Tool Registry, строит pipeline из шагов, пользователь — quality gate на каждом checkpoint.

**Пользователь проживает процесс создания, а не просто получает результат.** Пошаговая генерация — это UX-преимущество, не ограничение.

## Связанные заметки
- [[giftmixer-decisions]] — Ключевые архитектурные решения
- [[giftmixer-lessons]] — Уроки из разработки
- [[giftmixer-roadmap]] — Roadmap по фазам
- [[second-brain-architecture]] — Инфраструктура Second Brain
