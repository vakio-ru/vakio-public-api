# Документация по REST API и MQTT Vakio

- MQTT API
  - [Подключение прибора](#Connect)
  - [Управление VakioBaseSmart Rev2 (ESP32)](#BaseSmart)
    - [+/system](#BaseSystem)
    - [+/mode](#BaseMode)
    - [+/state](#BaseState)
    - [+/workmode](#BaseWorkmode)
    - [+/speed](#BaseSpeed)
  - [Управление VakioOpenair Rev2 (ESP32)](#Openair)
    - [device/+/openair/system | server/+/openair/system](#OpenairSystem)
    - [device/+/openair/mode | server/+/openair/mode](#OpenairMode)
    - [+/state](#OpenairState)
    - [+/workmode](#OpenairWorkmode)
    - [+/gate](#OpenairGate)
    - [+/speed](#OpenairSpeed)
    - [+/temp](#OpenairTemp)
    - [+/hud](#OpenairHud)
  - [Управление VakioAtmosphere](#Atmosphere)
    - [+/system](#AtmosphereSystem)
    - [+/temp](#AtmosphereTemp)
    - [+/co2](#AtmosphereCO2)
    - [+/hud](#AtmosphereHud)
- REST API
  - [Регистрация](#Register)
  - [Получение данных о приборах](#Info)
  - [Управление VakioBaseSmart](#RestBaseSmart)
---  

### <a name="Connect"></a> Подключение прибора

Подключение к локальному серверу c помощью MQTT для интеграции приборов Vakio c системами умного дома<br>
<a target="_blanc" href="https://vakio.ru/vakio-mqtt.pdf">Инструкция по подключению приборов по MQTT</a>.

---  

### <a name="BaseSmart"></a> Управление VakioBaseSmart  

Команды для актуальной версии **(1.2.1)**, если они недоступны, обновите прибор

"+" - Ваш топик по умолчанию (vakio/system)


```
  topic: +/system  
  message: 0609  
```

#### <a name="BaseSystem"></a> Топик +/system 

"+" - Ваш топик по умолчанию (vakio/system)

##### Команды прибора PUBLISH (исходящие)

_Регистрация прибора_ (Отправляется при каждом подключении)
```
0601{series:esp32,subtype:"subtype","xtal_freq":"xtal_freq"}
```  
```
060006versionmacaddress | 0600061.2.1FF:FF:FF:FF:FF
```  

##### Команды прибора SUBSCRIBE (входящие)  

_Запустить обновление прибора_
```
0609
```  
_Повтор регистрации_
```
0687
```  
Прибор отправит на +/system команды регистрации прибора и сообщение `0685`

_Сброс к заводским настройкам_
```
0608
```  

#### <a name="BaseMode"></a> Топик +/mode 

"+" - Ваш топик по умолчанию (vakio/mode)

##### Команды прибора PUBLISH/SUBSCRIBE

_Включить/Выключить прибор_
```
06000 - Выключить
06001 - Включить
```  
_Режим рекуперации_
```
06010 - Рекуперация лето
06011 - Рекуперация зима
```  
_Режим приток_
```
06021 - Приток
06022 - Приток MAX
```  
_Режим вытяжка_
```
06031 - Вытяжка
06032 - Вытяжка MAX
```  
_Режим ночной_
```
06041
```  
_Скорость_
```
0650X (X - от 1 до 7)
``` 

#### <a name="BaseState"></a> Топик +/state 

"+" - Ваш топик по умолчанию (vakio/state)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление состояние прибора (Вкл/Выкл)

Команда 
```
on - Включить
0ff - Выключить
```
#### <a name="BaseWorkmode"></a> Топик +/workmmode 

"+" - Ваш топик по умолчанию (vakio/workmmode)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление режимом прибора

Команда 
```
inflow - Приток
inflow_max - Приток MAX
recuperator - Рекуперация лето
winter - Рекуперация зима
outflow - Вытяжка
outflow_max - Вытяжка MAX
night - Ночной
```
#### <a name="BaseSpeed"></a> Топик +/speed 

"+" - Ваш топик по умолчанию (vakio/speed)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление скоростью прибора

Команда 
```
1-7 (Номер скорости)
```

--- 
### <a name="Openair"></a> Управление VakioOpenair Rev2  

Команды для актуальной версии **(1.1.0)**, если они недоступны, обновите прибор

**C версии 1.1.0 Openair поддерживает команды в json с массивами, так и в формате объектов**

"+" - Ваш топик по умолчанию (vakio/system)
```
Топик: server/+/openair/system
```

```json
  {
    "firmware":[
        {
            "server":"service.vakio.ru"
        },
        {
            "start":1
        }
    ]
  }
```

#### <a name="OpenairSystem"></a> device/+/openair/system | server/+/openair/system

"+" - Ваш топик по умолчанию (vakio/system)

##### Команды прибора PUBLISH (исходящие)

Топик для получения команд от прибора 
```
device/+/openair/system
```

_Регистрация прибора_ (Отправляется при каждом подключении)
```jsonc
{
    "type": "auth",
    "auth": {
        "device_mac": "FF:FF:FF:FF:FF",
        "version": "1.1.1"
    },
    "device_subtype": {
        "exchange_type": "json",
        "series": "esp32",
        "subtype": "tmp8015-chip",
        "xtal_freq": "40"
    }
}
```
_Ошибка переохладения_ (Отправляется при критически низкой температуры платы)

```jsonc
{
    "errors": {
        "shutdown":1 // 1 - Состояние переохлаждения | 0 - нормальное состояние
    }
}
``` 

##### Команды прибора SUBSCRIBE (входящие)  

Топик для отправки команд прибору
```
server/+/openair/system
```

_Настройка переохлаждения_
```jsonc
{
    "shutdown":{
        "limit": 0 // Значение температуры при котором войдет в состояние переохлаждения
    }
}
```  

_Сброс настроек_
```jsonc
{
    "reset":[
        {
           "wireless" : "reset" // Сброс настроек подключения 
        },
        {
           "device" : "reset" // Сброс параметров режима работы
        },
        {
           "all" : "reset" // Полный сброс настроек
        }
    ]
}
```
_Обновление прошивки_
```json
{
    "firmware":[
        {
            "server":"service.vakio.ru"
        },
        {
            "start":1
        }
    ]
}
```

Актальная версия прибора  (с 1.1.0)
```json
  {
    "firmware":{
        "server":"service.vakio.ru",
        "start":1
    }
    
  }
```

#### <a name="OpenairMode"></a> device/+/openair/mode | server/+/openair/mode

"+" - Ваш топик по умолчанию (vakio/system)

##### Команды прибора PUBLISH (исходящие)

Топик для получения команд от прибора 
```
device/+/openair/mode
```  
_Рабочий режим capabilities_ 
```jsonc
{
    "capabilities": {
        "mode": "manual", // Режим работы "manual", "super_auto"
        "on_off": "on", // Состояние прибора "on","off"
        "speed": 1, // 0-5
        "gate": 4 // 1 - 4 (4 - полностью открыт)
    }
}
```

_Настройки settings_ 
```jsonc
{
    "settings": {
        "temperature_speed": [20,5], // Настройка умного режима от ВНУТРЕННОГО датчика, 1 - темпераутра, 2 - скорость 
        "emerg_shunt": 10, // Температура при который клапан прекратит работу (Для избежания образования росы на плате)
        "gate": 4 // 1 - 4 (4 - полностью открыт) Положение заслонки в умном режиме от ВНУТРЕННОГО датчика
    }
}
```  
##### Команды прибора SUBSCRIBE (входящие)

Топик для отправки команд прибору
```
server/+/openair/mode
```  
_Управление прибором_ 
```jsonc
{
  "capabilities": [
    {"speed": 2},
    {"gate": 4},
    {"on_off":"on"},
    {"mode": "manual"} // "super_auto"
  ]
}
```

Актальная версия прибора  (с 1.1.0)
```jsonc
{
    "capabilities": {
        "mode": "manual", // Режим работы "manual", "super_auto"
        "on_off": "on", // Состояние прибора "on","off"
        "speed": 1, // 1-5
        "gate": 4 // 1 - 4 (4 - полностью открыт)
    }
}
```


_Настройка прибора_ 
```jsonc
{
    "settings": [
        {"gate": 1}, // 1-4 Положение заслонки в SMART режиме
        {"smart_speed": 1}, // 1-5 Скорость в SMART режиме
        {"emerg_shunt": 5}, // Темперура отключения прибора
    ]
}
```
Актальная версия прибора  (с 1.1.0)
```jsonc
{
    "settings": {
        "gate": 1, // 1-4 Положение заслонки в SMART режиме
        "smart_speed": 1, // 1-5 Скорость в SMART режиме
        "emerg_shunt": 5 // Темперура отключения прибора
    }
}
```
#### <a name="OpenairState"></a> Топик +/state 

"+" - Ваш топик по умолчанию (vakio/state)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление состояние прибора (Вкл/Выкл)

Команда 
```
on - Включить
0ff - Выключить
```
#### <a name="OpenairWorkmode"></a> Топик +/workmmode 

"+" - Ваш топик по умолчанию (vakio/workmmode)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление режимом прибора

Команда 
```
manual - Ручной режим
super_auto - SMART режим
```
#### <a name="OpenairSpeed"></a> Топик +/speed 

"+" - Ваш топик по умолчанию (vakio/speed)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление скоростью прибора

Команда 
```
0-5 (Номер скорости)
```
#### <a name="OpenairGate"></a> Топик +/gate 

"+" - Ваш топик по умолчанию (vakio/gate)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление заслонкой прибора

Команда 
```
1-4 (Позиция заслонки)
```
#### <a name="OpenairTemp"></a> Топик +/temp 

"+" - Ваш топик по умолчанию (vakio/temp)

##### Команды прибора PUBLISH

Показания внутренного датчика температуры 


```
Пример:
20 
```
#### <a name="OpenairHud"></a> Топик +/hud 

"+" - Ваш топик по умолчанию (vakio/hud)

##### Команды прибора PUBLISH

Показания внутренного датчика влажности 


```
Пример:
33 
```

---

### <a name="Atmosphere"></a> Управление VakioAtmosphere  

Команды для актуальной версии **(1.0.2)**, если они недоступны, обновите прибор

"+" - Ваш топик по умолчанию (vakio/system)


```
  topic: +/system  
  message: 0709  
```

#### <a name="AtmosphereSystem"></a> Топик +/system 

"+" - Ваш топик по умолчанию (vakio/system)

##### Команды прибора PUBLISH (исходящие)

_Регистрация прибора_ (Отправляется при каждом подключении)
```
0701{"series":"esp8266","subtype":"subtype","xtal_freq":"xtal_freq"}
```  
```
070007versionmacaddress | 0700061.2.1FF:FF:FF:FF:FF
```  

##### Команды прибора SUBSCRIBE (входящие)  

_Запустить обновление прибора_
```
0709
```  
_Вкл/Выкл светодиодов_
```
0732X (X - 0/1)
```  
_Ротация дисплея_
```
0731X (X - 0/1)
```  
_Выбор режима подсветки_
```
0727x (X - 0-1)
0 - ручная настройка яркости педсветки
1 - яркость подсветки зависит от освещенности
```  
_Яркость подсветки в ручном режиме_
```
0727x (X - 000-100) 3 символа
```  
_Сброс настроек_
```
0708
```  
_Проверка онлайна_
```
0787
```  

#### <a name="AtmosphereTemp"></a> Топик +/temp 

"+" - Ваш топик по умолчанию (vakio/temp)

##### Команды прибора PUBLISH

Показания внутренного датчика температуры 


```
Пример:
20 
```
#### <a name="AtmosphereHud"></a> Топик +/hud 

"+" - Ваш топик по умолчанию (vakio/hud)

##### Команды прибора PUBLISH

Показания внутренного датчика влажности 


```
Пример:
33 
```
#### <a name="AtmosphereCO2"></a> Топик +/co2 

"+" - Ваш топик по умолчанию (vakio/hud)

##### Команды прибора PUBLISH

Показания внутренного датчика CO2 


```
Пример:
1000 
```

---

## REST API ()
Открытый API для интеграции приборов Vakio c системами умного дома (для работы прибору необходим доступ к интернету)

### <a name="Register"></a> Регистрация

1) Загрузите приложение Vakio Smart Control с App Store или Google Play.
2) Зарегистрируйтесь и подтвердите Email.
3) Отправьте письмо на почту developer@vakio.ru с пометкой "Регистрация индивидуального API", в тексте укажите Email, имя и номер телeфона, которые относятся к этому аккаунту.
4) Мы вышлем вам данные для авторизации.

### Получение токена для авторизации с помощью пароля

*Адрес* 
```
POST https://api.vakio.ru/oauth/token
```
*Заголовки* 
```
'Content-Type': 'application/json'
```
*Тело*
```json
{
    "client_id": "<client_id>",
    "client_secret": "<client_secret>",
    "grant_type": "password",
    "username": "<you email>",
    "password": "<your password, not SHA1ed>"
}
```

### Получение Refresh токена

*Адрес* 
```
POST https://api.vakio.ru/oauth/token
```
*Заголовки* 
```
'Content-Type': 'application/json'
```
*Тело*
```json
{
    "client_id": "<client_id>",
    "client_secret": "<client_secret>",
    "grant_type": "refresh_token",
    "refresh_token": "<you refresh_token>",
}
```
*Успешный ответ*
```json
{
    "access_token": "f33e31633a2d70c29ef13adef639c36dc1445a93",
    "expires_in": 86400,
    "token_type": "Bearer",
    "scope": null,
    "refresh_token": "24bbee6297ee59d3b25e145da758cdf2b6504f39f"
}
```

---
### <a name="Info"></a> Получение данных о приборах

### Получение данных обо всех устройствах пользователя

*Адрес* 
```
GET https://api.vakio.ru/devices
```
*Заголовки* 
```
'Content-Type': 'application/json'
'Authorization': 'Bearer <token>'
```
*Успешный ответ*
```
{
    "code": 200,
    "content": [
        {
            "id": 19,
            "device_name": "Мой прибор 1",
            "device_type": {
                "name": "Vakio Plus Series",
                "slug": "vakio-window-plus",
                "image": "https://connect.vakio.ru/wp-content/uploads/vakio-window-plus.jpg",
            },
            "device_group": "кухня",
            "capabilities": {
                "on_off": "off",
                "mode": "inflow",
                "speed": "5"
            },
            "properties": [],
            "relation": {
                            "on_off_dependence":"<on/off>",
                            *// Для Atmosphere*
                            "dependence":{
                                "mode":"<inflow/outflow/recuperator>",
                                "device_id_master":"<device_id_master>",
                                "min_value":"<device_id_master>",
                                "step":"<device_id_master>",
                                "parametr":"<co2/temp/hud>"
                            }
                            *// Для Base Smart*
                            "dependence":{
                                "mode":"<sync/async>",
                                "device_id_master":"<device_id_master>",
                            }
                        },
            "verified": 1,
            "device_type_id": 2
        }
    ]
}
```
    
    
### Получение данных об устройстве пользователя

*Адрес* 
```
POST https://api.vakio.ru/devices/{DEVICE_ID}
```
*Заголовки* 
```
'Content-Type': 'application/json',
'Authorization': 'Bearer <token>',
```

*Успешный ответ*
```
см. Получение данных обо всех устройствах пользователя
```

### <a name="RestBaseSmart"></a> Управление BaseSmart

### Смена режима/ скорости работы Base Smart

*Адрес* 
```
PUT https://api.vakio.ru/devices/{DEVICE_ID}
```
*Заголовки* 
```
'Content-Type': 'application/json',
'Authorization': 'Bearer <token>',
},
```
*Тело*
```json
{
    "capabilities": [{
        "instance": "mode",
        "value": "inflow"
    },
    {
        "instance": "speed",
        "value": "3"
    },
    {
        "instance": "on_off",
        "value": "on"
    }],
}
```

*Успешный ответ*
```json
{
    "code": 200,
    "content": "updated"
}
```

#### Отправка одного параметра

```json
{
    "capabilities": [{
        "instance": "mode",
        "value": "inflow"
    }
   ]
}
```

#### Типы данных

1) Режимы Base Smart
> "inflow" - Приток
> 
> "outflow" - Вытяжка
> 
> "recuperator" - Рекуперация
> 
> "inflow_max" - Максимальный приток
> 
> "outflow_max" - Максимальная вытяжка
> 
> "night" - Ночной режим
> 
2) Скорости Base Smart - 
> 1 - 7
3) Вкл/ выкл Base Smart 
> "on"/ "off"


