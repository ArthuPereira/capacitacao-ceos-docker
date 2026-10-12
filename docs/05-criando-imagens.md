# Criando imagens com Dockerfile

## O que é um Dockerfile?
Como já vimos, imagens são instruções de como criar determinado container, mas como definir essas instruções? `Dockerfile` são arquivos que definem a composição de uma imagem. Normalmente na sua estruttura encontramos: uma imagem base, um código que o container vai guardar e executar, além de dependências extras.

## Criando o Dockerfile
Os comandos mais comuns para escrevermos um Dockerfile são:
- `FROM`: informa qual imagem usaremos como base.
- `WORKDIR`: define o diretório de trabalho padrão dentro do container onde os próximos comandos serão executados.
- `COPY`: copia arquivos e pastas da sua máquina para dentro da imagem.
- `RUN`: executa comandos no terminal **durante o build** da imagem (ex: instalar pacotes do sistema ou dependências).
- `CMD`: define o comando padrão executado **quando o container é iniciado**.
- `EXPOSE`: documenta em quais portas de rede o aplicativo dentro do container vai escutar.

> **RUN vs CMD**: Uma dúvida muito comum! O `RUN` acontece durante a construção da imagem (`docker build`), instalando programas e preparando o terreno. Já o `CMD` não roda no build: ele é o comando disparado quando o container finalmente ganha vida (`docker run`).

> Imagens base dizem respeito a uma característica das imagens Docker: elas são baseadas em camadas. Assim é possível "encaixar camadas" para criar novas imagens. As bases mais comuns são SOs e linguagens de programação.

Vamos ver um exemplo para deixar mais claro:
```Dockerfile
FROM ubuntu:24.04

WORKDIR /app

RUN apt-get update && apt-get install -y nginx

COPY arquivo_teste /app/arquivo_teste

CMD ["nginx", "-g", "daemon off;"]
```

Isso cria uma imagem com base no Ubuntu 24.04 LTS, define a pasta `/app` como diretório de trabalho, instala o `nginx`, copia o arquivo local para dentro de `/app` e, ao subir o container, inicia o servidor Nginx.

Outro exemplo mais simples de servidor estático:
```Dockerfile
FROM nginx:alpine

COPY ./src /usr/share/nginx/html

EXPOSE 80
```

### Dica: Aproveitando o Cache de Camadas
O Docker constrói imagens linha por linha. Se uma linha e os arquivos dela não mudaram desde o último build, o Docker **reutiliza o cache** daquela camada, tornando o build quase instantâneo!

Por isso, em projetos com linguagens como Python ou Node.js, é boa prática copiar primeiro os arquivos de dependência e instalá-las antes de copiar o restante do código:
```Dockerfile
# 1. Copia apenas arquivos de dependência
COPY pyproject.toml uv.lock ./

# 2. Instala dependências (essa etapa pesada fica em cache!)
RUN pip install uv && uv sync

# 3. Copia o código da aplicação (que muda com frequência)
COPY . .
```
Se você alterar apenas uma linha de código da sua aplicação, o Docker não precisará baixar todas as dependências novamente!

## Executando o Dockerfile
O principal comando para criar imagens é o `docker build`, um exemplo de uso é esse:

```bash
docker build -t site:ceos .
```

Explicando os outros termos:
- `-t`: declara a tag (nome e versão) da imagem que será criada (`site:ceos`).
- `.`: define o contexto de build (caminho onde está o Dockerfile e os arquivos do projeto; o ponto indica o diretório atual).

Para praticar os conceitos vistos vá para o [Laboratório 1](../labs/site-estatico/lab-01.md), onde você pode criar e executar uma imagem de um site estático.

## Dockerignore
Outro arquivo interessante para se usar na hora de criar uma imagem é o `.dockerignore`, ele guarda uma lista de arquivos, pastas e tudo aquilo que você não quer dentro da imagem. Isso evita que a imagem fique pesada guardando coisas não essenciais para seu funcionamento.

Um exemplo de um dockerignore de um projeto Python para você entender o que não é essencial:

```dockerignore
# Virtual env
.venv
venv

# Python cache
__pycache__
*.pyc
*.pyo

# Test cache
.pytest_cache
.coverage
htmlcov

# Git
.git
.gitignore

# IDE
.vscode
.idea

# Environment
.env
.env.* 

# Logs
*.log

# Docker
Dockerfile*
docker-compose*

# Docs
README.md

# Tests
tests/
```

<div align="center">
    <a href="./04-comandos.md">←Voltar</a>
    <a href="./06-volumes.md">Próximo→</a>
</div>