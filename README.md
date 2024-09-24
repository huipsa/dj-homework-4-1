# Алгоритм запуска проекта

1. Установите зависимости:
```bash
pip install -r requirements.txt
```

2. Убедитесь, что в settings.py правильно указаны параметры для подключения к базе данных (БД):
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'netology_orm_migrations',
        'HOST': '127.0.0.1',
        'PORT': '5432',
        'USER': 'postgres',
        'PASSWORD': 'postgres',
    }
}
```

3. Создайте БД с именем, указанным в NAME (netology_orm_migrations):
```bash
createdb -U postgres netology_orm_migrations
```

4. Убедитесь, что в json-файле school.json учителя связаны со студентами в формате `"teachers": [teacher_id]` (в текущем репозитории сделано именно так):
```
[
...
{
  "model": "school.student",
  "pk": 1,
  "fields": {
    "name": "\u0411\u0430\u0431\u0430\u0435\u0432\u0430 \u0412\u0435\u0440\u0430 \u0418\u0432\u0430\u043d\u043e\u0432\u043d\u0430",
    "teachers": [1],
    "group": "8\u0410"
  }
},
{
  "model": "school.student",
  "pk": 2,
  "fields": {
    "name": "\u041f\u043e\u0433\u043e\u0440\u0435\u043b\u043e\u0432 \u0414\u0435\u043d\u0438\u0441 \u0412\u0438\u0442\u0430\u043b\u044c\u0435\u0432\u0438\u0447",
    "teachers": [3],
    "group": "8\u0410"
  }
},
{
  "model": "school.student",
  "pk": 3,
  "fields": {
    "name": "\u041e\u0441\u0438\u043f\u043e\u0432 \u0418\u0432\u0430\u043d \u0412\u044f\u0447\u0435\u0441\u043b\u0430\u0432\u043e\u0432\u0438\u0447",
    "teachers": [3],
    "group": "8\u0411"
  }
}
]
```

5. Осуществите команды для создания миграций приложения с БД:
```bash
python manage.py makemigrations
python manage.py migrate
```

6. Загрузите данные из json в БД:
```bash
python manage.py loaddata school.json
```

7. Запустите приложение:
```bash
python manage.py runserver
```

# Текст основного задания ("Миграции")

## Задание

Есть страница сайта школы.
При создании был неверно выбран тип связи `многие к одному`.
И теперь у ученика есть только один учитель и чтобы добавить к нему второго, требуется
заново добавлять ученика в базу.

Необходимо поменять модели и сделать отношение `многие ко многим` между Учителями и Учащимися.
Это решит проблемы текущей архитектуры.

Задача:

1. Поменять отношения моделей `Student` и `Teacher` с `Foreign key` на `Many to many`.
2. Поправить шаблон списка учеников с учётом изменения моделей.

## Примечание

Сначала примените текущую миграцию, затем загрузите исходные данные с помощью `loaddata` и после этого меняйте модели и делайте новые миграции.

В противном случае `loaddata` не сможет загрузить данные на новую схему и их придётся заносить вручную.

## Подсказки

Для смены связи достаточно поменять поле модели с `ForeignKey` на `ManyToManyField`. Не забудьте поменять и название поля, ведь раньше был один учитель `teacher`, а теперь может быть много — `teachers`.

К полю `ManyToManyField` добавить параметр `related_name='students'`, чтобы можно было получить список студентов у одного учителя.
После смены поля необходимо создать новую миграцию и применить её к базе данных.

В шаблоне для отображения всех учителей ученика можно использовать вложенный цикл:

```html
{% for teacher in student.teachers.all %}
<p>{{ teacher.name }}: {{ teacher.subject }}</p>
{% endfor %}
```

Более подробно примеры как работать с `ManyToManyField` можно посмотреть в документации Django.
https://docs.djangoproject.com/en/4.2/ref/contrib/admin/#working-with-many-to-many-models

## Дополнительное задание

Проанализируйте число SQL-запросов. Напоминание: для этого можно использовать `django-debug-toolbar`. Для каждого студента будет выполняться отдельный запрос. Это не очень производительное решение — улучшите его с помощью `prefetch_related` ([документация](https://docs.djangoproject.com/en/3.2/ref/models/querysets/#prefetch-related)).

## Документация по проекту

Для запуска проекта необходимо

#### Установить зависимости:

```bash
pip install -r requirements.txt
```

#### Провести миграцию:

```bash
python manage.py migrate
```

#### Загрузить тестовые данные:

***Важно***: после изменения моделей и применения миграций загрузить данные не получится. Выполните загрузку данных **до** изменения моделей.

```bash
python manage.py loaddata school.json
```

Если все же изменили модели, но не загрузили тестовые данные, ничего страшного, можно создать тестовые данные самостоятельно в админке.

#### Запустить отладочный веб-сервер проекта:

```bash
python manage.py runserver
```
