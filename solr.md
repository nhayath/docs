- Install the latest Java Runtime
- download latest `solr.tgz` file from `solr` official website
- untar, install & start
```
$ tar -xzf solr.tgz
$ cd solr
$ bin/solr start
$ bin/solr status
$ bin/solr stop
```

- Point browser to `http://localhost:8083` for admin dashboard
- create a new collection
```
$ bin/solr create -c <collection-name>
```
- delete a collection
```
$ bin/solr delete -c <collection-name>
```

- Set min and max memory
```
Open solr.in.cmd in any text editor (path: bin/solr.in.cmd)

Add the set SOLR_JAVA_MEM=-Xms1g -Xmx1g ( it will set the minimum and maximum Java heap memory by 1 GB. however you will only able the see 981.38 MB in solr dashboard)

save the solr.in.cmd file and restart the solr
````

#### CRUD

```
- create
http://localhost:8983/solr/<collection_name>/update?commitWithin=1000
{
	[ 
		{ "name": "John", "age": 30, "cars": "BMW" }, 
		{ "name": "Harry", "age": 30, "cars": "BMW" }, 
		{ "name": "Pinku", "age": 30, "cars": "BMW" } 
	]
}
```
- update

- delete

```
# delete a specific document:

$ bin/post -c localDocs -d "<delete><id>SP2514N</id></delete>"

# delete all documents, you can use "delete-by-query" command like:

bin/post -c localDocs -d "<delete><query>*:*</query></delete>"

# delete the whole collection
$ bin/solr delete -c items
```

#### Custom Configuration
- Copy default config to desired config folder
```
$ cd <solr-dir>/server/solr/configsets
$ cp _default to <new-config-folder-name>
```
- make necessary changes
- use example config from `docs/configs/solr` 

#### JSON Api
- Get an item by `id`
```
curl http://localhost:8983/solr/<col>/get?id=book1
```

#### Faceted Search
- a sample faceted search 
- returns `gender`, `categories` & `stores`
```
{
	"query": "*:*",
	"limit": 0,
	"facet": {
		"gender": {
			"type": "terms",
			"field": "gender"
		},
		"categories": {
			"type": "terms",
			"field": "category_id",
			"domain": { "excludeTags": "cat"}
		},
		
		"stores": {
			"type": "terms",
			"field": "store_id",
			"limit": 5
		},
		
		"price_range": {
			"type": "range",
			"field": "price",
			"start": 0,
			"end": 10000,
			"gap": 1000,
			"other": "after",
			"hardened": true
		}
	}
}
```

- filter where `category: 32 or 26` & `store: 34 or 40`
```
{
	"query": "*:*",
	"limit": 0,
	"filter": ["{!tag=tagCategory}category_id:(32 26)", "{!tag=tagStore}store_id:(34 40)"],
	"facet": {
		"gender": {
			"type": "terms",
			"field": "gender"
		},
		"categories": {
			"type": "terms",
			"field": "category_id",
			"domain": { "excludeTags": "tagCategory" }
		},
		
		"stores": {
			"type": "terms",
			"field": "store_id",
			"limit": 5,
			"domain": { "excludeTags": "tagStore" }
		},
		
		"price_range": {
			"type": "range",
			"field": "price",
			"start": 0,
			"end": 10000,
			"gap": 1000,
			"other": "after",
			"hardened": true
		}
	}
}
```

#### Tutorials
- http://yonik.com/solr-tutorial/
- http://www.solrtutorial.com/solr-in-5-minutes.html
- http://lucene.apache.org/solr/guide/7_2/solr-tutorial.html
- facets
	- https://lucidworks.com/2009/09/02/faceted-search-with-solr/
	- http://yonik.com/multi-select-faceting/

