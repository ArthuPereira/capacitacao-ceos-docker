# Docker Compose

## Quando o número de containers cresce
Já vimos até agora a visão inicial de como lidar com containers, sabemos: buildar uma imagem, subir um container, definir mapeamento de portas e criar volumes. Mas junto dessa habilidade aumentamos o número de containers que gerenciamos, mas como manter isso para 3, 5 ou *até mais containers?*

Pense em como seria fazer `docker build`, `docker volume`, `docker run` várias e várias vezes, isso sem você esquecer de subir nenhum container. Esse cenário caótico é resolvido com o ***Docker Compose***, uma ferramenta que permite definir um arquivo com a descrição do estado final que desejamos, e o docker engine se encarrega de fazer tudo descrito nesse arquivo.

## Usando o Compose
O arquivo de definição que resolve isso é o `docker-compose.yml`, e junto dele temos um conjunto de comandos que fazem as mesmas operações que já vimos até então, mas agora para vários containers. Os comandos são:
- `docker compose build` para buildar as imagens de todos os serviços descritos
- `docker compose up` que inicia todos os serviços
- `docker compose stop` que encerra todos os serviços
- `docker compose ps` para listar todos os containers que o compose iniciou

> A instalação do Compose pode ser feita via gerenciadores de pacotes como o apt para ambiente Ubuntu ou através do Docker Desktop.

Para entendermos melhor como usar o compose vamos dissecar um arquivo de exemplo:
```yaml
services:
  api:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
      - redis
    environment:
      - DATABASE_URL=postgresql://postgres:senha@db:5432/meu_app
      - REDIS_URL=redis://redis:6379

  db:
    image: postgres:16
    volumes:
      - dados_db:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=senha
      - POSTGRES_DB=meu_app

  redis:
    image: redis:alpine

volumes:
  dados_db:
```

Agora, pedaço por pedaço:

```yaml
services:
  api:
    build: .
```

Cada bloco dentro de services é um container que o compose vai gerenciar, aqui chamamos de api, mas o nome é livre, é só como você vai identificar esse serviço no resto do arquivo. O `build: .` substitui o `docker build -t alguma-coisa .` que você já roda na mão.

---

```yaml
ports:
  - "3000:3000"
```

Isso é o mesmo `-p 3000:3000` que já usamos no docker run para conectar um banco ou servir uma página, declaramos a porta do nosso PC à esquerda e a do container à direita.

---

```yaml
depends_on:
  - db
  - redis
```

Isso não tem um equivalente direto no docker run, é algo que só faz sentido quando você tem múltiplos containers. Ele diz "antes de subir a api, sobe db e redis primeiro". Mas repare que não existe isso na sua rotina atual porque você mesmo, manualmente, já decide a ordem que digita os comandos.

---

```yaml
environment:
  - DATABASE_URL=postgresql://postgres:senha@db:5432/meu_app
  - REDIS_URL=redis://senha@redis:6379
```

Equivalente ao `-e` do docker run. Repare em uma coisa fundamental: no lugar de `localhost`, a URL usa `db` e `redis`, que são exatamente os nomes dos serviços definidos acima.

### O Pulo do Gato: Redes e DNS no Docker Compose
Por que não usamos `localhost` na URL de conexão?
- Dentro do container da API, `localhost` refere-se ao **próprio container da API**, onde não há nenhum banco de dados rodando!
- Ao iniciar, o Docker Compose cria automaticamente uma **rede interna dedicada**.
- O Docker possui um servidor de DNS embutido nessa rede: cada serviço passa a ser encontrado pelo seu próprio nome de serviço (`db`, `redis`, `api`).
- **E as portas em `ports`?** O mapeamento `- "5432:5432"` no banco serve apenas para o **seu computador host** acessar o banco de dados (ex: pelo DBeaver). Entre os containers na rede interna do Compose, a comunicação acontece de forma direta e isolada.

---

```yaml
  db:
    image: postgres:16
    volumes:
      - dados_db:/var/lib/postgresql/data
```

Aqui não tem build, só image, porque a gente não precisa criar essa imagem, só usar a pronta do Docker Hub. Mesma diferença de quando você roda docker run postgres direto, sem Dockerfile. O `volumes` é o mesmo que `-v dados_db:/var/lib/postgresql/data`, mas repare que aqui o volume será anexado, não criado.

---

```yaml
  redis:
    image: redis:alpine
```

Mesma lógica do banco: imagem pronta, sem build. Redis normalmente entra em projetos como cache ou fila, como se trata de um banco em memória ele precisa de menos configurações.

---

```yaml
volumes:
  dados_db:
```

Esse bloco no final, fora de services, é onde o compose declara os volumes nomeados que vão ser usados, é o equivalente ao `docker volume create dados_db` que rodamos antes do docker run do banco de dados. Sem essa declaração aqui, o volume citado lá em cima não existiria.

---

## Comandos Essenciais do Dia a Dia com Compose

No fluxo diário de desenvolvimento, você usará principalmente estes comandos:

```bash
# Iniciar todos os serviços em segundo plano
docker compose up -d

# Reconstruir imagens e subir (útil após instalar dependências novas)
docker compose up -d --build

# Acompanhar logs de todos os serviços (ou de um específico)
docker compose logs -f
docker compose logs -f api

# Executar comandos dentro de um serviço em execução (ex: terminal ou migrations)
docker compose exec api sh

# Reiniciar um serviço específico sem parar os demais
docker compose restart api

# Encerrar e remover containers e redes criadas
docker compose down

# Encerrar e APAGAR também os volumes (reseta o banco de dados do zero!)
docker compose down -v
```

## Usando Docker Compose na prática
Agora que já temos uma base sólida dos conceitos e da prática, chegou a hora de ir para um ambiente real. O [Laboratório 2](../labs/fastAPI/lab-02.md) reúne vários dos conceitos que já vimos em um repositório de uma API Python com FastAPI e PostgreSQL. Vamos lá!

<div align="center">
    <a href="./06-volumes.md">←Voltar</a>
    <a href="./08-boas-praticas.md">Próximo→</a>
</div>