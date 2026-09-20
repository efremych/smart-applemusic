# Рецепты сценариев

Сценарии живут в файле `Scenarios` и строятся по одной схеме:
**источники → фильтры → дедупликация → плейлист-снапшот**.

## Базовый шаблон

```js
function myScenario() {
  // 1. Источники: собираем кандидатов
  let tracks = [];
  tracks = tracks.concat(amCharts('20', 100));           // чарт альтернативы
  tracks = tracks.concat(amCatalogPlaylistTracks('pl.xxx')); // редакционный плейлист

  // 2. Унификация
  let infos = tracks.map(amTrackInfo_);

  // 3. Фильтры
  infos = infos.filter(t => t.title && t.artist);

  // 4. Дедупликация внутри выборки
  infos = dedupInfos_(infos);

  // 5. Снапшот
  const id = amCreatePlaylist('Мой сценарий ' + todayStr_(), 'собрано скриптом');
  amAddTracks(id, infos.map(t => t.appleId));
  Logger.log('Готово: %d треков в плейлисте %s', infos.length, id);
}

function dedupInfos_(infos) {
  const seen = {};
  return infos.filter(t => {
    const key = (t.artist + ' — ' + t.title).toLowerCase();
    if (seen[key]) return false;
    seen[key] = true;
    return true;
  });
}

function todayStr_() {
  return Utilities.formatDate(new Date(), 'Europe/Istanbul', 'dd.MM');
}
```

## Принципы

- **Снапшоты, а не обновление.** Удалять треки API не умеет — каждый запуск создаёт новый плейлист, старый убирается свайпом в приложении.
- **Дедупликация до добавления.** Перечитывайте плейлист-архив и вычитайте уже добавленное.
- **Толерантность к пустым ответам.** Персональные эндпоинты могут вернуть пусто — сценарий не должен падать.
- **Не более ~10k треков в плейлисте** — лимит Apple.

## Идеи

| Сценарий | Источники | Фильтры |
|---|---|---|
| Новинки недели | редакционные плейлисты + чарты | не в библиотеке, не в архиве |
| Last.fm любимое в Apple | `lfmGetLovedTracks` → матчинг поиском | не в библиотеке |
| Забытое | `lfmGetTopTracks('overall')` минус `lfmGetTopTracks('6month')` | есть в каталоге `tr` |
| Рекомендации | `amRecommendations()` | минус история, минус библиотека |

> Матчинг Last.fm → Apple: `amSearchSongs(artist + ' ' + title, 1)` и берём первый результат. Промахи будут — складывайте их в лог/отчёт.
