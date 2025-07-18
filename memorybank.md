# Memory Bank: tmd-editor

## Назначение
Плагин, который позволяет редактировать файлы с расширением .tmd (markdown-формат тренировок) в кастомном React-интерфейсе. Поддерживает добавление, редактирование и удаление упражнений и подходов, автосохранение, парсинг и сериализацию .tmd.

---

## Архитектура

- **Plugin** (TypeScript)
  - Точка входа: `src/main.ts`
  - Кастомный view: `src/TmdView.tsx` (наследуется от FileView)
  - React-интерфейс: `src/components/TmdEditor.tsx`
  - Парсер и сериализатор .tmd: `src/TmdParser.ts`
  - Манифест: `manifest.json`
  - Сборка: esbuild, итоговый файл `dist/main.js`

---

## Основные паттерны и решения

- **Парсинг .tmd**: Markdown-файл разбивается на упражнения по заголовкам `###`. Каждое упражнение содержит опциональную заметку и таблицу подходов. Таблицы парсятся устойчиво к пустым строкам и разделителям.
- **Редактирование**: Все изменения происходят в React-состоянии. Добавление/удаление подходов и упражнений, редактирование ячеек.
- **Автосохранение**: Сохраняется только при реальных изменениях пользователя (dirty=true). При открытии файла редактор не вызывает автосохранение.
- **Loader**: Пока файл не загружен (onLoadFile), отображается loader.
- **Удаление подхода**: Кнопка удаления справа от каждой строки подхода (кроме заголовка).
- **Сброс состояния**: При открытии нового файла редактор сбрасывает состояние на новое содержимое.

---

## Новые функции (2024)

- **Контекстное меню Obsidian**:
  - Кнопка "Создать tmd-файл" — создаёт новый tmd-файл с шаблоном, имя по умолчанию — текущая дата (dd.MM.YY).
  - Кнопка "Дублировать (tmd)" — для .md-файлов создаёт копию с расширением .tmd и уникальным именем.
- **Редактирование упражнений**:
  - Кнопка удаления упражнения рядом с названием, с диалогом подтверждения.
  - Кнопка редактирования названия упражнения (✎), открывает диалог с инпутом и кнопками "Отмена"/"Сохранить".
- **Редактирование имени файла**:
  - Вверху редактора отображается имя файла и кнопка ✎ для переименования.
  - Диалог переименования содержит инпут, кнопки "Отмена"/"Сохранить", три emoji-кнопки (🎒, 🦵🏼, ➡️) для вставки emoji, а также кнопку с текущей датой (dd.MM.YY), которая очищает поле и подставляет дату.
  - Проверка на уникальность имени и пустое значение.

---

## Примеры .tmd

```
[[Спина, бицепс]]
Начинать тренировку с задней дельты
### Пресс/молитва
| Вес | Повторы |
| --- | ------- |
|     | 20      |
|     | 20      |
|     | 20      |
|     |         |

### Гиперэкстензия
| Вес | Повторы |
| --- | ------- |
| 10  | 20      |
| 10  | 20      |
| 10  | 20      |
|     |         |
```

---

## Особенности
- Плагин не зависит от process.env, NODE_ENV задаётся через define в esbuild.
- Для корректной работы с файлами используется наследование от FileView.
- Парсер устойчив к пустым строкам, разделителям, заметкам между упражнениями.
- Все действия пользователя (добавление, удаление, редактирование) мгновенно отражаются в UI и автосохраняются.

---

## TODO / Идеи для развития
- Поддержка drag-and-drop для перестановки подходов/упражнений
- Поддержка заметок к отдельным подходам
- История изменений/undo
- Улучшение UX (анимации, подтверждения удаления)
- Тесты для парсера и сериализатора 