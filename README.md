# Документация по REST API и MQTT Vakio и интеграциям с умными домами

- MQTT API
  - [Подключение прибора](#connect)
  - [Управление VakioBaseSmart Rev2 (ESP32)](#basesmart)
    - [+/system](#basesystem)
    - [+/mode](#basemode)
    - [+/state](#basestate)
    - [+/workmode](#baseworkmode)
    - [+/speed](#basespeed)
  - [Управление VakioOpenair Rev2 (ESP32)](#openair)
    - [device/+/openair/system | server/+/openair/system](#openairsystem)
    - [device/+/openair/mode | server/+/openair/mode](#openairmode)
    - [+/state](#openairstate)
    - [+/workmode](#openairworkmode)
    - [+/gate](#openairgate)
    - [+/speed](#openairspeed)
    - [+/temp](#openairtemp)
    - [+/hud](#openairhud)
  - [Управление VakioAtmosphere](#atmosphere)
    - [+/system](#atmospheresystem)
    - [+/temp](#atmospheretemp)
    - [+/co2](#atmosphereco2)
    - [+/hud](#atmospherehud)
  - [Управление VakioCityAir](#cityair)
    - [+/+/lwt](#cityair_lwt)
    - [+/+/system](#cityair_system)
    - [+/+/log](#cityair_log)
    - [+/+/state](#cityair_state)
    - [+/+/ten_state](#cityair_ten_state)
    - [+/+/damper_state](#cityair_damper_state)
    - [+/+/target_temp](#cityair_target_temp)
    - [+/+/target_speed](#cityair_target_speed)
    - [+/+/speed](#cityair_speed)
    - [+/+/in_temp](#cityair_in_temp)
    - [+/+/out_temp](#cityair_out_temp)
    - [+/+/damper](#cityair_damper)
    - [+/+/filter](#cityair_filter)
    - [+/+/odometer](#cityair_odometer)
    - [+/+/error](#cityair_error)
    - [+/+/reset_error](#cityair_reset_error)
    - [+/+/reset_filter](#cityair_reset_filter)
    - [+/+/update](#cityair_update)
  - [Управление VakioVector](#vector)
    - [device/+/vector/mode](#vector)
    - [device/+/vector/mode](#vector)
- REST API
  - [Регистрация](#register)
  - [Получение данных о приборах](#info)
  - [Управление VakioBaseSmart](#restbasesmart)
  - [Управление VakioOpenAir](#restopenair)
  - [Управление VakioVector](rest/vector.md)
- Интеграции с системами умных домов
  - Home Assistant
    - <a target="_blanc" href="https://github.com/vakio-ru/vakio_base_smart">VAKIO Smart Series</a>
    - <a target="_blanc" href="https://github.com/vakio-ru/vakio_openair">VAKIO Openair</a>
    - <a target="_blanc" href="https://github.com/vakio-ru/vakio_atmosphere">VAKIO Atmosphere</a>
    - <a target="_blanc" href="https://github.com/vakio-ru/vakio_kiv">VAKIO Kiv Smart</a>
  - MajorDOMO
    - <a target="_blanc" href="https://github.com/vakio-ru/vakio_smart_control">Smart-устройства</a>
  - Sprut.hub (от партнеров)
    - <a target="_blanc" href="https://comf.life/kak-dobavit-rekuperator-vakio-v-umnyj-dom-wirenboard-yandeks-alisu-apple-home-spruthub.html">VAKIO Smart Series + Wirenboard</a>

---

### <a name="connect"></a> Подключение прибора

Подключение к локальному серверу c помощью MQTT для интеграции приборов Vakio c системами умного дома<br>
<a target="_blanc" href="https://vakio.ru/vakio-mqtt.pdf">Инструкция по подключению приборов по MQTT</a>.

---

### <a name="basesmart"></a> Управление VakioBaseSmart

Команды для актуальной версии **(1.2.1)**, если они недоступны, обновите прибор

"+" - Ваш топик по умолчанию (vakio/system)

```
  topic: +/system
  message: 0609
```

#### <a name="basesystem"></a> Топик +/system

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

#### <a name="basemode"></a> Топик +/mode

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

#### <a name="basestate"></a> Топик +/state

"+" - Ваш топик по умолчанию (vakio/state)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление состояние прибора (Вкл/Выкл)

Команда

```
on - Включить
0ff - Выключить
```

#### <a name="baseworkmode"></a> Топик +/workmode

"+" - Ваш топик по умолчанию (vakio/workmode)

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

#### <a name="basespeed"></a> Топик +/speed

"+" - Ваш топик по умолчанию (vakio/speed)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление скоростью прибора

Команда

```
1-7 (Номер скорости)
```

---

### <a name="openair"></a> Управление Vakioopenair Rev2

Команды для актуальной версии **(1.1.0)**, если они недоступны, обновите прибор

**C версии 1.1.0 openair поддерживает команды в json с массивами, так и в формате объектов**

"+" - Ваш топик по умолчанию (vakio/system)

```
Топик: server/+/openair/system
```

```json
{
  "firmware": [
    {
      "domain": "service.vakio.ru"
    },
    {
      "start": 1
    }
  ]
}
```

#### <a name="openairsystem"></a> device/+/openair/system | server/+/openair/system

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
    "shutdown": 1 // 1 - Состояние переохлаждения | 0 - нормальное состояние
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
  "shutdown": {
    "limit": 0 // Значение температуры при котором войдет в состояние переохлаждения
  }
}
```

_Сброс настроек_

```jsonc
{
  "reset": [
    {
      "wireless": "reset" // Сброс настроек подключения
    },
    {
      "device": "reset" // Сброс параметров режима работы
    },
    {
      "all": "reset" // Полный сброс настроек
    }
  ]
}
```

_Обновление прошивки_

```json
{
  "firmware": [
    {
      "domain": "service.vakio.ru"
    },
    {
      "start": 1
    }
  ]
}
```

Актальная версия прибора (с 1.1.0)

```json
{
  "firmware": {
    "domain": "service.vakio.ru",
    "start": 1
  }
}
```

#### <a name="openairmode"></a> device/+/openair/mode | server/+/openair/mode

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
    "temperature_speed": [20, 5], // Настройка умного режима от ВНУТРЕННОГО датчика, 1 - темпераутра, 2 - скорость
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
    { "speed": 2 },
    { "gate": 4 },
    { "on_off": "on" },
    { "mode": "manual" } // "super_auto"
  ]
}
```

Актальная версия прибора (с 1.1.0)

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
    { "gate": 1 }, // 1-4 Положение заслонки в SMART режиме
    { "smart_speed": 1 }, // 1-5 Скорость в SMART режиме
    { "emerg_shunt": 5 } // Темперура отключения прибора
  ]
}
```

Актальная версия прибора (с 1.1.0)

```jsonc
{
  "settings": {
    "gate": 1, // 1-4 Положение заслонки в SMART режиме
    "smart_speed": 1, // 1-5 Скорость в SMART режиме
    "emerg_shunt": 5 // Темперура отключения прибора
  }
}
```

#### <a name="openairstate"></a> Топик +/state

"+" - Ваш топик по умолчанию (vakio/state)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление состояние прибора (Вкл/Выкл)

Команда

```
on - Включить
0ff - Выключить
```

#### <a name="openairworkmode"></a> Топик +/workmode

"+" - Ваш топик по умолчанию (vakio/workmode)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление режимом прибора

Команда

```
manual - Ручной режим
super_auto - SMART режим
```

#### <a name="openairspeed"></a> Топик +/speed

"+" - Ваш топик по умолчанию (vakio/speed)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление скоростью прибора

Команда

```
0-5 (Номер скорости)
```

#### <a name="openairgate"></a> Топик +/gate

"+" - Ваш топик по умолчанию (vakio/gate)

##### Команды прибора PUBLISH/SUBSCRIBE

Управление заслонкой прибора

Команда

```
1-4 (Позиция заслонки)
```

#### <a name="openairtemp"></a> Топик +/temp

"+" - Ваш топик по умолчанию (vakio/temp)

##### Команды прибора PUBLISH

Показания внутренного датчика температуры

```
Пример:
20
```

#### <a name="openairhud"></a> Топик +/hud

"+" - Ваш топик по умолчанию (vakio/hud)

##### Команды прибора PUBLISH

Показания внутренного датчика влажности

```
Пример:
33
```

---

### <a name="atmosphere"></a> Управление VakioAtmosphere

Команды для актуальной версии **(1.0.2)**, если они недоступны, обновите прибор

"+" - Ваш топик по умолчанию (vakio/system)

```
  topic: +/system
  message: 0709
```

#### <a name="atmospheresystem"></a> Топик +/system

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
0 - ручная настройка яркости подсветки
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

#### <a name="atmospheretemp"></a> Топик +/temp

"+" - Ваш топик по умолчанию (vakio/temp)

##### Команды прибора PUBLISH

Показания внутренного датчика температуры

```
Пример:
20
```

#### <a name="atmospherehud"></a> Топик +/hud

"+" - Ваш топик по умолчанию (vakio/hud)

##### Команды прибора PUBLISH

Показания внутренного датчика влажности

```
Пример:
33
```

#### <a name="atmosphereco2"></a> Топик +/co2

"+" - Ваш топик по умолчанию (vakio/hud)

##### Команды прибора PUBLISH

Показания внутренного датчика CO2

```
Пример:
1000
```

### <a name="cityair"></a> Управление VakioCityair

Команды для актуальной версии, если они недоступны, обновите прибор.<br>
Для данного прибора формирование топиков происходит по следующей схеме:<br>
+/+/..<br>
"+" - последние 2 байта мас адреса устройства<br>
"+" - ваш топик (по умолчанию cityair)<br>
.. - изменяемая часть

#### <a name="cityair_lwt"></a> Топик +/+/lwt

При отключении прибора от серверу происходит публикация сообщения "Offline"

#### <a name="cityair_system"></a> Топик на подписку +/+/system

При получении прибором команды "GET" на сервер отправляются все настройки устройства

#### <a name="cityair_log"></a> Топик на публикацию +/+/log

При каждом подключении прибора к серверу происходит публикация сообщения
версии прибора, mac адреса прибора, ip прибора

#### <a name="cityair_state"></a> Топик на подписку +/+/state/set<br>Топик на публикацию +/+/state

Команды вкл/выкл прибора: "on"/"off"

#### <a name="cityair_ten_state"></a> Топик на подписку +/+/ten_state/set<br>Топик на публикацию +/+/ten_state

Команды вкл/выкл тена прибора: "on"/"off"

#### <a name="cityair_damper_state"></a> Топик на подписку +/+/damper_state/set<br>Топик на публикацию +/+/damper_state

Команды использования/не использования заслонки: "on"/"off"

#### <a name="cityair_target_temp"></a> Топик на подписку +/+/target_temp/set<br>Топик на публикацию +/+/target_temp

Команды целевой температуры тена: 10..25 (в градусах)

#### <a name="cityair_target_speed"></a> Топик на подписку +/+/target_speed/set<br>Топик на публикацию +/+/target_speed

Команды целевой скорости вентилятора: 1..7

#### <a name="cityair_speed"></a> Топик на публикацию +/+/speed

Команды скорости вентилятора: 0..100 (в процентах)

#### <a name="cityair_in_temp"></a> Топик на публикацию +/+/in_temp

Команды температуры датчика температуры на входе: -55..+125 (в градусах)

#### <a name="cityair_out_temp"></a> Топик на публикацию +/+/out_temp

Команды температуры датчика температуры на выходе: -55..+125 (в градусах)

#### <a name="cityair_damper"></a> Топик на публикацию +/+/damper

Команды положения заслонки: "closed", "opened", "opens", "closes"

#### <a name="cityair_filter"></a> Топик на публикацию +/+/filter

Команды фильтра наработки моточасов (в часах)

#### <a name="cityair_odometer"></a> Топик на публикацию +/+/odometer

Команды фильтра наработки часов (в часах)

#### <a name="cityair_error"></a> Топик на публикацию +/+/error

Команды ошибок прибора: "temp_hot", "temp_cold", "stop_hot", "stop_cold", "ds18_bus", "ds18_lack", "no"

#### <a name="cityair_reset_error"></a> Топик на подписку +/+/reset_error

Команда сброса ошибок прибора: любое значение

#### <a name="cityair_reset_filter"></a> Топик на подписку +/+/reset_filter

Команда сброса фильтра: любое значение

#### <a name="cityair_update"></a> Топик на подписку +/+/update

Команда обновления прошивки прибора: любое значение

---

### <a name="vector"></a> Управление VakioVector

Команды актуальны для версии **(0.1.8)** и выше.

Топики:  
device/+/vector/mode - отправка команд от прибора на сервер  
server/+/vector/mode - отправка команд от сервера на прибор

"+" - Ваш топик по умолчанию (vakio)

При подключении прибор отправляет два json объекта с настройками capabilities, settings.

##### Команды прибора capabilities

Состояние прибора включить/выключить (on/off) (прием от сервера, отправка на сервер)  
```
Пример:
{"capabilities": {"on_off": "on"}}
```

Скорость вентилятора (1-7) (прием от сервера, отправка на сервер)  
```
Пример:
{"capabilities": {"speed": 3}}
```

Подогрев выходящего воздуха, в градусах цельсия (5-30) (прием от сервера, отправка на сервер)  
```
Пример:
{"capabilities": {"heat": 14}}
```

Режим работы, smart доступен только при настройке через приложение (manual/smart) (прием от сервера, отправка на сервер)  
```
Пример:
{"capabilities": {"mode": "manual"}}
```

Режим продувки вкл/выкл (on/off) (прием от сервера, отправка на сервер)  
```
Пример:
{"capabilities": {"blow": "on"}}
```

Состояние заслонки (0-3) (0 - открытие, 1 - открыта, 2 - закрывается, 3 - закрыта) (отправка на сервер)  
```
Пример:
{"capabilities": {"stepper": 2}}
```

##### Команды прибора settings

Яркость экрана, в процентах (5-100) (прием от сервера, отправка на сервер)  
```
Пример:
{"settings": {"disp_bright": 37}}
```

Режим автоматической яркости экрана вкл/выкл (on/off) (прием от сервера, отправка на сервер)  
```
Пример:
{"settings": {"disp_mode": "off"}}
```

Поворот экрана (0-3) (0 - портретный, 1 - портретный инвертированный, 2 - горизонтальный,  
3 - горизонтальный инвертированный) (прием от сервера, отправка на сервер)  
```
Пример:
{"settings": {"disp_rotate": 2}}
```

Включение экрана при приеме команд от сервера вкл/выкл (on/off) (прием от сервера, отправка на сервер)  
```
Пример:
{"settings": {"disp_wakeup": "on"}}
```

Постоянно включенный экран вкл/выкл (on/off) (прием от сервера, отправка на сервер)  
```
Пример:
{"settings": {"always_on": "off"}}
```

Работа нагревателя вкл/выкл (on/off) (прием от сервера, отправка на сервер)  
```
Пример:
{"settings": {"heater_state": "on"}}
```

Время продувки, в минутах (1-10) (прием от сервера, отправка на сервер)  
```
Пример:
{"settings": {"blow_time": 6}}
```

Наличие датчика co2 (on/off) (отправка на сервер)  
```
Пример:
{"settings": {"sens_co2": "off"}}
```

Разные команды от одного объекта можно отправлять вместе  
```
Пример:
{"settings": {"speed": 2, "heat": 11}}
```

---

## REST API ()

Открытый API для интеграции приборов Vakio c системами умного дома (для работы прибору необходим доступ к интернету)

### <a name="register"></a> Регистрация

1. Загрузите приложение Vakio Smart Control с App Store или Google Play.
2. Зарегистрируйтесь и подтвердите Email.
3. Отправьте письмо на почту <developer@vakio.ru> с пометкой "Регистрация индивидуального API", в тексте укажите Email, имя и номер телeфона, которые относятся к этому аккаунту.
4. Мы вышлем вам данные для авторизации.

### Получение токена для авторизации с помощью пароля

_Адрес_

```
POST https://api.vakio.ru/oauth/token
```

_Заголовки_

```
'Content-Type': 'application/json'
```

_Тело_

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

_Адрес_

```
POST https://api.vakio.ru/oauth/token
```

_Заголовки_

```
'Content-Type': 'application/json'
```

_Тело_

```json
{
  "client_id": "<client_id>",
  "client_secret": "<client_secret>",
  "grant_type": "refresh_token",
  "refresh_token": "<you refresh_token>"
}
```

_Успешный ответ_

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

### <a name="info"></a> Получение данных о приборах

### Получение данных обо всех устройствах пользователя

_Адрес_

```
GET https://api.vakio.ru/devices
```

_Заголовки_

```
'Content-Type': 'application/json'
'Authorization': 'Bearer <token>'
```

_Успешный ответ_

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
            "properties": {
                *// Для устройств с датчиками (Atmosphere, Openair и др.)*
                "humidity": 24,
                "temperature": 345
            },
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

_Адрес_

```
POST https://api.vakio.ru/devices/{DEVICE_ID}
```

_Заголовки_

```
'Content-Type': 'application/json',
'Authorization': 'Bearer <token>',
```

_Успешный ответ_

```
см. Получение данных обо всех устройствах пользователя
```

### <a name="restbasesmart"></a> Управление basesmart

### Смена режима/ скорости работы Base Smart

_Адрес_

```
PUT https://api.vakio.ru/devices/{DEVICE_ID}
```

_Заголовки_

```
'Content-Type': 'application/json',
'Authorization': 'Bearer <token>',
},
```

_Тело_

```json
{
  "capabilities": [
    {
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
    }
  ]
}
```

_Успешный ответ_

```json
{
  "code": 200,
  "content": "updated"
}
```

#### Отправка одного параметра

```json
{
  "capabilities": [
    {
      "instance": "mode",
      "value": "inflow"
    }
  ]
}
```

#### Типы данных

1. Режимы Base Smart
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
2. Скорости Base Smart -
   > 1 - 7
3. Вкл/ выкл Base Smart
   > "on"/ "off"

### <a name="restopenair"></a> Управление OpenAir

### Смена режима/положения заслонки/скорости работы OpenAir

_Адрес_

```
PUT https://api.vakio.ru/devices/{DEVICE_ID}
```

_Заголовки_

```
'Content-Type': 'application/json',
'Authorization': 'Bearer <token>',
```

_Тело_

```json
{
  "capabilities": [
    {
      "instance": "mode",
      "value": "manual"
    },
    {
      "instance": "speed",
      "value": "3"
    },
    {
      "instance": "gate",
      "value": "1"
    },
    {
      "instance": "on_off",
      "value": "on"
    }
  ]
}
```

_Успешный ответ_

```json
{
  "code": 200,
  "device": {
    // Информация об изменённом устройстве
  }
}
```

#### Отправка одного параметра

```json
{
  "capabilities": [
    {
      "instance": "mode",
      "value": "manual"
    }
  ]
}
```

#### Типы данных

1. Режимы Openair
   > "manual" - Режим ручного управления
   >
   > "smart_auto" - Smart-режим
2. Скорости Openair
   > 0 - 5
3. Положение заслонки Openair _(доступно не на всех устройствах)_
   > 1 - 4
   >
   > _\*доступно не на всех устройствах._
   >
   > _\*\*изменение положения заслонки не работает, если значение скорости больше 0._
4. Вкл/выкл Openair
   > "on"/ "off"
