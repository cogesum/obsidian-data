- [x] удалить тмп файл modules/dumprepo/local_repo_downloader.go:142
- [x] логи (не забыть повесить тег) закончить
- [x] сделать общий поиск по таскам дампов удалений и копирования
- [x] переделать options для тасков, унести их в api и оттуда доставать для task payload
- [x] добавить status for repodump для удаления, чтобы был недоступен
- [x] добавить корректное описание для таски

- [x] flow
- [x] flow
- [x] flow
- [ ] flow



run github.com/go-swagger/go-swagger/cmd/swagger@v0.31 generate spec -x "gitflame.ru/sdk" -o './templates/swagger/v1_json.tmpl' --tags validation,delayrepodelete,dumprepo,vault