# WORDPRESS DOCKER TEMPLATE
Este é um template que deverá ser utilizado na criação de novos repositórios 
em Wordpress.

O intúito é facilitar a vida, porém alguams alterações precisam ser feitas após
a criação do repositório:

### 1. Setar o nome do projeto no arquivo .env
```dotenv
COMPOSE_PROJECT_NAME=project_name
```

### 2. Setar o nome do projeto na parte de baixo deste README.md
```dotenv
# PROJECT_NAME - DOCKER
```

### 3. Ajustar a versão do wordpress
Lista oficial: https://hub.docker.com/_/wordpress?tab=tags&page=1&ordering=last_updated \
O que devemos levar em consideração aqui é: se projeto existe, fixa inicialmente
a versão do projeto, caso contrário, iremos buscar a numeração da última versão \
No arquivo wordpress/Dockerfile
```dockerfile
FROM wordpress:5.7.2-php7.4-apache
```

### 4. Ligar o docker para criar um wordpress vazio
```shell
docker compose up
```

### 5. Criar o submódulo do tema apontando para a pasta themes
Aqui costumamos utilizar o nome do repositório como nome do tema na pasta
   themes
```shell
git submodule add git_repo_url ./wordpress/wp-content/themes/git_repo_name
```

### 6. Opcional - Caso seja um projeto já existente, importar via all in one
Se o projeto for muito grande, podemos remover arquivos de upload

### 7. Gerar dump do banco pelo docker

Este processo garante que o domínio será localhost
```shell
docker compose exec mysql sh -c 'mysqldump -psecret default > /docker-entrypoint-initdb.d/createdb.sql'
```

### 8. Opcional - compactar dump
Caso dump tenha ficado acima de 50MB, compactá-lo com gzip.
Isso definirá se teremos a etapa 2 do README abaixo ou não.
Se mesmo após a compactação ficar muito grande, devemos utilizar um link externo
ou remover registros do banco
```shell
gzip mysql//docker-entrypoint-initdb.d/createdb.sql
```

### 9. Commitar e pushar tudo isso.
```shell
git ...ah, você sabe ;)
```

### 10. Remover o conteúdo deste README daqui pra cima

---
# PROJECT_NAME - DOCKER

## Abrindo o projeto pela primeira vez em ambiente local

### 1. Clone o repositório com recurse-submodules
```shell
git clone project_git_url --recurse-submodules
```

### 2. Descompacte o dump do banco que veio zipado para caber no git
```shell
gunzip mysql/docker-entrypoint-initdb.d/createdb.sql.gz
```

### 3. Construa/Ligue o ambiente do docker compose estando na raiz do repositório
```shell
docker compose up
```

### 4. Faça checkout em alguma branch na pasta do tema wp-content/themes/theme-name
```shell
git checkout dev
```

Pronto!

Neste momento já deve ser possível acessar o site em http://localhost

## Trabalhando no projeto

Talvez você precise trabalhar com dump do banco de dados para exportar/importar.
Ambos comandos trabalham com o dump em: mysql/docker-entrypoint-initdb.d/createdb.sql.

Exportar:
```shell
docker compose exec mysql sh -c 'mysqldump -psecret default > /docker-entrypoint-initdb.d/createdb.sql'
```

Importar:
```shell
docker compose exec mysql sh -c 'mysql -psecret default < /docker-entrypoint-initdb.d/createdb.sql'
```

### Importante

Arquivos na pasta wp-content deverão ser commitados sim, pois a idéia é facilitar a vida do próximo desenvolvedor ou de
você mesmo no futuro quando pegar este projeto e ele ter o mínimo de assets e plugins para poder trabalhar.

Também devemos commitar o banco na pasta mysql/docker-entrypoint-initdb.d/createdb.sql, ou seja, rodando um comando de
exportação sempre que houver alteração e salvando no git enquanto este dump é menor do que 50MB.
Quando ele atinge este tamanho, devemos removê-lo do git e trabalhar com links externos aqui no README.