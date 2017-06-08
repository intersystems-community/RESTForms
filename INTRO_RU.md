# RESTForms - REST API для ваших классов
В этой статье я хотел бы представить проект RESTForms - универсальный бэкэнд REST API для современных веб-приложений.
Идея проекта проста - после того, как я написал несколько REST APIs стало понятно, что, как правило, REST API состоит из двух частей:
- Работа с хранимыми данными
- Пользовательская бизнес-логика

И, хотя вам придется писать свою собственную бизнес-логику, RESTForms предоставляет все необходимое для работы с хранимыми данными из коробки.

## Варианты использования:
- У Вас уже есть модель данных в Caché, и Вы хотите представить некоторую (или всю) хранящуюся информацию в форме REST API.
- Вы разрабатываете новое Caché приложение и сразу предоставлять REST API.
## Клиент
Этот проект разработан как бэкэнд для JS веб-приложений, поэтому JS сразу может начинать работу с RESTForms, не требуется преобразования форматов данных и т.п. 
## CRUD
Над сущностью можно выполнять 4 операции:
- Создание
- Чтение
- Изменение
- Удаление
## Функциональность
Что уже можно делать с RESTForms:
- CRUD по классу данных - возможно получить метаданные класса, создавать, обновлять и удалять свойства класса.
- CRUD по объекту - возможно получать, создавать, обновлять и удалять объекты.
- R по наборам (через SQL) - защита от SQL-инъекций.
- Selfdiscovery – сначала вы получаете список доступных классов, после этого вы можете получить метаданные класса, и на основе этих метаданных выполнять  CRUD запросы по объекту.

## Пути
Далее представлена таблица доступных методов API, которая демонстрирует то, что Вы можете сделать через RESTForms.

|                      URL                         |                   Description                     |
|--------------------------------------------------|---------------------------------------------------|
| info                                             | List of all available classes                     |
| info/all                                         | Get metadata for all classes                      |
| info/:class                                      | Class metadata                                    |
| field/:class                                     | Add property to class                             |
| field/:class                                     | Modify class property                             |
| field/:class/:property                           | Delete class property                             |
| object/:class/:id                                | Retrieve object                                   |
| object/:class/:id/:property                      | Retrieve one property of the object               |
| object/:class                                    | Create object                                     |
| object/:class/:id                                | Update object from dynamic object                 |
| object/:class                                    | Update object from object                         |
| object/:class/:id                                | Delete object                                     |
| objects/:class/:query                            | (SQL) Get objects for the class by query          |
| objects/:class/custom/:query                     | (SQL) Get objects for the class by custom query   |

## Как начать использовать RESTForms?

1. Импортируйте проект из GitHub (рекомендуется: добавить его как подмодуль (submodule) в ваш собственный репозиторий или просто загрузить релиз).
2. Для каждого класса, который Вы хотите представить через RESTForms:
 - Унаследуйте от класса адаптера
 - Определите полномочия (например, вы можете представить некоторые классы только для чтения)
 - Определите свойство, используемое в качестве отображаемого значения для объекта
 - Определите отображаемые имена свойств, которые Вы хотите отобразить
## Установка
1.	Загрузите и импортируйте из последнего релиза на [странице релизов](https://github.com/intersystems-ru/RESTForms/releases/tag/v1.0) 20161.xml (для Caché 2016.1) или 201162.xml (для Caché 2016.2 +) в любую область
2.	Создайте новое веб-приложение `/forms` с классом отправки `Form.REST.Main`
3.	Откройте http://localhost:57772/forms/test?Debug в браузере, чтобы проверить установку (должен выводиться {"Status": "OK"} и, возможно, будет запрошен пароль).
4. Если Вы хотите сгенерировать тестовые данные, вызовите:
```xml
do ##class(Form.Util.Init).populateTestForms()
```
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
На данный момент с RESTForms поставляются 3 тестовых класса. Давайте посмотрим метаданные для Person (класс Form.Test.Person). Чтобы получить эти данные, нужно вызвать:
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

В определении класса это выглядит следующим образом:
```xml
/// Test form: Person
Class Form.Test.Person Extends (%Persistent, Form.Adaptor, %Populate)
{

/// Form name, not a global key so it can be anything
/// Set to empty string (like here) to not have a class as a form 
Parameter FORMNAME = "Person";

/// Default permissions
/// Objects of this form can be Created, Read, Updated and Deleted
/// Redefine this parameter to change permissions for everyone
/// Redefine checkPermission method (see Form.Security) for this class
/// to add custom security based on user/roles/etc.
Parameter OBJPERMISSIONS As %String = "CRUD";

/// Property used for basic information about the object
/// By default getObjectDisplayName method gets its value from it
Parameter DISPLAYPROPERTY As %String = "name";

/// Use value of this parameter in SQL, as ORDER BY clause value 
Parameter FORMORDERBY As %String = "dob";

/// Person's name.
Property name As %String(COLLATION = "TRUNCATE(250)", DISPLAYNAME = "Name", MAXLEN = 2000);

/// Person's Date of Birth.
Property dob As %Date(DISPLAYNAME = "Date of Birth", POPSPEC = "Date()");

Property ts As %TimeStamp(DISPLAYNAME = "Timestamp") [ InitialExpression = {$ZDATETIME($ZTIMESTAMP, 3, 1, 3)} ];

Property num As %Numeric(DISPLAYNAME = "Number") [ InitialExpression = "2.15" ];

/// Person's age.<br>
/// This is a calculated field whose value is derived from <property>DOB</property>.
Property аge As %Integer(DISPLAYNAME = "Age") [ Calculated, SqlComputeCode = { set {*}=##class(Form.Test.Person).currentAge({dob})}, SqlComputed, SqlComputeOnChange = dob ];

/// This class method calculates a current age given a date of birth <var>date</var>.
ClassMethod currentAge(date As %Date = "") As %Integer [ CodeMode = expression ]
{
$Select(date="":"",1:($ZD($H,8)-$ZD(date,8)\10000))
}

/// Person's spouse.
/// This is a reference to another persistent object.
Property relative As Form.Test.Person(DISPLAYNAME = "Relative");

/// Person's home address. This uses an embedded object.
Property Home As Form.Test.Address(DISPLAYNAME = "House");

/// The company this person works for.
Relationship company As Form.Test.Company(DISPLAYNAME = "Company") [ Cardinality = one, Inverse = employees ];
}
```
## RESTForms – добавляем класс
Чтобы сделать хранимый класс доступным RESTForms, надо:
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
И, вот ответ (сгенерированные данные, могут отличаться):
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
В этой статье я только начал говорить о функциональных возможностях RESTForms. В следующей я бы хотел рассказать о некоторых дополнительных функциях, а именно о запросах, которые позволяют клиенту безопасно получать фрагменты данных без риска SQL-инъекций.
## Ссылки
- [RESTForms GitHub repository](https://github.com/intersystems-ru/RESTForms/)
- [RESTForms UI GitHub repository](https://github.com/intersystems-ru/RESTFormsUI/)


