# Самостоятельная работа 1

**Выполнила:** Наумова Надя, группа ЦИБ - 241
Вариант 2
---

## Цель работы

Научиться подключаться к базе данных PostgreSQL, создавать структуру данных, наполнять ее тестовыми данными и выполнять аналитические запросы. Построить визуализацию показателей KPI сотрудников в сервисе DataLens.

---

## Ход работы

### 1. Подключение к базе данных

Работа была выполнена на локальном сервере.

---

### 2. Создание структуры базы данных

Создана база данных **crm** со следующими таблицами:

- `companies` — компании-клиенты (20 компаний, 10+ отраслей)
- `contacts` — контактные лица (24 контакта, связаны с компаниями)
- `deals` — сделки (24 сделки, суммы от 56K до 5.5M)
- `activities` — активности (24 активности: звонки, встречи, email)

```sql
-- Создание таблицы companies
CREATE TABLE IF NOT EXISTS companies (
    company_id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    industry VARCHAR(100),
    website VARCHAR(100)
);

-- Создание таблицы contacts
CREATE TABLE IF NOT EXISTS contacts (
    contact_id SERIAL PRIMARY KEY,
    company_id INT REFERENCES companies(company_id),
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100)
);

-- Создание таблицы deals
CREATE TABLE IF NOT EXISTS deals (
    deal_id SERIAL PRIMARY KEY,
    company_id INT REFERENCES companies(company_id),
    deal_name VARCHAR(100),
    stage VARCHAR(50),
    amount DECIMAL
);

-- Создание таблицы activities
CREATE TABLE IF NOT EXISTS activities (
    activity_id SERIAL PRIMARY KEY,
    contact_id INT REFERENCES contacts(contact_id),
    type VARCHAR(50),
    activity_date DATE DEFAULT CURRENT_DATE,
    notes TEXT
);
```

---

### 3. Наполнение данными

Таблицы заполнены тестовыми данными, включающими:

- Компании из 10+ отраслей (IT, Construction, Healthcare, Finance и др.)
- Контакты (по 1-4 представителя на компанию)
- Сделки с суммами от 56 000 до 5 500 000 рублей
- Активности трех типов: звонки, встречи, email за период октябрь-ноябрь 2024

```sql
INSERT INTO companies (name, industry, website) VALUES
('Technopark LLC', 'IT', 'tehnopark.ru'),
('Global Trade IE', 'Trading', 'globaltrade.ru'),
('StroyMontazh JSC', 'Construction', 'stroymontazh.ru'),
('MedService LLC', 'Healthcare', 'medservice.ru'),
('Petrov Consulting IE', 'Consulting', 'petrov-ss.ru'),
('WebStudio LLC', 'IT', 'webstudio.ru'),
('PharmGroup JSC', 'Pharmaceuticals', 'farmgroup.ru'),
('TransLogistik LLC', 'Logistics', 'translogistik.ru'),
('InvestCapital JSC', 'Finance', 'investcapital.ru'),
('EnergoSnab LLC', 'Energy', 'energosnab.ru'),
('Kozlova Education IE', 'Education', 'kozlova-mi.ru'),
('AutoGarant LLC', 'Automotive', 'avtogarant.ru'),
('AgroProm JSC', 'Agriculture', 'agroprom.ru'),
('WellnessTour LLC', 'Tourism', 'wellnesstour.ru'),
('StrakhExpert JSC', 'Insurance', 'strahexpert.ru'),
('MediaPlus LLC', 'Media', 'mediaplus.ru'),
('Sokolov Legal IE', 'Legal', 'sokolov-av.ru'),
('MashZavod LLC', 'Manufacturing', 'mashzavod.ru'),
('OptTorg JSC', 'Wholesale', 'opttorg.ru'),
('BioTech LLC', 'Biotechnology', 'biotech.ru');

INSERT INTO contacts (company_id, first_name, last_name, email) VALUES
(1, 'Dmitry', 'Kovalev', 'd.kovalev@tehnopark.ru'),
(2, 'Anna', 'Morozova', 'a.morozova@globaltrade.ru'),
(3, 'Sergey', 'Volkov', 's.volkov@stroymontazh.ru'),
(4, 'Elena', 'Zaitseva', 'e.zaitseva@medservice.ru'),
(5, 'Alexey', 'Smirnov', 'a.smirnov@petrov-ss.ru'),
(6, 'Olga', 'Kuznetsova', 'o.kuznetsova@webstudio.ru'),
(7, 'Igor', 'Mikhailov', 'i.mikhailov@farmgroup.ru'),
(8, 'Tatiana', 'Novikova', 't.novikova@translogistik.ru'),
(9, 'Pavel', 'Fedorov', 'p.fedorov@investcapital.ru'),
(10, 'Marina', 'Vasilieva', 'm.vasilieva@energosnab.ru'),
(11, 'Nikolay', 'Stepanov', 'n.stepanov@kozlova-mi.ru'),
(12, 'Yulia', 'Pavlova', 'y.pavlova@avtogarant.ru'),
(13, 'Vladimir', 'Semenov', 'v.semenov@agroprom.ru'),
(14, 'Natalia', 'Grigorieva', 'n.grigorieva@wellnesstour.ru'),
(15, 'Andrey', 'Nikitin', 'a.nikitin@strahexpert.ru'),
(16, 'Ksenia', 'Soboleva', 'k.soboleva@mediaplus.ru'),
(17, 'Maxim', 'Borisov', 'm.borisov@sokolov-av.ru'),
(18, 'Irina', 'Timofeeva', 'i.timofeeva@mashzavod.ru'),
(19, 'Denis', 'Gerasimov', 'd.gerasimov@opttorg.ru'),
(20, 'Ekaterina', 'Chernysheva', 'e.chernysheva@biotech.ru'),
(3, 'Viktor', 'Alexandrov', 'v.alexandrov@stroymontazh.ru'),
(5, 'Larisa', 'Efimova', 'l.efimova@petrov-ss.ru'),
(8, 'Georgy', 'Kalinin', 'g.kalinin@translogistik.ru'),
(12, 'Valentina', 'Tikhonova', 'v.tikhonova@avtogarant.ru');

INSERT INTO deals (company_id, deal_name, stage, amount) VALUES
(1, 'Cloud Infrastructure', 'Proposal', 850000.00),
(2, 'Product Supply', 'Qualification', 210000.00),
(3, 'Office Reconstruction', 'Negotiation', 1800000.00),
(4, 'Medical Software', 'Proposal', 450000.00),
(5, 'Legal Support', 'Qualification', 95000.00),
(6, 'Website Development', 'In Progress', 120000.00),
(7, 'Pharmaceutical Supply', 'Negotiation', 670000.00),
(8, 'International Delivery', 'Proposal', 340000.00),
(9, 'Investment Analysis', 'Closed', 280000.00),
(10, 'Network Modernization', 'Qualification', 1250000.00),
(11, 'Corporate Training', 'Proposal', 89000.00),
(12, 'Fleet Acquisition', 'Negotiation', 3200000.00),
(13, 'Fertilizer Supply', 'Proposal', 430000.00),
(14, 'Travel Packages', 'Qualification', 56000.00),
(15, 'Corporate Insurance', 'In Progress', 195000.00),
(16, 'Advertising Campaign', 'Proposal', 310000.00),
(17, 'M&A Deal', 'Negotiation', 5500000.00),
(18, 'Production Line Setup', 'Qualification', 4200000.00),
(19, 'Warehouse Stock', 'Proposal', 780000.00),
(20, 'Market Research', 'Qualification', 150000.00),
(4, 'Diagnostic Equipment', 'Negotiation', 890000.00),
(7, 'Medical Supplies', 'Proposal', 230000.00),
(10, 'Energy Audit', 'Qualification', 120000.00),
(15, 'Health Insurance', 'In Progress', 420000.00);

INSERT INTO activities (contact_id, type, notes, activity_date) VALUES
(1, 'Call', 'Technical requirements discussion', '2024-10-15 11:30:00'),
(2, 'Meeting', 'Product presentation', '2024-10-16 14:00:00'),
(3, 'Email', 'Commercial proposal sent', '2024-10-17 09:45:00'),
(4, 'Call', 'Delivery timeline clarification', '2024-10-18 15:20:00'),
(5, 'Meeting', 'Contract consultation', '2024-10-19 12:00:00'),
(6, 'Email', 'Technical specification provided', '2024-10-20 10:15:00'),
(7, 'Call', 'Quality certificates discussion', '2024-10-21 16:30:00'),
(8, 'Meeting', 'Logistics cost calculation', '2024-10-22 11:00:00'),
(9, 'Email', 'Investment report sent', '2024-10-23 13:45:00'),
(10, 'Call', 'Repair planning', '2024-10-24 09:00:00'),
(11, 'Meeting', 'Training program development', '2024-10-25 14:30:00'),
(12, 'Email', 'Vehicle specifications sent', '2024-10-26 11:15:00'),
(13, 'Call', 'Agricultural consultation', '2024-10-27 10:30:00'),
(14, 'Meeting', 'Travel planning discussion', '2024-10-28 15:00:00'),
(15, 'Email', 'Insurance terms sent', '2024-10-29 12:20:00'),
(16, 'Call', 'Media plan discussion', '2024-10-30 16:45:00'),
(17, 'Meeting', 'Legal review', '2024-10-31 13:15:00'),
(18, 'Email', 'Technical documentation sent', '2024-11-01 09:30:00'),
(19, 'Call', 'Price negotiation', '2024-11-02 14:15:00'),
(20, 'Meeting', 'Product presentation', '2024-11-03 11:45:00'),
(21, 'Email', 'Reconstruction estimate sent', '2024-11-04 10:00:00'),
(22, 'Call', 'Contract adjustment', '2024-11-05 15:30:00'),
(23, 'Meeting', 'Logistics audit', '2024-11-06 12:00:00'),
(24, 'Email', 'Specification approval', '2024-11-07 16:20:00');
```

---

### 4. Визуализация в DataLens

Создано подключение по файлу. Разработаны чарты и дашборд с визуализацией данных.
<img width="1568" height="846" alt="image" src="https://github.com/user-attachments/assets/b1a99285-a4a8-4c13-a041-53bc207dd923" />


Ссылка на дашборд: https://datalens.yandex/7b1aaw1d8nhmr
---

## Результаты

### Аналитические выводы:

- **По компаниям** — лидеры по выручке: M&A Deal (5.5M), Production Line Setup (4.2M), Vehicle Fleet (3.2M). Компании из отраслей Finance и Construction показывают наибольшую сумму сделок, аутсайдеры — Tourism и Education с минимальными сделками.

- **По стадиям сделок** — большинство сделок находятся на стадиях Proposal и Qualification, лишь одна сделка закрыта. Это указывает на необходимость усиления работы по доведению сделок до закрытия.

- **По активности** — наиболее активные компании имеют по 2-3 активности, наименее активные — по 1 активности. Типы активностей распределены равномерно (звонки, встречи, email по 8 записей).

- **По отраслям** — IT, Finance и Construction генерируют наибольшую выручку, Tourism и Education — наименьшую.

---

## Заключение

В ходе работы получены навыки:

- Подключения и работы с PostgreSQL через Python и pgAdmin
- Проектирования схемы базы данных CRM с учетом связей между таблицами
- Наполнения данными компаний, контактов, сделок и активностей
- Написания сложных JOIN-запросов для объединения данных из 4 таблиц
- Создания вычисляемых полей для категоризации компаний (лидеры, середняки, аутсайдеры)
- Визуализации данных в BI-системе (Yandex DataLens)

```
