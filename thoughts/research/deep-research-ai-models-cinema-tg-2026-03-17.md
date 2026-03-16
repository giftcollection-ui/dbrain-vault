---
type: research
title: "Deep Research: AI Models, Cinema Pipeline, TG Mini App"
date: 2026-03-17
tags: [ai-models, cinema, telegram, fal-ai, kling, kontext, wan, hailuo, veo3, nb2]
source: Perplexity deep research + FAL.ai docs
relevance: 1.0
tier: active
---

# Deep Research: AI Models, Cinema Pipeline, TG Mini App

Дата: 2026-03-17
20 вопросов, исследованы слепые зоны GiftMixer.

## Ключевые открытия

### 1. Kontext Pro/Max — НЕ IP-Adapter
Kontext использует **sequence concatenation** (flow matching). Входное изображение конкатенируется с текстом в latent space. Нет отдельного image encoder.
- **Нет параметра `image_prompt_strength`** — FAL.ai тихо игнорирует
- Preservation контролируется через промпт: "Maintain exact facial features..." / "Keep same character design..."
- Промпт лимит: 512 токенов
- Pro $0.04/img, Max $0.08/img (лучше типография)

### 2. Kling 3.0 — полные спецификации
- Standard: 1080p/30fps, $0.084/sec. Pro: 4K/60fps, ~$0.12/sec
- cfg_scale: 0-1, default 0.5. Chibi: 0.6-0.7, фоны: 0.4-0.5
- Multi-Shot: до 6 shots, 15s total, `multi_prompt` array
- Elements API: `frontal_image_url` + `reference_image_urls[]`, привязка через `@Element1`
- Negative prompt: YES, default "blur, distort, and low quality"

### 3. WAN 2.6
- Hard limit: **800 символов** (реально слышит первые 200-300)
- Negative prompt: YES (полноценный)
- R2V mode: reference image для character consistency (альтернатива Kling Elements)
- Flash 1.3B: 480p, быстрая генерация (~2.7 sec), для превью

### 4. FAL.ai Rate Limits
- Старт: 2 concurrent requests
- Scale: авто до 40 concurrent при $1000+ инвойсов
- Queue: запросы сверх лимита — IN_QUEUE (не отклоняются)
- Best practice: `submit()` + `webhook_url` для видео, `subscribe()` для изображений

### 5. Nano Banana 2
- До 14 reference images, 5 персонажей
- $0.15/image (дороже Kontext, но multi-character)
- Gemini 3.1 Flash Image базовая модель

### 6. Новые модели (март 2026)
- Hailuo 02 Pro: 1080p, 25fps, ~$0.07/sec, физика лучше Kling
- Veo 3: $0.10/sec, аудио, но нет Elements API (до 3 ref images в 3.1)
- Veo 3: **НЕ поддерживает negative prompt**
- Kling 3.0 остаётся SOTA для character consistency

### 7. Krea.ai vs FAL.ai
- Krea: subscription ($9-$200/мес), Kling только 1.0 Pro (не 3.0!)
- FAL: pay-per-use, Kling 3.0, лучшее API, webhooks
- Вывод: FAL предпочтителен для продакшна

## TG Mini App
- 2-3 `<video>` тега одновременно безопасно, >3 → memory pressure на iOS
- IndexedDB > localStorage (TG может очистить localStorage)
- Service Workers не поддерживаются
- Stars Subscriptions теперь доступны (multiple tiers)
- shareURL баг: `t.me/share?url=` → `t.me/share/url?url=` (наш shareToStory не затронут)

## Действия выполнены
- [x] Убрали image_prompt_strength из Kontext API calls → prompt engineering
- [x] Обновили videoProviders.ts: +Hailuo, Veo3 4.0x→2.9x, Kling Pro 3.2x→3.4x
- [x] Добавили suggestedCfgScale в archetypeDefinitions (13 архетипов)
- [x] WAN prompt limit комментарий (800 chars hard limit)
- [x] Обновили skill references: provider-prompt-kb, cost-model, cinema-pipeline

## Backlog (P1-P2)
- [ ] FAL Webhook endpoint для видео-генерации
- [ ] Cinema draft state в IndexedDB
- [ ] Kling Multi-Shot (3 shots × 5 sec)
- [ ] WAN R2V integration
- [ ] Stars Subscriptions

## Связанные заметки
- [[giftmixer-decisions]] — архитектурные решения
- [[giftmixer-lessons]] — уроки
- [[cinema-roadmap-p0-p3]] — roadmap