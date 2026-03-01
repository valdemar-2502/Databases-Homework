# Домашнее задание к занятию «Базы данных» - Kadancev-Vladimir

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в разделе «Вопросы по заданию» в личном кабинете.

Желаем успехов в выполнении домашнего задания.

---
### Чек-лист готовности к практическому заданию
Изучены лонгриды:
* Введение в реляционные базы данных
* Работа с SQL
* Проектирование и развёртывание баз данных
  
Выполнить это задание вам поможет пример из раздела «Проектирование схемы БД» в лонгриде «Проектирование и развёртывание баз данных».

### Легенда

Заказчик передал вам [файл в формате Excel](https://github.com/netology-code/sdb-homeworks/blob/main/resources/hw-12-1.xlsx), в котором сформирован отчёт. 

На основе этого отчёта нужно выполнить следующие задания.

### Задание 1

Опишите не менее семи таблиц, из которых состоит база данных. Определите:

- какие данные хранятся в этих таблицах,
- какой тип данных у столбцов в этих таблицах, если данные хранятся в PostgreSQL.

Начертите схему полученной модели данных. Можете использовать онлайн-редактор: https://app.diagrams.net/

Этапы реализации:
1.	Внимательно изучите предоставленный вам файл с данными и подумайте, как можно сгруппировать данные по смыслу.
2.	Разбейте исходный файл на несколько таблиц и определите список столбцов в каждой из них. 
3.	Для каждого столбца подберите подходящий тип данных из PostgreSQL. 
4.	Для каждой таблицы определите первичный ключ (PRIMARY KEY).
5.	Определите типы связей между таблицами. 
6.	Начертите схему модели данных.
На схеме должны быть чётко отображены:
   - все таблицы с их названиями,
   - все столбцы  с указанием типов данных,
   - первичные ключи (они должны быть явно выделены),
   - линии, показывающие связи между таблицами.

**Результатом выполнения задания** должен стать скриншот получившейся схемы базы данных.

---
### Решение

---
![database](https://github.com/valdemar-2502/Databases-Homework/blob/main/Diagram_Database.png)

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению. Вы можете их выполнить, если хотите глубже и шире разобраться в материале.


### Задание 2*

1. Разверните СУБД Postgres на своей хостовой машине, на виртуальной машине или в контейнере docker.
2. Опишите схему, полученную в предыдущем задании, с помощью скрипта SQL.
3. Создайте в вашей полученной СУБД новую базу данных и выполните полученный ранее скрипт для создания вашей модели данных.

В качестве решения приложите SQL скрипт и скриншот диаграммы.

---
Решение
---
### SQL скрипт
---

```
-- 1. Таблица-справочник должностей
CREATE TABLE positions (
    position_id SERIAL PRIMARY KEY,
    position_name VARCHAR(100) UNIQUE NOT NULL
);

-- 2. Таблица-справочник подразделений
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(255) NOT NULL,
    department_type VARCHAR(50) NOT NULL -- Например: 'Отдел', 'Группа', 'Департамент'
);

-- 3. Таблица-справочник адресов филиалов
CREATE TABLE branch_addresses (
    address_id SERIAL PRIMARY KEY,
    full_address VARCHAR(255) UNIQUE NOT NULL,
    city VARCHAR(100),
    street VARCHAR(200),
    building VARCHAR(20)
);

-- 4. Таблица-справочник проектов
CREATE TABLE projects (
    project_id SERIAL PRIMARY KEY,
    project_name VARCHAR(255) UNIQUE NOT NULL
);

-- 5. Основная таблица сотрудников
CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    full_name VARCHAR(255) NOT NULL,
    salary NUMERIC(10,2) NOT NULL,
    hire_date DATE NOT NULL,
    position_id INTEGER NOT NULL REFERENCES positions(position_id),
    department_id INTEGER NOT NULL REFERENCES departments(department_id),
    branch_address_id INTEGER NOT NULL REFERENCES branch_addresses(address_id)
);

-- 6. Связующая таблица для связи "многие-ко-многим" между сотрудниками и проектами
CREATE TABLE employee_projects (
    employee_project_id SERIAL PRIMARY KEY,
    employee_id INTEGER NOT NULL REFERENCES employees(employee_id) ON DELETE CASCADE,
    project_id INTEGER NOT NULL REFERENCES projects(project_id) ON DELETE CASCADE,
    UNIQUE(employee_id, project_id) -- Чтобы один сотрудник не был назначен на один проект дважды
);

-- Индексы для ускорения поиска по внешним ключам (рекомендуется для больших таблиц)
CREATE INDEX idx_employees_position_id ON employees(position_id);
CREATE INDEX idx_employees_department_id ON employees(department_id);
CREATE INDEX idx_employees_branch_address_id ON employees(branch_address_id);
CREATE INDEX idx_employee_projects_employee_id ON employee_projects(employee_id);
CREATE INDEX idx_employee_projects_project_id ON employee_projects(project_id);
```

---
![database](https://github.com/valdemar-2502/Databases-Homework/blob/main/Diagram_Database.png)
![database](https://github.com/valdemar-2502/Databases-Homework/blob/main/Diagram_Database.png)

Для написания и редактирования sql удобно использовать  специальный инструмент dbeaver.
