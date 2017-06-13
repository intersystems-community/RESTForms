# RESTForms - REST API для ваших классов InterSystems Caché
В этой статье я хотел бы представить проект RESTForms - универсальный REST API бэкэнд для современных веб-приложений.
Идея проекта проста - после написания нескольких REST API стало понятно, что, как правило, REST API состоит из двух частей:
- Работа с хранимыми данными
- Пользовательская бизнес-логика

И, хотя вам придется писать свою собственную бизнес-логику, RESTForms предоставляет все необходимое для работы с хранимыми данными из коробки.

## Варианты использования:
- У Вас уже есть модель данных в Caché, и Вы хотите представить некоторую (или всю) хранящуюся информацию в форме REST API.
- Вы разрабатываете новое Caché приложение и хотите сразу предоставлять REST API.
## Клиент
Этот проект разработан как бэкэнд для JS веб-приложений, поэтому JS сразу может начинать работу с RESTForms, не требуется преобразования форматов данных и т.п. 
## Возможности
Что уже можно делать с RESTForms:
- [CRUD](https://ru.wikipedia.org/wiki/CRUD) по классу данных - возможно получить метаданные класса, создавать, обновлять и удалять свойства класса.
- CRUD по объекту - возможно получать, создавать, обновлять и удалять объекты.
- R по наборам (через SQL) - защита от SQL-инъекций.
- Selfdiscovery – сначала вы получаете список доступных классов, после этого вы можете получить метаданные класса, и на основе этих метаданных выполнять  CRUD запросы по объекту.

## Пути
Далее представлена таблица доступных методов API, которая демонстрирует то, что Вы можете сделать через RESTForms.

| URL                               | Описание                                 |
|-----------------------------------|------------------------------------------|
| test                              | Тестовый метод                           |
| form/info                         | Список классов                           |
| form/info/all                     | Метаинформация всех классов              |
| form/info/:class                  | Метаинформация одного класса             |
| form/field/:class                 | Добавить свойство в класс                |
| form/field/:class                 | Изменить свойство                        |
| form/field/:class/:property       | Удалить свойство                         |
| form/object/:class/:id            | Получить объект                          |
| form/object/:class/:id/:property  | Получить свойство объекта                |
| form/object/:class                | Создать объект                           |
| form/object/:class/:id            | Обновить объект из динамического объекта |
| form/object/:class                | Обновить объект из объекта класса        |
| form/object/:class/:id            | Удалить объект                           |
| form/objects/:class/:query        | Выполнить SQL запрос                     |
| form/objects/:class/custom/:query | Выполнить пользовательский SQL запрос    |

## Установка
1.	Загрузите и импортируйте из последнего релиза на [странице релизов](https://github.com/intersystems-ru/RESTForms/releases/tag/v1.0) 20161.xml (для Caché 2016.1) или 201162.xml (для Caché 2016.2 +) в любую область
2.	Создайте новое веб-приложение `/forms` с классом брокером `Form.REST.Main`
3.	Откройте http://localhost:57772/forms/test?Debug в браузере, чтобы проверить установку (должен выводиться `{"Status": "OK"}`, возможно, будет запрошен пароль).
4. Если Вы хотите сгенерировать тестовые данные, вызовите:
```xml
do ##class(Form.Util.Init).populateTestForms()
```
## Как начать использовать RESTForms?

1. Импортируйте проект из GitHub (рекомендуется: добавить его как подмодуль (submodule) в ваш собственный репозиторий или просто загрузить релиз).
2. Для каждого хранимого класса, который Вы хотите представить через RESTForms:
 - Унаследуйте ваш хранимый класс от класса адаптера (Form.Adaptor)
 - Определите полномочия (например, вы можете представить некоторые классы только для чтения)
 - Определите свойство, используемое в качестве "имени" объекта
 - Определите отображаемые имена свойств
 
 Вот как выглядит список объектов класса:
 ![список классов](https://habrastorage.org/web/28d/759/b86/28d759b8637b4a13889987c2a5db5a5f.png)
 
 И объект класса:
 ![объект класса](https://habrastorage.org/web/8a4/cad/c93/8a4cadc931bd48b5b617c541fd2e5184.png)


## Пример использования
Во-первых, вы хотите знать, какие классы доступны. Чтобы получить эту информацию, вызовите:
```xml
http://localhost:57772/forms/form/info
```
Вы получите в ответ что-то вроде этого:
```xml
[
   { "name":"Company",     "class":"Form.Test.Company" },
   { "name":"Person",      "class":"Form.Test.Person"  },
   { "name":"Simple form", "class":"Form.Test.Simple"  }
]
```
На данный момент с RESTForms поставляются 3 тестовых класса. Давайте посмотрим метаданные для формы Person (класс Form.Test.Person). Чтобы получить эти данные, нужно вызвать:
```xml
http://localhost:57772/forms/form/info/Form.Test.Person
```
В ответ Вы получите метаданные класса:
```xml
  {
   "name":"Person",
   "class":"Form.Test.Person",
   "displayProperty":"name",
   "objpermissions":"CRUD",
   "fields":[  
      { "name":"name",     "type":"%Library.String",    "collection":"", "displayName":"Name",          "required":0, "category":"datatype" },
      { "name":"dob",      "type":"%Library.Date",      "collection":"", "displayName":"Date of Birth", "required":0, "category":"datatype" },
      { "name":"ts",       "type":"%Library.TimeStamp", "collection":"", "displayName":"Timestamp",     "required":0, "category":"datatype" },
      { "name":"num",      "type":"%Library.Numeric",   "collection":"", "displayName":"Number",        "required":0, "category":"datatype" },
      { "name":"аge",      "type":"%Library.Integer",   "collection":"", "displayName":"Age",           "required":0, "category":"datatype" },
      { "name":"relative", "type":"Form.Test.Person",   "collection":"", "displayName":"Relative",      "required":0, "category":"form"     },
      { "name":"Home",     "type":"Form.Test.Address",  "collection":"", "displayName":"House",         "required":0, "category":"serial"   },
      { "name":"company",  "type":"Form.Test.Company",  "collection":"", "displayName":"Company",       "required":0, "category":"form"     }
   ]
}
```
Что все это значит?

Метаданные класса:
- name – отображаемое имя класса
- class – название хранимого класса
- displayProperty – свойство объекта, использующееся при отображении объекта
- objpermissions – что может делать пользователь с объектом. В нашем случае пользователь может читать (R), создавать (C) новые объекты, изменять (U) и удалять (D) существующие объекты. 

Метаданные объектов:
- name – название свойства
- collection – является ли класс коллекцией (списком или массивом)
- displayName – отображаемое имя свойства
- required – обязательное ли свойство
- type – тип свойства
- category – категория типа свойства. Это обычные категории класса Caché, кроме всех классов, наследующихся от адаптера RESTForms - они имеют категорию "form"

Определение класса выглядит следующим образом:
```xml
/// Test form: Person
Class Form.Test.Person Extends (%Persistent, Form.Adaptor)
{

/// Отображаемое имя формы
Parameter FORMNAME = "Person";

/// Разрешения
/// Объекты этого класса могут быть Созданы (C), Получены (R), Изменены (U), и удалены (D)
Parameter OBJPERMISSIONS As %String = "CRUD";

/// Свойство "имени" объекта
Parameter DISPLAYPROPERTY As %String = "name";

/// Свойство сортировки по-умолчанию
Parameter FORMORDERBY As %String = "dob";

/// Имя
Property name As %String(DISPLAYNAME = "Name");

/// Дата рождения
Property dob As %Date(DISPLAYNAME = "Date of Birth");

/// Число
Property num As %Numeric(DISPLAYNAME = "Number") [ InitialExpression = "2.15" ];

/// Возраст, вычисляется автоматически
Property аge As %Integer(DISPLAYNAME = "Age") [ Calculated, SqlComputeCode = { set {*}=##class(Form.Test.Person).currentAge({dob})}, SqlComputed, SqlComputeOnChange = dob ];

/// Вычисление возраста
ClassMethod currentAge(date As %Date = "") As %Integer [ CodeMode = expression ]
{
$Select(date="":"",1:($ZD($H,8)-$ZD(date,8)\10000))
}

/// Родственник - ссылка на другой объект класса Form.Test.Person
Property relative As Form.Test.Person(DISPLAYNAME = "Relative");

/// Адрес
Property Home As Form.Test.Address(DISPLAYNAME = "House");

/// ссылка на компания, в которой человек работает
/// http://docs.intersystems.com/latest/csp/docbook/DocBook.UI.Page.cls?KEY=GOBJ_relationships
Relationship company As Form.Test.Company(DISPLAYNAME = "Company") [ Cardinality = one, Inverse = employees ];
}
```
## RESTForms – добавляем класс
Чтобы сделать хранимый класс доступным для RESTForms, надо:
1. Добавить наследование от Form.Adaptor
2. Добавить параметр `FORMNAME` со значением – имя класса
3. Добавить параметр `OBJPERMISSIONS`  – что можно делать с объектами класса (CRUD)
4. Добавить параметр `DISPLAYPROPERTY` – имя свойства, используемое для отображения имени объекта
5. Добавить параметр `FORMORDERBY` – свойство по умолчанию для сортировки по запросам с использованием RESTForms
6. Для каждого свойства, которое хочется видеть в метаданных, надо добавить параметр свойства DISPLAYNAME 

После того как мы сгенерировали некоторые тестовые данные (см. Установку, шаг 4), давайте получим Person с идентификатором 1. Чтобы получить объект, вызовем:
```xml
http://localhost:57772/forms/form/object/Form.Test.Person/1
```
И получим ответ:
```xml
{
   "_class":"Form.Test.Person",
   "_id":1,
   "name":"Klingman,Rhonda H.",
   "dob":"1996-10-18",
   "ts":"2016-09-20T10:51:31.375Z",
   "num":2.15,
   "аge":20,
   "relative":null,
   "Home":{
      "_class":"Form.Test.Address",
      "House":430,
      "Street":"5337 Second Place",
      "City":"Jackson"
   },
   "company":{
      "_class":"Form.Test.Company",
      "_id":60,
      "name":"XenaSys.com",
      "employees":[
         null
      ]
   }
}
```
Чтобы изменить объект (в частности, свойство num), вызовем:
```xml
PUT http://localhost:57772/forms/form/object/Form.Test.Person
```
С телом:
```xml
{
   "_class":"Form.Test.Person",
   "_id":1,
   "num":3.15
}
```
Обратите внимание на то, что для улучшения скорости, только `_class`, `_id` и измененные свойства должны быть в теле запроса.

Теперь, давайте создадим новый объект. Вызовем:
```xml
POST http://localhost:57772/forms/form/object/Form.Test.Person
```
С телом:
```xml
{
   "_class":"Form.Test.Person",
    "name":"Test person",
    "dob":"2000-01-18",
    "ts":"2016-09-20T10:51:31.375Z",
    "num":2.15,
    "company":{ "_class":"Form.Test.Company", "_id":1 }
}
```
Если создание объекта завершилось успешно, RESTForms вернёт идентификатор:
```xml
{"Id": "101"}
```
В противном случае, будет возвращена ошибка в формате JSON. Обратите внимание на то, что на персистентные свойства необходимо ссылаться через свойства `_class` и `_id`.
## Демо
Вы можете попробовать RESTForms онлайн [здесь](http://176.112.210.99:57772/forms/) (пользователь: Demo, пароль: Demo).
Кроме того, есть приложение RESTFormsUI - редактор для данных RESTForms. Оно доступно [здесь](http://176.112.210.99:57772/csp/restforms/index.html#/forms) (пользователь: Demo, пароль: Demo). Скриншот списка классов:
![image](https://community.intersystems.com/sites/default/files/inline/images/risunok1.png)
## Заключение
RESTForms выполняет почти всю работу, требуемую от REST API в отношении хранимых классов.
## Что же дальше?
В этой статье я только начал говорить о функциональных возможностях RESTForms. В следующей я бы хотел рассказать о некоторых дополнительных функциях, а именно о запросах, которые позволяют клиенту безопасно получать наборы данных без риска SQL-инъекций.
## Ссылки
- [RESTForms GitHub repository](https://github.com/intersystems-ru/RESTForms/)
- [RESTForms UI GitHub repository](https://github.com/intersystems-ru/RESTFormsUI/)


