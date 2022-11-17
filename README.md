# TP_ElasticSearch: Sokhona Diakarou et Ndiaye Mamel Alboury 
# 2.Importation de données

----
```sh
curl -H "Content-Type: application/json" -XPOST `http://localhost:9200/mapping/test" -d @mapping.json'
```

```sh
curl -XPUT -H "Content-Type: application/json" `localhost:9200/_bulk --data-binary @movies.json'
```
### 3.En utilisant les requêtes simples http : 

#### 3.1 Recherchez tous les films de Ridley+Scott : que signifient les scores associés à ces films ?

```sh
curl -XGET `http://localhost:9200/movies/_search?q=Ridley+Scott'
```

les scores associés à ces films est: 10.3528595

#### 3.2 Refaites cette recherche en précisant qu'il s'agit d'un "directors".
```sh
curl -XGET `http://localhost:9200/movies/_search?q=Ridley+Scott:fields.directors'
```

#### 3.3 Modifiez votre requête pour trouver les films de Ridley Scott ayant pour acteur Russell Crowe.

```sh 
curl -XGET `http://localhost:9200/movies/_search?q=Ridley+Scott AND fields.actors:Russell+Crowe'
```


## 4. Pour des requêtes plus complexes, il faut passer par le langage d'ElasticSearch, en json. 

4.1.Ré-écrivez les trois requêtes précédentes en json.
 
4.1.1.Recherchez tous les films de Ridley Scott :
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ 
  "size":10000,
  "query": {
        "query_string":
        {
           "query" : "Ridley Scott"
        }
  }
}
'
```
4.1.2.Refaites cette recherche en précisant qu'il s'agit d'un "directors":
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ 
  "query" : {
      "match":{
           "fields.directors":"Ridley Scott"
     }
  }
}
'
```
4.1.3.Modifiez votre requête pour trouver les films de Ridley Scott ayant pour acteur Russell Crowe :
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ 
  "query" : {
      "bool": {
          "must" : [
              {"match": {"fields.directors":"Ridley Scott"}},
              {"match": {"fields.actors":"Russel Crowe"}}
          ]

        }
    }
}
'
```
4.2.Recherchez tous les films de Ridley Scott dont le "rank" est inférieur à 2000
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ "query" : {
     "bool": {
          "must" : [
               { "match" : {"fields.directors" : "Ridley Scott"}},
               { "range" : {"fields.rank" : {"lt" : 2000}}}
           ]
    }
  }
}
'
```
4.3.Recherchez tous les films de Ridley Scott de score (rating) supérieur à 6 et qui soient du genre "Adventure":
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ "query" : {
     "bool": {
          "must" : [
               { "match" : {"fields.directors" : "Ridley Scott"}}, 
               { "match": {"fields.genres" : "Adventure"}},
               { "range" : {"fields.rating" : {"gt" : 6}}}
           ]
    }
  }
}
'
```
## 5.Les agrégations:

5.1.Écrivez une requête json permettant de compter le nombre de films produits par année:
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ 
  "aggs" : {
      "nb_par_annee" : {
          "terms" : {"field" : "fields.year"}
      }
  }
}
'
```
5.2.Donnez le "rank" moyen et la note moyenne des films de Ridley Scott:
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ 
  "query" : {
      "match": {"fields.directors" : "Ridley Scott"}
  },
  "aggs" : {
       "rang_moyen" : {
             "avg" : {"field": "fields.rank"}
        },
       "note_moyenne" : {
            "avg" : {"field":"fields.rank"}
       }
  }
}
'
```

5.3.Donnez la note moyenne de tous les films par année, en les ordonnant par note de la plus grande vers la plus petite:
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ 
  "aggs" : {
      "group_year" : {
          "terms" : {
               "field" : "fields.year",
                "order" : { "note_moyenne" : "desc" }
          },
          "aggs" : {
            "note_moyenne" : {"avg" : {"field" : "fields.rating"}}
          }
      }
  }
}
'
```

5.4.En utilisant "group_range", donnez le nombre de films ayant une note inférieure à 2, entre 3 et 5, et supérieure à 7:
```sh
curl -XGET -H "Content-type: application/json" 'http://localhost:9200/movies/_search?pretty' -d '
{ 
  "aggs" : {
      "group_range" : {
          "range" : {
             "field" : "fields.rating",
             "ranges" : [
                 {"to" : 1.9},
                 { "from" : 3, "to" : 5},
                 { "from" : 7}
              ]
          }
      }
  }
}
'
```
# KIBANA 

## 1. Discover

1.1.Affichez dans Kibana tous les films sortis depuis 1950.
```sh
fields.release_date >= 1950
```

2.2.Restreignez votre recherche aux seuls films de Ridley Scott sur cette période.
```sh
fields.release_date >= 1950 and fields.directors : "Ridley Scott"
```

## Visualisation :

![Générer un "camembert" (régionalisation des exercices)](image.png)

