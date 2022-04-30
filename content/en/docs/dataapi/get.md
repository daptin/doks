---
title: "Read / Query"
description: "Get by id or search rows in a table"
lead: "Quick start for data apis"
date: 2022-04-`16T13:59:39+01:00
lastmod: 2020-11-16T13:59:39+01:00
draft: false
images: []
menu: 1
docs:
parent: "help"
weight: 110
toc: true
---


- `<server>/api/<tableName>`
- `<server>/api/<tableName>/<referenceId>`

## Parameters

### Select column

`?fields=col1,col2`

Include values for columns col1 and col2. Skip other columns in responsee

### Filter rows by keyword

`?filter=value`

Filter results by searching `value` in indexed label columns in the table

Filter does a lookup in all indexed `label` type columns. See (Query)[#Query] for advanced filtering.

### Query

`?query=[QueryObject, ]`

- QueryObject

  `{"column": "col1", "operator": "is", "value": "value 1"}`

All objects are `AND`ed together in the query

List of [all operators here](#Filtering)

### Include relations

`?included_relations=column_name1,column_name2`

Fetch associated second level row, or asset object and return as part of included objects in the response

### Parameters overview

| Name               |  parameter type          |  default value |  example value                                            |
|--------------------|--------------------------|----------------|-----------------------------------------------------------|
| page[number]       |  integer                 |  1             |  5                                                        |
| page[size]         |  integer                 |  10            |  100                                                      |
| query              |  json base64             |  []            | [{"column": "name", "operator": "is", "value": "england"}] |
| group              |  string                  |  -             |  [{"column": "name", "order": "desc"}]                     |
| included_relations |  comma separated string  |  -             |  user post author                                         |
| sort               |  comma separated string  |  -             |  created_at amount guest_count                            |
| filter             |  string                  |  -             |  england                                                  |

## Read API response format

```json
{
  "links": {
    "current_page": 1,
    "from": 0,
    "last_page": 1,
    "per_page": 10,
    "to": 10,
    "total": 1
  },
  "data": [
    {
      "type": "book",
      "id": "29d11cb3-3fad-4972-bf3b-9cfc6da9e6a6",
      "attributes": {
        "__type": "book",
        "confirmed": 0,
        "created_at": "2018-04-05 15:47:29",
        "title": "book title",
        "name": "book name",
        "permission": 127127127,
        "reference_id": "29d11cb3-3fad-4972-bf3b-9cfc6da9e6a6",
        "updated_at": null,
        "user_id": "696c98d3-3b8b-41da-a510-08e6948cf661"
      },
      "relationships": {
        "author_id": {
          "links": {
            "related": "/api/book/<book-id/author_id",
            "self": "/api/book/<book-id>/relationships/author_id"
          },
          "data": []
        }
      }
    }
  ]
}
```

## Examples

### Curl example

    curl '/api/<entityName>?sort=&page[number]=1&page[size]=10' \
      -H 'Authorization: Bearer <AccessToken>'

### jQuery ajax example

    $.ajax({
        method: "GET",
        url: '/api/<entityName>?sort=&page[number]=1&page[size]=10',
        success: function(response){
            console.log(response.data);
        }
      })

### Node js example

    var request = require('request');

    var headers = {
        'Authorization': 'Bearer <AccessToken>'
    };

    var options = {
        url: '/api/<entityName>?sort=&page[number]=1&page[size]=10',
        headers: headers
    };

    function callback(error, response, body) {
        if (!error && response.statusCode == 200) {
            console.log(body);
        }
    }

    request(options, callback);

### Python example

    import requests

    headers = {
        'Authorization': 'Bearer <AccessToken>',
    }

    params = (
        ('sort', '-created_at'),
        ('page[number]', '1'),
        ('page[size]', '10'),
    )

    response = requests.get('http://localhost:6336/api/laptop', headers=headers, params=params)

### PHP example

    <?php
    include('vendor/rmccue/requests/library/Requests.php');
    Requests::register_autoloader();
    $headers = array(
        'Authorization' => 'Bearer <AccessToken>'
    );
    $response = Requests::get('http://localhost:6336/api/laptop?sort=&page[number]=1&page[size]=10', $headers);

## Filtering

Used to search items in a table that match the filter's conditions. Filters follow the
syntax `query=[{"column": "<column_name>", "operator": "<compare-operator>", "value":"<value>"}]`

| Daptin operator|  SQL compare operator  |
|----------------|------------------------|
| contains       |  like  '%\<value>'     |
| not contains   |  not like  '%\<value>' |
| is             |  =                     |
| is not         |  !=                    |
| before         |  <                     |
| less then      |  <                     |
| after          |  >                     |
| more then     |  >                     |
|  any of        |  in                    |
|  none of       |  not in                |
|  is empty      |  is null               |
|  is not empty  |  is not null           |

### Example

    curl '/api/world?query=[{"column": "is_hidden", "operator": "any of", "value":"1,0"}] \
      -H 'Authorization: Bearer <AccessToken>'

## Create

!!! example "Curl Example"

```curl
curl '/api/<EntityName>'
-H 'Authorization: Bearer <Token>'
--data-binary '{
"data": {
"type": "<EntityName>",
"attributes": {
"name": "name"
}
}
}'
```

!!! example "Nodejs example"

```nodejs
var request = require('request');

     var headers = {
         'Authorization': 'Bearer <Token>',
     };

     var dataString = '{
                         "data": {
                             "type": "<EntityName>",
                             "attributes": {
                                 "name": "name"
                             }
                         }
                       }';

     var options = {
         url: '/api/<EntityName>',
         method: 'POST',
         headers: headers,
         body: dataString
     };

     function callback(error, response, body) {
         if (!error && response.statusCode == 200) {
             console.log(body);
         }
     }

     request(options, callback);
     ```


!!! example "Python example"
```python
import requests

     headers = {
         'Authorization': 'Bearer <Token>',
     }

     data = '{
                 "data": {
                     "type": "<EntityName>",
                     "attributes": {
                         "name": "name"
                     }
                 }
             }'

     response = requests.post('/api/<EntityName>', headers=headers, data=data)
     ```


!!! example "PHP Example"
```php
<?php
include('vendor/rmccue/requests/library/Requests.php');
Requests::register_autoloader();
$headers = array(
'Authorization' => 'Bearer <Token>',
);
$data = '{
"data": {
"type": "<EntityName>",
"attributes": {
"name": "name"
}
}
}';
$response = Requests::post('/api/<EntityName>', $headers, $data);
```

## Update

!!! example "Curl example"

```curl
curl '/api/<EntityName>/<ReferenceId>' \
-X PATCH \
-H 'Authorization: Bearer <Token>' \
--data-binary '{
"data": {
"type": "<EntityName>",
"attributes": {
"confirmed": false,
"email": "update@gmail.com",
"name": "new name",
"password": ""
},
"id": "<ReferenceId>"
}
}'
```

!!! example "Nodejs example"

```nodejs
var request = require('request');

    var headers = {
        'Authorization': 'Bearer <Token>'
    };

    var dataString = '{
                      "data": {
                          "type": "<EntityName>",
                          "attributes": {
                              "confirmed": false,
                              "email": "update@gmail.com",
                              "name": "new name",
                              "password": "",
                              "permission": 127127127,
                          },
                          "relationships": {
                              "relation_name": [ ... ]
                          },
                          "id": "<ReferenceId>"
                      }
                    }';

    var options = {
        url: '/api/<EntityName>/<ReferenceId>',
        method: 'PATCH',
        headers: headers,
        body: dataString
    };

    function callback(error, response, body) {
        if (!error && response.statusCode == 200) {
            console.log(body);
        }
    }


    request(options, callback);
    ```



!!! example "Python example"
```python
import requests

    headers = {
        'Authorization': 'Bearer <Token>',
    }

    data = '{
              "data": {
                  "type": "<EntityName>",
                  "attributes": {
                      "confirmed": false,
                      "email": "update@gmail.com",
                      "name": "new name",
                      "password": "",
                      "permission": 127127127,
                  },
                  "relationships": {
                      "relation_name": [ ... ]
                  },
                  "id": "<ReferenceId>"
              }
            }'

    response = requests.patch('/api/<EntityName>/<ReferenceId>', headers=headers, data=data)
    ```


!!! example "PHP example"
```php
<?php
include('vendor/rmccue/requests/library/Requests.php');
Requests::register_autoloader();
$headers = array(
'Authorization' => 'Bearer <Token>'
);
$data = '{
"data": {
"type": "<EntityName>",
"attributes": {
"confirmed": false,
"email": "update@gmail.com",
"name": "new name",
"password": "",
"permission": 127127127,
},
"relationships": {
"relation_name": [ ... ]
},
"id": "<ReferenceId>"
}
}';
$response = Requests::patch('/api/<EntityName>/<ReferenceId>', $headers, $data);
```
