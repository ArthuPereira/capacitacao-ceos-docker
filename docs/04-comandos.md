# Principais Comandos

## Imagens
Como vimos, as imagens são os manifestos para criarmos containers, e é no [Docker Hub](https://hub.docker.com/) que achamos imagens oficiais dos desenvolvedores e empresas.

Vamos começar baixando a imagem do [Nginx](https://hub.docker.com/_/nginx), um servidor web, mas que também pode ser usado como proxy reverso, balanceador de carga e cache de HTTP. Vamos usar o seguinte comando:

```bash
docker pull nginx:alpine3.24
```

Explicando cada comando:  
- `docker pull` é o comando que busca e baixa uma imagem no docker hub  
- `nginx:alpine3.24` é o nome e tag da imagem que deve ser buscada.  

Tags são usadas para classificar imagens; como pode haver várias imagens de um mesmo serviço, elas são usadas para rastrear versão e correções que ela aplica. No nosso caso `alpine3.24` informa que é uma imagem leve (típico de imagens alpine) na versão 3.24.

Se você não informar versão, é usada automaticamente a versão *latest*, que é a mais atual.

---
Agora seria bom ver as imagens que baixamos, para isso vamos usar:  

```bash
docker images
```

Você deve ver algo como:  
```bash
IMAGE              ID             DISK USAGE   CONTENT SIZE   EXTRA
nginx:alpine3.24   c8497b180665        103MB         29.7MB        
nginx:latest       d0d674272be3        253MB         69.2MB 
```

Justo as imagens que baixamos no passo anterior, olhe com atenção as tags e como a imagem alpine é mais leve (DISK USAGE).

> O comando docker run já baixa e inicia a imagem em um único comando, facilita se você já tem certeza da imagem a ser usada

## Containers
Chegou a hora de criar nosso primeiro container:

```bash
docker run -d --name meu_servidor -p 8080:80 nginx
```

Dissecando as flags essenciais:
- `-d` (*detached*): roda o container em segundo plano, sem travar o seu terminal com os logs do processo.
- `--name meu_servidor`: dá um nome amigável ao container. Se não passar essa flag, o Docker gera um nome aleatório engraçado (como `hopeful_bardeen`). Em projetos, dar nome aos containers facilita pará-los e inspecioná-los.
- `-p 8080:80` (*ports*): faz o **mapeamento de portas** (`porta_no_seu_pc:porta_no_container`). Containers são isolados por padrão; sem essa flag, você não conseguiria acessar o Nginx pelo navegador! Com isso, abra `http://localhost:8080` no navegador para ver o Nginx funcionando.

---
Assim como fizemos com as imagens, vamos ver os containers que criamos com:

```bash
docker ps
```

O `docker ps` lista os containers em execução. Se quiser ver também os que já foram parados, use:

```bash
docker ps -a
```

Isso deve mostrar algo como:

```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                  NAMES
d17c903bf547   nginx     "/docker-entrypoint.…"   11 seconds ago   Up 10 seconds   0.0.0.0:8080->80/tcp   meu_servidor
```

> Vale lembrar que rodar vários `docker run` vai criar vários containers distintos, cada um com seu próprio ID.

---
### Investigando o Container: Logs e Execução

No dia a dia de desenvolvimento, duas ferramentas são cruciais para resolver problemas:

1. **Acompanhar logs:**  
Se o container roda em segundo plano, como saber o que aconteceu se algo der errado?
```bash
# Ver o histórico de logs
docker logs meu_servidor

# Acompanhar logs em tempo real (equivalente ao tail -f)
docker logs -f meu_servidor
```

2. **Entrar no container em execução:**  
Se precisar rodar comandos dentro de um container que já está ativo:
```bash
docker exec -it meu_servidor sh
```
> **Atenção à diferença**: `docker run` cria e inicia um **novo** container a partir de uma imagem. O `docker exec` entra ou roda um comando em um container que **já existe e está rodando**.

---
### Parando e Reiniciando
Para desligar o container:

```bash
docker stop meu_servidor
```

E para ligá-lo de volta:

```bash
docker start meu_servidor
```
*(Você pode usar tanto o nome quanto o CONTAINER_ID nos comandos).*

## Colocando em prática
Agora é sua vez! A ideia é que você use esses conceitos para ver o Docker sendo útil:
Você deve iniciar um container interativo usando uma imagem alpine de Python, entrar nele e testar comandos no interpretador:
1. Inicie o container: `docker run -it --name teste_python python:3.12-alpine sh`
2. Descubra a versão: `python --version`
3. Abra o interpretador interativo com `python`, rode um comando simples (como `print("Docker na Ceos!")`) e saia com `exit()`.
4. Ao sair do terminal, liste com `docker ps -a` e remova o container criado.

## Limpando eventuais bagunças
Imagens e containers ocupam espaço, às vezes **muito espaço** a depender do projeto, por isso aqui vão comandos para ajudar a excluir o que não é mais usado:

Excluir container por nome ou ID:
```bash
docker rm meu_servidor
```

Excluir imagem por ID ou tag:
```bash
docker rmi <IMAGEM_ID>
```

### Faxina Geral Automática
O Docker possui um comando moderno e seguro para limpar recursos parados sem precisar de comandos longos:

```bash
# Remove todos os containers parados, redes não utilizadas e buffers órfãos
docker system prune

# Para remover também imagens não utilizadas, adicione a flag -a
docker system prune -a
```

<div align="center">
    <a href="./03-instalacao.md">←Voltar</a>
    <a href="./05-criando-imagens.md">Próximo→</a>
</div>