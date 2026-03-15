# Triple Audit Insights — Story Builder + Cinema Studio
Дата: 2026-03-15
Контекст: Три независимых аудита (PDF, два MD) проанализированы и сверены с кодом

---

## Ключевые уроки

### 1. Корень визуальных артефактов = двойной стиль в промпте
Все 3 аудита сходятся: пользователь выбирает стиль (style_lock), AI назначает другой (archetype), оба попадают в промпт одновременно. Модель пытается совместить "vinyl toy" + "cinematic film" = каша.

### 2. Двойная трансформация промптов
Story Builder НЕ отправляет client_adapted=true → сервер запускает adaptPromptForProvider() на уже структурированный промпт от Storyteller → разбивает по запятым → теряет контекст. Cinema Studio отправляет этот флаг — поэтому Cinema работает лучше.

### 3. NB2 для duo = identity destruction
Nano Banana 2 в edit mode переинтерпретирует стиль обоих персонажей. 60% duo-сцен нерабочие. Gemini 3.1 Pro Image значительно лучше для multi-character composition.

### 4. Формула стоимости дублирована и расходится
StoryBriefInput показывает статическую цену (для duration=5). При duration=8 пользователь видит 35 молний, платит 50. Четыре разных места хранят формулу.

### 5. giftIdentityLock.ts НЕ используется Cinema
ironRoleResolver.ts с hardcoded описаниями ("felt button eyes") — это ТОЛЬКО для стикеров. Cinema использует свою систему (outfitLock от AI + image_prompt_strength). Разные пайплайны identity.

### 6. 12 из 20 проблем были уже исправлены
Рефакторинг 2→1 агент, STYLE_VISUAL_MAP, identity clause, adaptive strength — всё это уже работает. Аудиты писались ДО последних коммитов.

## Применение
- При следующей работе над Cinema/Story: сверяться с планом в `noble-inventing-rivest.md`
- Sprint A (7 задач) можно сделать за 1-2 часа — все Easy/Trivial
- UX-проблемы (Sprint C) отдать на Lovable