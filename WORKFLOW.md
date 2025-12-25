# Workflow: Конвертация Word → Markdown

Пошаговое руководство по конвертации документов из Word (.docx) в Markdown для репозитория guides.

## Требуемые инструменты

### Обязательные
- **Pandoc** — основной конвертер (https://pandoc.org/)
- **VS Code** — редактор с поддержкой Markdown
- **Git** — система контроля версий

### Рекомендуемые расширения VS Code
- Markdown All in One
- Markdown Preview Enhanced
- YAML

## Шаг 1: Подготовка

### 1.1 Определите место файла в структуре

Перед конвертацией определите:

1. **Программа** (1-personal, 2-work, 3-research)
2. **Руководство** (например, 1.1-self-development)
3. **Раздел** (01-attention, 02-focus и т.д.)
4. **Тип контента**:
   - Инструкция → `.md`
   - Таблица → `.table.yaml`
   - Вопрос → `.question.yaml`

### 1.2 Подготовьте имя файла

Формат: `NN-slug-name.extension`

Правила:
- `NN` — двузначный порядковый номер (01, 02, ...)
- `slug-name` — краткое название в kebab-case (латиницей)
- Используйте транслитерацию для русских названий

Примеры:
- `01-pomodoro-technique.md`
- `02-attention-log.table.yaml`
- `03-reflection-question.question.yaml`

## Шаг 2: Конвертация с Pandoc

### 2.1 Базовая конвертация

```bash
pandoc "Название файла.docx" -o output.md --wrap=none
```

### 2.2 Конвертация с извлечением изображений

```bash
pandoc "Название файла.docx" \
  -o output.md \
  --wrap=none \
  --extract-media=./assets
```

Изображения будут сохранены в папку `assets/media/`.

### 2.3 Дополнительные опции

```bash
pandoc "Название файла.docx" \
  -o output.md \
  --wrap=none \
  --extract-media=./assets \
  --standalone \
  --markdown-headings=atx
```

## Шаг 3: Постобработка Markdown

### 3.1 Добавьте YAML frontmatter

В начало файла добавьте:

```yaml
---
id: "1.1.01.01"
title: "Название подраздела"
description: "Краткое описание (1-2 предложения)"
order: 1
guide_ref: "1.1"
section_ref: "1.1.01"
tags: ["тег1", "тег2"]
created: "2025-01-01"
updated: "2025-01-01"
---
```

### 3.2 Проверьте и исправьте заголовки

- Заголовок H1 (`#`) — должен быть один, совпадает с `title`
- Подзаголовки H2-H4 (`##`, `###`, `####`) — иерархия
- Удалите дублирующиеся заголовки

### 3.3 Исправьте таблицы

Pandoc часто генерирует сложные таблицы. Упростите до:

```markdown
| Колонка 1 | Колонка 2 | Колонка 3 |
|-----------|-----------|-----------|
| Значение  | Значение  | Значение  |
```

### 3.4 Исправьте пути к изображениям

Замените:
```markdown
![](./assets/media/image1.png)
```

На:
```markdown
![Описание изображения](./assets/image-name.png)
```

Переименуйте изображения по схеме:
`I.P.G.SS.NN-description.png`

Пример: `I.1.1.01.01-pomodoro-diagram.png`

### 3.5 Исправьте списки

Убедитесь, что:
- Маркированные списки используют `-`
- Нумерованные списки используют `1.`, `2.`, ...
- Есть пустая строка перед/после списка

### 3.6 Проверьте ссылки

Внутренние ссылки:
```markdown
[Название](../путь/к/файлу.md)
```

Внешние ссылки:
```markdown
[Название](https://example.com)
```

## Шаг 4: Валидация

### 4.1 Проверьте YAML frontmatter

```bash
# Используйте онлайн-валидатор или:
python -c "import yaml; yaml.safe_load(open('file.md').read().split('---')[1])"
```

### 4.2 Проверьте Markdown

- Откройте Preview в VS Code (Ctrl+Shift+V)
- Убедитесь, что все отображается корректно

### 4.3 Checklist перед коммитом

- [ ] YAML frontmatter заполнен корректно
- [ ] id соответствует расположению файла
- [ ] Заголовок H1 совпадает с title
- [ ] Все изображения на месте и отображаются
- [ ] Таблицы рендерятся корректно
- [ ] Ссылки работают
- [ ] Файл в правильной папке

## Шаг 5: Размещение в репозитории

### 5.1 Создайте необходимые папки

```bash
mkdir -p programs/1-personal/1.1-self-development/01-section/assets
```

### 5.2 Скопируйте файлы

```bash
# Markdown файл
cp output.md programs/1-personal/1.1-self-development/01-section/01-subsection.md

# Изображения
cp assets/media/* programs/1-personal/1.1-self-development/01-section/assets/
```

### 5.3 Обновите индексные файлы

Добавьте ссылку на новый раздел в:
- `programs/1-personal/1.1-self-development/index.md`

## Шаг 6: Git операции

```bash
# Добавьте файлы
git add programs/1-personal/1.1-self-development/01-section/

# Создайте коммит
git commit -m "feat(1.1): добавлен раздел 01 - Название раздела"

# Отправьте в репозиторий
git push
```

## Примеры конвертации

### Пример 1: Простая инструкция

**Исходный Word:**
> Заголовок: Техника Pomodoro
> Текст о технике...

**Результат Markdown:**

```markdown
---
id: "1.1.01.01"
title: "Техника Pomodoro"
description: "Метод управления временем для повышения продуктивности"
order: 1
guide_ref: "1.1"
section_ref: "1.1.01"
tags: ["внимание", "продуктивность", "практика"]
created: "2025-01-01"
updated: "2025-01-01"
---

# Техника Pomodoro

## Что это такое

Техника Pomodoro — это метод управления временем...

## Как применять

1. Выберите задачу
2. Установите таймер на 25 минут
3. Работайте до сигнала
4. Сделайте короткий перерыв (5 минут)
5. Повторите

## Связанные материалы

- [Дневник внимания](./02-attention-log.table.yaml)
```

### Пример 2: Таблица моделирования

Если в Word есть таблица для заполнения студентами, создайте `.table.yaml`:

```yaml
id: T.AttentionLog
title: "Дневник внимания"
description: "Таблица для ежедневного отслеживания фокусировки внимания"
guide_ref: "1.1.01.01"

schema:
  type: object
  required: [date, task, duration, distractions]
  properties:
    date:
      type: string
      format: date
      title: "Дата"
    task:
      type: string
      title: "Задача"
      minLength: 5
    duration:
      type: integer
      title: "Длительность (мин)"
      minimum: 1
    distractions:
      type: integer
      title: "Отвлечения"
      minimum: 0

columns:
  - name: date
    title: "Дата"
    width: 100
  - name: task
    title: "Задача"
    width: 300
  - name: duration
    title: "Время (мин)"
    width: 100
  - name: distractions
    title: "Отвлечения"
    width: 100

examples:
  - date: "2025-01-01"
    task: "Написание отчета"
    duration: 25
    distractions: 2

homework:
  min_rows: 7
  criteria:
    - "Заполнено минимум 7 дней"
    - "Все поля заполнены"
    - "Задачи описаны конкретно"
```

## Типичные проблемы и решения

| Проблема | Решение |
|----------|---------|
| Кривые таблицы после Pandoc | Упростите вручную до базового Markdown |
| Изображения не отображаются | Проверьте пути, переименуйте файлы |
| Кириллица в именах файлов | Используйте транслитерацию |
| Сложное форматирование Word | Упростите, удалите лишнее |
| Встроенные объекты (Excel, диаграммы) | Экспортируйте как изображения |

## Поддержка

При возникновении вопросов:
1. Проверьте [спецификацию структуры](https://github.com/aisystant/ecosystem-development/blob/main/content/0.%20Управление/0.9.%20Входящие/Структура%20репозитория%20руководств%200.9.md)
2. Используйте шаблоны из `_templates/`
3. Обратитесь к maintainer репозитория
