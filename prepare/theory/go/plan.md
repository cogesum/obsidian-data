
повторить слайсы и мапы, что будет при чтении и добавлении в nil, пустые, не инициализированные 

## Внутреннее устройство Go runtime

**Планировщик Go (Scheduler):**
- Модель G-M-P (Goroutines-Machines-Processors)[](https://nghiant3223.github.io/2025/04/15/go-scheduler.html)[](https://www.twilio.com/en-us/blog/developers/community/memory-management-go-4-effective-approaches)
- Внутреннее устройство планировщика: локальные и глобальная очереди, work stealing[](https://habr.com/ru/articles/743266/)
- GOMAXPROCS и влияние на производительность[](https://habr.com/ru/articles/743266/)
- Preemption и управление горутинами[](https://go.dev/ref/mem)

**Память и сборщик мусора:**
- Управление памятью в Go: heap vs stack[](https://habr.com/ru/articles/804145/)
- Escape analysis и когда переменные попадают в heap[](https://borodyadka.wtf/golang-strings/)
- Алгоритмы сборки мусора и их оптимизация

**Конкурентность и синхронизация:**
- Когда использовать channels vs mutex[](http://varyous-simbir.blogspot.com/2013/01/go.html)[](https://appmaster.io/ru/blog/realizatsiia-interfeisa-pereiti)[](https://ru.stackoverflow.com/questions/1449322/golang-interface-%D0%9F%D1%80%D0%B0%D0%B2%D0%B8%D0%BB%D1%8C%D0%BD%D0%BE-%D0%BB%D0%B8-%D1%8F-%D0%B8%D1%85-%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D1%83%D1%8E)
- Внутреннее устройство каналов[](https://tour.ardanlabs.com/tour/eng/goroutines)
- Примитивы синхронизации: sync.Mutex, sync.RWMutex[](https://habr.com/ru/articles/691956/)
- Контексты (context) и их применение[](https://www.linkedin.com/pulse/message-queue-go-nodejs-duc-nguyen-lb81c)

**Структуры данных:**
- Внутреннее устройство slice, map, string[](https://go.dev/src/runtime/HACKING)[](https://github.com/leolara/conveyor)
- Интерфейсы: внутренняя структура iface, динамическая и статическая типизация[](https://github.com/aminst/message-broker)[](https://pkg.go.dev/github.com/micro/go-micro/broker)[](https://www.rabbitmq.com/)

## Практические вопросы
- Race conditions и способы их предотвращения
- Работа с defer, panic, recover
- Embedding и композиция vs наследование