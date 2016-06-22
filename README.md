# Forms
Forms adapter for InterSystems Cache

# Requests

| URL                         | Type   | Description                            |
|-----------------------------|--------|----------------------------------------|
| test                        | GET    | Test request                           |
| logout                      | GET    | End current session                    |
| catalog/info                | GET    | List of all availible catalogs         |
| catalog/members/:class      | GET    | (SQL) Get all members of the catalog   |
| form/info                   | GET    | List of all availible forms            |
| form/info/:form             | GET    | Form metainformation                   |
| form/field/:class           | POST   | Add field to form                      |
| form/field/:class           | PUT    | Modify form field                      |
| form/field/:class/:property | DELETE | Delete  form field                     |
| form/object/:form/:id       | GET    | Retrieve form object                   |
| form/object/:form           | POST   | Create form object                     |
| form/object/:form/:id       | PUT    | Update form object from dynamic object |
| form/object/:form           | PUT    | Update form object from object         |
| form/object/:form/:id       | DELETE | Delete form object                     |

For POST/PUT requests see method descriptions for request body samples

#SQL requests

`GET http://localhost:57772/forms/catalog/members/Form.TestRef?size=2&page=1&orderby=Value+desc&filter=(Value+contains+W)`

Url arguments:

| Argument | Sample Value       | Description     |
|----------|--------------------|-----------------|
| size     | 2                  | page size       |
| page     | 1                  | page number     |
| filter   | (Value+contains+W) | WHERE clause    |
| orderby  | Value+desc         | ORDER BY clause |

