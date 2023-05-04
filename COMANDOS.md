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
            "properties": { "year": {"type": "date" }}
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

### Dados para teste

- Baixar `wget http://media.sundog-soft.com/es7/shakes-mapping.json`
- `curl -H 'Content-Type: application/json' -XPUT 127.0.0.1:9200/shakespeare --data-binary @shakes-mapping.json`
- `wget http://media.sundog-soft.com/es7/shakespeare_7.0.json`
- `curl -H 'Content-Type: application/json' -XPOST '127.0.0.1:9200/shakespeare/_bulk?pretty' --data-binary @shakespeare_7.0.json`
- `curl -H 'Content-Type: application/json' -XGET '127.0.0.1:9200/shakespeare/_search?pretty' -d '{"query" : {"match_phrase" : {"text_entry":"to be or not to be"}}}'`
