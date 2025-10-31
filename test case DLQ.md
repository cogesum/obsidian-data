- check delivery_route_consumer_lost_postings_total (move to full model)
- check internal/app/express_postings_updates/... (move to service layer)
- включить update-posting


- проверить как создаются постинги
- тест кейс 1
	- выключить дкд
	- создать постинг
	- проверить его наличие в бд и других записей
	- запустить дкд обратно
	- следить как раскидаются сообщения в dlq

```
"SELECT \"created_at\", \"format\", \"id\", \"key\", \"next_retry_at\", \"payload\", \"retry_count\", \"topic\" FROM \"retry_queue\" WHERE ((\"topic\" = 'express_postings_updates') AND (\"next_retry_at\" <= NOW())) ORDER BY \"next_retry_at\" ASC LIMIT 400"



v5.Rows(*gitlab.ozon.ru/platform/go/database-pg/v2/internal/observability/tracing.rows) *{pgxRow: github.com/jackc/pgx/v5.Rows(gitlab.ozon.ru/platform/go/database-pg/v2/internal/pool.timeoutRows) {Rows: github.com/jackc/pgx/v5.Rows(*github.com/jackc/pgx/v5/pgxpool.poolRows) ..., cancel: context.WithDeadlineCause.func3}, span: github.com/opentracing/opentracing-go.Span(github.com/opentracing/opentracing-go.noopSpan) {}, totalSize: 0, metaSize: 65}
```



```
func (h RetryHandler) calculatePriority(msg *databus.MessageV2) int {
if h.config.Topic == "express_postings_updates" {
var cmd expresspostingsupdates.Cmd
if err := json.Unmarshal(msg.Body, &cmd); err == nil {
switch cmd.EventType {
case "POSTING_CREATED":
return 0
case "POSTING_UPDATE":
return 10
case "TIMESLOT_CHANGE":
return 20
case "STATUS_CHANGE":
return 30
}
}


ORDER BY priority ASC, next_retry_at ASC

}

return 50 // default priority

}
```


  
  
  strategy pattern?