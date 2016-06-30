# Forms
Forms adapter for InterSystems Cache.

# Installation

Import classes and create web app with `Form.REST.Main` broker.

# Requests


| URL                                  | Type   | Description                            |
|--------------------------------------|--------|----------------------------------------|
| test                                 | GET    | Test request                           |
| logout                               | GET    | End current session                    |
| form/info                            | GET    | List of all availible forms            |
| form/info/:class                     | GET    | Form metainformation                   |
| form/field/:class                    | POST   | Add field to form                      |
| form/field/:class                    | PUT    | Modify form field                      |
| form/field/:class/:property          | DELETE | Delete  form field                     |
| form/object/:class/:id               | GET    | Retrieve form object                   |
| form/object/:class                   | POST   | Create form object                     |
| form/object/:class/:id               | PUT    | Update form object from dynamic object |
| form/object/:class                   | PUT    | Update form object from object         |
| form/object/:class/:id               | DELETE | Delete form object                     |
| form/objects/:class/:query           | GET    | (SQL) Get all members for the form by query|
| form/file/:class/:id/:property       | POST   | Add files to this property             |
| form/file/:class/:id/:property       | DELETE | Delete all files from this property    |
| form/file/:class/:id/:property/:name | DELETE | Delete one file from property          |
| form/file/:class/:id/:property/:name | GET    | Download one file from property        |

For POST/PUT requests see method descriptions for request body samples

# SQL requests

`GET http://localhost:57772/forms/form/objects/Form.TestForm/info?size=2&page=1&orderby=text`
`GET http://localhost:57772/forms/form/objects/Form.TestForm/all?orderby=text+desc`

## Query types

It's a second REST (not URL) parameter in `form/objects/:class/:query` request, it determines the contents of query between SELECT and FROM.
Currently new query types can be specified as parameters in `Form.REST.Objects` class. 

| Query    |  Description         |
|----------|----------------------|
| all      | all information      |
| info     | displayName and id   |

## URL arguments:

| Argument | Sample Value       | Description     |
|----------|--------------------|-----------------|
| size     | 2                  | page size       |
| page     | 1                  | page number     |
| filter   | (Value+contains+W) | WHERE clause    |
| orderby  | Value+desc         | ORDER BY clause |

## ORDER BY clause

Value can be: `Column` or `Column+desc`

## WHERE clause 

In a format: `(Column+condition+Value)`. 

Several conditions are possible: `(Column+condition+Value+Column2+condition2+Value2)`.

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
| doesnotcontain | '[           |


#Samples

See `Form.TestForm` and `Form.TestRef` for samples.