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

### Dados para teste
- Baixar `wget http://media.sundog-soft.com/es7/shakes-mapping.json`
- `curl -H 'Content-Type: application/json' -XPUT 127.0.0.1:9200/shakespeare --data-binary @shakes-mapping.json`
- `wget http://media.sundog-soft.com/es7/shakespeare_7.0.json`
- `curl -H 'Content-Type: application/json' -XPOST '127.0.0.1:9200/shakespeare/_bulk?pretty' --data-binary @shakespeare_7.0.json`
- `curl -H 'Content-Type: application/json' -XGET '127.0.0.1:9200/shakespeare/_search?pretty' -d '{"query" : {"match_phrase" : {"text_entry":"to be or not to be"}}}'`