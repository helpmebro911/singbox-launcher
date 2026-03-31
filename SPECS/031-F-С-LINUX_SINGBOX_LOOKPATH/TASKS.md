# TASKS: 031 — Linux sing-box через LookPath, затем `bin/`

## Этап 1 — Дизайн полей и точки использования

- [x] Пройти все вхождения `SingboxPath` / запуск `sing-box` / `GetCoreBinaryPath` и зафиксировать: где нужен **resolved** путь, где только **bundled** (установка из лаунчера).
- [x] Выбрать схему API (`ResolvedSingboxPath()` + неизменный bundled путь или два поля в `FileService`) согласно PLAN.

## Этап 2 — Реализация

- [x] Linux: при инициализации или лениво — `exec.LookPath("sing-box")`; при успехе и валидном исполняемом файле использовать как путь запуска; иначе `<ExecDir>/bin/sing-box`.
- [x] Подключить resolved-путь в `process_service`, `core_version`, `controller`, `ui_service`, визард-адаптер; **downloader** — запись только в bundled `bin/`.
- [x] Debug-лог выбранного пути (одна строка на сессию или при первом разрешении — без спама).

## Этап 3 — Проверка и документация

- [x] `go build ./...`, `go test ./...`, `go vet ./...`.
- [x] Обновить **docs/release_notes/upcoming.md**; при необходимости одну правку в пользовательской документации (README / BUILD_LINUX).
- [x] Заполнить **IMPLEMENTATION_REPORT.md**.
