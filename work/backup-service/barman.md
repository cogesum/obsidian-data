#### config files
`/etc/barman.conf`

```
[barman]
active = true
barman_user = root
barman_home = /var/lib/barman
configuration_files_directory = /etc/barman.d
log_file = /var/log/barman/barman.log
log_level = DEBUG
compression = gzip
retention_policy = REDUNDANCY 3
immediate_checkpoint = true
last_backup_maximum_age = 4 DAYS
minimum_redundancy = 1
```

---

`/etc/barman.d/db-back.conf`

```
[db-back]
description = "Postgres DB"
conninfo = host=base-db port=5432 user=barman dbname=testdb password=passwd sslmode=disable
backup_method = postgres
archiver = off

# for wal archivinig
# streaming_conninfo = host=base-db port=5432 user=streaming_barman dbname=testdb password=passwd sslmode=disable
# streaming_archiver = on
streaming_archiver = off
# slot_name = db-back
```