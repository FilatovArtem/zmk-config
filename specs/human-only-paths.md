# Human-Only Paths

Project-specific overrides поверх глобальной матрицы
[ai-human-split](~/.claude/memory/ai-human-split.md).

Файлы/директории из этого списка ВСЕГДА `owner: human` при классификации
через `/speckit.classify`, даже если по глобальным критериям получается `ai` или `pair`.

Читается агентом `task-classifier.md`.

## Human-only

- `config/lily58.keymap` — личная раскладка, muscle memory; крупные изменения только pair с пользователем
- `build.yaml` — build matrix; меняется редко, требует подтверждения

## Force-AI (override в обратную сторону)

(нет)

## Правила

- Изменения — через коммит с обоснованием в сообщении
- Для human → ai переходов обязателен хотя бы один test-case
