# vakio-public-api
Открытый API для интеграции приборов Vakio в системы умного дома

### Регистрация

1) Загрузите приложение Vakios Smart Control с App store или Google Play.
2) Зарегистрируйтесь и подтвердите Email.
3) Отправьте письмо на почту developer@vakio.ru с пометкой "Регистрация индивидуального API", в тексте укажите Email, имя и номер теелфона, которые относятся к этому аккаунту.
4) Мы вышлем вам данные для авторизации.

### Получение токена для авторизации с помощью пароля

*Адрес* 
```
POST https://api.vakio.ru/oauth/token
```
*Заголовки* 
```
headers: {
    'Content-Type': 'application/json'
},
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
headers: 
{
    'Content-Type': 'application/json'
},
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
    "refresh_token": "24bbee6297ee59d3b25e145a758cdf2b6504f39f"
}
```

### Получение данных обо всех устройствах пользователя

*Адрес* 
```
GET https://api.vakio.ru/devices
```
*Заголовки* 
```
headers: {
    'Content-Type': 'application/json',
    Authorization: 'Bearer <token>',
},
```
*Успешный ответ*
```json
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
headers: {
    'Content-Type': 'application/json',
    Authorization: 'Bearer <token>',
},
```

*Успешный ответ*
```
см. Получение данных обо всех устройствах пользователя
```
### Обновление устройства пользователя

*Адрес* 
```
PUT https://api.vakio.ru/devices/{DEVICE_ID}
```
*Заголовки* 
```
headers: {
    'Content-Type': 'application/json',
    Authorization: 'Bearer <token>',
},
```
*Тело*
```json
{

    "device_name": "<your device_name>",
    "device_group": "<your device_group>"
    "device_type": "<your device_type>",
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
    "properties": <your properties array>
    "relation": 
    *// Для метеостанции*
    [
        {
            "instance":"on_off_dependence",
            "value":"on"
        },
        {
            "instance":"dependence",
            "value":{
                "mode":"<inflow/outflow/recuperator>",
                "device_id_master":"<device_id_master>",
                "min_value":"<device_id_master>",
                "step":"<device_id_master>",
                "parametr":"<co2/temp/hud>"
            }
        }
    ]
    *// Для Бейса*
    [
        {
            "instance":"on_off_dependence",
            "value":"on"
        },
        {
            "instance":"dependence",
            "value":{
                "mode":"<sync/async>",
                "device_id_master":"<device_id_master>",
            }
        }
    ]
}
```

*Успешный ответ*
```
{
    "code": 200,
    "content": "updated"
}
```

### Смена режима работы Base Smart

*Адрес* 
```
PUT https://api.vakio.ru/devices/{DEVICE_ID}
```
*Заголовки* 
```
headers: {
    'Content-Type': 'application/json',
    Authorization: 'Bearer <token>',
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

