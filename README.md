# API_YamDB

REST API для сервиса YaMDb — базы отзывов о фильмах, книгах и музыке.

## Описание
Проект YaMDb собирает отзывы пользователей на произведения. Произведения делятся на категории: «Книги», «Фильмы», «Музыка».
Произведению может быть присвоен жанр. Новые жанры может создавать только администратор.
Читатели оставляют к произведениям текстовые отзывы и выставляют произведению рейтинг (оценку в диапазоне от одного до десяти).
Из множества оценок автоматически высчитывается средняя оценка произведения.

## Стек технологий
- проект написан на Python с использованием Django REST Framework
- библиотека Simple JWT - работа с JWT-токеном
- библиотека django-filter - фильтрация запросов
- базы данны - SQLite3 и PostgreSQL
- автоматическое развертывание проекта - Docker, docker-compose
- система управления версиями - git

## Как запустить проект, используя Docker (база данных PostgreSQL):
1) Клонируйте репозитроий с проектом:
```
git clone https://github.com/netshy/api_yamdb/
```
2) В директории проекта создайте файл .env, по пути `project_name/api_yatube/.env`, в котором пропишите следующие переменные окружения (для тестирования можете использовать указанные значения переменных):
 - DB_ENGINE=django.db.backends.postgresql
 - DB_NAME=postgres
 - DB_USER=postgres
 - DB_PASSWORD=postgres
 - DB_HOST=db
 - DB_PORT=5432
 - POSTGRES_USER=postgres
 - POSTGRES_PASSWORD=postgres
 - POSTGRES_DB=postgres
3) С помощью Dockerfile и docker-compose.yaml разверните проект:
```
docker-compose up --build
```
##### Подготовка к запуску
1) Выполните миграции:
```
docker-compose exec web python manage.py migrate
```
2) Создать суперпользователя (для раздачи прав админам):
```
docker-compose exec web python manage.py createsuperuser
```
3) Команда для заполнения базы данных:
```
docker-compose exec web python manage.py loaddata fixtures.json
```

###### Ваш проект запустился на http://127.0.0.1/

###### Полная документация доступна по адресу http://127.0.0.1/redoc/

С помощью команды 
```
docker exec -ti <container_id> pytest
``` 
можете запустить тесты и проверить работу модулей

_________________________________

## Алгоритм регистрации пользователей
- Пользователь отправляет запрос с параметрами *email* и *username* на */auth/email/*.
- YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на адрес *email* .
- Пользователь отправляет запрос с параметрами *email* и *confirmation_code* на */auth/token/*, в ответе на запрос ему приходит token (JWT-токен).

## Ресурсы API YaMDb

- Ресурс AUTH: аутентификация.
- Ресурс USERS: пользователи.
- Ресурс TITLES: произведения, к которым пишут отзывы (определённый фильм, книга или песня).
- Ресурс CATEGORIES: категории (типы) произведений («Фильмы», «Книги», «Музыка»).
- Ресурс GENRES: жанры произведений. Одно произведение может быть привязано к нескольким жанрам.
- Ресурс REVIEWS: отзывы на произведения. Отзыв привязан к определённому произведению.
- Ресурс COMMENTS: комментарии к отзывам. Комментарий привязан к определённому отзыву.
______________________________________________________________________
### Пример http-запроса (POST) для создания нового комментария к отзыву:
```
url = 'http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/comments/'
data = {'text': 'Your comment'}
headers = {'Authorization': 'Bearer your_token'}
request = requests.post(url, data=data, headers=headers)
```
### Ответ API_YamDB:
```
Статус- код 200
{
 "id": 0,
 "text": "string",
 "author": "string",
 "pub_date": "2020-08-20T14:15:22Z"
}
```
***Над проектом работали:***
* Александр Пименов | Github:https://github.com/Lets-dancing | Тимлид, разработка части, касающейся управления пользователями (Auth и Users): система регистрации и аутентификации, права доступа, работа с токеном, система подтверждения через e-mail
* Степан Солнышкин | Github:https://github.com/Stepan-Solnyshkin| Разработчик, написание части с категориями (Categories), жанрами (Genres) и произведениями (Titles): моделями, представлениями и эндпойнтами для них.
* Дмитрий Пермяков | Github:https://github.com/taren4ik | Разработчик, написание части с отзывами (Review) и комментариями (Comments): описание модели, представления, настройка эндпойнтов, определение прав доступа для запросов. Рейтинги произведений.

***Настройку docker выполнил:***
* Александр Пименов | Github:https://github.com/Lets-dancing |