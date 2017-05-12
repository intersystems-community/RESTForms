# Forms

Forms adapter for InterSystems Cache. Supports Caché versions 2017+.
For 2016.* versions, please see [this tree](https://github.com/ZitRos/RESTForms/tree/8e3ef48fbb4f6ce1dc3c471ec43cfc6e65bb2c40).

# Installation

Import classes and create web app with `Form.REST.Main` broker.

# Usage description and examples

See in this [post  on Developer Community](https://community.intersystems.com/post/restforms-rest-api-your-classes).

# Requests


| URL                                  | Type   | Description                            |
|--------------------------------------|--------|----------------------------------------|
| test                                 | GET    | Test request                           |
| info                                 | GET    | Basic information (currently language list)|
| logout                               | GET    | End current session                    |
| form/info                            | GET    | List of all availible forms            |
| form/info/all                        | GET    | Get metainformation for all forms      |
| form/info/:class                     | GET    | Form metainformation                   |
| form/field/:class                    | POST   | Add field to form                      |
| form/field/:class                    | PUT    | Modify form field                      |
| form/field/:class/:property          | DELETE | Delete form field                      |
| form/object/:class/:id               | GET    | Retrieve form object                   |
| form/object/:class/:id/:property     | GET    | Retrieve one field of the form object  |
| form/object/:class                   | POST   | Create form object                     |
| form/object/:class/:id               | PUT    | Update form object from dynamic object |
| form/object/:class                   | PUT    | Update form object from object         |
| form/object/:class/:id               | DELETE | Delete form object                     |
| form/objects/:class/:query           | GET    | (SQL) Get all members for the form by query|
| form/objects/:class/custom/:query    | GET    | (SQL) Get all members for the form by custom query|
| form/file/:class/:id/:property       | POST   | Add files to this property             |
| form/file/:class/:id/:property       | DELETE | Delete all files from this property    |
| form/file/:class/:id/:property/:name | DELETE | Delete one file from property          |
| form/file/:class/:id/:property/:name | GET    | Download one file from property        |

For POST/PUT requests see method descriptions for request body samples

Postman
-----------

You can use [Postman](https://www.getpostman.com/) to query RESTForms API. [Collection](RESTForms.postman_collection.json). [Environment](CACHE.postman_environment.json).

# SQL requests

`GET http://localhost:57772/forms/form/objects/Form.Test.Simple/info?size=2&page=1&orderby=text`

`GET http://localhost:57772/forms/form/objects/Form.Test.Simple/all?orderby=text+desc`

`GET http://localhost:57772/forms/form/objects/Form.Test.Simple/all?filter=text%20eq%20A9044`

`GET http://localhost:57772/forms/form/objects/Form.Test.Simple/all?filter=text%20in%20A9044~B5920`

Note, that for SQL access user must have relevant SQL privileges (SELECT on form table).

## Query types

It's a second REST (not URL) parameter in `form/objects/:class/:query` request, it determines the contents of query between SELECT and FROM.
Currently new query types can be specified as parameters in `Form.REST.Objects` class. 

| Query    |  Description         |
|----------|----------------------|
| all      | all information      |
| info     | displayName and id   |
| infoclass| displayName, id, class|

You can define your own class with queries. To define your own query named `myq`:
  1. Define a class 
  2. Define there a `MYQ` parameter or `queryMYQ` class method. Parameter takes precedence over the method. 
  3. Method or param must return the part of SQL query between SELECT and FROM
  4. Execute in a terminal: `Do ##class(For.Settings).setSetting("queryclass", YourClassName)`

Method signature is: `ClassMethod queryMYQ(class As %String) As %String` 

You can define a class-specific query. To define your own class query named `myq`:
  1. Define a `queryMYQ` class method in your form class
  2. Method signature is: `ClassMethod queryMYQ() As %String` 
  3. Method must return the part of SQL query between SELECT and FROM

RESTForms looks for a query named  `myq` in the following paths (till first hit):
  1.  Class method `queryMYQ` in your form class
  2.  Parameter `MYQ` in your query class
  3.  Class method `queryMYQ` in your query class
  4.  Parameter `MYQ` in `Form.REST.Objects` class
  5.  Class method `queryMYQ` in `Form.REST.Objects` class

## URL arguments:

all arguments are optional.

| Argument | Sample Value       | Description     |
|----------|--------------------|-----------------|
| size     | 2                  | page size       |
| page     | 1                  | page number     |
| filter   | Value+contains+W   | WHERE clause    |
| orderby  | Value+desc         | ORDER BY clause |
| collation| SQLUPPER           | COLLATION clause|

## ORDER BY clause

Value can be: `Column` or `Column+desc`. Column is a column from the sql table or a colum number.

## WHERE clause 

In a format: `Column+condition+Value`. 

Several conditions are possible: `Column+condition+Value+Column2+condition2+Value2`.

If Value contains white spaces replace them with tabs before sending to the server.

Conditions:

| URL            | SQL         |
|----------------|-------------|
| neq            | !=          |
| eq             | =           |
| gte            | >=          |
| gt             | >           |
| lte            | <=          |
| lt             | <           |
| startswith     | %STARTSWITH |
| contains       | [           |
| doesnotcontain | '[          |
| in             | IN          |

## COLLATION clause

In a format: `collation=SQLUPPER' or `collation=EXACT`. 
Forces specified collation on WHERE clause. If omitted, default collation is used.

## Custom Queries

Custom query allows user code to determine the full content of the query. 
Query name is passed as a second REST (not URL) parameter in `form/objects/:class/custom/:query` request, URL parameters besides `size` and `page` are unavailable. Your method must parse all other url parameters. 

To define your own custom query named `myq`:
  1. Define a `customqueryMYQ` class method in your form class
  2. Method signature is: `ClassMethod customqueryMYQ() As %String` 
  3. Method must return a valid SQL query
  
# Settings

You can setup several settings.
Set them via `Write ##class(For.Settings).setSetting(Setting, Value)`.

| Setting     | Values             | Description     |
|-------------|--------------------|-----------------|
| queryclass  | Caché class name   | Class with your own queries. See Query types for details.|
| fileDir     | Directory path     | Directory for files. Defaults to MGR\DB directory|
| timezone    | ignore, utc        | Affects how timestamps are converted for a client. UTC has Z on end, ignore does not|


#Samples

See `Form.Test.Simple` and other forms in `Form.Test` package for samples. 
To remove test forms permanently from your local repository 
  1. Enter `Form\Test` directory from git bash
  2. `git update-index --assume-unchanged $(git ls-files | tr '\n' ' ')`
  3. Delete `Form\Test` directory

# Object creation

For `Form.Test.Simple`.

`POST http://localhost:57772/forms/form/object/Form.Test.Simple`
Headers must contain `Content-Type` and (probably) authorization

```
Content-Type: application/json
Authorization: Basic Base64String
```  

Body:
```
{
        "_class":"Form.Test.Simple",
        "text":3
}
```

# Object update

For `Form.TestForm`.

`PUT http://localhost:57772/forms/form/object/Form.Test.Simple`

Body:
```
{
        "_class":"Form.Test.Simple",
        "_id":3,
        "text":4444
}
```

