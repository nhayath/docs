#### Java
- install latest java
```
sudo add-apt-repository ppa:webupd8team/java
sudo apt update
sudo apt -y install oracle-java9-installer
```

### Installing
- go to http://www.elastic.co/downloads
- and download latest `elasticsearch` & `kibana`

```
sudo dpkg -i elasticsearch
sudo dpkg -i kibana
```

- for automatic startup
```
sudo update-rc.d elasticsearch defaults
```

### Configuring
- configuration file located in `/etc/elasticsearch` directory
- `elasticsearch.yml` - Server settings
- `logging.yml` - logging settings
- Edit `/etc/elasticsearch/elasticsearch.yml` & change
	- node.name & cluster.name
- start elastic server & check
```
sudo service elasticsearch start
curl -X get 'http://localhost:9200'
```
- log
```
# check log
sudo journalctl --unit elasticsearch
# from a specific time
sudo journalctl --unit elasticsearch --since  "2016-10-30 18:17:16"
```

#### kibana
- install
```
sudo dpkg -i kibana.dev
sudo service kibana start
```
- check on `http://localhost:5601`

#### CRUD
```
# post a item, this will generate a unique id
POST /test/items
{
  "name": "Bensharman Sj Shirt",
  "category": 1,
  "store": 1,
  "score": 1200,
  "gender": "men",
  "price": 600,
  "discountPct": 80
}

# search
GET /test/items/_search

# use put to supply your own id
PUT /test/items/1
{
  "name": "Toreo TX Shirt",
  "category": 1,
  "store": 2,
  "score": 1200,
  "gender": "men",
  "price": 1299,
  "discountPct": 75
}

# delete the index
DELETE /test

# bulk insert, single line
POST /test/items/_bulk
{ "index": { "_id": 2}}
{"name": "Jis Cali Shirt","category": 1,"store": 1,"score": 1150, "gender": "men","price": 1149,"discountPct": 80}
{ "index": { "_id": 3}}
{ "name": "Zaap Sinta Shirt", "category": 1,"store": 2,"score": 1048, "gender": "men","price": 1395,"discountPct": 50}
{ "index": { "_id": 4}}
{ "name": "Pinkk Marvella Shirt", "category": 2,"store": 2,"score": 1048, "gender": "women","price": 2595,"discountPct": 60}
{ "index": { "_id": 5}}
{ "name": "Zara Woin Shirt", "category": 2,"store": 1,"score": 1248, "gender": "women","price": 2795,"discountPct": 45}
# search for shirt
# shirt: will search for `shirt`
# blue shirt: will search for `blue` or `shirt`
# ranked by relevance
GET /test/items/_search
{
  "query": {
    "match": {
      "name": "zaap"
    }
  }
}

# phrase search
# blue shirt: matches 'blue shirt' not 'shirt blue'
GET /test/items/_search
{
  "query": {
    "match_phrase": {
      "name": "zaap sinta"
    }
  }
}

# boolean combinations query
# bool [must, shoudl, must_not, filter]
# must: must match both fields
# search: name=shirt and store=1
GET /test/items/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "SHIRT"
          }
        },
        {
          "match": {
            "store": 1
          }
        }
      ]
    }
  }
}

# negation query
# search: skip item that has `zaap` in it's name
GET /test/items/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "match": {
            "name": "ZAAP"
          }
        }
      ]
    }
  }
}

# filtering
# score > 0 & order by score desc
GET /test/items/_search
{
  "query": {
    "range": {
      "score": {
        "gte": 0
      }
    }
  },
  
  "sort": [
    { "score": "desc" }  
  ]
}

# aggregation or attribute filtering
# aggs by gender doesn't work, we need to use mapping and set it as keyword field
GET /test/items/_search
{
  
  "aggs": {
    "stores": {
      "terms": {"field": "store"}
    },
    
    "categories": {
      "terms": {"field": "category"}
    },
    
    "genders": {
      "terms": {
        "field": "gender"
      }
    }
  }
}

# get current mapping
GET /test/_mapping/items

# set mapping, define field types
# delte old records first
# delete the index
DELETE /test
# create index test
PUT /test
# set fields type
PUT /test/_mapping/items
{
  "properties": {
    "category": { "type": "integer"},
    "discountPct": { "type": "integer" },
    "store": { "type": "integer" },
    "price": { "type": "integer" },
    "score": { "type": "integer" },
    "gender": { "type": "keyword" },
    "name": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      }
  }
}

# update
# update item/1 with new price
# to replace use `PUT`
POST /test/items/1/_update
{
  "doc": {
    "price": 1399
  }
}
 
# delete a doc
DELETE /test/items/5
```
