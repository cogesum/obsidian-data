docs: https://wal-g.readthedocs.io/PostgreSQL/

https://habr.com/ru/articles/506610/


``` shell
# команда для запуска бекапа, передаем где путь в psql
wal-g backup-push /var/lib/postgresql/data
```

---

```shell
# просмотреть список бекапов
wal-g backup-list
```

```
name                          modified             wal_segment_backup_start

base_000000010000000000000004 2025-04-26T23:56:39Z 000000010000000000000004
```

---
| Step | Command/Action |

|---------------------|-------------------------------------------------------------------------------|

| Stop PostgreSQL | pg_ctl stop -D /var/lib/postgresql/data |

| Remove/Move Data | mv /var/lib/postgresql/data /var/lib/postgresql/data_old |

| Restore Base Backup | wal-g backup-fetch /var/lib/postgresql/data LATEST |

| Restore WALs | Set restore_command in postgresql.conf |

| Set Permissions | chown -R postgres:postgres /var/lib/postgresql/data |

| Start PostgreSQL | pg_ctl start -D /var/lib/postgresql/data |


--- 
Отдельно стоит отметить что для восстановления на тестовом окружении (всё то, что не production) – нужно использовать Read Only аккаунт в S3, чтобы случайно не перезаписать бэкапы. В случае с WAL-G нужно задать пользователю S3 следующие права в Group Policy (_Effect: Allow_): _s3:GetObject_, _s3:ListBucket_, _s3:GetBucketLocation_. И, конечно, предварительно не забыть выставить _archive_mode=off_ в файле настроек _postgresql.conf_, чтобы ваша тестовая база не захотела сбэкапиться по-тихому.




--- 
Пример настройки для s3 хранилища:
```
export WALG_STORAGE_TYPE=s3
export WALG_S3_PREFIX=s3://your-bucket/path/
export AWS_ACCESS_KEY_ID=your-access-key
export AWS_SECRET_ACCESS_KEY=your-secret-key
export PGDATA=/var/lib/postgresql/data
```

```minio
export WALG_S3_ENDPOINT=http://minio.example.com:9000
export WALG_S3_ACCESS_KEY_ID=minioadmin
export WALG_S3_SECRET_ACCESS_KEY=minioadmin
```



apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
    - name: app
      image: your-main-app
      volumeMounts:
        - name: app-data
          mountPath: /app/data

    - name: wal-g-backup
      image: ghcr.io/wal-g/wal-g:main
      env:
        - name: WALG_STORAGE_TYPE
          value: s3
        - name: WALG_S3_PREFIX
          value: s3://your-bucket/app-backups/
        - name: AWS_ACCESS_KEY_ID
          valueFrom: { secretKeyRef: { name: aws-secrets, key: access-key } }
        - name: AWS_SECRET_ACCESS_KEY
          valueFrom: { secretKeyRef: { name: aws-secrets, key: secret-key } }
      volumeMounts:
        - name: app-data
          mountPath: /app/data
      command:
        - /bin/sh
        - -c
        - |
          while true; do
            wal-g backup-push /app/data
            sleep 3600 # каждые 60 минут
          done
  volumes:
    - name: app-data
      persistentVolumeClaim:
        claimName: app-pvc