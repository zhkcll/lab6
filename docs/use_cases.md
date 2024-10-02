# Розроблення функціональних вимог до системи

## Модель прецедентів

### 1. Загальна схема

```plantuml
@startuml

    actor "Guest" as Guest
    actor "User" as User
    actor "Tech Expert" as Expert

    usecase "<b>USER_LOGIN</b>\nВхід користувача до системи" as UC_1
    usecase "<b>USER_REG</b>\nРеєстрація облікового запису користувача в системі" as UC_2
    usecase "<b>USER_LOGOUT</b>\nВихід користувача з системи" as UC_3
    usecase "<b>USER_SEARCH_REQUEST</b>\nПошук контенту" as UC_4
    usecase "<b>USER_ACCOUNT_DELETE</b>\nВидалення облікового запису користувача" as UC_5
    usecase "<b>USER_MEDIA_CREATE</b>\nКористувач створює новий\n медіа-контент в системі" as UC_6
    usecase "<b>USER_MEDIA_DELETE</b>\nКористувач видаляє наявний медіа-контент" as UC_7
    usecase "<b>USER_MEDIA_EDIT</b>\nКористувач редагує інформацію про медіа-контент" as UC_8
    usecase "<b>USER_SUPPORT</b>\nКористувач звертається до технічної підтримки" as UC_9
    usecase "<b>USER_ACCOUNT_BAN</b>\nВидалення облікового запису\n користувача технічним експертом" as UC_10

    User -l-|> Guest
    Expert -r-|> Guest

    Guest -d-> UC_1
    Guest -u-> UC_2

    User -u-> UC_3
    User -u-> UC_4
    User -u-> UC_5
    User -r-> UC_6
    User -d-> UC_7
    User -d-> UC_8
    User -d-> UC_9

    Expert -d-> UC_9
    Expert -l-> UC_10
@enduml
```

<div align="center">
Рис. 1 Діаграма прецедентів
</div>

### 2. Схема використання для гостя

```plantuml
@startuml

    actor "Guest" as Guest

    usecase "<b>USER_LOGIN</b>\nВхід користувача до системи" as UC_1
    usecase "<b>USER_REG</b>\nРеєстрація облікового запису користувача в системі" as UC_2

    Guest -d-> UC_1
    Guest -d-> UC_2
@enduml
```
<div align="center">
Рис. 2 Схема можливостей гостя
</div>

### 3. Схема використання для користувача

```plantuml
@startuml

    actor "User" as User

    usecase "<b>USER_LOGOUT</b>\nВихід користувача з системи" as UC_3
    usecase "<b>USER_SEARCH_REQUEST</b>\nПошук контенту" as UC_4
    usecase "<b>USER_ACCOUNT_DELETE</b>\nВидалення облікового запису користувача" as UC_5
    usecase "<b>USER_MEDIA_CREATE</b>\nКористувач створює новий\n медіа-контент в системі" as UC_6
    usecase "<b>USER_MEDIA_DELETE</b>\nКористувач видаляє наявний медіа-контент" as UC_7
    usecase "<b>USER_MEDIA_EDIT</b>\nКористувач редагує інформацію про медіа-контент" as UC_8
    usecase "<b>USER_SUPPORT</b>\nКористувач звертається до технічної підтримки" as UC_9

    User -u-> UC_3
    User -u-> UC_4
    User -u-> UC_5
    User -r-> UC_6
    User -d-> UC_7
    User -d-> UC_8
    User -d-> UC_9

@enduml
```

<div align="center">
Рис. 3 Схема можливостей користувача
</div>

### 4. Схема використання для технічного експерта

```plantuml
@startuml

    actor "Tech Expert" as Expert

    usecase "<b>USER_SUPPORT</b>\nКористувач звертається до технічної підтримки" as UC_9
    usecase "<b>USER_ACCOUNT_BAN</b>\nВидалення облікового запису\n користувача технічним експертом" as UC_10

    Expert -d-> UC_9
    Expert -d-> UC_10
@enduml
```

<div align="center">
Рис. 4 Схема можливостей технічного експерта
</div>


### 5. Сценарії використання для незареєстрованого користувача (гостя)
// (USER_REG, USER_LOGIN)
### 6. Сценарії використання для користувача

| **ID:**                   | USER_LOGOUT |
|---------------------------|----------------|
| **НАЗВА:**                | Вихід користувача з системи |
| **УЧАСНИКИ:**             | Користувач, Система |
| **ПЕРЕДУМОВИ:**           | Користувач авторизований у системі |
| **РЕЗУЛЬТАТ:**            | Вихід з системи |
| **ВИКЛЮЧНІ СИТУАЦІЇ:**    | Користувач не авторизований у системі (USER_NOT_AUTHORIZED) |
| **ОСНОВНИЙ СЦЕНАРІЙ:**    | Користувач натискає кнопку виходу (Logout) із системи |
|                           | Система завершує поточну сесію з користувачем |
|                           | Система перенаправляє користувача на головну сторінку додатку |

```plantuml
@startuml

|Користувач|
start
: Користувач натискає кнопку "Вихід";

|Система|
: Система отримує запит на вихід;
: Система завершує поточну сесію;

note right #ff7070
<b>Можлива виключна ситуація</b>
<b>USER_NOT_AUTHORIZED</b>
end note

: Система перенаправляє на головну сторінку;

|Користувач|
stop

@enduml
```

<div align="center">
Рис. 7 Сценарій USER_LOGOUT
</div>   
<br>

| **ID:**                   | USER_SEARCH_REQUEST |
|---------------------------|------------------------------------|
| **НАЗВА:**                | Пошук контенту            |
| **УЧАСНИКИ:**             | Користувач, Система                                |
| **ПЕРЕДУМОВИ:**           | Користувач має бути авторизований у системі        |
| **РЕЗУЛЬТАТ:**            | Знайдені дані відображаються на екрані             |
| **ВИКЛЮЧНІ СИТУАЦІЇ:**    | У запиті містяться недопустимі символи (INVALID_QUERY_CHARACTERS) |
|                           | Забагато запитів за короткий період (TOO_MANY_REQUESTS) |
| **ОСНОВНИЙ СЦЕНАРІЙ:**    | Користувач заповнює форму пошуку              |
|                           | Користувач надсилає запит на пошук |
|                           | Система виконує пошук у базі даних              |
|                           | Система виводить результат пошуку на екран       |

```plantuml
@startuml

|Користувач|
start
: Користувач заповнює форму пошуку;
: Користувач надсилає запит на пошук;

|Система|
: Система перевіряє правильність запиту;
note right #ff7070
<b>Можлива виключна ситуація</b>
<b>INVALID_QUERY_CHARACTERS</b>
end note
: Система виконує пошук у базі даних;

note right #ff7070
<b>Можлива виключна ситуація</b>
<b>TOO_MANY_REQUESTS</b>
end note

: Система виводить результат пошуку на екран;

|Користувач|
stop

@enduml
```

<div align="center">
Рис. 8 Сценарій USER_SEARCH_REQUEST
</div>
<br> 

// (USER_ACCOUNT_DELETE, USER_MEDIA_CREATE, USER_MEDIA_DELETE, USER_MEDIA_EDIT, USER_SUPPORT)
### 7. Сценарії використання для технічного експерта
// (USER_ACCOUNT_BAN)
