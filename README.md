# Goofy для Apple Music — страница авторизации

Публичная страница для обновления music user token. Хостится на GitHub Pages:
https://efremych.github.io/smart-applemusic/

## Как пользоваться

1. Открыть страницу (с телефона или компьютера).
2. Шаг 1: вставить developer token (берётся из Script Properties проекта Apps Script, свойство `AM_DEV_TOKEN`).
3. Шаг 2: «Войти через Apple» — авторизоваться Apple ID с подпиской Apple Music.
4. Шаг 4: «Отправить токен в скрипт» — токен уедет в Apps Script сам.
   При успехе придёт письмо-подтверждение на Gmail.

Адрес эндпоинта и секрет страница запоминает в браузере (localStorage) —
второй раз вводить не нужно.

## Что здесь НЕ хранится

Никаких секретов: ни ключей `.p8`, ни токенов, ни секрета эндпоинта.
Всё чувствительное вводится вручную и живёт либо в localStorage браузера,
либо в Script Properties проекта Apps Script. Репозиторий безопасно
держать публичным.

## Локальный запуск (запасной вариант)

```
cd E:\AppleMusic\MusicKeys
python -m http.server 8080
```
→ http://localhost:8080/music_auth.html
