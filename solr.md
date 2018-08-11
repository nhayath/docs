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
```

#### CRUD
- create
```
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
```
# partial update
http://localhost:8983/solr/items/update?commitWithin=1000 -d 
[{
	"id": "5ac0e9ab5726a9f3d3df500e",
	"price": {"set": 1000}
}]
```

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
- add synanoms: edit `synonyms.txt` in config directory
- tuts: https://www.elastic.co/guide/en/elasticsearch/reference/master/analysis-synonym-graph-tokenfilter.html
```
# pants to trouser
pant => trouser
denim => jeans
```

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
#### Advanced facet query
```
{
	"query": "*:*",
	"filter": ["{!tag=tagStore}store_id:(4 OR 6 OR 2)", "{!tag=tagCategory}category_id:(7 OR 9)", "{!tag=tagPrice}price: [2000 TO *]"],
	"sort": "discountPct desc",
	"facet": {
		"gender": {
            "type": "terms",
            "field": "gender",
            "domain": { "excludeTags": "tagGender" }
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
            "end": 500000,
            "gap": 500000,
            "hardend": true,
            "domain": { "excludeTags": "tagPrice" },
            "facet": {
            	"min": "min(price)",
            	"max": "max(price)"
            }
             
        }
            
	}
}
```

##### Incrase open file limits
```
# maximum capability of system
user@ubuntu:~$ cat /proc/sys/fs/file-max
708444

# available limit
user@ubuntu:~$ ulimit -n
1024

# To increase the available limit to say 200000
user@ubuntu:~$ sudo vim /etc/sysctl.conf

# add the following line to it
fs.file-max = 200000

# run this to refresh with new config
user@ubuntu:~$ sudo sysctl -p

# edit the following file
user@ubuntu:~$ sudo vim /etc/security/limits.conf

# add following lines to it
* soft     nproc          200000    
* hard     nproc          200000   
* soft     nofile         200000   
* hard     nofile         200000
root soft     nproc          200000    
root hard     nproc          200000   
root soft     nofile         200000   
root hard     nofile         200000

# edit the following file
user@ubuntu:~$ sudo vim /etc/pam.d/common-session

# add this line to it
session required pam_limits.so

# logout and login and try the following command
user@ubuntu:~$ ulimit -n
200000

```


#### Tutorials
- http://yonik.com/solr-tutorial/
- http://www.solrtutorial.com/solr-in-5-minutes.html
- http://lucene.apache.org/solr/guide/7_2/solr-tutorial.html
- facets
	- https://lucidworks.com/2009/09/02/faceted-search-with-solr/
	- http://yonik.com/multi-select-faceting/

