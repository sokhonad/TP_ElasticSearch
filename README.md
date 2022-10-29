# TP_ElasticSearch
# 2.Importation de données

----
```curl -H "Content-Type: application/json" -XPOST "http://localhost:9200/mapping/test" -d @mapping.json```

```curl -XPUT -H "Content-Type: application/json" localhost:9200/_bulk --data-binary @movies.json```

### 3.En utilisant les requêtes simples http : 

#### 3.1 Recherchez tous les films de Ridley+Scott : que signifient les scores associés à ces films ?

```curl -XGET http://localhost:9200/movies/_search?q=Ridley+Scott```

les scores associés à ces films est: 10.3528595

#### 3.2 Refaites cette recherche en précisant qu'il s'agit d'un "directors".
```curl -XGET http://localhost:9200/movies/_search?q=Ridley+Scott:fields.directors```

#### 3.3 Modifiez votre requête pour trouver les films de Ridley Scott ayant pour acteur Russell Crowe.

```curl -XGET http://localhost:9200/movies/_search?q=Ridley+Scott AND fields.actors:Russell+Crowe```


#### 4.1
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/movie/_search' -d'
{
  "size":10000,
  "query":
      {"query_string": 
         {
        "query":"Ridley Scott"
      }
    }
}'


POST /customer/_doc/1
{
  "firstname": "Jennifer",
  "lastname": "Walters"
}

