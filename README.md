# Вырвиглазное тестовое задание на момент 10.04.22

# PHP Backend | Full stack  test task

## Введение

Данное задание представляет собой упрощенную версию реального проекта. Если вы выполните его, вам будет проще начать работать с нами.  

Задание может показаться объемным, но это не так. Мы отметили те места в коде, где требуется внедренить изменения для заданий, но, если посчитаете нужным, вы можете создавать новые файлы и писать код в неотмеченных местах. 
Мы так же постарались сделать развертку максимально удобной. 


## Описание

В папке `web/application` реализован полный пример приложения. Все задания выполняются там.

В папке `web/system` находится наше модифицированное ядро CodeIgniter v3.

Точка входа: `web/application/controllers/Main_page.php` - контроллер со всеми основными методами.

Краткое руководство по нашему улучшенному CI находится в `web/system/docs/Code_style_and_guide.md`

После его прочтения становится понятно как использовать Emerald_model'ы и остальные плюшки фреймворка. Мы всегда работаем с моделями Emerald_model - это наши ORM-OOP модели
для работы с базой, связями. Они представляют базу данных и реализуют работу с ней, вся логика хранится в них, в том числе и "сервисный слой". Для работы с базой данных мы используем
Sparrow. В тестовом задании не используйте Database-класс CodeIgniter!

Phinx-миграции в тестовом задании делать не нужно. Достаточно залить дамп!

## Инициализация через Docker

### Init env on Windows

``init.bat`` - Initialize docker, pull images, init the db, install composer dependencies.

### Base commands for Unix

**Все команды нужно запускать от имени администратора/root(sudo) или от имени пользователя входящего в docker группу.**

``make init`` - Initialize docker, pull images, init the db, install composer dependencies.  
``make docker-start`` - Start docker  
``make docker-stop`` - Stop docker containers  
``make logs`` - get containers logs

### phpMyAdmin

Вход в PMA:  
``server`` - оставьте пустым
``username`` - root  
``password`` - root

### Конфигурация приложения (не требует изменений)

``'hostname' => 'frozeneon-mysql',
'username' => 'dev',
'password' => 'dev',
'database' => 'test_task'``

## Инициализация без Docker (no_docker_setup)

### PHP

Версии PHP 7.3+, задание составлялось и проверялось на 7.3

### nginx

no_docker_setup/nginx/test_task.conf - в nginx conf.d или sites_available, укажите ваш домен `CI_DOMAIN`, к примеру `sometestworkspace.com`

### Приложение

Изменить на ваши пути public/index.php - **$application_folder**

config/database.php - **параметры базы**

### MySQL

db_dump/init_db.sql - залить дамп

--------------------------

## ЗАДАЧИ

Задачи перечислены по возрастанию сложности.
Дополнительные задачи необязательны, но их выполнение может улучшить впечатление о Вас.

Все выполненные задачи соберите в одной ветке и сделайте Pull Request в Master **в своём репозитории**. Исходный **код тестового задания не должен быть отформатирован** (нужно, чтобы в Pull Request были только касающиеся задач изменения)

У нас в нашем платном мини-инстаграме уже есть список постов и просмотр информации о посте. Но вот не хватает чуть монетизации и юзабилити:

API должно работать через HTTP-запросы

**Задача 1**. `/main_page/login` - Реализовать аутентификацию. Login model уже содержит наброски для работы с сессией.

**Задача 2**. `/main_page/comment` - Реализовать возможность комментирования постов. Дополнительно: реализовать вложенные комментарии, количество уровней вложенности не ограничено.

**Задача 3**. `/main_page/like` - Реализовать возможность лайкнуть пост или комментарий. Число лайков на один пост/комментарий не ограничено (пользователь может лайкать пока у него на балансе еще есть лайки. Для этого уже создано поле `likes_balance` в таблице пользователей ).

**Задача 4**. `/main_page/add_money` - Реализовать монетизацию. Есть готовый API-Endpoint для зачисления средств принимающий количество валюты. У юзеров в базе есть
столбец `wallet_balance`, отвечающий за баланс, столбец `wallet_total_refilled` - сумма, на которую юзер пополнил баланс за все время, `wallet_total_withdrawn` - сумма, которую юзер потратил (превратил в лайки). Эти два поля должны учитывать все действия по счету пользователя (пополнения и траты). Используемая валюта - доллар США. Любая работа с балансом пользователя должна быть максимально безопасна и отказоустойчива (все вытекающие из этого технические решения нужно описать и обосновать).

### Дополнительные задачи backend

**Дополнительная задача 1**. `/main_page/buy_boosterpack` - Поскольку сейчас в мире самым популярным способом монетизации игр является покупка "бустерпаков" - сундуков/ящиков/кейсов с
предметами/карточками/деньгами, - предлагаем реализовать эту максимально простую и интересную функциональность для наших пользователей :)

В базе уже есть 3 бустерпака, класс также частично реализован.
Бустерпак должен содержать в себе опеределенное количество айтемов, для этого уже созданы таблицы `items` и `boosterpack_info`, хранящая в себе связь айтемов и паков.
Айтем, полученный из бустерпака — это некоторое количество лайков; 1 лайк эквивалентен 1 usd.

Покупая такой пак, пользователь получает случайный айтем в виде количества лайков которые может потратить на "лайкинг" постов и комментариев.
Лайки попадают на "лайк-счет" (поле `likes_balance`) пользователя с которого он и будет их
тратить, то есть параллельно мы храним как счет в $, так и отдельный счет числа лайков у юзера.

Логика бустерпака:

```
1 лайк = 1 usd
У каждого бустерпака есть свой профитбанк общий для всех пользователей (поле `bank`).
Так же для каждого бустерпака предусмотренна комиссия (поле `us`).

При открытии бустерпака нужно выбрать выигрыш на рандом среди подходящих айтемов с учетом накопленного профитбанка и цены бустерпака.
Чтобы выбрать подходящие айтемы, нужно вычислисть максимальную стоимость айтема: [макс. стоимость = профитбанк + (цена открываемого бустерпака - комиссия)]
После осуществления открытия нужно посчитать и сохранить новое значение профитбанка:
[профитбанк = профитбанк + цена бустерпака - комиссия - стоимость выданных в текущем открытии лайков]
Таким образом разность между результатом юзера и ценой бустерпака, которую мы не выдали ему, используется в будущих открытиях и выдается кому-то из следующих юзеров.
```

**Дополнительная задача  2** Необходимо реализовать полное логирование любых изменений баланса и написать sql запросы в соответствии с требованиями.

Таблица и класс `Analytics_model` для работы с транзакциями (изменениями баланса) уже созданы.

Подсказка:  
Создайте класс `Transaction_type`, расширяющий наш класс enum (`\System\Emerald\Emerald_enum`), в котором будут константы для таблицы транзакций, он
должен отвечать за тип транзакции в логе - списание или зачисление средств. Enum класс `Transaction_info` должен указывать на источник зачисления средств или на цель их списания.

В файле `mysql/6task.sql` напишите, пожалуйста, ровно два запроса:

1. Сколько денег потрачено на бустерпаки по каждому паку отдельно, почасовая выборка. Также нужно показать, сколько получили юзеры из каждого пока в эквиваленте $. Выборка должна
   быть за последние 30 дней.
2. Выборка по юзеру, на сколько он пополнил баланс и сколько получил лайков за все время. Текущий остаток баланса в $ и лайков на счету.


### Дополнительная задача по Vue.js | Frontend - Fullstack

**Дополнительная задача 3**. Реализовать описанную в задачах выше функциональность на фронте.

**Дополнительная задача 4**. В отдельной модалке реализовать через запрос на бек отображение истории пополнений и трат баланса, в
отдельном табе модалки показать общую пополненную юзером сумму, общую потраченную сумму, текущий баланс.

**Дополнительная задача 5**. История открытия бустерпаков (стоимость каждого, число полученных из каждого открытия лайков).

По связи безопасности бэка и фронта конкретных требований нет, но Вы можете проявить креативность.

   
## После выполнения задач:
   
- Создайте копию проекта в своем публичном репозитории на GitHub.
- Все выполненные задачи соберите в одной ветке и сделайте Pull Request в Master. Исходный код тестового задания не должен быть отформатирован (нужно, чтобы в Pull Request были только касающиеся задач изменения)
- Убедитесь, что у сторонних лиц есть возможность просматривать и оставлять комментарии в созданном пулл реквесте

#### Удачи Вам! ;)
