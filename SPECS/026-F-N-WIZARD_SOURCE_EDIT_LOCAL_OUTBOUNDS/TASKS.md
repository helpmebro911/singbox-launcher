# Задачи: Edit-окно источника, локальные auto/select, два bool на ProxySource

## Этап 1: Модель и парсер

- [ ] **`ProxySource`:** **`exclude_from_global`**, **`expose_group_tags_to_global`** — см. **SPEC.md** раздел **«Новые поля»**.
- [ ] **`ParsedNode.SourceIndex`** (или эквивалент); выставлять на всех путях в **`GenerateOutboundsFromParserConfig`**.
- [ ] Все глобальные **`ParserConfig.outbounds`**: фильтрация пула нод по **`exclude_from_global`** (тип не ограничивать); локальные — **`nodesBySource[i]`**.
- [ ] Тесты: exclude; **`expose`** + эффективный список; **`outbounds[].filters`** отсекают expose при несовпадении синтетики (**SPEC §5**); JSON **`addOutbounds`** без фильтра; сериализованный **`addOutbounds`** не меняется из‑за **`expose`**; локальные urltest/selector источника работают.
- [ ] При необходимости: **`migrator`**, версия ParserConfig.

## Этап 2: UI — Edit

- [ ] **View → Edit**, локали.
- [ ] Табы **Настройки** / **Просмотр**.
- [ ] Настройки по **SPEC** (таблица UI): префикс, auto, select, два bool, предупреждение exclude; **`expose`** всегда виден, без локальных групп — **Disabled** + tooltip; **`expose`/`exclude`** только в **`proxies[]`**, без мутации **`ParserConfig.outbounds`** в JSON (**PLAN §3**).
- [ ] Префикс только в Edit; в списке — отображение.
- [ ] Предупреждение exclude без пары auto+select — ключи локалей.

## Этап 3: Сериализация и маркеры `WIZARD:`

- [ ] Синхронизация галочек ↔ **`proxies[i].outbounds`** (**SPEC §1–§2**).
- [ ] Сохранение валидного ParserConfig JSON.

## Этап 4: Документация и закрытие

- [ ] **`docs/ParserConfig.md`** — по чеклисту **PLAN §6** (подраздел proxies, оба поля, пример, сценарии).
- [ ] **`docs/release_notes/upcoming.md`**.
- [ ] **`IMPLEMENTATION_REPORT.md`**, папка **`026-F-C-…`**.

## Проверки

- [ ] `go vet ./...`, `go build ./...`, `go test ./...` (GUI — CONSTITUTION).
- [ ] Ручная проверка: Edit, exclude, expose, превью outbounds.
