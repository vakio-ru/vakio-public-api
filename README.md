# vakio-public-api
Открытый API для интеграции приборов Vakio в системы умного дома

### Создание нового пользователя

*Адрес* 
```
POST /user
```
*Тело*
```
{
    "name": "<your name>",
    "email": "<your email>",
    "phone": "your phone",
    "password": "<your password, not SHA1ed>",
}
```

*Успешный ответ*
```
{
    "code": 201,
    "content": {
        "id": 3,
        "email": "max.v@yandex.ru",
        "phone": "544534534",
        "password": "8cb2237d0679ca88db6464eac60da96345513964",
        "name": "max",
        "scope": "",
        "verified": 0,
        "verify_code": 0,
        "registered": "0000-00-00 00:00:00"
    }
}
```

### Восстановление данных пользователя по email

*Адрес* 
```
POST https://api.vakio.ru/forgot
```
*Тело*
```
{
    "email": "<your email>"
}
```

### Подтверждение нового пароля по email

*Адрес* 
```
POST https://api.vakio.ru/confirm
```
*Тело*
```
{
    "email": "<your email>",
    "verify_code": "<your verify_code>",
    "password": "<your new password, not SHA1ed>",
}
```
