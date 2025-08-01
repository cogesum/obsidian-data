## Channels vs Mutex:
## Основные принципы выбора

Философия Go проста: **"Не общайтесь через разделяемую память, а разделяйте память через общение"**. Однако это не означает, что channels всегда лучше mutex. Правильный выбор зависит от конкретной задачи. 
#### Когда использовать Channels
-  **Передача владения данными** между горутинами
- **Распределение единиц работы** (work distribution)
- **Обмен асинхронными результатами**
- **Координация между горутинами**
```go
func processData(data <-chan int, result chan<- int) {
	for item := range data{
		result <- item * 2
	}
}
```

#### Когда использовать Mutex
- **Защиты состояния** (state protection)
- **Кеширования данных**
- **Защиты внутренних полей структур**
- **Высокопроизводительных операций** (атомарные операции быстрее)
``` go
type SafeCounter struct {
	mu sync.Mutex
	count int
}

func (c *SafeCounter)  Incr() {
	c.mu.Lock()
	defer c.mu.Unlock()

	count++
}
```

#### Сравнение производительности
Channels являются более высокоуровневыми примитивами и **имеют меньшую производительность** по сравнению с механизмами блокировки из пакета `sync`.
Mutex предпочтительнее для:
- Критических по производительности участков кода
- Простых операций защиты состояния
- Сценариев с высокой конкуренцией

### Внутреннее устройство каналов
Все каналы в Go реализованы на основе структуры `hchan` (channel header)
```go
type hchan struct {
    qcount   uint         // общее количество данных в очереди
    dataqsiz uint         // размер кольцевого буфера
    buf      unsafe.Pointer // указатель на буфер
    elemsize uint16       // размер элемента
    closed   uint32       // флаг закрытия канала
    elemtype *_type       // тип элемента
    sendx    uint         // индекс для отправки
    recvx    uint         // индекс для получения
    recvq    waitq        // очередь ожидающих получателей
    sendq    waitq        // очередь ожидающих отправителей
    lock     mutex        // мьютекс для синхронизации
}
```

#### Буферизованные vs небуферизованные каналы
**Небуферизованные каналы** (synchronous)
- Блокируют отправителя до готовности получателя
- Обеспечивают синхронную связь
- Capacity = 0, прямая передача между горутинами

**Буферизованные каналы** (asynchronous)
- Позволяют отправлять N значений без немедленного получателя
- Блокируют отправителя только при заполнении буфера
- Обеспечивают временную развязку операций
### Механизм блокировки
Каналы используют две связанные очереди для блокировки горутин
```go
type waitq struct {
    first *sudog  // первая ожидающая горутина
    last  *sudog  // последняя ожидающая горутина
}
```
Когда горутина не может выполнить операцию с каналом, она добавляется в соответствующую очередь ожидания.

## Примитивы синхронизации
### sync.Mutex

**Mutual exclusion lock** — основной примитив для взаимоисключающего доступа
```go
type SafeMap struct {
    mu    sync.Mutex
    data  map[string]int
}

func (sm *SafeMap) Get(key string) int {
    sm.mu.Lock()
    defer sm.mu.Unlock()
    return sm.data[key]
}
```
- Простая блокировка/разблокировка
- Deadlock при повторной блокировке
- Не должен копироваться после первого использования

### sync.RWMutex
**Read-Write Mutex** — оптимизированная версия mutex для сценариев с частыми чтениями.
```go
type SafeCache struct {
    mu    sync.RWMutex
    cache map[string]interface{}
}

func (sc *SafeCache) Get(key string) interface{} {
    sc.mu.RLock()         // читающая блокировка
    defer sc.mu.RUnlock()
    return sc.cache[key]
}

func (sc *SafeCache) Set(key string, value interface{}) {
    sc.mu.Lock()          // пишущая блокировка
    defer sc.mu.Unlock()
    sc.cache[key] = value
}
```

- Множественные читатели могут работать одновременно
- Только один писатель может работать в любой момент времени
- Писатель блокирует всех читателей

## Контексты (context) и их применение
#### Основные концепции

Context предоставляет стандартизированный способ передачи метаданных и управления жизненным циклом операций
```go
type Context interface {
    Deadline() (deadline time.Time, ok bool)
    Done() <-chan struct{}
    Err() error
    Value(key interface{}) interface{}
}
```
#### Типы контекстов
**Базовые контексты:**
- `context.Background()` — корневой контекст для приложения
- `context.TODO()` — placeholder для неопределенных контекстов
**Производные контексты:**
- `context.WithCancel()` — добавляет возможность отмены
- `context.WithTimeout()` — автоматическая отмена через время
- `context.WithDeadline()` — отмена в определенный момент времени
- `context.WithValue()` — передача значений между функциями
## Практические примеры

**Таймаут для HTTP-запроса:**
```go
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

req, err := http.NewRequestWithContext(ctx, "GET", "https://api.example.com", nil)
if err != nil {
    return err
}

resp, err := http.DefaultClient.Do(req)
```

**Отмена горутин:**
```go
func worker(ctx context.Context) {
    for {
        select {
        case <-ctx.Done():
            fmt.Println("Worker cancelled:", ctx.Err())
            return
        default:
            // выполнение работы
            time.Sleep(100 * time.Millisecond)
        }
    }
}
```

#### Лучшие практики

**Правила использования контекста:**
- Всегда передавайте контекст как первый аргумент функции
- Не храните контексты в структурах
- Используйте `context.TODO()` если неясно, какой контекст применить
- Всегда вызывайте `cancel()` для избежания утечек ресурсов
- Не передавайте `nil` контекст

## Атомарные операции