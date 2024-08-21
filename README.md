#Para transformar esse projeto em um repositório no GitHub, o fluxo sugerido seria:

1. **Criar o Repositório no GitHub**: Crie um repositório no GitHub, onde você poderá armazenar o código.
   
2. **Estrutura do Repositório**:
   - A raiz do repositório conterá o `Dockerfile` e o `docker-compose.yml`.
   - O código PHP, incluindo os arquivos `create.php`, `delete.php` e `db.php`, ficará dentro de uma pasta chamada `app` (ou similar).

Aqui está o passo a passo para organizar e subir o código no GitHub:

### Estrutura de Pastas

```bash
├── app/
│   ├── create.php
│   ├── delete.php
│   ├── db.php
│   ├── index.php (se houver)
│   └── ... (outros arquivos PHP)
├── Dockerfile
├── docker-compose.yml
└── README.md
```

### 1. **Dockerfile**

Coloque o seguinte conteúdo na raiz do repositório, no arquivo `Dockerfile`:

```Dockerfile
# Utilizar uma imagem leve do PHP com Apache
FROM php:8.1-apache

# Instalar extensões necessárias, como mysqli para a conexão com o MySQL
RUN docker-php-ext-install mysqli

# Copiar os arquivos da aplicação para o diretório padrão do Apache
COPY ./app /var/www/html/

# Configurar o Apache para aceitar conexões de qualquer origem
EXPOSE 80

# Definir limite de memória no PHP
RUN echo "memory_limit = 256M" > /usr/local/etc/php/conf.d/memory_limit.ini

# Comando para iniciar o Apache
CMD ["apache2-foreground"]
```

### 2. **docker-compose.yml**

Também na raiz do repositório, crie o arquivo `docker-compose.yml` com o seguinte conteúdo:

```yaml
version: '3.7'

services:
  web:
    build: .
    ports:
      - "80:80"
    depends_on:
      - db
    deploy:
      resources:
        limits:
          memory: 300M
          cpus: '1.0'

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: crud_db
      MYSQL_USER: jesse
      MYSQL_PASSWORD: 12345678
    volumes:
      - db_data:/var/lib/mysql
    deploy:
      resources:
        limits:
          memory: 500M
          cpus: '1.0'
  
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
    deploy:
      resources:
        limits:
          memory: 200M
          cpus: '4.0'

volumes:
  db_data:
```

### 3. **Arquivos PHP**

Todos os arquivos PHP, como `create.php`, `delete.php`, e `db.php`, devem ser colocados na pasta `app/`. Esse será o diretório raiz da sua aplicação, que será copiado para dentro do container Docker na etapa de build.

### 4. **README.md**

Crie um arquivo `README.md` no repositório, com instruções para execução. Por exemplo:

```markdown
# Sistema CRUD com Docker

Este é um sistema CRUD desenvolvido em PHP, utilizando Docker para a containerização.

## Requisitos

- Docker
- Docker Compose

## Como executar

1. Clone o repositório:
   ```bash
   git clone https://github.com/seu-usuario/seu-repositorio.git
   cd seu-repositorio
   ```

2. Execute o Docker Compose:
   ```bash
   docker-compose up -d
   ```

3. Acesse o sistema:
   - Sistema CRUD: `http://localhost`
   - Adminer (gerenciador de banco de dados): `http://localhost:8080`

## Notas

- Certifique-se de que o banco de dados está configurado corretamente. A tabela precisa ser criada manualmente.
```

### 5. **Subindo para o GitHub**

No terminal, na pasta do projeto, execute os seguintes comandos:

```bash
git init
git add .
git commit -m "Primeiro commit - Sistema CRUD com Docker"
git branch -M main
git remote add origin https://github.com/seu-usuario/seu-repositorio.git
git push -u origin main
```

### Conclusão

Depois de seguir esses passos, o repositório estará no GitHub, pronto para ser clonado e executado em qualquer máquina que tenha Docker e Docker Compose instalados.
