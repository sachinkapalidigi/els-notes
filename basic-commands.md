# Elastic search - basic commands

```
GET /_cluster/health

GET /_cat/nodes?v

GET /_cat/indices?v

GET /.kibana/_search
{
  "query": {
    "match_all": {}
  }
}

GET /_cat/shards?v
```

Create an index

```
PUT /pages

DELETE /pages
```

Create index with settings

```
PUT /products
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}
```

Add a document to index

```
POST /products/_doc
{
  "name": "Coffee Maker",
  "price": 64,
  "in_stock": 10
}
```

> Id for the document will be auto generated(above)

Update a document - create if not present

```
PUT /products/_doc/100
{
  "name": "Tea Maker",
  "price": 33,
  "in_stock": 88
}
```

> 100 here is the id of the document - if document is not presnt with id = 100, it will be created

Get product with id = 100

```
GET /products/_doc/100
```

Updating a product

```
POST /products/_update/100
{
  "doc": {
    "in_stock": 3
  }
}

POST /products/_update/100
{
  "doc": {
    "tags": ["electronics"]
  }
}
```

> old key values can be update, new key value(s) can be added

Updating by running scripts

```
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock--"
  }
}

POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock = 10"
  }
}

POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock -= params.quantity",
    "params": {
      "quantity": 4
    }
  }
}
```

Multi line scripts

```
POST /products/_update/100
{
  "script": {
    "source": """
      if(ctx._source.in_stock == 0){
        ctx.op = 'noop';
      }
      ctx._source.in_stock--;
    """
  }
}


POST /products/_update/100
{
  "script": {
    "source": """
      if(ctx._source.in_stock == 0){
        ctx.op = 'delete';
      }
      ctx._source.in_stock--;
    """
  }
}
```

> 'noop' - no operation for the document meeting condition
> 'delete' - delete the document meeting condition

Upserts - if id specified is not present - upsert will create new - else runs the script

```
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock++"
  },
  "upsert": {
    "name": "Blender",
    "price": 50,
    "in_stock": 6
  }
}
```

Replace & Delete

```
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 40,
  "in_stock": 5
}

DELETE /products/_doc/101
```

> PUT will completely replace the document with the ID
