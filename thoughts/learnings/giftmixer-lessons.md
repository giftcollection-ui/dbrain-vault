---
type: learning
title: GiftMixer — Lessons Learned
date: 2026-03-04
tags: [giftmixer, ai-prompts, architecture, cinema]
---
# GiftMixer — Уроки из разработки

## AI промпты

- **Seedream "raw" mode не существует** — только "standard" и "fast". Молча деградирует.
- **Оптимальная длина промпта: 30-100 слов.** Больше 100 = деградация.
- **Bracket tags `[BASE SUBJECT]` — модель НЕ понимает.** Только natural language.
- **Стиль первым в промпте** — Seedream приоритизирует ранние токены.
- **I2V промпт = ТОЛЬКО движение + камера.** Описание персонажа/сеттинга → дубли поверх изображения.
- **АУРА = свечение ВОКРУГ силуэта.** Слово "wrap" = буквально обернуть → перекрывает лицо.
- **V28 правило: доверяй vision модели.** Не навязывай rigid categories — дай модели увидеть изображение и решить.

## Код и архитектура

- **Всегда используй `getGiftImageUrl()`** — `gift.imageUrl` может быть локальный path.
- **`store.abortController` в useCallback deps** создаёт бесконечный re-render — читать через `.getState()`.
- **Craft pipeline: `unifiedGenerator.ts` НЕ используется** — только StickerWizard.
- **Stale closure в setInterval**: `setFrames(prev => { read prev; return prev; })` — единственный способ.

## Процесс

- **ВСЕГДА сначала ищи рабочий паттерн в кодовой базе.** generate-motion уже работал. Вместо этого изобретали с нуля. Потерянные часы.
- **Аудит ПЕРЕД реализацией** экономит время — находишь баги до того как пишешь код поверх них.
- **Никогда force kill браузер** — потеря cookies, сессий. Только ручное закрытие.
- **Не лезть в сырые CLI инструменты** если задача не критична. nlm CLI стоил потерянного времени и данных.

## Cinema / Видео

- **Visual Bible** (10-15 слов) — стиль-дескриптор для единого стиля всех сцен.
- **Anti-mutation rule** — "NEVER change physical geometry" в Director промпте.
- **First-Frame Chaining** — extract last frame → передать следующей сцене.

## Связанные заметки
- [[giftmixer-overview]] — Обзор проекта
- [[giftmixer-decisions]] — Архитектурные решения
- [[giftmixer-roadmap]] — Roadmap
