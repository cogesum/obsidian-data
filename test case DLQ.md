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
