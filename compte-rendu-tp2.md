---
noteId: "5543fe108cd711ed98af298e2d8958f9"
tags: []

---

curl -u admin:admin --insecure -XGET "https://localhost:9200/movies/_search?pretty" -H 'Content-Type: application/json' -d '{
  "query": {
    "term": {
      "genres": "Action"
    }
  }
}'

curl -u admin:admin --insecure -XGET "https://localhost:9200/movies/_search?pretty" -H 'Content-Type: application/json' -d '{
  "query": {
    "term": {
      "genres.keyword": "Action"
    }
  }
}'

curl -u admin:admin --insecure -XGET "https://localhost:9200/movies/_search?pretty" -H 'Content-Type: application/json' -d '{
  "query": {
    "term": {
      "rank": "1295"
    }
  }
}'

curl -u admin:admin --insecure -XGET "https://localhost:9200/movies/_search?pretty" -H 'Content-Type: application/json' -d '{
  "query": {
    "terms": {
      "rank": ["1295", "1296"]
    }
  }
}'

curl -u admin:admin --insecure -XGET "https://localhost:9200/movies/_search?pretty" -H 'Content-Type: application/json' -d '{
  "query": {
    "range": {
        "rank":{
                "gte": "1290",
                "lte": "1299"
        }
    }
  }
}'

curl -u admin:admin --insecure -XGET "https://localhost:9200/movies/_search?pretty" -H 'Content-Type: application/json' -d '{
  "query": {
    "match": {
      "title": "Justice League"
    }
  }
}'

Match regarde les titres qui contient un des mots ou les deux

curl -u admin:admin --insecure -XGET "https://localhost:9200/movies/_search?pretty" -H 'Content-Type: application/json' -d '{
 "query": {
   "match": {
     "title": {
       "query": "Justice League",
       "fuzziness": "AUTO",
       "fuzzy_transpositions": true,
       "operator":  "or",
       "minimum_should_match": 2,
       "boost": 1
     }
   }
 }
}'

minimum_should_match:  c'est le minimum de résultat qu'on veux
fuzzines: 
fuzzy_transpositions:
