# Elasticsearch

Os dados são organizados em **JSON**.

### Comandos

- `sudo /bin/systemctl daemon-reload` = recarregar configurações.
- `sudo /bin/systemctl enable elasticsearch.service` = ativar serviço.
- `sudo /bin/systemctl start elasticsearch.service` = iniciar serviço

### Conceitos

- Documentos = dados que você procura. POdem ser mais do que texto. Elasticsearch está estruturado sobre Json. Todo documento tem um ID único e um tipo.
- Índices = potencializa a busca em todos os documentos de uma coleção. Eles contêm um índice invertido, que permite busca em tudo ao mesmo tempo.

Fazendo analogia entre entre Elasticsearch e SQL:

- Cluster = equivale a um banco de dados.
- índices = equivale a tabelas.
- Documentos = equivale a linhas nas tabelas.

### CURL

- `curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/shakespeare/_search?pretty' -d '{ "query": { "match_phrase: { "text_entry": "to be or not to be" }}}'` = requisição curl tipo GET ao elasticsearch.

- `curl -H "Content-Type: application/json" -XPUT '127.0.0.1:9200/movies/movie/109487' -d '{ "genre": ["IMAX", "Sci-Fi"], "title": "Interstellar", "year": 2014 }'` = requisição curl tipo PUT ao elasticsearch.

### Inserir, buscar, criar, remover

- Criar índice:

```json
    put http://127.0.0.1:9200/movies
    {
        "mappings": {
            "properties": {
                "year": {"type": "date" }
            }
        }
    }
```

- Buscar índice:

```json
    get http://127.0.0.1:9200/movies/_mapping
```

- Inserir filme:

```json
    post http://127.0.0.1:9200/movies/_doc/109487
    {
        "genre": ["IMAX", "Sci-Fi"],
        "title": "Interstellar",
        "year": 2014
    }
```

- Atualizar filme parcialmente:

```json
    post http://127.0.0.1:9200/movies/_doc/109487/_update
  {
    "doc": {
        "title": "Interestellar"
    }
  }
```

- Atualizar filme condicionalmente:

```json
    post http://127.0.0.1:9200/movies/_doc/109487?if_seq_no=7&if_primary_term=2
  {
    "doc": {
        "title": "Interstellar Foo"
    }
  }
```

- inserir em lote:

```json
put http://127.0.0.1:9200/movies/_bulk?pretty
{ "create" : { "_index" : "movies", "_id" : "135569" } }
{ "id": "135569", "title" : "Star Trek Beyond", "year":2016 , "genre":["Action", "Adventure", "Sci-Fi"] }
{ "create" : { "_index" : "movies", "_id" : "122886" } }
{ "id": "122886", "title" : "Star Wars: Episode VII - The Force Awakens", "year":2015 , "genre":["Action", "Adventure", "Fantasy", "Sci-Fi", "IMAX"] }

```

- Buscar filmes inseridos:

```json
    get http://127.0.0.1:9200/movies/_search?pretty
```

- Buscar filme por id:

```json
    get http://127.0.0.1:9200/movies/_doc/109487?pretty
```

- Excluir filme:

```json
    delete http://127.0.0.1:9200/movies/_doc/58559
```

- Excluir índice:

```json
    delete http://127.0.0.1:9200/movies
```

### Dados para teste

- Baixar `wget http://media.sundog-soft.com/es7/shakes-mapping.json`
- `curl -H 'Content-Type: application/json' -XPUT 127.0.0.1:9200/shakespeare --data-binary @shakes-mapping.json`
- `wget http://media.sundog-soft.com/es7/shakespeare_7.0.json`
- `curl -H 'Content-Type: application/json' -XPOST '127.0.0.1:9200/shakespeare/_bulk?pretty' --data-binary @shakespeare_7.0.json`
- `curl -H 'Content-Type: application/json' -XGET '127.0.0.1:9200/shakespeare/_search?pretty' -d '{"query" : {"match_phrase" : {"text_entry":"to be or not to be"}}}'`

### Analyzers

- Keyword type mapping = às vezes campos de texto devem ter a correspondência exata. Usar `keyword mapping` em vez de texto.
- Text Type mapping = busca em campos de texto vai retornar qualquer coisa, mesmo que com baixa relevância.
  Depende do `analyzer`, o resultado será sensível a maiúsculas e minúsculas , flexões (`stemmed`), remoção `stopwords`, aplicação de sinônimos, etc.
  Busca com múltiplos termos não precisa ter uma correspondência de todos.

**Exemplos**:

- Busca usando analyzer, busca parcial

```json
    get http://127.0.0.1:9200/movies/_search?pretty
    {
        "query": {
            "match": {
                "title": "Star Trek"
            }
        }
    }
```

- Busca usando analyzer, busca exata

```json
    get http://127.0.0.1:9200/movies/_search?pretty
    {
        "query": {
            "match_phrase": {
                "genre": "sci"
            }
        }
    }
```

**Mapping**:

```json
    put http://127.0.0.1:9200/movies
    {
        "mappings": {
            "properties": {
                "id": {"type": "integer"},
                "year": {"type": "date"},
                "genre": {"type": "keyword"},
                "title": {"type": "text", "analyzer": "english"}
            }
        }
    }
```

### Relacionamentos

Inserção em lote usando **curl**:

- `curl -XPUT 127.0.0.1:9200/_bulk?pretty --data-binary @series.json` = inserção de dados em lote via curl, no qual o nome do arquivo de dados é **@series.json**.

Mapeamento de relacionamento **franquia-file**:

```json
    put http://127.0.0.1:9200/series
    {
        "mappings": {
            "properties": {
                "film_to_franchise": {"franchise": "film"}
            }
        }
    }
```

Busca de filmes por franquia:

```json
get http://127.0.0.1:9200/series/_search?pretty
{
    "query": {
        "has_parent": {
            "parent_type": "franchise",
            "query": {
                "match": {
                    "title": "Star Wars"
                }
            }
        }
    }
}
```

Busca de filme relacionado com franquia:

```json
get http://127.0.0.1:9200/series/_search?pretty
{
    "query": {
        "has_child": {
            "type": "film",
            "query": {
                "match": {
                    "title": "The Force Awakens"
                }
            }
        }
    }
}
```
