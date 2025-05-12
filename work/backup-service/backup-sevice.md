### barman 
	+ работает в фоне как сервис
	+ может управлять несколькими инстансами psql

barman работает в двух режимах:
1. **Push-based** (база пушит WAL в Barman)
- Используется `archive_command = 'rsync %p barman@barman-host:/var/lib/barman/pg/incoming'`
- PostgreSQL сам инициирует отправку WAL'ов
- Barman "подбирает" эти архивы и складывает в свою структуру
    
 2. **Pull-based** (Barman сам забирает данные)
- Используется `pg_receivewal` или `barman receive-wal`
- Barman подключается как `replication user` к `postgres`
- Создаёт слот и **стримит WAL в real-time**
    

🟩 **Современный подход — использовать pull-based (streaming_archiver = on)**  
Он более надёжен и не зависит от ssh/rsync

- не забыть сделать юзера barman:
```sql
	CREATE USER barman WITH REPLICATION PASSWORD 'barman_password';
	GRANT CONNECT ON DATABASE postgres TO barman;
```
- конфиг файл
- настройка pg_hba.conf
- настройка postges.conf
- https://sidmid.ru/barman-%D0%BC%D0%B5%D0%BD%D0%B5%D0%B4%D0%B6%D0%B5%D1%80-%D0%B1%D1%8D%D0%BA%D0%B0%D0%BF%D0%BE%D0%B2-%D0%B4%D0%BB%D1%8F-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%BE%D0%B2-postgresql/
### grpc
- ? заворачивать в архив все, для удобства
