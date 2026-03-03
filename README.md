# Документация по REST API и MQTT Vakio

Документация предназначена для интеграции приборов Vakio с системами умного дома.

## Содержание

- [MQTT API](#mqtt-api)
  - [Подключение прибора](#connect)
  - [Управление Vakio Base Smart Rev2 (ESP32)](#basesmart)
  - [Управление Vakio Openair Rev2 (ESP32)](#openair)
  - [Управление Vakio Atmosphere](#atmosphere)
  - [Управление Vakio Atmosphere 2.0](#atmosphere2)
  - [Управление Vakio CityAir (250, 500)](#cityair)
  - [Управление Vakio Vector](#vector)
- [REST API](#rest-api)
  - [Регистрация](#register)
  - [Получение данных о приборах](#info)
  - [Управление Vakio Base Smart](#restbasesmart)
  - [Управление Vakio OpenAir](#restopenair)
- [Интеграции с системами умных домов](#integrations)

---

## MQTT API

### <a name="connect"></a> Подключение прибора

Подключение к локальному серверу с помощью MQTT для интеграции приборов Vakio c системами умного дома.
📎 [Инструкция по подключению приборов по MQTT](https://vakio.ru/vakio-mqtt.pdf)

---

### <a name="basesmart"></a> Управление Vakio Base Smart Rev2 (ESP32)

**Актуальная версия прошивки:** `1.2.1`

`+` — Ваш топик по умолчанию (например, `vakio`).

#### <a name="basesystem"></a> Топик `+/system`

**Команды прибора PUBLISH (исходящие)**

*Регистрация прибора (отправляется при каждом подключении):*
```json
0601{series:esp32,subtype:"subtype","xtal_freq":"xtal_freq"}
```
```text
060006versionmacaddress
// Пример:
0600061.2.1FF:FF:FF:FF:FF
```

**Команды прибора SUBSCRIBE (входящие)**

| Команда | Описание |
| :--- | :--- |
| `0609` | Запустить обновление прибора |
| `0687` | Повтор регистрации (прибор отправит команды регистрации и сообщение `0685`) |
| `0608` | Сброс к заводским настройкам |

---

#### <a name="basemode"></a> Топик `+/mode`

**Команды прибора PUBLISH/SUBSCRIBE**

| Команда | Описание |
| :--- | :--- |
| `06000` | Выключить прибор |
| `06001` | Включить прибор |
| `06010` | Рекуперация лето |
| `06011` | Рекуперация зима |
| `06021` | Приток |
| `06022` | Приток MAX |
| `06031` | Вытяжка |
| `06032` | Вытяжка MAX |
| `06041` | Ночной режим |
| `0650X` | Скорость (X — от 1 до 7) |

---

#### <a name="basestate"></a> Топик `+/state`

Управление состоянием прибора (Вкл/Выкл).

| Команда | Описание |
| :--- | :--- |
| `on` | Включить |
| `off` | Выключить |

---

#### <a name="baseworkmode"></a> Топик `+/workmode`

Управление режимом прибора.

| Команда | Описание |
| :--- | :--- |
| `inflow` | Приток |
| `inflow_max` | Приток MAX |
| `recuperator` | Рекуперация лето |
| `winter` | Рекуперация зима |
| `outflow` | Вытяжка |
| `outflow_max` | Вытяжка MAX |
| `night` | Ночной |

---

#### <a name="basespeed"></a> Топик `+/speed`

Управление скоростью прибора.

*   **Команда:** `1`-`7` (Номер скорости)

---

### <a name="openair"></a> Управление Vakio Openair Rev2 (ESP32)

**Актуальная версия прошивки:** `1.1.0`
Поддерживаются команды в JSON (массивы и объекты).

`+` — Ваш топик по умолчанию.

#### <a name="openairsystem"></a> Топик `device/+/openair/system` \| `server/+/openair/system`

**PUBLISH (от прибора):** `device/+/openair/system`

*Регистрация:*
```json
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
*Ошибка переохлаждения:*
```json
{
  "errors": {
    "shutdown": 1  // 1 - переохлаждение, 0 - норма
  }
}
```

**SUBSCRIBE (к прибору):** `server/+/openair/system`

*Настройка переохлаждения:*
```json
{
  "shutdown": {
    "limit": 0 // Температура входа в состояние переохлаждения
  }
}
```
*Сброс настроек:*
```json
{
  "reset": [
    { "wireless": "reset" },   // Сброс настроек подключения
    { "device": "reset" },     // Сброс параметров режима
    { "all": "reset" }         // Полный сброс
  ]
}
```
*Обновление прошивки (для версий < 1.1.0):*
```json
{
  "firmware": [
    { "domain": "service.vakio.ru" },
    { "start": 1 }
  ]
}
```
*Обновление прошивки (актуально, с 1.1.0):*
```json
{
  "firmware": {
    "domain": "service.vakio.ru",
    "start": 1
  }
}
```

---

#### <a name="openairmode"></a> Топик `device/+/openair/mode` \| `server/+/openair/mode`

**PUBLISH (от прибора):** `device/+/openair/mode`

*Состояние (capabilities):*
```json
{
  "capabilities": {
    "mode": "manual",   // "manual" или "super_auto"
    "on_off": "on",     // "on" или "off"
    "speed": 1,         // 0-5
    "gate": 4           // 1-4 (4 - полностью открыт)
  }
}
```
*Настройки (settings):*
```json
{
  "settings": {
    "temperature_speed": [20, 5], // Умный режим: [температура, скорость]
    "emerg_shunt": 10,            // Температура отключения клапана
    "gate": 4                     // Положение заслонки в умном режиме
  }
}
```

**SUBSCRIBE (к прибору):** `server/+/openair/mode`

*Управление (capabilities):*
```json
// Актуальный формат (с 1.1.0)
{
  "capabilities": {
    "mode": "manual",
    "on_off": "on",
    "speed": 1,
    "gate": 4
  }
}
```
*Настройка (settings):*
```json
// Актуальный формат (с 1.1.0)
{
  "settings": {
    "gate": 1,          // Позиция заслонки (1-4) в SMART режиме
    "smart_speed": 1,   // Скорость (1-5) в SMART режиме
    "emerg_shunt": 5    // Температура отключения
  }
}
```

---

#### Остальные топики Openair

| Топик | Описание | Значения |
| :--- | :--- | :--- |
| <a name="openairstate"></a>`+/state` | Состояние (Вкл/Выкл) | `on`, `off` |
| <a name="openairworkmode"></a>`+/workmode` | Режим работы | `manual`, `super_auto` |
| <a name="openairspeed"></a>`+/speed` | Скорость | `0`-`5` |
| <a name="openairgate"></a>`+/gate` | Положение заслонки | `1`-`4` |
| <a name="openairtemp"></a>`+/temp` | Температура (внутренний датчик) | Пример: `20` |
| <a name="openairhud"></a>`+/hud` | Влажность (внутренний датчик) | Пример: `33` |

---

### <a name="atmosphere"></a> Управление Vakio Atmosphere

**Актуальная версия прошивки:** `1.0.2`

#### <a name="atmospheresystem"></a> Топик `+/system`

**PUBLISH (исходящие):**
*   Регистрация: `0701{"series":"esp8266","subtype":"subtype","xtal_freq":"xtal_freq"}`
*   Версия/MAC: `070007versionmacaddress`

**SUBSCRIBE (входящие):**

| Команда | Описание |
| :--- | :--- |
| `0709` | Запустить обновление |
| `0732X` | Вкл/Выкл светодиодов (X: 0/1) |
| `0731X` | Ротация дисплея (X: 0/1) |
| `0727X` | Режим подсветки (X: 0-ручная, 1-авто) |
| `0728XXX` | Яркость подсветки (XXX: 000-100) |
| `0708` | Сброс настроек |
| `0787` | Проверка онлайна |

#### Датчики

*   <a name="atmospheretemp"></a>`+/temp` — Температура (Пример: `20`)
*   <a name="atmospherehud"></a>`+/hud` — Влажность (Пример: `33`)
*   <a name="atmosphereco2"></a>`+/co2` — CO2 (Пример: `1000`)

---

### <a name="atmosphere2"></a> Управление Vakio Atmosphere 2.0

**Актуальная версия прошивки:** `1.0.11`

| Топик | Описание | Пример значения |
| :--- | :--- | :--- |
| `+/co2` | Углекислый газ (ppm) | `402` |
| `+/hum` | Влажность (%) | `31` |
| `+/temp` | Температура (*C) | `24` |
| `+/lux` | Освещенность (lux) | `131` |
| `+/atmosphere-v2/yellow` | Установка границы желтого цвета (ppm) | `1200` |
| `+/atmosphere-v2/red` | Установка границы красного цвета (ppm) | `1800` |
| `+/atmosphere-v2/bright_disp` | Установка яркости экрана (0-100) | `84` |
| `+/atmosphere-v2/bright_strip` | Установка яркости подсветки (0-100) | `10` |
| `+/atmosphere-v2/auto_bright` | Установка режима подсветки (0-ручной, 1-авто) | `1` |

---

### <a name="cityair"></a> Управление Vakio CityAir (250, 500)

Схема топиков: `+/+/..`
Первый `+` — последние 2 байта MAC адреса.
Второй `+` — ваш топик (по умолчанию `cityair`).

#### Состояние и настройки

| Топик | Описание | Значения |
| :--- | :--- | :--- |
| <a name="cityair_lwt"></a>`+/+/lwt` | Статус LWT (только 250) | `Offline` при отключении |
| <a name="cityair_system"></a>`+/+/system` | Запрос настроек (только 250) | Отправить `GET` для получения данных |
| <a name="cityair_log"></a>`+/+/log` | Лог подключения | Версия, MAC, IP при подключении |
| <a name="cityair_state"></a>`+/+/state` | Состояние (Вкл/Выкл) | `on`, `off` (set: `+/+/state/set`) |
| <a name="cityair_ten_state"></a>`+/+/ten_state` | Состояние ТЭНа | `on`, `off` (set: `+/+/ten_state/set`) |
| <a name="cityair_damper_state"></a>`+/+/damper_state` | Состояние заслонки | `on`, `off` (set: `+/+/damper_state/set`) |
| <a name="cityair_target_temp"></a>`+/+/target_temp` | Целевая темп. ТЭНа | `10`..`25` °C (set: `+/+/target_temp/set`) |
| <a name="cityair_target_speed"></a>`+/+/target_speed` | Целевая скорость | `1`..`7` (set: `+/+/target_speed/set`) |

#### Данные и датчики (Publish)

| Топик | Описание | Значения |
| :--- | :--- | :--- |
| <a name="cityair_speed"></a>`+/+/speed` | Скорость вентилятора | `0`..`100` % |
| <a name="cityair_in_temp"></a>`+/+/in_temp` | Температура на входе | `-55`..`+125` °C |
| <a name="cityair_out_temp"></a>`+/+/out_temp` | Температура на выходе | `-55`..`+125` °C |
| <a name="cityair_damper"></a>`+/+/damper` | Положение заслонки | `closed`, `opened`, `opens`, `closes` |
| <a name="cityair_filter"></a>`+/+/filter` | Наработка фильтра | В часах |
| <a name="cityair_odometer"></a>`+/+/odometer` | Общий пробег | В часах |
| <a name="cityair_error"></a>`+/+/error` | Ошибки | `temp_hot`, `temp_cold`, `stop_hot`, `stop_cold`, `ds18_bus`, `ds18_lack`, `no` |

#### Управление (Subscribe)

| Топик | Команда |
| :--- | :--- |
| <a name="cityair_reset_error"></a>`+/+/reset_error` | Сброс ошибок (любое значение) |
| <a name="cityair_reset_filter"></a>`+/+/reset_filter` | Сброс фильтра (любое значение) |
| <a name="cityair_update"></a>`+/+/update` | Обновление прошивки (любое значение) |

---

### <a name="vector"></a> Управление Vakio Vector

**Версия:** 0.1.8+
Топики: `device/+/vector/mode` (от прибора), `server/+/vector/mode` (к прибору).

#### Команды capabilities

| Параметр | Описание | Значения/Пример |
| :--- | :--- | :--- |
| `on_off` | Вкл/Выкл | `on`, `off` |
| `speed` | Скорость | `1`-`7` |
| `heat` | Подогрев выхода | `5`-`30` °C |
| `mode` | Режим работы | `manual`, `smart` |
| `blow` | Режим продувки | `on`, `off` |
| `stepper` | Состояние заслонки | `0` (открывается), `1` (открыта), `2` (закрывается), `3` (закрыта) |

**Пример отправки:**
```json
{"capabilities": {"on_off": "on", "speed": 3}}
```

#### Команды settings

| Параметр | Описание | Значения |
| :--- | :--- | :--- |
| `disp_bright` | Яркость экрана | `5`-`100` % |
| `disp_mode` | Авто-яркость | `on`, `off` |
| `disp_rotate` | Поворот экрана | `0` (портрет), `1` (портрет инверт.), `2` (горизонт.), `3` (горизонт. инверт.) |
| `disp_wakeup` | Пробуждение экрана | `on`, `off` |
| `always_on` | Постоянно включенный экран | `on`, `off` |
| `heater_state` | Работа нагревателя | `on`, `off` |
| `blow_time` | Время продувки | `1`-`10` мин |
| `sens_co2` | Наличие датчика CO2 | `on`, `off` (только чтение) |

---

## REST API

Открытый API для интеграции (требуется доступ в интернет).

### <a name="register"></a> Регистрация

1.  Скачайте приложение **Vakio Smart Control** (App Store / Google Play).
2.  Зарегистрируйтесь и подтвердите Email.
3.  Отправьте письмо на <developer@vakio.ru> с темой "Регистрация индивидуального API", укажите Email, имя и телефон аккаунта.
4.  Получите данные для авторизации (`client_id`, `client_secret`).

---

### Авторизация

#### Получение токена по паролю

**POST** `https://api.vakio.ru/oauth/token`

*Headers:* `Content-Type: application/json`

```json
{
  "client_id": "<client_id>",
  "client_secret": "<client_secret>",
  "grant_type": "password",
  "username": "<your_email>",
  "password": "<your_password>"
}
```

#### Обновление токена (Refresh Token)

**POST** `https://api.vakio.ru/oauth/token`

```json
{
  "client_id": "<client_id>",
  "client_secret": "<client_secret>",
  "grant_type": "refresh_token",
  "refresh_token": "<your_refresh_token>"
}
```

*Успешный ответ:*
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

#### Список всех устройств

**GET** `https://api.vakio.ru/devices`

*Headers:*
```text
Content-Type: application/json
Authorization: Bearer <token>
```

*Пример ответа:*
```json
{
    "code": 200,
    "content": [
        {
            "id": 19,
            "device_name": "Мой прибор 1",
            "device_type": {
                "name": "Vakio Plus Series",
                "slug": "vakio-window-plus"
            },
            "capabilities": {
                "on_off": "off",
                "mode": "inflow",
                "speed": "5"
            },
            "properties": {
                "humidity": 24,
                "temperature": 24.5
            }
        }
    ]
}
```

#### Данные об одном устройстве

**GET** `https://api.vakio.ru/devices/{DEVICE_ID}`

---

### <a name="restbasesmart"></a> Управление Base Smart

**PUT** `https://api.vakio.ru/devices/{DEVICE_ID}`

*Body:*
```json
{
  "capabilities": [
    { "instance": "mode", "value": "inflow" },
    { "instance": "speed", "value": "3" },
    { "instance": "on_off", "value": "on" }
  ]
}
```

**Типы данных:**
*   **Режимы (`mode`):** `inflow`, `outflow`, `recuperator`, `inflow_max`, `outflow_max`, `night`.
*   **Скорость (`speed`):** `1` - `7`.
*   **Вкл/Выкл (`on_off`):** `on`, `off`.

---

### <a name="restopenair"></a> Управление OpenAir

**PUT** `https://api.vakio.ru/devices/{DEVICE_ID}`

*Body:*
```json
{
  "capabilities": [
    { "instance": "mode", "value": "manual" },
    { "instance": "speed", "value": "3" },
    { "instance": "gate", "value": "1" },
    { "instance": "on_off", "value": "on" }
  ]
}
```

**Типы данных:**
*   **Режимы (`mode`):** `manual`, `smart_auto`.
*   **Скорость (`speed`):** `0` - `5`.
*   **Заслонка (`gate`):** `1` - `4` (не работает при скорости > 0).
*   **Вкл/Выкл (`on_off`):** `on`, `off`.

---

## <a name="integrations"></a> Интеграции с системами умных домов

### Home Assistant
*   [VAKIO Smart Series](https://github.com/vakio-ru/vakio_base_smart)
*   [VAKIO Openair](https://github.com/vakio-ru/vakio_openair)
*   [VAKIO Atmosphere](https://github.com/vakio-ru/vakio_atmosphere)
*   [VAKIO Kiv Smart](https://github.com/vakio-ru/vakio_kiv)

### MajorDOMO
*   [Smart-устройства](https://github.com/vakio-ru/vakio_smart_control)

### Sprut.hub (от партнеров)
*   [VAKIO Smart Series + Wirenboard](https://comf.life/kak-dobavit-rekuperator-vakio-v-umnyj-dom-wirenboard-yandeks-alisu-apple-home-spruthub.html)
