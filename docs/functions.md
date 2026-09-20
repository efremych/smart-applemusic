# Библиотека функций

Все функции вызываются из любого файла проекта (общая глобальная область Apps Script).

## Унификация треков

### `amTrackInfo_(resource)`

Приводит любой трек-ресурс Apple к плоскому виду:

```js
{ artist: 'ABBA', title: 'The Winner Takes It All',
  album: 'Super Trouper', appleId: '1422648844', durationMs: 295000 }
```

## Apple Music: каталог (только developer token)

### `amSearchSongs(term, limit)`

Поиск песен в каталоге `tr`.

```js
const found = amSearchSongs('abba winner', 5);
Logger.log(found[0].id + ' — ' + found[0].attributes.name);
```

### `amCharts(genre, limit)`

Топ песен. `genre` — id жанра Apple (`'14'` поп, `'20'` альтернатива…), без него — общий чарт.

```js
const top = amCharts('20', 50).map(amTrackInfo_);
```

### `amCatalogPlaylistTracks(playlistId)`

Треки редакционного плейлиста целиком (с пагинацией).

```js
const tracks = amCatalogPlaylistTracks('pl.f4d106fed2bd41149aaacabb0eb5b324');
```

## Apple Music: моя библиотека (нужен user token)

### `amMyPlaylists()`

Все мои плейлисты (с пагинацией).

```js
const names = amMyPlaylists().map(p => p.attributes.name);
```

### `amMyPlaylistTracks(playlistId)`

Треки моего плейлиста целиком.

```js
const tracks = amMyPlaylistTracks('p.06aW30xsVmdDQJZ').map(amTrackInfo_);
```

### `amMyLibrarySongs()`

Все песни библиотеки. На больших библиотеках — долго.

### `amRecentPlayed(limit)`

Недавно сыгранное, новые первыми, максимум ~30. Без точных времён.

### `amRecommendations()`

Персональные рекомендации. На холодном профиле может вернуть пусто — это норма.

## Apple Music: запись

### `amCreatePlaylist(name, description)` → `id`

```js
const id = amCreatePlaylist('Новинки 20.09', 'собрано скриптом');
```

### `amAddTracks(playlistId, catalogIds)`

Добавление по каталожным id. **Удаления не существует** — фильтруйте до добавления.

```js
amAddTracks(id, ['1422648844', '1440857781']);
```

### `amCreateFolder(name)` → `id`

Папка плейлистов.

## Last.fm

### `lfmGetLovedTracks(limit, page)`

Любимые треки (сердечки).

```js
const loved = lfmGetLovedTracks(50).map(t => ({ artist: t.artist.name, title: t.name }));
```

### `lfmGetRecentTracks(limit)`

Недавние скробблы, новые первыми.

### `lfmGetTopTracks(period, limit)`

Топ за период: `overall`, `7day`, `1month`, `3month`, `6month`, `12month`.

```js
const top = lfmGetTopTracks('7day', 25);
```

### `lfmScrobbleBatch(tracks)`

Пакетный скробблинг (до 50). `tracks`: `{artist, title, album?, timestamp}`.

### `lfmNowPlaying(track)`

«Сейчас играет» на профиле.

## History (накопительная история)

### `setupProjectStorage()`

Одноразовая установка: папка `SmartAppleMusic` на Drive + таблица `History`. Идемпотентна.

### `testHistory()`

Показывает ссылку на таблицу и число накопленных строк.

### `historyAppendTracks_(tracks)`

Внутренняя. Дозапись в конец таблицы; новизну гарантирует вызывающий (точка отсечения скробблера).

## Транспорт (низкий уровень)

### `apiGet(path, params)` / `apiPost(path, body)`

Сырые вызовы Apple API, если доменной функции не хватает. Ретраи 429/500 встроены.

### `lfmCall_(method, params, mode)`

Сырой вызов Last.fm. `mode`: `'open'` | `'signed'` | `'session'`.
