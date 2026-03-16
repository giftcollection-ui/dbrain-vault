---
type: idea
title: GiftMixer — Key Decisions
date: 2026-03-15
tags: [giftmixer, architecture, decisions]
last_accessed: 2026-03-15
relevance: 1.0
tier: core
---
# GiftMixer — Ключевые архитектурные решения

## 2026-03 — Cinema Compose Root Cause (image_prompt_strength bug)

**Контекст:** Kontext Pro (`fal-ai/flux-pro/kontext`) использовал `image_prompt_strength: 0.92` — 92% веса на текст, 8% на reference image. Персонаж ИГНОРИРОВАЛСЯ.
**Решение:** `image_prompt_strength: 0.35` (хорошо сохраняет внешность) или `0.45` (компромисс).
**Урок:** Удалили compose step вместо того чтобы исправить параметр — это откатило архитектуру НАЗАД.
**Pipeline:** Cast → Compose (Kontext Pro, strength=0.35) → I2V

## 2026-03 — Cinema Pipeline v2 — Character Consistency

**Три принципа (экспертный аудит):**
1. **NO Flux Character Sheets** — подарки = авторские 3D-модели. Flux галлюцинирует "вид сзади". Извлекать оригинальные кадры из анимации, не AI.
2. **Inpainting/Compositing** — НЕ голый Flux с reference. Нейросеть генерит фон, персонаж вписывается через Kontext Pro / маскинг.
3. **Cache anchors, NOT generations** — кэшировать промпты и маски, НЕ готовые видео.

## 2026-03 — Security Hardening v2 (10 fixes)

**Контекст:** Полный security аудит выявил 10 уязвимостей.
**Решения:**
1. JWT auth middleware (`_shared/auth.ts`) — enforced на craft-roll, create-invoice, generate-video-async
2. Telegram initData HMAC validation в sync-profile
3. Server-side price catalog в create-invoice (клиент не может задать цену)
4. Mandatory webhook signature validation (was optional)
5. CORS hardening — dev origins только при `ENVIRONMENT=development`
6. Atomic lootbox claim (race condition prevention)
7. Hardcoded owner ID → `OWNER_TELEGRAM_ID` env var
8. `_retry_of` payment bypass → server-side validation against failed jobs in DB
9. `_corsHeaders` race condition → json() принимает corsHeaders как параметр
10. `kling-30`/`kling-30-pro` promptKey mapping fix

## 2026-03 — Prompt Architecture v2 (Two-Layer Design)

**Контекст:** Промпты были статичные, одинаковые каждый раз. Нет адаптации под провайдеров.
**Решения:**
1. Два слоя: Technical (fixed) + Creative (pooled variants)
2. `promptPool.ts` — FNV-1a hash seed для детерминированного выбора вариантов
3. STYLE_GUIDES/ATMOSPHERE/PATTERNS → пулы с ротацией
4. Provider-specific builders: WAN (natural lang), Hailuo (Delta), Kling (structured), Veo3 (5-Part), Seedance
5. CriticVerdict с diagnosticTag для адаптивного re-compose
**Файл знаний:** `.claude/skills/prompt-architect/references/provider-prompt-kb.md`

## 2026-03 — Security Sprint (4 CRITICAL)

**Контекст:** Аудит безопасности перед запуском.
**Решения:**
1. Auth bypass fix: `isDevEnvironment()` = `import.meta.env.DEV` only
2. Payment webhook: signature validation (`x-telegram-bot-api-secret-token`) + idempotency
3. API key: OpenRouter → server-side `adapt-prompt` edge fn (translate mode)
4. CORS: `Allow-Origin: *` → whitelist через `_shared/cors.ts` для всех 57 edge functions

## 2026-03 — Agent Second Brain

**Контекст:** Разработка генерирует знания которые теряются между сессиями.
**Решение:** Telegram бот + Claude CLI на VPS + Obsidian vault с Git sync + Memory Engine (Ebbinghaus decay).
**Результат:** Единая система где информация автоматически классифицируется, связывается и доступна из любой точки.

## 2026-03 — Агентный инжиниринг (27 скиллов)

**Контекст:** Проект слишком большой для одного контекстного окна.
**Решение:** 27 специализированных скиллов с доменными знаниями, cross-domain knowledge и self-update rules. Маршрутизация по триггерам в CLAUDE.md.
**Формат:** CRITICAL → KEY FILES → PATTERNS → DELEGATE → INLINE KNOWLEDGE → SELF-UPDATE → REFERENCES

## 2026-02 — Provider Router с fallback chains

**Контекст:** AI-модели нестабильны, каждая имеет свои сильные стороны.
**Решение:** Роутер с цепочками fallback: nanoBananaPro → falFlux → lovable.
**Результат:** Генерация не падает если один провайдер лежит.

## 2026-02 — Identity System (strict/balanced/free)

**Контекст:** При крафтах нужно контролировать насколько AI сохраняет исходный подарок.
**Решение:** 3 режима: strict (максимальное сохранение), balanced (творческая свобода), free (полная свобода).

## 2026-02 — Seedream Compose для multi-image

**Решение:** Seedream V4/V5 Edit с multi-image composition. Natural language промпты.
**Урок:** ByteDance модели не знают культурные ссылки. Только универсальные визуальные дескрипторы.

## 2026-02 — bypassServerEnhancement для крафтов

**Решение:** `bypassServerEnhancement: true` для craft pipeline — smart-prompt-engine НЕ используется.
**Результат:** Клиент полностью контролирует промпт. `appendProviderSuffix` тоже скиппается.

## 2026-02 — Sticker Pack Builder V2

- Zustand store `stickerPackBuilderStore` отдельный от `workspaceStore`
- Parallel generation: Semaphore(3), контроль concurrency
- Smart ordering: happy first, then contrasting, then rest
- Situation IDs plain (`moon`), не prefixed
- 10 templates

## 2026-02 — FAL Provider Migration

- Hailuo: Krea → FAL `fal-ai/minimax/hailuo-02/standard/image-to-video`
- Kling: Krea → FAL `fal-ai/kling-video/v2.5-turbo/pro/image-to-video`
- Polling fix: model-agnostic `falResultUrlFromStatus()`
- Seedream v4 Edit + Reve Edit через `generate-edit-fal` edge fn

## 2026-02 — Style System SSOT

- SINGLE source of truth: `sharedStyleProfiles.ts` — 51 стиль
- Каждый стиль: craftHints (composeSuffix, fusionSuffix, preferredProvider)
- mandatoryPrefix для трансформативных стилей
- Identity levels: strict → balanced → free

## 2026-02 — Admin Panel

- 5 секций: Генерации, Инфраструктура, Подарки, AI & Аналитика, Пользователи
- URL params: `?s=section&t=tab`

## 2026-02 — Animation Pipeline

- 3 режима: color-transfer, shape-replace (основной), ai-generate
- Shape-replace через VTracer vectorization
- Edge function `generate-lottie-structure` создана но НЕ задеплоена

## Связанные заметки
- [[giftmixer-overview]] — Обзор проекта
- [[giftmixer-lessons]] — Уроки
- [[giftmixer-roadmap]] — Roadmap

### 2026-03-17: Atomic Lightning + Refund Architecture
- **Decision**: Migrate from spend_lightning to spend_lightning_atomic with idempotency key and FOR UPDATE lock.
- **Decision**: Refunds restore balance as balance_purchased (never expires), linked via refund_for FK.
- **Decision**: Cost multipliers live in backend PROVIDER_COST_MULTIPLIERS object (SSOT), frontend mirrors for preview only.
- **Decision**: GENERATION_COSTS in types/lightning.ts is the SSOT for sticker/craft/enhance costs. All consumers import from there.
- **Rationale**: Race conditions and cost mismatches are P0 launch blockers per Perplexity deep research audit.

### 2026-03-17: Vision-Based Style & Motion Extraction (Phase 3.1B)
- **Decision**: При генерации сцен анализировать исходный референс (gift-анимация, видео, фото) через Gemini Vision для автоматического определения визуального стиля и паттерна движения.
- **Decision**: Результат анализа = auto-styleId + motion hint для video-gen промпта. Заменяет ручной выбор стиля когда исходник анимированный.
- **Кейсы**: подарки из коллекции TG (анимированные), загруженные видео-референсы, статичные фото с auto-style.
- **Rationale**: Стиль сцены должен вытекать из персонажа, а не задаваться отдельно. Источник идеи — Creative Content Engine (RoboNuggets): "analyze reference videos, extracting style/tone/pacing to inform prompts".
