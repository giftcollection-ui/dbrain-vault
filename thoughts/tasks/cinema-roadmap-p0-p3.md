# Cinema Studio — Verified Roadmap
Дата: 2026-03-14
Контекст: AI-аудит верифицирован против кодовой базы. 20 задач в 4 приоритетных блоках.
---

## P0 — Критические (неделя 1-2)
1. Декомпозиция CinemaBuilderPage (1403 строки -> 5 phase-компонентов) [3-5 дней]
2. Error Boundaries для Cinema [1 день]
3. Race condition world background (EdgeRuntime.waitUntil) [1 день]
4. Credit atomicity (spend_lightning до генерации) [2 дня]

## P1 — Важные (неделя 3-4)
5. 4-angle character reference (FaceRefUploader) [3 дня]
6. Creativity slider (CFG hardcoded 0.5) [1 день]
7. Mode selection hierarchy (T2V/I2V/V2V) [2 дня]
8. suggestedCast type mismatch fix [0.5 дня]
9. Галерея фильмов (cinema_movies page) [3 дня]
10. Аналитика генераций (admin dashboard) [3 дня]

## P2 — Улучшения (месяц 2)
11-16: Chained render UI, Veo 3.1, Inpainting, SeeDance, V2A, marketplace_presets

## P3 — Масштабирование (месяц 3+)
17-20: V2V UI, Video editor, A/B testing, UGC templates

## Исправления аудита
- 8 стилей (не 13)
- KeyFrames неприменим к Cinema (T2V/I2V API)
- Nano Banana не в Cinema pipeline