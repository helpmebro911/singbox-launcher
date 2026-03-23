# Задачи: Rules — custom + библиотека (027)

## Этап 1: State, засев, миграция

- [ ] Признак/версия «миграция library выполнена»; условие срабатывания миграции со старого формата.
- [ ] Миграция: блок из шаблона по порядку + старые `custom_rules`; перенос enabled/outbound из `selectable_rule_states`; очистка selectable при сохранении; идемпотентность.
- [ ] Первый засев без сохранённого state: в `custom_rules` только пресеты с **`"default": true`** в `selectable_rules`, порядок как в шаблоне.
- [ ] При необходимости — версия **WizardState** и правки в `wizard_state_file.go`.

## Этап 2: Клон и merge

- [ ] Функция глубокого копирования пресета → запись `custom_rules` (rule / rules / rule_sets, тип через **DetermineRuleType**, 018).
- [ ] `MergeRouteSection` и вызовы: после миграции только `custom_rules`.
- [ ] Убрать отдельный UI-блок selectable после миграции; согласовать restore/save selectable.

## Этап 3: UI

- [ ] Кнопка **Add from library**; модалка: скролл, чекбоксы, Cancel / **Add selected**; описание в tooltip/подстрочнике.
- [ ] **Empty state** при пустом `custom_rules` (SPEC R8).
- [ ] Строки locale (EN).

## Этап 4: Закрытие

- [ ] Ручная проверка: SRS-пресеты, preview, старый state, новый профиль без state.
- [ ] `go build ./...`, `go test ./...`, `go vet ./...`.
- [ ] docs, `docs/release_notes/upcoming.md`, **IMPLEMENTATION_REPORT.md**.
