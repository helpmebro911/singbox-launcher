# План: Rules Library (027)

## 1. Архитектура

1. **Каталог** — только чтение из `WizardModel.TemplateData.SelectableRules` (текущая модель пресета: label, description, rule, rule_sets, default outbound, is_default и т.д.).
2. **Точка входа (выбрать один вариант до реализации):**
   - **P1** — кнопка **Add from library** на вкладке **Rules** (рядом с **Add rule**): меньше табов, контекст очевиден.
   - **P2** — отдельная вкладка **Library** в ряду Sources / … / Rules: открывает тот же диалог или встроенный список.
3. **Диалог** — Fyne modal: прокручиваемый список строк `Check` + label; tooltip с description; внизу **Cancel** / **Add selected**. После **Add** — показ информационного сообщения или статусной строки с числом добавленных и пропущенных (R4 SPEC).
4. **Клонирование** — `CloneSelectablePresetToCustomRule(preset, userStateFromSelectable?) -> *RuleState`:
   - глубокое копирование JSON: `rule`, `rules`, `rule_sets` (и всё, что использует `MergeRouteSection` / превью);
   - выставить **`library_source_label`** = `preset.Label` (trimmed);
   - **Type** в PersistedCustomRule: **`DetermineRuleType`** от клона (или `raw`, если объект не укладывается — не вводить отдельный тип `library` в контракте 018);
   - **enabled** / **selected_outbound**: при добавлении из UI — из пресета (`IsDefault`, `DefaultOutbound`) или как сейчас у нового custom; при **миграции A** — из `PersistedSelectableRuleState`.
5. **Дубликаты** — перед вставкой: множество «занятых» ключей = все `library_source_label` в custom + для записей без поля — `trimmed Rule.Label`, совпадающий с каким-либо label пресета в шаблоне (чтобы не задвоить после миграции). Превентивно в UI: disabled checkbox + tooltip **Already in rules** для занятых пресетов.
6. **Merge** — после миграции A: `MergeRouteSection` обходит **только** `customRules`; аргумент `states` пустой или внутри функции игнорируется; вызовы (`buildRouteSection`, тесты) обновить. Удалить мёртвый код путей selectable только если нет варианта B.
7. **Миграция A (рекомендация плана):** один флаг в state или bump `version` + при первой загрузке: построить мигрированные custom, порядок: **сначала** пресеты в порядке **шаблона** с переносом enabled/outbound из `selectable_rule_states`, **затем** прежние `custom_rules` как были. Очистить `selectable_rule_states` в памяти и при следующем сохранении в JSON. Идемпотентность: повторная загрузка не дублирует блок.

## 2. Файлы (ориентир)

| Зона | Файлы |
|------|--------|
| UI | `ui/wizard/tabs/rules_tab.go`, опционально `ui/wizard/dialogs/library_rules_dialog.go`, `wizard.go` |
| Клон / дубликаты | `ui/wizard/business/` или `ui/wizard/models/` |
| State / миграция | `ui/wizard/models/wizard_state_file.go`, `presenter_state.go` |
| Конфиг | `ui/wizard/business/create_config.go` |
| Локаль | `internal/locale/en.json` |
| Документация | `docs/WIZARD_STATE.md`, `SPECS/002-F-C-WIZARD_STATE/WIZARD_STATE_JSON_SCHEMA.md` при изменении схемы, `docs/ARCHITECTURE.md` |

## 3. Решения до кодинга (чеклист)

- [ ] **P1** vs **P2** (точка входа).
- [ ] Подтвердить **A** и порядок: мигрированный блок **перед** или **после** существующих custom (SPEC рекомендует явно зафиксировать; по умолчанию плана: **шаблонный блок первым**, затем старые custom).
- [ ] Вариант **B** — только если нужен мягкий rollout; тогда описать антидублирование в merge.

## 4. Зависимости

- Подсистема типов и **DetermineRuleType** — **018**.
- Шаблон: при желании позже добавить в `wizard_template.json` стабильный **`preset_id`** (не обязателен при семантике «только label»).

## 5. Тесты

- Юнит-тесты: клон пресета (в т.ч. с `rule_sets`), детектор дубликатов, идемпотентность миграции (псевдо-state в памяти).
- Интеграция: `MergeRouteSection` только custom после флага миграции.
