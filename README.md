# Fuely Automation Templates

Файл `templates.json` — источник данных для маркетплейса шаблонов Chatfuel.
Он читается лендингом через `MARKETPLACE_TEMPLATES_URL` (GitHub raw) и применяется в дашборде через мутацию `fuelyAutomationApplyTemplate`.

---

## Структура файла

```json
{
  "templates": [ ...TemplateDetail ],
  "authors":   [ ...AuthorProfile ]
}
```

---

## TemplateDetail

| Поле | Тип | Описание |
|------|-----|----------|
| `id` | `string` | Уникальный slug. Используется в URL (`/templates/{id}`) и как ключ для выбора шаблона в дашборде. |
| `title` | `string` | Название шаблона. |
| `category` | `string` | Основная категория (одна из значений `CATEGORIES`). |
| `categories` | `string[]` | Все категории, к которым относится шаблон (включает основную). |
| `rating` | `number` | Рейтинг от 1.0 до 5.0. |
| `installs` | `number` | Количество установок. |
| `iconType` | `"cursor" \| "moon" \| "repeat" \| "chat" \| "wa"` | Тип иконки для карточки. |
| `author` | `string` | ID автора (ссылка на `authors[].id`). |
| `whatItRuns` | `string` | Краткое описание того, что запускает шаблон (показывается в сайдбаре страницы шаблона). |
| `whatClientGets` | `string` | Что получает клиент (показывается в сайдбаре). |
| `exampleOutcome` | `string` | Пример результата, например `"+34% inquiries covered"`. |
| `aboutParagraphs` | `string[]` | Параграфы описания шаблона (раздел "About"). |
| `videoUrl` | `string` | URL видео-демонстрации. Пустая строка — если нет. |
| `knowledgeBaseUrl` | `string` | Ссылка на базу знаний. Пустая строка — если нет. |
| `funnelSteps` | `string[]` | Шаги воронки (5 штук). `\n` внутри строки — перенос строки в UI. |
| `revenueMin` | `number` | Минимальная выручка агентства в год (USD) при `defaultClients` клиентах. |
| `revenueMax` | `number` | Максимальная выручка агентства в год (USD) при `defaultClients` клиентах. |
| `defaultClients` | `number` | Начальное значение слайдера "клиентов на этой воронке". |
| `maxClients` | `number` | Максимальное значение слайдера. |
| `reviews` | `TemplateReview[]` | Отзывы пользователей. |
| `fuelyAutomationTemplate` | `FuelyAutomationTemplateInput` | Конфигурация автоматизации, передаётся в мутацию `fuelyAutomationApplyTemplate`. |

### TemplateReview

| Поле | Тип | Описание |
|------|-----|----------|
| `name` | `string` | Имя рецензента. |
| `avatarIndex` | `1 \| 2` | Индекс аватара: `1` → `avatar-reviewer1.png`, `2` → `avatar-reviewer2.png`. |
| `rating` | `number` | Рейтинг отзыва. |
| `text` | `string` | Текст отзыва. |

### FuelyAutomationTemplateInput

Передаётся напрямую в GraphQL-мутацию `fuelyAutomationApplyTemplate` в дашборде.

| Поле | Тип | Описание |
|------|-----|----------|
| `baseAutomations` | `FuelyTemplateBaseAutomationInput[]` | Настройки базовых автоматизаций по scope. |
| `customAutomations` | `FuelyTemplateCustomAutomationInput[]` | Кастомные автоматизации, которые будут созданы (выключены по умолчанию). |

#### FuelyTemplateBaseAutomationInput

| Поле | Тип | Описание |
|------|-----|----------|
| `scope` | `FuelyAutomationScope` | Область автоматизации (см. список scope ниже). |
| `settings` | `FuelySettingUpdateInput[]` | Настройки, которые будут применены к базовой автоматизации этого scope. |

#### FuelyTemplateCustomAutomationInput

| Поле | Тип | Описание |
|------|-----|----------|
| `scope` | `FuelyAutomationScope` | Область автоматизации. |
| `name` | `string` | Название кастомной автоматизации (не более 200 символов). |
| `settings` | `FuelySettingUpdateInput[]` | Начальные настройки. |

#### FuelyAutomationScope (доступные значения)

- `All`
- `WhatsAppDirectMessages`
- `WhatsAppClickFromAds`
- `WhatsAppClickFromPosts`
- `InstagramDirectMessages`
- `InstagramPostComments`
- `InstagramAdComments`
- `InstagramStoryReplies`
- `InstagramIgMeLinks`
- `InstagramClickFromAds`

#### FuelySettingUpdateInput (доступные настройки)

Каждый объект в массиве `settings` содержит **одно** из следующих полей:

| Поле | Тип | Описание |
|------|-----|----------|
| `whenAIReplies` | `{ update: { option: "Always" \| "OutsideOfWorkingHours" } }` | Когда AI отвечает. |
| `incomingMessages` | `{ update: { howToReply: "UsingAI" \| "DontReply", messagePrompt: string } }` | Настройка ответов на входящие сообщения. `messagePrompt` — системный промпт для AI (до 5000 символов). |
| `followUps` | `{ update: { howToSend: "Send" \| "DontSend", messagePrompt: string } }` | Настройка follow-up сообщений. `messagePrompt` до 3000 символов. |
| `switchToHuman` | `{ update: { howToSwitch: "SwitchToTeammates" \| "DontSwitch", rules: SwitchToHumanRule[] } }` | Передача диалога человеку. |
| `messageDelays` | объект | Настройка задержек между сообщениями. |
| `collectContactInfo` | объект | Сбор контактных данных. |
| `keywords` | объект | Реакция на ключевые слова. |

#### SwitchToHumanRule

| Поле | Тип | Описание |
|------|-----|----------|
| `switchingConditions` | `string` | Условие передачи (до 3000 символов). |
| `messagePrompt` | `string` | Сообщение пользователю при передаче (до 3000 символов). |
| `assignees` | `array` (опционально) | Назначенные сотрудники. В шаблонах **не указывать** — вызовет ошибку `FuelyTemplateSwitchToHumanAssigneesNotSupported`. |

---

## AuthorProfile

| Поле | Тип | Описание |
|------|-----|----------|
| `id` | `string` | Уникальный slug автора. Совпадает с `template.author`. Используется в URL (`/templates/author/{id}`). |
| `name` | `string` | Полное имя автора. |
| `avatarUrl` | `string` | Путь к аватару. Локальные файлы: `/assets/template-installation/avatar1.png` … `avatar8.png`. |
| `aboutParagraphs` | `string[]` | Параграфы описания автора (раздел "About"). |
| `socialLinks` | `AuthorSocialLinks` | Ссылки на соцсети. |
| `reviews` | `AuthorReview[]` | Отзывы о авторе. |

### AuthorSocialLinks

Все поля опциональны. Отсутствующее поле — иконка не отображается.

| Поле | Иконка |
|------|--------|
| `whatsapp` | `icon-wa.svg` |
| `instagram` | `icon-social-instagram.svg` |
| `facebook` | `icon-social-facebook.svg` |
| `tiktok` | `icon-social-tiktok.svg` |
| `messenger` | `icon-social-messenger.svg` |
| `website` | `icon-globe.svg` |

### AuthorReview

| Поле | Тип | Описание |
|------|-----|----------|
| `name` | `string` | Имя рецензента. |
| `avatarIndex` | `1 \| 2` | Индекс аватара. |
| `rating` | `number` | Рейтинг. |
| `text` | `string` | Текст отзыва. |

---

## Категории

Допустимые значения для `category` и `categories`:

- `Beauty & aesthetics`
- `Clinics`
- `Real estate`
- `Local services`
- `Fitness coaching`
- `Education`

---

## Структура репозитория

```
index.json                     # Компактный список шаблонов + ID авторов (для листинга)
templates/
  qualify-inbound-leads.json   # Полные данные шаблона (TemplateDetail)
  never-miss-after-hours.json
  recover-quiet-leads.json
authors/
  ana-torres.json              # Полный профиль автора (AuthorProfile)
  lucas-ferreira.json
  priya-nair.json
```

### index.json

Содержит только поля, нужные для карточек (`MarketplaceTemplate`), и список ID авторов:

```json
{
  "templates": [
    { "id": "...", "title": "...", "category": "...", "rating": 4.5, "installs": 358, "iconType": "cursor", "author": "..." }
  ],
  "authorIds": ["ana-torres", "lucas-ferreira", "priya-nair"]
}
```

Детальные данные живут в отдельных файлах `templates/{id}.json` и `authors/{id}.json`.

---

## Деплой изменений

Лендинг читает данные через `MARKETPLACE_TEMPLATES_BASE_URL` (GitHub raw):

```
https://raw.githubusercontent.com/enenlingre/test-fuely-automations/main
```

Запросы строятся так:
- Листинг: `{base}/index.json`
- Детали шаблона: `{base}/templates/{id}.json`
- Профиль автора: `{base}/authors/{id}.json`

После редактирования файлов локально — закоммитить и запушить в `enenlingre/test-fuely-automations`.

### Добавить новый шаблон

1. Создать `templates/{new-id}.json` с полными данными `TemplateDetail`.
2. Добавить компактную запись в `index.json` → `templates[]`.
3. Если автор новый — создать `authors/{author-id}.json` и добавить ID в `index.json` → `authorIds`.
4. Запушить всё вместе.
