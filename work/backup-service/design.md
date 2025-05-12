1. Dumper
В module есть dumper интерфейс, который имеет методы:
- GetRepoInfo
- GetIssues
- GetPullRequests
- GetRepoGit
И реализация, где методы GetRepoInfo, GetIssues, GetPullRequests запрашивают из БД данные и перегоняют их в yaml файлы. 
Метод GetRepoGit коннектится к gitaly и забирает оттуда бандл,
В итоге получается структура:
/repo_name/
	/backup_88248934 (unix time)
		/issue.yaml
		/pull_request.yaml
		/repo.yaml
		 /git/...  (bundle repo)
	/backup_88248980 (unix time)
		/issue.yaml
		/pull_request.yaml
		/repo.yaml
		 /git/...  (bundle repo)

Эта структура пакуется в архив и отправляется в s3 хранилище. 
Инстанс интерфейса dumper будет на уровне сервиса и логика самого вызова дампа будет реализована на уровне сервис. 
К сервису можно будет получить доступ только по endpoint /backup/:repo_id.

! Для консистентонсти взять последний коммит
! Подумать над метаданными
