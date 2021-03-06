# Couchbase Inventory + DNS + REST API
![logo](http://www.mke.ee/media/k2/items/cache/12f9f506f45b1b21464c8409fbc6d2a2_XL.jpg)
 
## Описание
* Использование бакета Couchbase в качестве хранилища DNS-записей;

* Каждый документ бакета - отдельный хост, в описании которого помимо IP можно указать дополнительные параметры, такие как:
  * **tag** - [ ]string,
  * **apps** - [ ]string,
  * **active** - bool,
  * **params** - map[string]string;
  
* Два REST API инструмента для работы с записями бакета:
  * **Manager** - манипулирование записями с помощью методов **GET**, **POST**, **DELETE**, **UPDATE**;
  * **Search** - полнотекстовый поиск записей с использованием комбинированных запросов;
  
* Логирование с возможностью вывода в **stderr**, **stdout** и **файл**;

* Метрики Prometheus с количеством DNS и HTTP запросов.

## Установка и запуск
**1.** Склонируйте репозиторий в удобное место:
```
git clone https://github.com/eikoshelev/zavhoz.git
```
**2.** Просмотрите конфигурационный файл `config.yaml` и внесите изменения, если это необходимо;

**3.** Запустите готовый скомпилированный бинарный файл `zavhoz` либо, если были внесены изменения в существующий код, скомпилируйте новый исполняемый файл:
```
go build 
```
```
sudo ./zavhoz
```
> `sudo` может понадобиться для доступа приложения к `DNS` и `HTTP` портам, указанным в `config.yaml`

## Использование
* **Manager** - манипулирование записями бакета Couchbase:

    * **GET** - получение записи с id "host-1":
    
    ```
    curl -X GET 127.0.0.1:8059/manager/host-1
    ```
    * **POST** - добавление новой записи с указанными в теле запроса данными:
    
    ```
    curl -X POST 127.0.0.1:8059/manager/host-2 -d '{"ip":"1.2.3.4","tag":["tag1","tag2"],"apps":["foo","bar"],"active":true,"params":{"key1":"val1",,"key2":"val2"}}'
    ```
    * **DELETE** - удаление записи с id "host-3":
    
    ```
    curl -X DELETE 127.0.0.1:8059/manager/host-3
    ```
    * **UPDATE** - изменение записи. Возможно указание любых полей, неуказанные поля останутся без изменений:
    
    ```
    curl -X UPDATE 127.0.0.1:8059/manager/host-4 -d '{"ip":"0.0.0.0","active":false,"params":{"foo":"bar"}}'
    ```
    ---
* **Search** - полнотекстовый поиск записей с использованием комбинированных запросов:

    * поиск всех записей в бакете, удовлетворяющих запросу - возможно указание любых интересующих полей для поиска:
    
    ```
    curl 127.0.0.1:8059/search/ -d '{"ip":"0.0.0.0","tag":["one", "two"],"apps":["foo","bar"],"active":true,"params":{"key1":"val1","key2":"val2"}}'
    ```
    * Поиск записей id которых содержит "host" и удовлетворяющих телу запроса:
    
    ```
    curl 127.0.0.1:8059/search/host -d '{"ip":"0.0.0.0","tag":["one", "two"],"apps":["foo","bar"],"active":true,"params":{"key1":"val1","key2":"val2"}}'
    ```
## Использованные библиотеки
* [The Couchbase Go SDK](https://github.com/couchbase/gocb)
* [DNS library in Go](https://github.com/miekg/dns)
* [Prometheus instrumentation library for Go applications](https://github.com/prometheus/client_golang)
* [Structured, pluggable logging for Go](https://github.com/sirupsen/logrus)
* [YAML support for the Go language](https://github.com/go-yaml/yaml)
