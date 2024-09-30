# REST API - VAKIO Vector

## Оглавление

1. [Информация](#Информация)
    1. [Общая информация](#Общая-информация)
    2. [Дополнительная информация](#Дополнительная-информация)
    2. [Информация по REST](#Дополнительная-информация)

2. [Получение информации](#Получение-информации)
    1. [Вся информация обо всех устройствах](#вся-информация-обо-всех-устройствах)

3. [Управление](#управление-vector)
    1. [Управление режимом](#обновление-режима-работы)
    2. [Управление настройками](#обновление-настроек)


## Информация

### Общая информация

Прибор является умной приточной вентиляцией. Основной функционал — это регулировка скорости вентилятора и подогрева входящего воздуха, включение проветривания. На экране прибора отображается текущая скорость вентилятора и температура выходящего воздуха, с дополнительной индикацией о включенной авто яркости экрана, изменении положения заслонки, первоначальной настройки датчиков температуры и уведомление об ошибке.

### Дополнительная информация

Подсветку нельзя регулировать при автоматической регулировке от датчика света.

При включенном ночном режиме максимальная скорость вентилятора в умном режиме ограничивается.

### Информация по REST

Авторизация производится по стандарту OAuth2 (Password Grant).

Полученный в результате авторизации токен нужно использовать в Заголовках запросов:

```
Authorization: Bearer <token>
```

## Получение информации

### Вся информация обо всех устройствах

Конечная точка - `/devices/{id}`

Метод - `GET`

#### Пример ответа

```json
{
  "code": 200,
  "content": [
    {
      "id": 4332386,
      "user_id": 331234,
      "device_name": "VakioVector_F854",
      "device_group": "Нет группы",
      "device_type": {
        "name": "Vakio Airflow",
        "slug": null,
        "image": null
      },
      "uuid": "b233b452707ae01812623c8cb5ae26deec4ce543",
      "capabilities": {
        "on_off": "on",
        "speed": 3,
        "heat": 30,
        "mode": "manual",
        "blow": "off"
      },
      "properties": {
        "in_temp": 27,
        "temperature": 23,
        "co2_level": 759
      },
      "scenario": null,
      "master_device_id": null,
      "master_device_id_tmp": null,
      "optional": {
        "smart_mode_type": 43525
      },
      "motominutes": 0,
      "registered": "2024-09-02 04:17:34.682958+00",
      "verified": "1",
      "relation": {
        "external": "on",
        "limit": 800,
        "device_id_master": 43525,
        "on_off_dependence": "on",
        "dependence": {
          "limit": 1250,
          "device_id_master": 43525
        }
      },
      "version": "0.0.9",
      "device_mac": "94:3C:C6:B8:F8:54",
      "state": "1",
      "device_ip": "37.193.86.180",
      "settings": {
        "disp_bright": 100,
        "disp_mode": "off",
        "disp_rotate": 0,
        "disp_wakeup": "on",
        "always_on": "on",
        "night": "off",
        "heater_state": "on",
        "blow_time": 1,
        "sens_co2": "on"
      },
      "device_subtype": "{\"series\": \"esp32\", \"subtype\": \"default\", \"xtal_freq\": \"40\"}",
      "errors": {
        "overheating": "off",
        "underheating": "off",
        "cold_in": "off",
        "internal_sens": "off"
      },
      "device_group_id": 8551,
      "device_type_id": 8,
      "access_type": 0
    }
  ]
}
```

## Управление Vector

### Обновление режима работы

Конечная точка - `/devices/{id}`

Метод - `PUT`

- `{id}` - ID устройства

#### Тело запроса

```json
{
  "capabilities": [
    {
      "instance": "heat",
      "value": 23
    }
  ]
}
```
```
capabilities.*.instance - свойство, для которого требуется обновить значение
capabilities.*.value - новое значение для свойства

Свойства и их значения:
- device_state: on/off, (Включить/выключить прибор)
- fan: 0-7, (Скорость вентилятора)
- heat: 10-25, (Температура выходного воздуха)
- mode: manual/smart, (Режим работы ручной и умный)
- blow: on/off (Включить/выключить проветривание)

```




#### Пример запроса

```json
{
  "capabilities": [
    {
      "instance": "on_off",
      "value": "off"
    }
  ]
}
```

#### Пример ответа
```json
{
  "code": 200,
  "device": {
    "id": 453823,
    "user_id": 15,
    "device_name": "Vakio Vector",
    "device_group": "Нет группы",
    "device_type": {
      "name": "Vakio Airflow",
      "slug": null,
      "image": null
    },
    "uuid": "0b54af8257317d8bd8af823340fd0a49f98881cd",
    "capabilities": {
      "on_off": "off",
      "speed": 0,
      "heat": 20,
      "mode": "manual",
      "blow": "off"
    },
    "properties": [],
    "scenario": null,
    "master_device_id": null,
    "master_device_id_tmp": null,
    "optional": [],
    "motominutes": 0,
    "registered": "2024-02-19 09:48:14.150952+00",
    "verified": "1",
    "relation": [],
    "version": null,
    "device_mac": null,
    "state": "1",
    "device_ip": null,
    "settings": [],
    "device_subtype": "{\"series\":\"esp32\",\"subtype\":\"nidec\",\"xtal_freq\":\"40\"}",
    "errors": [],
    "device_group_id": 2334,
    "device_type_id": 8,
    "access_type": 0
  }
}
```

### Обновление настроек

Конечная точка - `/devices/{id}`

Метод - `PUT`

- `{id}` - ID устройства

#### Тело запроса

```json
{
  "settings": [
    {
      "instance": "disp_bright",
      "value": 83
    }
  ]
}
```
```
capabilities.*.instance - свойство, для которого требуется обновить значение
capabilities.*.value - новое значение для свойства

Свойства и их значения:
- disp_bright: 5-100, (Уровень подсветки экрана)
- disp_mode: manual/auto (Переключатель автояркости)
- disp_rotate: 0-2 (Поворот экрана 0 – портретная, 1 – горизонтальная, 2 – инвертированная горизонтальная)
- disp_wakeup: on/off (Переключатель включения экрана при входящих настройках от сервера)
- always_on: on/off (Переключатель постоянно включенного экрана)
- night: on/off (Переключатель ночного режима)
- heater_state: on/off (Переключатель нагревателя)
- blow_time: 1-10 (Время проветривания)
- sens_co2: on/off (Наличие датчика co2)
```




#### Пример запроса

```json
{
  "settings": [
    {
      "instance": "disp_bright",
      "value": 83
    }
  ]
}
```

#### Пример ответа
Ответ общего формата (как в примере с `capabilities`)