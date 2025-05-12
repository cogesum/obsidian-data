1)
pg_ctl stop -D /var/lib/postgresql/data -m fast

pg_ctl status -D /var/lib/postgresql/data



--- 

```bash
#!/bin/bash
set -e

PGDATA=/var/lib/postgresql/data
WALG_RESTORE_TRIGGER=/tmp/trigger_restore

# Если есть триггерный файл — делаем restore
if [ -f "$WALG_RESTORE_TRIGGER" ]; then
  echo "Restore mode активирован"

  # Останавливаем Postgres (если запущен)
  pg_ctl status -D "$PGDATA" && pg_ctl stop -D "$PGDATA" || true

  # Удаляем старый PID файл
  rm -f "$PGDATA"/postmaster.pid

  # Чистим данные
  rm -rf "$PGDATA"/*
  
  # Делаем restore
  wal-g backup-fetch "$PGDATA" LATEST
  touch "$PGDATA"/recovery.signal

  # Убираем триггер
  rm -f "$WALG_RESTORE_TRIGGER"
fi

# Запускаем Postgres
exec postgres -D "$PGDATA"
```

