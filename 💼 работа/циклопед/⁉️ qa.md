#### Если mobi control сломался:
https://chatzone.o3t.ru/ozon/pl/v0040l4q41q3fbc8vcb9hldeju

Открой Терминал Вставь команду в терминале 
```
sudo killall MobiControl; cd /Library/Application\ Support/MobiControl_Resources;sudo sh MCUninstaller.sh

``` 
Нажми enter После выполнения команды - перезагрузись.

---
#### расчет кол-во сообщений
> Мне предлагали считать примерный поток сообщений, деленный на количество подов, 300 это для кафка ридера с 12 подами, для клик-сервиса с 8 должно быть больше на четверть

--- 
#### если возникла проблема с обновлением платформы o3authkafka.ListTokes ...
> сделай `go get gitlab.ozon.ru/platform/go/o3auth@v1.0.6`
   и потом `go install gitlab.ozon.ru/platform/scratch/cmd/scratch@v1.16.22 && scratch update --confirm`

https://chatzone.o3t.ru/ozon/pl/v0040miq5ldhrh9rqpvnva4ckr

---
#### Если нужно прокинуть трейсы кафки
https://chatzone.o3t.ru/ozon/pl/v00405cph0uusucgs6kfhsr6k5
