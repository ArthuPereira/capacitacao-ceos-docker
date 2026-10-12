# Laboratório 2: Ambiente Completo com FastAPI, PostgreSQL e Docker Compose

Bem-vindo! Aqui você vai vivenciar um cenário real de um projeto da Ceos Jr: subindo uma aplicação backend completa (API em Python com FastAPI + banco de dados PostgreSQL) utilizando **Docker Compose**, testando o CRUD, inspecionando o banco por dentro do container e comprovando a persistência com volumes.

> A API utilizada foi adaptada da [capacitação em FastAPI da Ceos](https://github.com/ernestogo99/fastAPI).

---

## Estrutura do Laboratório

Dentro de `/labs/fastAPI`:
```
/fastAPI
├── docker-compose.yaml  # Orquestra os serviços (api e postgres)
├── Dockerfile           # Instruções de build da API com Python e uv
├── .dockerignore        # Arquivos ignorados no build
├── .env.example         # Modelo de variáveis de ambiente
├── pyproject.toml       # Dependências e manifesto da aplicação Python
├── lab-02.md            # Este roteiro
└── src/                 # Código-fonte da aplicação FastAPI
```

---

## Etapa 1: Configuração das Variáveis de Ambiente

Antes de subir qualquer serviço, precisamos definir as credenciais e configurações de conexão.

1. **Copie o arquivo de exemplo para `.env`:**
   ```bash
   cp .env.example .env
   ```

2. **Entenda as variáveis:**
   Abra o `.env`. Ele contém:
   - `DATABASE_URL`: URL assíncrona de conexão (`postgresql+asyncpg://myuser:secret@postgres:5432/mydatabase`).  
     *Repare no host `postgres`: a API encontra o banco pelo nome do serviço na rede do Compose!*
   - `DB_NAME`, `DB_USERNAME`, `DB_PASSWORD`: Credenciais utilizadas pelo PostgreSQL.
   - `DB_PORT`: Porta exposta para a sua máquina host.

> **Dica Prática de Troubleshooting:** Se você já tiver um PostgreSQL rodando nativamente no seu computador na porta `5432`, altere no `.env` para `DB_PORT=5433`. A API continuará funcionando normalmente porque a comunicação entre os containers ocorre pela rede interna na porta `5432`!

---

## Etapa 2: Subindo o Ambiente com Docker Compose

Com o `.env` criado, podemos iniciar toda a infraestrutura com um único comando:

1. **Inicie os serviços:**
   ```bash
   docker compose up -d --build
   ```

2. **O que o Compose faz nos bastidores?**
   - Cria a rede interna `fastapi_default` e o volume nomeado `postgres_data`.
   - Baixa a imagem do `postgres:16.9` e inicia o banco.
   - Executa o healthcheck do PostgreSQL (`pg_isready`) até o banco estar pronto para receber conexões.
   - Constrói a imagem da API via `Dockerfile` com o gerenciador `uv`.
   - Inicia o container da API (`api-fast-api`) apenas após a confirmação de que o banco está saudável.

3. **Verifique o status:**
   ```bash
   docker compose ps
   ```
   Você verá o `postgres` com status `Up (healthy)` e o `api-fast-api` como `Up`.

4. **Acompanhe os logs da API:**
   ```bash
   docker compose logs -f api
   ```
   *(Pressione `Ctrl + C` para sair dos logs).*

---

## Etapa 3: Validando as Operações de CRUD

Com a API no ar, vamos testar o fluxo de criação e consulta de usuários:

### Opção A: Pela Documentação Interativa (Swagger UI)
Abra no seu navegador: `http://localhost:8000/docs`

Você pode testar cada rota diretamente pela interface clicando em **"Try it out"** e **"Execute"**.

### Opção B: Pelo Terminal via `curl`
No seu terminal, teste os seguintes comandos:

1. **Criar um novo usuário (`POST`):**
   ```bash
   curl -X POST http://localhost:8000/users/ \
     -H "Content-Type: application/json" \
     -d '{"name": "Arthur Silva", "email": "arthur@ceos.com"}'
   ```
   *Resposta esperada: JSON com o `id`, `name`, `email` e `created_at` gerados.*

2. **Listar todos os usuários (`GET`):**
   ```bash
   curl http://localhost:8000/users/
   ```

3. **Buscar usuário por ID (`GET`):**
   ```bash
   curl http://localhost:8000/users/1
   ```

---

## Etapa 4: Live Reload em Desenvolvimento

Repare no `docker-compose.yaml`:
```yaml
    volumes:
      - ./src:/app/src
```
Esse **Bind Mount** sincroniza a pasta local `src` com a pasta `/app/src` do container. Como o Uvicorn foi iniciado com a flag `--reload`, qualquer edição que você fizer no código é recarregada instantaneamente!

**Teste:**
Abra o arquivo [`src/main.py`](./src/main.py) e adicione uma rota raiz de boas-vindas:
```python
@app.get("/")
def home():
    return {"message": "Bem-vindo à API da Ceos Jr com Docker!"}
```
Salve o arquivo e acesse `http://localhost:8000/` no navegador. A alteração entra em vigor imediatamente, sem rebuildar a imagem!

---

## Etapa 5: Inspecionando o PostgreSQL por Dentro do Container

Como desenvolvedor, muitas vezes precisamos consultar o banco diretamente para depurar queries ou dados. Podemos abrir o cliente interativo `psql` direto no container do banco:

```bash
docker compose exec postgres psql -U myuser -d mydatabase
```

No prompt do PostgreSQL (`mydatabase=#`), execute:

1. **Listar as tabelas criadas pelo SQLAlchemy:**
   ```sql
   \dt
   ```
2. **Consultar os registros na tabela:**
   ```sql
   SELECT * FROM users;
   ```
   *Você verá a linha correspondente ao usuário cadastrado na Etapa 3!*
3. **Sair do psql:**
   ```sql
   \q
   ```

---

## Etapa 6: O Teste Definitivo da Persistência de Dados

Para comprovar que o volume nomeado realmente protege os dados contra o encerramento do container:

1. **Derrube os containers:**
   ```bash
   docker compose down
   ```

2. **Suba os containers novamente:**
   ```bash
   docker compose up -d
   ```

3. **Consulte os usuários novamente:**
   ```bash
   curl http://localhost:8000/users/
   ```
   *Seus dados continuam lá! O volume `postgres_data` preservou o banco de dados mesmo após a destruição do container.*

---

## Finalizando e Limpeza

Para concluir a prática e remover todos os recursos criados (incluindo os dados do volume para não ocupar espaço no disco):

```bash
docker compose down -v
```
*(A flag `-v` remove os volumes nomeados gerenciados pelo Compose).*

<div align="center">
    <a href="../../docs/07-compose.md">←Voltar</a>
    <a href="../../docs/08-boas-praticas.md">Próximo→</a>
</div>