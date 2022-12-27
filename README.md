<center> <img src = https://raw.githubusercontent.com/AndreyRysistov/DatasetsForPandas/main/hh%20label.jpg alt="drawing" style="width:400px;"> </center>

# <center> Проект: Анализ вакансий из HeadHunter </center>

## В проекте осуществляется работа с базой данных посредством запросов на языке Python.

## Данные в базе представлены следующими таблица:

<center> <img src=images/SQL_pj2_2_1.png width=500 height=500> </center>

#

# VACANCIES

## Таблица хранит в себе данные по вакансиям и содержит следующие столбцы:
<center> <img src=images/SQL_pj2_2_2.png width=700 height=300> </center>

# AREAS

## Таблица-справочник, которая хранит код города и его название.
<center> <img src=images/SQL_pj2_2_3.png width=700 height=110> </center>

# EMPLOYERS

## Таблица-справочник со списком работодателей.
<center> <img src=images/SQL_pj2_2_4.png width=700 height=140> </center>

# INDUSTRIES

## Таблица-справочник вариантов сфер деятельности работодателей.
<center> <img src=images/SQL_pj2_2_5.png width=700 height=110> </center>

# EMPLOYERS_INDUSTRIES

## Дополнительная таблица, которая существует для организации связи между работодателями и сферами их деятельности.
<center> <img src=images/SQL_pj2_2_6.png width=700 height=130> </center>

#

# Содержание проекта:
## 1. Изучение страктуры базы данных из Python
### 1.1. Чтение данных с помощью библиотеки Pandas.
### 1.2. Формирование запросов в базу данных с помощью пакета PSYCOPG2.
### 1.3. Анализ информации о числе элементов в базе.

## 2. Детальный анализ вакансий
### Проведен разведывательный анализ (EDA), проанализировано:
* количества вакансий по регионам;
* пропуски в данных о заработной плате;
* размера заработной платы;
* число вакнасий для различных графиков работы и типов занятости;
* число вакансий для соискателей с различным опытом работы;

## 3. Анализ работодателей

### Проведен разведывательный анализ (EDA), проанализировано:
* число вакансий различных работодателей;
* число работодаталей по регионам регистрации;
* число регионов присутствия отдельных работодателей;
* сферы деятельности работодателей и их количество;
* отдельный акцент сделан на сфере деятельности "Разработка программного обеспечения".

### Дополнительно из [внешнего источника](https://ru.wikipedia.org/wiki/%D0%93%D0%BE%D1%80%D0%BE%D0%B4%D0%B0-%D0%BC%D0%B8%D0%BB%D0%BB%D0%B8%D0%BE%D0%BD%D0%B5%D1%80%D1%8B_%D0%A0%D0%BE%D1%81%D1%81%D0%B8%D0%B8) получен список городов-милионников Российской Федерации и проведен анализ вакансий, размещенных в них, на примере компании "Яндекс".
### Для получения данных был использован модуль requests и библиотека BeautifulSoup:
```python
import requests # Импортируем модуль requests
from bs4 import BeautifulSoup # импортируем библиотеку BeautifulSoup

# определяем адрес страницы
url = 'https://ru.wikipedia.org/wiki/%D0%93%D0%BE%D1%80%D0%BE%D0%B4%D0%B0-%D0%BC%D0%B8%D0%BB%D0%BB%D0%B8%D0%BE%D0%BD%D0%B5%D1%80%D1%8B_%D0%A0%D0%BE%D1%81%D1%81%D0%B8%D0%B8'
response = requests.get(url)  # выполняем GET-запрос

# Отправляем запрос на получение таблицы класса 'standard sortable'
page = BeautifulSoup(response.text, 'html.parser').find('table', class_='standard sortable')

cities = [] # Создаем список городов-миллионников

for td in page.find('tbody').find_all('td'): # В теле таблицы ищем тег 'a'
    if td.find('a'): # Если тег 'a' найден, получаем атрибут 'title'
        cities.append(td.find('a')['title']) # Наполняем список городов-миллионников
```

## 4. Предметный анализ

### 4.1. Вакансии проанализированы на принадлежность к работе с данными.
```sql
lower(name) like '%data%' or lower(name) like '%данн%'
```

### 4.2. Вакансии проанализированы на принадлежность к дата-саентистам.
```sql
(lower(name) like '%data scientist%' or lower(name) like '%data science%' or lower(name) like '%исследователь данных%' or lower(name) like '%machine learning%' or lower(name) like '%машинн%обучен%' or name like '%ML%') and (name not like '%HTML%')
```

### 4.3. Проведен дополнительный анализ вакансий.
* для специалистов уровня Junior
```sql
lower(name) like '%junior%' or experience = 'Нет опыта' or employment = 'Стажировка'
```
* количество вакансий с ключевыми навыками SQL или postgres
```sql
key_skills like '%SQL%' or key_skills like '%postgres%'
```
* количество вакансий с ключевыми навыками Python
```sql
lower(key_skills) like '%python%'
```

### 4.4. Проведен анализ средней заработной платы для дата-саентисов.
#### Для данных, в которых пропущены значения, применена функция coalesce:
```sql
(coalesce(salary_from, salary_to) + coalesce(salary_to, salary_from))/2
```

## 5. Дополнительный анализ
### 5.1. Проанализированы ТОП-5 работодателей по числу вакансий.
### 5.2. Проанализированы отличия в заработной плате дата-саентистов и работников других сфер деятельность.

<center> <img src=images/exp_salary.png width=700 height=680> </center>

### 5.3. Проведен анализ наиболее распространенных навыков для дата-саентистов и город, в которых преобладает число вакаксий для них.

## Использованные инструменты и библиотеки
* pandas (1.5.1)
* psycopg2 (2.9.5)
* requests (2.28.1)
* beautifulsoup4 (4.11.1)
* plotly (5.11.0)