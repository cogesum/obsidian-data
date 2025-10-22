# Добавление типов групп GROUP_TYPE_CLICK_BIG_OZON и GROUP_TYPE_CLICK_FRESH

## Обзор

  

Нужно различать типы кликовых групп в зависимости от типа постингов в доставке:

  

- Если все постинги БО → `GROUP_TYPE_CLICK_BIG_OZON`

- Если все постинги фреш → `GROUP_TYPE_CLICK_FRESH`

- Если смешанные → обычный `GROUP_TYPE_CLICK`

  

## Файлы для изменения

  

### 1. Добавить новые константы в Go типы

  

**Файл**: `internal/pkg/client/route_api/datastruct.go`

  

Добавить в enum `GroupType` (строка ~1006):

  

```go

const (

GroupTypeOrdinary GroupType = iota

GroupTypeAdditionalOrder

GroupTypeClick

GroupTypeClickBigOzon // новый

GroupTypeClickFresh // новый

)

```

  

### 2. Обновить логику определения типа группы

  

**Файл**: `internal/service/route_sheet/service.go`

  

В методе `EnrichUnassignedPoints` после формирования `itemsByGroup` (после строки 330), добавить логику уточнения типа для кликовых групп:

  

```go

// После строки 330, перед циклом orders := make(...)

for groupInfo, postings := range itemsByGroup {

if groupInfo.Type == route_api.GroupTypeClick {

// Подсчитываем типы кликов в группе

hasBigOzon := false

hasFresh := false

for _, posting := range postings {

switch posting.ClickType {

case route_api.ClickTypeBigOzon:

hasBigOzon = true

case route_api.ClickTypeFresh:

hasFresh = true

}

}

// Определяем конкретный тип группы

if hasBigOzon && !hasFresh {

groupInfo.Type = route_api.GroupTypeClickBigOzon

} else if hasFresh && !hasBigOzon {

groupInfo.Type = route_api.GroupTypeClickFresh

}

// Если оба типа - оставляем GroupTypeClick

// Обновляем ключ в map

delete(itemsByGroup, groupInfo)

itemsByGroup[groupInfo] = postings

}

}

```

  

**Важно**: Нужно пересоздать map entry, так как `groupInfo` используется как ключ.

  

### 3. Обновить proto конвертер

  

**Файл**: `internal/app/ozon/express/supply_chain/logistics/couriers/delivery_route_bff/v4/conversion.go`

  

Обновить функцию `GroupTypeToProto` (строка 64):

  

```go

func GroupTypeToProto(in route_api.GroupType) desc.BatchGetUnassignedPointsResponse_GroupType {

switch in {

case route_api.GroupTypeOrdinary:

return desc.BatchGetUnassignedPointsResponse_GROUP_TYPE_ORDINARY

case route_api.GroupTypeAdditionalOrder:

return desc.BatchGetUnassignedPointsResponse_GROUP_TYPE_ADDITIONAL_ORDER

case route_api.GroupTypeClick:

return desc.BatchGetUnassignedPointsResponse_GROUP_TYPE_CLICK

case route_api.GroupTypeClickBigOzon:

return desc.BatchGetUnassignedPointsResponse_GROUP_TYPE_CLICK_BIG_OZON

case route_api.GroupTypeClickFresh:

return desc.BatchGetUnassignedPointsResponse_GROUP_TYPE_CLICK_FRESH

default:

return desc.BatchGetUnassignedPointsResponse_GROUP_TYPE_UNKNOWN

}

}

```

  

### 4. Обновить proto enum (пользователь добавит сам)

  

**Файл**: `api/delivery-route-bff/v4/delivery-route-bff.proto`

  

```proto

enum GroupType {

GROUP_TYPE_UNKNOWN = 0;

GROUP_TYPE_ORDINARY = 1;

GROUP_TYPE_ADDITIONAL_ORDER = 2;

GROUP_TYPE_CLICK = 3;

GROUP_TYPE_CLICK_BIG_OZON = 4;

GROUP_TYPE_CLICK_FRESH = 5;

}

```

  

## Проверка SSE

  

SSE автоматически получит корректные типы через:

  

- `internal/kafka/consumer/handler/express_dcd_socket/event/conversion.go:179` использует `GroupTypeToProto`

- После обновления этой функции SSE будет отдавать правильные значения

  

## Тестирование

  

После изменений нужно проверить:

  

1. HTTP ручка `/v4/unassigned_points` возвращает правильные типы групп

2. SSE события содержат корректный `groupType`

3. Группы с mixed постингами (БО+фреш) имеют `GROUP_TYPE_CLICK`