# Задачи: Rules Library (027)

## Этап 0: Решения

- [ ] Точка входа: **P1** (кнопка на Rules) или **P2** (вкладка Library).
- [ ] Миграция: **A** (рекомендуется) с фиксацией порядка: блок из шаблона **перед** существующими custom / иной порядок — записать в IMPLEMENTATION_REPORT.
- [ ] Политика дубликатов: skip + сообщение с числами (SPEC R4); чекбоксы «уже в списке» disabled + tooltip.

## Этап 1: Модель, клон, state

- [ ] `PersistedCustomRule` + `RuleState`: поле **`library_source_label`** (optional JSON), заполнять при добавлении из каталога и при миграции A.
- [ ] Функция глубокого клонирования пресета → custom `RuleState` (включая rule_sets, множественные rules при наличии).
- [ ] Миграция A: флаг/версия state, идемпотентность, перенос enabled/outbound из `selectable_rule_states`, очистка selectable при сохранении.
- [ ] При необходимости — обновить **WizardState** version и миграции в `wizard_state_file.go`.

## Этап 2: UI каталога

- [ ] Модальный диалог: список пресетов, чекбоксы, Cancel / Add selected, tooltips description.
- [ ] Пометка уже добавленных пресетов (disabled + tooltip).
- [ ] После Add — итоговое уведомление *Added / skipped* (locale EN).
- [ ] **Empty state** на вкладке Rules при отсутствии custom (SPEC R8).

## Этап 3: Rules tab и merge

- [ ] Убрать блок selectable из UI после миграции (или спрятать за флагом, если B).
- [ ] `MergeRouteSection`: один проход по `custom_rules`; обновить все вызовы и тесты.
- [ ] `restoreSelectableRuleStates` / сохранение: согласовать с «selectable только каталог» (не восстанавливать в модель как маршрутные, если A).

## Этап 4: Закрытие

- [ ] Ручная проверка: SRS-пресеты, preview, старый state.json.
- [ ] `go build ./...`, `go test ./...`, `go vet ./...`.
- [ ] docs + release_notes + IMPLEMENTATION_REPORT.
