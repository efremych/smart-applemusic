# Установка и настройка

## Предварительно

- Аккаунт Apple Developer Program (активная подписка)
- Apple ID с подпиской Apple Music (storefront `tr`)
- Google-аккаунт (для Apps Script)
- Аккаунт Last.fm (для скробблинга)

## 1. Ключи Apple

1. [developer.apple.com](https://developer.apple.com) → Certificates, Identifiers & Profiles → Identifiers → «+» → **Media IDs** → включить чекбокс MusicKit.
2. Keys → «+» → MusicKit → скачать `AuthKey_<KEY_ID>.p8`. **Ключ скачивается один раз, не терять.**
3. Запомнить `KEY_ID` (из имени файла) и `TEAM_ID` (меню имени справа вверху портала).

## 2. Проект Apps Script

1. [script.google.com](https://script.google.com) → «Новый проект».
2. Создать файлы `Config`, `Api`, `AppleMusic`, `Auth`, `LastFM`, `Scrobbler`, `Test` и вставить содержимое из репозитория.

## 3. Script Properties

Настройки проекта → Свойства скрипта:

| Свойство | Что вставить |
|---|---|
| `AM_DEV_TOKEN` | developer token (заполнится автоматически при первом запуске `refreshDevToken`) |
| `AM_USER_TOKEN` | music user token (через страницу авторизации) |
| `AM_KEY_ID` | 10 символов из имени `AuthKey_...p8` |
| `AM_TEAM_ID` | Team ID с портала |
| `AM_PRIVATE_KEY` | всё содержимое `.p8`, включая строки BEGIN/END |
| `STOREFRONT` | `tr` |
| `OWNER_EMAIL` | ваш Gmail (запасной адрес для уведомлений) |
| `INBOX_SECRET` | придуманная длинная случайная строка |
| `LFM_API_KEY` | API key Last.fm ([создать](https://www.last.fm/api/account/create)) |
| `LFM_SECRET` | shared secret Last.fm |
| `LFM_SESSION_KEY` | заполнится сам при авторизации Last.fm |
| `LFM_USER` | заполнится сам |
| `WEBAPP_URL` | URL веб-эндпоинта (появится на шаге 4) |

## 4. Веб-эндпоинт

1. «Начать развертывание» → «Новое развертывание» → тип «Веб-приложение» → «Запуск от имени: от моего имени» → «Доступ: все» → «Развернуть».
2. Скопировать URL (`.../exec`) в свойство `WEBAPP_URL`.
3. Проверка: открыть URL в браузере → `{"ok":true,...}`.

> **Важно:** после любого изменения кода — «Управление развертываниями» → карандаш → «Новая версия» → «Развернуть», иначе эндпоинт работает по-старому.

## 5. Хранилище (папка и таблица на Google Диске)

Запустить один раз: `setupProjectStorage` — создаст папку `SmartAppleMusic`
и таблицу `History` внутри неё, id сохранит в свойства сам
(`DRIVE_FOLDER_ID`, `HISTORY_SHEET_ID`).

## 6. Триггеры (по одному запуску каждой функции)

```
refreshDevToken          ← заполнит AM_DEV_TOKEN
installDevTokenTrigger   ← раз в 30 дней
installWatchdogTrigger   ← каждый день
installScrobblerTrigger  ← каждые 15 минут
```

## 7. Music user token

1. Открыть страницу авторизации (GitHub Pages).
2. Вставить developer token → «Войти через Apple» (Apple ID с подпиской).
3. «Отправить токен в скрипт»: URL эндпоинта + `INBOX_SECRET`.
4. Придёт письмо-подтверждение на Gmail.

## 8. Last.fm

1. `printLastfmAuthUrl` → открыть ссылку из лога → «Yes, allow access» → «Готово!».
2. Проверка: `testLastfmSession`.

## Проверки

```
testProperties        ← все свойства заданы
testDeveloperToken    ← developer token работает
testUserToken         ← user token работает, список плейлистов
testWatchdogEmail     ← письмо доходит
testLastfmSession     ← Last.fm подключён
testScrobbler         ← пробный прогон скробблера (ничего не отправляет)
```
