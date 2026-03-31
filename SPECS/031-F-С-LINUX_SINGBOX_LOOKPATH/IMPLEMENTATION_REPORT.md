# Отчёт: 031-F-С-LINUX_SINGBOX_LOOKPATH

**Статус:** реализовано.

## Поведение

- **Linux:** при старте `FileService` задаёт `SingboxBundledPath` = `<ExecDir>/bin/sing-box`, `SingboxPath` = результат `exec.LookPath("sing-box")` при успехе и валидном файле (не каталог), иначе bundled.
- **Windows / macOS:** `ResolveSingboxExecPath` возвращает только bundled-путь (без изменений логики).
- **Core → Download:** `installBinary` пишет в `SingboxBundledPath`, системный бинарник из `PATH` не перезаписывается.

## Файлы

| Файл | Изменение |
|------|-----------|
| `internal/platform/singbox_exec_path.go` | `!linux`: resolve = bundled |
| `internal/platform/singbox_exec_path_linux.go` | `linux`: LookPath + Stat, debuglog |
| `core/services/file_service.go` | `SingboxBundledPath`, `SingboxPath` через `ResolveSingboxExecPath` |
| `core/core_downloader.go` | установка в `SingboxBundledPath` |
| `core/core_version.go` | `GetCoreBinaryPath`: относительный путь под деревом лаунчера, иначе полный |
| `docs/ARCHITECTURE.md`, `docs/BUILD_LINUX.md`, `README.md`, `README_RU.md`, `docs/release_notes/upcoming.md` | описание для пользователя |

## Проверки

- `go build ./...`, `go test ./...`, `go vet ./...` — выполнены локально при сдаче.
