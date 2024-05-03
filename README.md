# <a name="up" />Проект Яндекс Прилавок (API)

Яндекс Прилавок - это сервис, который предоставляет предпринимателям и компаниям возможность создавать собственные виртуальные магазины в онлайн-пространстве. Этот инструмент облегчает процесс создания и настройки интернет-магазинов, управления продажами и обработки заказов. Он также обеспечивает интеграцию с онлайн-платежами, что делает его полезным для развития электронной коммерции и совершения успешных онлайн-сделок.

## Содержание
- [Задачи тестировщика](#задачи-тестировщика)
- [Требования по проекту](#требования-по-проекту)
- [Инструменты](#инструменты)
- [Проектирование тестовой документации](#проектирование-тестовой-документации)
- [Выполнение тестов](#выполнение-тестов)
- [Отчёт](#Отчёт)

## Задачи тестировщика

<details>
<summary> Задачи </summary> 

1. Проанализировать требования к новой функциональности бэкенда Яндекс.Прилавка  
2. Изучить документацию к API в Apidoc  
3. Спроектировать тесты в виде чек-листа, чтобы покрыть новую функциональность  
-Работа с наборами: возможность добавлять продукты в набор    
Ручка **POST /api/v1/kits/:id/products**;   
-Работа с курьерами: возможность проверить, есть ли доставка курьерской службой «Привезём быстро» и сколько она стоит  
Ручка **POST /fast-delivery/v3.1.1/calculate-delivery.xml**;   
-Работа с корзиной:  
возможность получить список продуктов, которые добавили в корзину    
Ручка **GET /api/v1/orders/:id**;  
возможность добавлять продукты в корзину  
Ручка **PUT /api/v1/orders/:id**;  
возможность удалять корзину  
Ручка **DELETE /api/v1/orders/:id**.  
4. Протестировать API через Postman и завести баг-репорты в YouTrack
   
***

</details>

## Требования по проекту

<details>
<summary> Требования к бэкенду приложения Яндекс.Прилавка </summary>

<img width="1415" alt="image" src="https://github.com/qkitech/YandexPrilavok/assets/157276532/2ec8fc41-c260-4846-919c-f901728d403b">

***

</details>

<details>
<summary> API Яндекс.Прилавка </summary>

### API Яндекс.Прилавок 3.1.1 

### Warehouses
**Warehouses - [SOAP] Проверить количество товаров на складах**
**POST**
`/api/wsdl`  
Параметр:  
| Название | Тип | Описание |
| ------------------- | ------------------- | ------------------- |
| ids       | number[]      | Массив идентификаторов продуктов (после id в таблице product_model).       |

[XML] Проверяем количество товаров
```
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  xmlns:tns="WebServices.MainWsdl">
    <soap:Body>
        <Request xmlns="WebServices.MainWsdl">
            <ids>1</ids>
            <ids>4</ids>
            <ids>44</ids>
        </Request>
    </soap:Body>
</soap:Envelope>
```

Ответ: На каком складе, что есть и сколько
```
    HTTP/1.1 200 OK
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"  xmlns:tns="WebServices.MainWsdl">
    <soap:Body>
        <Response>
            <name>Имеется всё</name>
            <products>
                <name>Sprite классический</name>
                <quantity>9</quantity>
            </products>
            <products>
                <name>Чипсы кукурузные классик Salto Nachos</name>
                <quantity>6</quantity>
            </products>
        </Response>
        <Response>
            <name>Чердак</name>
            <products>
                <name>Сок Jumex апельсин без сахара</name>
                <quantity>3</quantity>
            </products>
            <products>
                <name>Sprite классический</name>
                <quantity>12</quantity>
            </products>
        </Response>
        <Response>
            <name>Большой мир</name>
            <products>
                <name>Сок Jumex апельсин без сахара</name>
                <quantity>1</quantity>
            </products>
        </Response>
        <Response>
            <name>Шведский дом</name>
            <products>
                <name>Сок Jumex апельсин без сахара</name>
                <quantity>3</quantity>
            </products>
            <products>
                <name>Sprite классический</name>
                <quantity>12</quantity>
            </products>
        </Response>
    </soap:Body>
</soap:Envelope>
```

**Warehouses - Получить список складов**
**GET**
`/api/v1/warehouses`  

Ответ: Успешное получение складов
```
HTTP/1.1 200 OK
[
    {
           "name": "Имеется всё",
           "workingHours": {
               "start": 7,
               "end": 23
           }
       },
    {
           "name": "Шведский дом",
           "workingHours": {
               "start": 8,
               "end": 23
           }
       },
    {
           "name": "Чердак",
           "workingHours": {
               "start": 8,
               "end": 21
           }
       },
    {
           "name": "Большой мир",
           "workingHours": {
               "start": 5,
               "end": 20
           }
       }
    ]
```

**Warehouses - Проверить количество товаров на складах**  
Версия этого эндпоинта для SOAP называется - [SOAP] Проверить количество товаров на складах  
**POST**
`/api/v1/warehouses/amount`  

[JSON] Пример заголовков
```
{
    "Content-Type": "application/json"
}
```
[XML] Пример заголовков
```
{
    "Content-Type": "application/xml"
}
```

Параметр:  
| Название | Тип | Описание |
| ------------------- | ------------------- | ------------------- |
| ids       | number[]      | Массив идентификаторов продуктов (после id в таблице product_model).       |
| dataType      | string      | Формат входных данных. Может принимать значения: "json" - тело запроса ожидается в JSON-формате "xml" - тело запроса ожидается в XML-формате По умолчанию: json     |

[JSON] Проверяем количество товаров
```
{
    "ids": [
        1,
        4,
        44
    ]
}
```
[XML] Проверяем количество товаров
```
<root>
    <id>1</id>
    <id>4</id>
    <id>44</id>
</root>
```

Ответ: На каком складе, что есть и сколько
```
HTTP/1.1 200 OK
{
       "Имеется всё": {
           "Sprite классический": 9,
           "Чипсы кукурузные классик Salto Nachos": 6
       },
       "Чердак": {
           "Сок Jumex апельсин без сахара": 3,
           "Sprite классический": 12
       },
       "Большой мир": {
           "Сок Jumex апельсин без сахара": 1
       },
       "Шведский дом": {
           "Сок Jumex апельсин без сахара": 3,
           "Sprite классический": 12
       }
   }
```





























***

</details>

## Инструменты
<p align="left"> 
  <a href="https://docs.google.com/" target="_blank" rel="noreferrer"><img src="https://w7.pngwing.com/pngs/240/1015/png-transparent-g-suite-google-docs-google-angle-rectangle-logo.png" width="36" height="36" alt="Google Sheets" /></a>
  <a href="https://www.figma.com/" target="_blank" rel="noreferrer"><img src="https://raw.githubusercontent.com/danielcranney/readme-generator/main/public/icons/skills/figma-colored.svg" width="36" height="36" alt="Figma" /></a>
  <a href="https://www.jetbrains.com/youtrack/" target="_blank" rel="noreferrer"><img src="https://upload.wikimedia.org/wikipedia/commons/9/95/YouTrack_Icon.png" width="36" height="36" alt="Youtrack" /></a>
 <a href="https://www.postman.com/" target="_blank" rel="noreferrer"><img src="https://seeklogo.com/images/P/postman-logo-0087CA0D15-seeklogo.com.png" title="postman" width="36" height="36" alt="Postman" /></a>
</p> 


## Процесс работы
### Проектирование тестовой документации
[Чек-лист_ API](https://docs.google.com/spreadsheets/d/1gA6NLPg8-L0pwPDYAbXYDMSMDvtLyVZzBG3-KMBqHnY/edit#gid=2006427015)

### Выполнение тестов

[Баг-репорты](https://kiropurr.youtrack.cloud/issues?q=tag:%20%7BAPI%20%D0%AF%D0%BD%D0%B4%D0%B5%D0%BA%D1%81.%D0%9F%D1%80%D0%B8%D0%BB%D0%B0%D0%B2%D0%BA%D0%B0%7D)

## Отчёт

<details>   
<summary> Отчёт о тестировании </summary>

Примерно половина чек-листов содержит баг,  что является неприемлемым для готового продукта.
Нет блокирующих ошибок, но обнаружено слишком много багов, чтобы продукт был готов к выпуску. Команда тестирования не рекомендует публиковать текущую версию API до устранения найденных багов.
Следует проанализировать функционирование ручек, опираясь на выявленные дефекты и учитывая требования и документацию

</details>
