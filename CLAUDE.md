# zmk-config

Личный ZMK firmware config для split-клавиатуры Lily58 на контроллере nice!nano v2 (nRF52840). В `build.yaml` плата называется `nice_nano@2.0.0//zmk` (HWMv2-имена Zephyr 4.1).

## Tech Stack

- **ZMK Firmware** (upstream `zmkfirmware/zmk`, branch `main` через `config/west.yml`)
- **Zephyr RTOS** (через ZMK)
- **Devicetree** (`.keymap`, `.conf`, `.dtsi`, `.overlay`) — описание layout, behaviors, sensors
- **GitHub Actions** — сборка прошивки (`.github/workflows/build.yml`)

## Dev Environment

Локальной сборки нет. Все билды — на GitHub Actions через push в master.

```bash
# Проверить статус последних билдов
gh run list --workflow=build.yml --limit 5

# Посмотреть лог конкретного билда
gh run view <run-id> --log

# Скачать артефакт firmware (.uf2 файлы)
gh run download <run-id>
```

Прошивка nice_nano_v2: двойной RESET → mount `NICENANO` → drag `.uf2`.

## Architecture

```
config/
  lily58.keymap     — слои, bindings, custom behavior
  lily58.conf       — Kconfig флаги (OLED-дисплей, заряд батареи в процентах)
  west.yml          — manifest, upstream ZMK `main`
boards/shields/     — кастомные shields (пусто — используется upstream lily58)
build.yaml          — матрица для GitHub Actions (left/right/settings_reset + left с USB-логированием)
.github/workflows/build.yml — CI build pipeline
```

**Build matrix** (`build.yaml`):
- `nice_nano@2.0.0//zmk + lily58_left`
- `nice_nano@2.0.0//zmk + lily58_right`
- `nice_nano@2.0.0//zmk + settings_reset` (для очистки BLE-пейринга)
- `nice_nano@2.0.0//zmk + lily58_left` со snippet `zmk-usb-logging` → артефакт `lily58_left_logging`

**Keymap слои** (см. `config/lily58.keymap`):
- L0 — qwerty_code_macos (Cmd/Opt mapping)
- L1 — qwerty_code_linux
- L2 — navigation_layer
- L3 — f_layer (F-клавиши)

Custom behavior: `kpPRESS_or_toHOLD` — hold-tap: tap = `&kp`, hold = `&to` (переключение слоя), `tapping-term-ms = 1000`.

## Code Style

- Devicetree: 4-space indent, выравнивание `&kp` колонок для читаемости keymap-grid
- Комментарии в keymap: ASCII-схема раскладки над `bindings`
- Имена custom behaviors: `camelCase_with_UNDERSCORES` (как в ZMK examples)

## Git

Каждое изменение раскладки = новый коммит на `master` (нет fork-flow для личного config). Conventional Commits — последние коммиты так и идут; старые в свободном формате (`update_2025-04-26_v4`).

После push — следить за CI и скачивать артефакты.

## Testing

Тестов нет. Верификация:
1. `gh run list --workflow=build.yml --limit 5` — все билды зелёные
2. Прошить обе половинки + settings_reset
3. Проверить раскладку физически на клавиатуре

## Human-only paths

Файлы, которые правит только человек: `specs/human-only-paths.md`.

## Resources

- ZMK docs: https://zmk.dev/docs
- Keymap editor: https://nickcoutsos.github.io/keymap-editor/
- Devicetree bindings: https://zmk.dev/docs/keymaps
- Lily58 shield (upstream): `zmk/app/boards/shields/lily58/`
