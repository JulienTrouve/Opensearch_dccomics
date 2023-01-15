![logo](DC_Comics_logo2.png)

# Personnage de DC Comics

## Caractéristiques 

    Ce dataset liste tout les personnages présent de chaque comics de DC Comics avec les informations suivante:

        Nom
        Url du Wiki (https://dc.fandom.com/)
        ID (identité secréte ou entité publique)
        Align (Vilain ou Gentil)
        Couleurs des yeux
        Couleurs des cheveux
        Le genre (Homme/Femme etc...)
        Alive (En vie ou mort)
        Appearances (Nombre de fois ou le personnage est apparu)
        Date de la premiére apparance du personnage


## Méthode utilisé pour produire une version importable 

    J'ai créer un script js permettant de changer le .csv en fichier .json



## Commande d'importation du dataset dans OS 
docker-compose up dp
curl -u admin:admin --insecure -XGET https://localhost:9200
curl -u admin:admin --insecure -XPUT "https://localhost:9200/dccomics?pretty"
curl -u admin:admin --insecure -XPUT "https://localhost:9200/dccomics/_mapping?pretty" -H 'Content-Type: application/json' -d @perso-dc-comics.json
curl -u admin:admin --insecure -XGET https://localhost:9200/dccomics/_search?pretty

## 5 requêtes utiles à la compréhension du dataset et du mapping et extrait de la sortie produite 

### 1) Tout les personnages ce nommant Batman
    curl -u admin:admin --insecure -XGET "https://localhost:9200/dccomics/_search?pretty" -H 'Content-Type: application/json' -d '{
        "query": {
            "term": {
            "name.keyword": "Batman (Bruce Wayne)"
            }
        }
    }'

### 2) Affiche les enregistrements qui contient l'un des mots de la chaine de caractére rentré ici, cela affiche tout les personnages qui se nomme Jason
    curl -u admin:admin --insecure -XGET "https://localhost:9200/dccomics/_search?pretty" -H 'Content-Type: application/json' -d '{
        "query": {
            "match": {
                "name": {
                "query": "Jason",
                "fuzziness": "AUTO",
                "fuzzy_transpositions": true,
                "operator":  "or",
                "minimum_should_match": 2,
                "boost": 1
                }
            }
        }
    }'

### 3) Affiche les enregistrements qui contient la phrase rentré, ici on recherche tout les personnages qui ont une identité secrete
    curl -u admin:admin --insecure -XGET "https://localhost:9200/dccomics/_search?pretty" -H 'Content-Type: application/json' -d '{
        "query": {
                "match_phrase": {
                "ID": {
                    "query": "Secret Identity"
                }
                }
        }
    }'
    


## 5 aggrégations utiles à la compréhension du dataset et du mapping et un extrait de la sortie produite 

### 1) Trie le nombre de film par nom en affichant seulement les 10 premiers
    curl -u admin:admin --insecure -XGET "https://localhost:9200/dccomics/_search?pretty" -H 'Content-Type: application/json' -d '{
        "size": 0,
        "aggs": {
                "response_codes": {
                "terms": {
                    "field": "name.keyword",
                    "size": 10
                }
            }
        }
    }'

### 2) distribution par année

    curl -u admin:admin --insecure -XGET "https://localhost:9200/dccomics/_search?pretty" -H 'Content-Type: application/json' -d '{
        "size": 0,
        "aggs": {
                "hist_year_1": {
                "histogram": {
                    "field": "YEAR",
                    "interval": 1
                }
            }
        }
    }'

### 3) Compter le nombre de personnage dont le ALIGN est de Good Characters et Bad Characters
curl -u admin:admin --insecure -XGET "https://localhost:9200/dccomics/_search?pretty" -H 'Content-Type: application/json' -d '{
  "size": 0,
  "aggs": {
    "interactions": {
      "adjacency_matrix": {
        "filters": {
          "grpA": {
            "match": {
              "ALIGN.keyword": "Good Characters"
            }
          },
          "grpB": {
            "match": {
              "ALIGN.keyword": "Bad Characters"
            }
          }
        }
      }
    }
  }
}'