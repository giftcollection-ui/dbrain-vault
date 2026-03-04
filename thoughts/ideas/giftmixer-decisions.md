---
type: decisions
title: GiftMixer — Key Decisions
last_accessed: 2026-03-04
relevance: 1.0
tier: core
---
# GiftMixer — Ключевые архитектурные решения

## 2026-02 — Provider Router с fallback chains

**Контекст:** AI-модели нестабильны, каждая имеет свои сильные стороны.
**Решение:** Роутер с цепочками fallback: nanoBananaPro → falFlux → lovable. Каждый провайдер адаптирует промпт под свои особенности.
**Результат:** Генерация не падает если один провайдер лежит. Разные модели для разных задач.

## 2026-02 — Identity System (strict/balanced/free)

**Контекст:** При крафтах нужно контролировать насколько AI сохраняет исходный подарок.
**Решение:** 3 режима identity в промптах: strict (максимальное сохранение), balanced (творческая свобода с узнаваемостью), free (полная свобода).
**Результат:** Пользователь контролирует "насколько похоже на оригинал".

## 2026-02 — Seedream Compose для multi-image

**Контекст:** Нужно объединять 2+ изображения в одно (fusion подарков).
**Решение:** Seedream V4/V5 Edit с multi-image composition. Natural language промпты (не bracket tags). Стиль первым в промпте.
**Урок:** ByteDance модели не знают культурные ссылки (Ohuenko, Lisa Frank). Только универсальные визуальные дескрипторы.

## 2026-02 — bypassServerEnhancement для крафтов

**Контекст:** Серверный prompt enhancement портил крафт-промпты.
**Решение:** Флаг `bypassServerEnhancement: true` для craft pipeline — промпт идёт на модель as-is.
**Результат:** Клиент полностью контролирует промпт.

## 2026-03 — Cinema Visual Consistency

**Контекст:** В видео-сценах персонажи менялись между кадрами.
**Решение:**
- Visual Bible (10-15 слов) — компактный стиль-дескриптор для всех сцен
- I2V промпт = ТОЛЬКО движение + камера (никаких описаний персонажа)
- First-Frame Chaining — последний кадр предыдущей сцены → input следующей
- Anti-mutation rule в Director промпте
**Результат:** Визуальная консистентность между сценами.

## 2026-03 — Security Sprint (4 CRITICAL)

**Контекст:** Перед запуском — аудит безопасности.
**Решение:**
1. Auth bypass fix: `isDevEnvironment()` = `import.meta.env.DEV` only
2. Payment webhook: signature validation + idempotency
3. API key: OpenRouter → server-side edge function
4. CORS: `Allow-Origin: *` → whitelist для всех 57 edge functions
**Результат:** Production-ready security.

## 2026-03 — Agent Second Brain

**Контекст:** Разработка генерирует много знаний которые теряются между сессиями.
**Решение:** Telegram бот + Claude CLI на VPS + Obsidian vault с Git sync + Memory Engine (Ebbinghaus decay).
**Результат:** Единая система где информация не забывается — автоматически классифицируется, связывается и доступна из любой точки.

## 2026-03 — Агентный инжиниринг (15 скиллов)

**Контекст:** Проект слишком большой для одного контекстного окна.
**Решение:** 15 специализированных скиллов: prompt-architect, lottie-animator, craft-engineer, supabase-ops, telegram-native, data-engineer, quality-guardian, unit-economist, growth-marketer, security-ops, agent-factory, code-reviewer, edge-reviewer, model-scout, video-pipeline-engineer.
**Результат:** Каждый скилл содержит доменные знания, cross-domain knowledge, и self-update rules. Маршрутизация по триггерам в CLAUDE.md.
