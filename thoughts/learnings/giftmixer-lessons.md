---
type: learning
title: GiftMixer — Lessons Learned
date: 2026-03-15
tags: [giftmixer, ai-prompts, architecture, cinema, process]
last_accessed: 2026-03-15
relevance: 1.0
tier: core
---
# GiftMixer — Уроки из разработки

## AI промпты

- **Seedream "raw" mode не существует** — только "standard" и "fast". Молча деградирует.
- **Оптимальная длина промпта: 30-100 слов.** Больше 100 = деградация, стили перестают различаться.
- **Bracket tags `[BASE SUBJECT]` — модель НЕ понимает.** Только natural language, "Figure N" нотация.
- **Культурные ссылки (Ohuenko, Utya, VK Spotty, Lisa Frank, Aardman) — ByteDance модель НЕ знает.** Только универсальные визуальные дескрипторы: материал + освещение + цвет.
- **Стиль первым в промпте** — Seedream приоритизирует ранние токены.
- `providerHints.seedream` — ИЗОЛИРОВАН, используется ТОЛЬКО в SeedreamComposeStudio.tsx. Безопасно менять без влияния на nanoBananaPro/cinema/sticker.
- **АУРА = свечение ВОКРУГ силуэта** — лицо и глаза видны. Слово "wrap" = буквально обернуть, перекрывает лицо. Не использовать.
- **BASE lock:** "is the main character — keep its shape, pose, and face"
- **V26** over-simplified (30 слов) — потерялась reference image инструкция
- **V27** rigid role labels (headwear/holding) — переограничивала творчество
- **V28:** Let Gemini see the image and decide placement. Trust its vision, guide with strong verbs not rigid categories
- **Never-lists** — шум для smart models. Заменять на один позитивный directive: "ONE cohesive character"
- Когда `bypassImgInstructions=true`, server не добавляет "use image as reference" — клиент ДОЛЖЕН компенсировать
- "Reference Analysis" block в user prompt помогает модели parse composite до генерации
- Domain verbs: "mutate into body parts" (fusion), "equip/outfit" (compose) — НЕ generic "fuse/enhance"
- Style as technical components: "Soft 2.5D vector, smooth gradients, volumetric lighting" > "authentic Telegram"
- System prompt = Role + Task + Input Structure + Rules. User prompt = Reference Analysis + Action.

## Код и архитектура

- **Всегда используй `getGiftImageUrl()`** — `gift.imageUrl` может быть локальный path
- Lovable (AI IDE) может ревертить локальные изменения — следить за system-reminders
- Windows env: npm/npx может не быть в PATH — использовать PowerShell если нужно
- `store.abortController` в useCallback deps создаёт бесконечный re-render — читать через `.getState()` в момент вызова
- Situation IDs в situationTemplates.ts plain (moon, victory), не prefixed (crypto-moon, gaming-victory)
- baseSlotIndex bug: `useCraftGeneratorV3.ts` передавал filledInputs (filtered) но baseSlotIndex ссылался на state.inputs (original)
- **Craft pipeline: `unifiedGenerator.ts` НЕ используется** — только StickerWizard (sticker flow)

## Cinema / AI Video

- **I2V промпт = ТОЛЬКО движение + камера.** Описание персонажа/сеттинга вызывает дубли поверх изображения.
- `adaptPromptForProvider()` — при `hasImageUrl=true` worldContext НЕ вставляется в промпт
- Cinematographer агент тоже обучен: "FORBIDDEN: Location/setting words, character appearance"
- **Visual Bible** (10-15 слов) — компактный стиль-дескриптор для единого стиля всех сцен
- Вставляется в Kontext Pro и Nano Banana 2 compose промпты
- **Anti-mutation rule** — "NEVER change physical geometry" в Director промпте
- **First-Frame Chaining** — extract last frame (FAL FFmpeg `-sseof -0.2 -frames:v 1`) → input следующей
- Stale closure проблема: `setFrames(prev => { read prev; return prev; })` — единственный способ прочитать актуальный state из интервала

## Процесс

- **НИКОГДА force kill браузер** — потеря cookies, сессий. Только ручное закрытие.
- **Не лезть в сырые CLI инструменты** если задача не критична. nlm CLI — сырой, гео-заблокирован, стоил потерянного времени.
- **ВСЕГДА сначала ищи рабочий паттерн в кодовой базе.** generate-motion уже работал с WAN v2.6. Потерянные часы.
- **Аудит ПЕРЕД реализацией** — находишь баги до того как пишешь код поверх них
- При работе со стилями — проверяй что `craftHints.composeSuffix` реально попадает в промпт на сервер
- При добавлении новых ID в шаблоны — проверяй что они существуют в source data (STICKER_EMOTIONS, TOP_REACTIONS, situationTemplates)

## Second Brain / Инфраструктура

- **Git-based sync надёжнее Syncthing** — Syncthing на Windows имеет проблемы совместимости
- **nlm CLI на Windows не работает** — порт 9223 заблокирован системно. Использовать через VPS или браузер.
- **Memory Engine (Ebbinghaus decay) работает** — знания не теряются, автоклассификация по tier
- **Запись в vault через Write tool** надёжнее чем REST API (проблемы с кириллицей через API)

## Связанные заметки
- [[giftmixer-overview]] — Обзор проекта
- [[giftmixer-decisions]] — Архитектурные решения
- [[giftmixer-roadmap]] — Roadmap
- [[second-brain-architecture]] — Архитектура Second Brain
