# План: Linux — `LookPath("sing-box")` перед локальным `bin/`

## Подход

1. Ввести явное различие:
   - **Bundled path** — `<ExecDir>/bin/sing-box` (цель для `core_downloader`, создание каталога `bin/` как сейчас).
   - **Resolved path для запуска** — на Linux: результат `exec.LookPath("sing-box")` при успехе и существующем исполняемом файле; иначе bundled path.

2. Реализация (вариант без лишнего дублирования):
   - В **`core/services/file_service.go`**: оставить поле для bundled-пути (имя можно сохранить `SingboxPath` для обратной совместимости вызовов установщика **или** переименовать в `SingboxBundledPath` и пройти по местам использования — предпочтительно минимальный дифф: добавить поле `SingboxExecPath` / метод `ResolvedSingboxPath()` и использовать его везде, где выполняется `exec.Command`, `os.Stat` для версии, capabilities, UI «путь к ядру»).
   - Либо: при инициализации на Linux вычислить `effectivePath` и хранить в отдельном поле; `SingboxPath` оставить только для install target — **уточнить в реализации** по факту grep всех использований `SingboxPath`.

3. **Обязательно обновить** все места, где запускается или проверяется бинарник:
   - `core/process_service.go`
   - `core/core_version.go` (`GetInstalledCoreVersion`, при необходимости `GetCoreBinaryPath` для отображения реального пути)
   - `core/controller.go` (capabilities)
   - `core/uiservice/ui_service.go` (проверка существования для UI)
   - `core/core_downloader.go` — только путь **назначения** при копировании: всегда bundled `bin/`.
   - Визард: `ui/wizard/business/file_service_adapter.go` / интерфейсы — метод для пути **валидации** `sing-box check` должен совпадать с resolved путём запуска.

4. **Платформа**: при желании вынести выбор пути в `internal/platform` (например `ResolveSingboxExecPath(execDir string, bundled string) string` только для `linux` build tag), чтобы `file_service` не разрастался условиями `GOOS`.

## Риски

- Если в `PATH` старый `sing-box`, а в `bin/` лежит новый — по SPEC приоритет у **PATH**; пользователь может symlink или поправить PATH (зафиксировать в release notes).
- GUI-процесс может иметь урезанный `PATH` в некоторых окружениях (desktop file) — тогда сработает fallback на `bin/`; при полном отсутствии обоих — текущая ошибка.

## Документация

- `docs/release_notes/upcoming.md` — пункт про Linux и PATH.
- Краткое примечание в **README_RU** / **README** или **docs/BUILD_LINUX.md** (по согласованию с объёмом правок в TASKS).
