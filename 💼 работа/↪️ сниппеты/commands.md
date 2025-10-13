
- `o3 pg connect pgpass` - для авторизации

- `o3 pg connect print --role master --database express-couriers-delivery-route-api --environment stg --format jdbc` - получение jdbc
``` txt
jdbc:postgresql://ikoronatov@master.express-couriers-delivery-route-api.stg.pg.db.o3.ru:6532/express-couriers-delivery-route-api
```

- `o3 pg connect list-permissions -e stg` - показать права
 - `scratch update --confirm` обновить scratch


---
