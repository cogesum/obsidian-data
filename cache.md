
/autorouting/v1/unassigned_postings
- в коде сделать фичу флаг на sql ниже и протестить в сборке




```
explain analyze

SELECT p.id,

p.posting_number,

p.posting_lozon_id,

p.warehouse_id,

p.address_enc::BYTEA AS address_enc,

p.time_slot_start_at,

p.time_slot_end_at,

p.lat_enc::BYTEA AS lat_enc,

p.lng_enc::BYTEA AS lng_enc,

p.delivery_type,

p.delivery_variant_id,

p.courier_zone_name_enc::BYTEA AS courier_zone_name_enc,

p.article_box_tare_id,

COUNT(p.id) OVER () AS total_points,

p.delivery_name_enc,

p.delivery_phone_enc,

p.state,

p.created_at,

p.united_delivery_type,

p.labels,

p.scan_it

FROM point AS p

LEFT JOIN route_point_v2 AS rp ON (rp.point_id = p.id)

WHERE (

p.warehouse_id = '40826'

AND p.time_slot_start_at >= '2025-04-15 21:00:00Z'

AND p.time_slot_end_at <= '2025-04-16 20:59:59.999Z'

AND p.time_slot_start_at >= '2020-01-01 00:00:00Z'

AND p.deleted_at is NULL

AND rp.point_id is null

)

LIMIT 18000

OFFSET 0;
```