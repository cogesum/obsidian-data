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

