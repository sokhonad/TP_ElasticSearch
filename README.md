# TP_ElasticSearch
# 2.Importation de données

```curl -H "Content-Type: application/json" -XPOST "http://localhost:9200/mapping/test" -d @mapping.json```
----
```curl -XPUT -H "Content-Type: application/json" localhost:9200/_bulk --data-binary @movies.json```

# 3.En utilisant les requêtes simples http : 

# 3.1

http://localhost:9200/movies/_search?q=Ridley+Scott

les scores associés à ces films: 10.3528595

// 3.2
http://localhost:9200/movies/_search?q=Ridley+Scott:fields.directors

//3.3

http://localhost:9200/movies/_search?q=Ridley+Scott AND fields.actors:Russell+Crowe


//4.1
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search' -d'
{
  "size":10000,
  "query":
      {"query_string": 
         {
        "query":"Ridley Scott"
      }
    }
}'
