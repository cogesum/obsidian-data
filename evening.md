- почитать про платформенную либу + добавить засунуть ее


```json
{
   "clients": [], // for all
   "handlers": [
   "/ozon.express.supply_chain.logistics.inbound.fresh_inbound_proxy.placement.v1.PlacementServiceV1/BatchPlaceArticle"
   ],
   "limit": 60,
   "timeout": "3s", // ?? если 0, то возвращаем сразу ошибку (пока не пойму какую)
   "type": "local",
   "is_per_client": false
}
```

https://gitlab.ozon.ru/platform/scratch/-/tree/master/pkg/mw#ratelimiter

- для конкретного batch такой вариант не подойдет, лушче старое решение, но чуть его переосмыслить
- if err := c.limiter.Wait(ctx); err != nil - можем использовать Wait с ctx.WithTimeout - чтобы аккуратно обрубать, если слишком долго висим у клиента
- через Allow() - достигли лимита, отдаем ошибку


- почитать про circut breaker и посмотреть как добавить ее