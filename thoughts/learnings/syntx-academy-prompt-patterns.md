# Syntx Academy — Prompt Patterns for Cinema Studio
Дата: 2026-03-14
Контекст: Извлечено из 22 гайдов academy.syntx.ai для интеграции в Cinema pipeline
---

## 1. Универсальная структура видео-промпта
```
[камера] -> [действие субъекта] -> [освещение/атмосфера] -> [диалог "в кавычках"]
```
ПРАВИЛО: Действие ВСЕГДА перед речью (для корректного lip-sync).

## 2. Multi-Shot формат (Kling 3.0)
```
Shot 1: duration: 3.0sec
Scene: Wide shot... camera smoothly zooms in...
Dialogue: (Man Left): "..."
Shot 2: duration: 2.0sec
Scene: Close-up...
Dialogue: (Man Right): "..."
instructions: General description of the scene concept
```
- Max 15 секунд total, распределить между shot'ами
- Каждый shot: ракурс + камера + действие + диалог
- instructions внизу = общий контекст

## 3. KeyFrames паттерн (Kling 3.0)
3 ключевых кадра:
- Frame 1: начало сцены
- Frame 2: переходная точка (общий объект заполняет кадр)
- Frame 3: конец новой сцены
Логика: сцена 1 ПРИВОДИТ к frame 2, сцена 2 НАЧИНАЕТСЯ от frame 2.
Промпт описывает камеру и трансформацию подробно.
Creativity: default. Quality: 720p test, 1080p final.

## 4. SeeDance [CUT] команда
Разделяет сцены внутри одного видео из одного изображения:
```
"Character sits sadly...
[CUT]
Raccoon jumps to cheer up...
[CUT]
Close-up — expression changes..."
```
Каждый сегмент: простые фразы + главное движение + эмоции.

## 5. 4-Angle Character Reference
Промпт для Nano Banana Pro:
```
"One character, 4 angles in one image: 0deg, 90deg, 180deg, 270deg.
Complete identity of appearance, no distortion, full height, neutral background"
```
Формат: 16:9, Banana Pro, 4K.
Потом: close-up каждого персонажа отдельно ("Make a close-up shot of X. Keep the original pose.")

## 6. V2V (Video-to-Video) промпт
```
"Replace the background around [subject] with [new environment].
[Subject description + new elements].
Keep the main object and camera movement unchanged."
```
Модель: Kling o1 Video to Video. Professional mode auto. Duration = source.

## 7. Inpainting (Nano Banana Pro Editor)
1. Brush: выделить область изменения
2. Reference image: загрузить объект для вставки
3. Prompt: "Add [object]. Apply lighting and perspective to the entire composition."
Для удаления: "Remove [object]." — без reference.

## 8. Veo 3.1 — Voiceover из коробки
```
"The woman smiles and says, 'This video was created easily!'"
```
ПРАВИЛО: действие → речь. Max 1-2 предложения. Image-to-Video mode.

## 9. Motion Copy (Kling Motion)
- Professional Mode + Video orientation
- Промпт минимальный: "The character speaks." или "The character is dancing."
- Важно: поза на reference image = поза на reference video

## 10. Looping Video (Luma Ray 2 Flash)
- Активировать Loop option
- Центральная композиция + сбалансированный фон
- Query Amplifier для точного соответствия тексту

## 11. Quality Control
- 720p для тестов, 1080p для финала
- Creativity default = баланс, max = художественно, min = точность
- Professional mode: auto для V2V и Motion
- Negative prompt: "No text, no logos, no people" (если не нужны)

## Применение к Cinema Studio
- Cinematographer agent уже генерирует промпты — обогатить паттернами 1-3
- Multi-Shot format можно автоматически собирать из Director scenes
- 4-angle reference — новая фича для useFaceRefUploader
- [CUT] command — может быть полезен для SeeDance интеграции
- V2V prompt template — для будущего V2V UI