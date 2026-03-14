---
type: learning
title: Cinema — Parallel Design Risk Analysis
date: 2026-03-14
tags: [cinema, parallel-work, risk-analysis, coordination]
---
# Cinema: Параллельный дизайн — анализ рисков и правила

Контекст: 3 параллельных вектора изменений Cinema (наш Claude Code, Lovable agent, REFACTORING_PLAN)

## Ситуация

Три параллельных источника изменений одновременно:
1. **Claude Code** (мы) — shared config consolidation, trio compose, mobile-first SceneCard
2. **Lovable/Cursor agent** — UI redesign по lovable-cinema-redesign-prompt.md
3. **REFACTORING_PLAN.md** — 22-задачный roadmap от другого AI (Gemini?)

## Критические зоны конфликта

### ZONE 1: CinemaSceneCard.tsx — ГОРЯЧАЯ ТОЧКА
Все три вектора меняют этот файл ОДНОВРЕМЕННО:
- Мы: импорт из shared config, compact/expanded pattern
- Lovable: убрать все shadow glow, промпт visible, увеличить touch targets
- Plan: progressive disclosure (collapsed/expanded), 756→350 строк

**ПРАВИЛО**: Перед любым изменением SceneCard — git pull + проверить нет ли параллельных PR.

### ZONE 2: CinemaBuilderPage.tsx — ДЕКОМПОЗИЦИЯ
Plan предлагает разбить на 5 фазовых файлов + useCinemaWizard хук.
Мы добавляем import shared config + archetypeSuffix в handleRecompose.
Lovable трогает cost display.

**ПРАВИЛО**: НЕ начинать декомпозицию CinemaBuilderPage пока не ясно — делает ли это Lovable agent. Наши изменения (import, handleRecompose body) переживут декомпозицию — они в логике, не в структуре.

### ZONE 3: Дизайн-система (glass-card, chip, btn)
Lovable определяет CSS классы (.glass-card, .chip, .btn-primary).
Мы используем inline Tailwind в SceneCard.

**ПРАВИЛО**: Если Lovable внедряет design tokens — адаптировать SceneCard под них при merge, не сопротивляться.

## Что безопасно делать параллельно

| Зона | Безопасно? | Почему |
|------|------------|--------|
| archetypeDefinitions.ts | YES | Наш файл, никто больше не трогает |
| videoProviders.ts | YES | Наш файл, SSOT |
| recompose-scene edge function | YES | Backend, Lovable не трогает edge functions |
| generate-story-brief edge | YES | Backend, только мы + Plan |
| CinemaSceneCard.tsx | DANGER | Все три вектора одновременно |
| CinemaBuilderPage.tsx | CAUTION | Plan хочет разбить, мы добавляем imports |
| StoryBuilder.tsx | SAFE | Lovable трогает, но мы не трогаем |
| New components (Stepper, ErrorBoundary) | SAFE | Новые файлы, нет конфликтов |

## Что принять к сведению из REFACTORING_PLAN

### Хорошие идеи (внедрить):
1. **Vision-анализ стикера перед Director** (задача #18) — решает корневую проблему identity drift. Gemini Vision описывает стикер → точный Character Lock. Это P0 improvement.
2. **Adaptive image_prompt_strength** (задача #8) — cartoon/sticker → 0.35, photo → 0.60. Простой classifier.
3. **Error Boundary** (задача #5) — обязательно, стоит 30 минут работы.
4. **Sticky CTA** (задача #3) — position: sticky, bottom: 0. Тривиально.

### Спорные идеи (обсудить):
1. **Разбить CinemaBuilderPage на 5 файлов** — правильно по принципу SRP, но 1400 строк это не катастрофа для wizard. Risk: 5 файлов = 5x merge conflicts. Better: extract hooks first (useCinemaWizard), UI later.
2. **Inline template gallery вместо bottom-sheet** — bottom-sheet это TG-native pattern, inline может не влезть на 320px. Нужен A/B.
3. **Prompt quality scoring** (#17) — overengineering если нет метрики "хороший промпт". Пока skip.

### Плохие идеи (отклонить):
1. **modelRouter.ts как отдельный файл** — у нас routing уже в recompose-scene, дублировать на фронт = рассинхрон.
2. **assetAnalyzer.ts** — classifier для adaptive strength это 1 if/else, не нужен отдельный файл.
3. **consistencyChecker.ts** — нет метрик, нет данных, premature.

## Что принять из lovable-prompt

### Обязательно:
1. **Убрать animate-pulse на статичных элементах** — performance win на мобилке
2. **Touch targets 44x44px** — TG Mini App guideline, без этого UX broken
3. **glass-card/chip/btn стандарты** — полезно если весь Cinema переходит

### Осторожно:
1. **Промпт visible по умолчанию** — на мобилке 320px это 3 строки font-mono = 100px высоты на карточку. При 5 сценах = scroll hell. Progressive disclosure ЛУЧШЕ.
2. **Убрать ВСЕ shadow glow** — lose visual hierarchy. Лучше: оставить 1-2 акцента, убрать массовые.

## Зона потенциального развития

### Style DNA (из cinema-adaptive-architecture.md)
Идея: AI генерирует уникальный визуальный стиль для каждого фильма вместо выбора из 12 архетипов.
Статус: Правильная идея, premature execution. Prerequisites:
1. Сначала: архетипы работают стабильно (мы это делаем)
2. Потом: A/B test — архетип vs StyleDNA на 100 генераций
3. Потом: если StyleDNA выигрывает — migrate

### Recommended next architecture step
НЕ Style DNA, а: **Composition Preview Pipeline**
- Before video gen: user sees 2-3 composed frames (cheap, fast)
- Selects best → only that goes to expensive I2V
- Saves 60-70% cost on bad generations
- This is what PreviewFrameGrid component (from Lovable) enables