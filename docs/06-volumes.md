# Armazenamento com Volumes

## Guardando dados de um container
Já vimos que containers podem guardar vários tipos de serviços, mas também precisamos guardar informações deles, seja um banco de dados, arquivos... Mas como fazer isso?

A arquitetura em camadas dos containers fornece uma solução inicial, o copy-on-write, mas ela não é muito útil, pois só guarda informação enquanto o container existe, logo um `docker rm` destrói todos os dados. A solução mais completa é criar ***volumes***.

## Volumes
A ideia de volumes é criar uma partição de armazenamento próprio, que sobrevive à morte do container e que pode ser facilmente apagada. Essa partição fica fora do container, em um caminho do sistema onde o próprio docker gerencia.

> Vale ressaltar que existem outras formas de armazenamento no docker, mas volumes são a opção mais comum e prática. As outras são Bind mounts e tmpfs mounts.

Vamos agora para um cenário onde vamos criar e usar um volume, começamos criando um com:
```bash
docker volume create dados_db
```

Podemos verificar esse novo volume com `docker volume ls`, você verá algo como isso:
```
DRIVER    VOLUME NAME
local     24457ab58e63999091738b0827458a0bbfe723c9e51b6cb070c12cfb1bc4c4f6
``` 

> Para mais detalhes sobre o volume criado use docker inspect com o nome do volume

E agora vamos subir um container com uma imagem de um banco de dados PostgreSQL:
```bash
docker run -d --name meu_postgres -p 5432:5432 -v dados_db:/var/lib/postgresql/data -e POSTGRES_PASSWORD=senha postgres
```

Dissecando o comando:
- `-p 5432:5432`: mapeamento de portas (`porta_host:porta_container`).
- `-v dados_db:/var/lib/postgresql/data`: mapeamento do volume nomeado para a pasta onde o PostgreSQL grava seus arquivos de dados.
- `-e POSTGRES_PASSWORD=senha`: variável de ambiente com a senha do banco.

Agora você pode se conectar a esse banco criado usando um DBeaver ou outro cliente SQL de sua preferência. Ao rodar `docker rm -f meu_postgres` e criar um novo container apontando para o mesmo volume `dados_db`, seus dados continuarão lá intactos!

---

## Bind Mounts: O Segredo do Live Reload no Desenvolvimento

Além dos volumes nomeados (gerenciados pelo Docker), existe outra forma de montagem essencial no dia a dia: o **Bind Mount**.

Em vez de criar uma partição interna gerenciada pelo Docker, o *Bind Mount* conecta uma pasta da sua própria máquina diretamente para dentro do container:

```bash
docker run -d -p 8080:80 -v $(pwd)/src:/usr/share/nginx/html --name site_dev nginx:alpine
```

O comando `-v $(pwd)/src:/usr/share/nginx/html` diz:
> *"Espelhe a pasta local `./src` exatamente dentro de `/usr/share/nginx/html` no container."*

### Por que isso é incrível no desenvolvimento?
Sem bind mount, toda vez que você altera um arquivo HTML, CSS ou código Python, você teria que rodar `docker build` e `docker run` novamente. Com bind mount, você edita o código no seu editor (VS Code, etc.) e o container **enxerga a alteração na mesma hora**, permitindo **Live Reload / Hot Reload** imediato!

---

## Resumo: Quando Usar Cada Um?

| Tipo | Onde os dados ficam? | Quem gerencia? | Principal Uso |
| :--- | :--- | :--- | :--- |
| **Named Volume** (`-v meu_volume:/caminho`) | Área interna gerenciada pelo Docker (`/var/lib/docker/volumes/...`) | Docker | Bancos de dados (PostgreSQL, MySQL) e persistência segura em produção. |
| **Bind Mount** (`-v ./src:/app/src`) | Em qualquer diretório da sua máquina host | Você | Código-fonte em ambiente de desenvolvimento local (Live Reload). |
| **Tmpfs Mount** (`--tmpfs /caminho`) | Apenas na memória RAM da máquina | Host | Dados temporários ou segredos que nunca devem ser gravados em disco. |

<div align="center">
    <a href="./05-criando-imagens.md">←Voltar</a>
    <a href="./07-compose.md">Próximo→</a>
</div>