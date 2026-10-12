# Instalando e rodando seu primeiro container

Vale lembrar que o docker tem uma versão tradicional via terminal e outra com interface gráfica, o [Docker Desktop](https://docs.docker.com/desktop/) que está disponível para os sistemas como Windows, Ubuntu, Fedora, etc.

## Docker Engine
O Docker engine é a forma mais tradicional que citei, os comandos e pacotes mudam um pouco de sistema para sistema, então dê preferência para o [guia oficial](https://docs.docker.com/engine/install/)

## Pós-instalação
Esse passo é opcional, mas ajuda bastante, que é adicionar o seu usuário ao grupo Docker, por padrão, só o `root` consegue usar o Docker. Para não precisar digitar `sudo` toda vez:

```bash
sudo usermod -aG docker $USER
```

Depois feche essa aba do terminal e abra outra, já deve estar funcionando. Para testar, use:

```
docker --version
```

Você deve ver algo como `Docker version 29.x.x`, e é sinal que deu tudo certo.

## Primeiro Container
Para finalmente começar a subir nossos containers vamos usar esse comando

```bash
docker run hello-world
```

Parte do resultado que você vai ver se parece com isso

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

Recomendo fortemente ler todo o resultado, pois ele explica o fluxo desde o comando até esse "Hello"

## Teste um pouco mais a fundo
Um próximo passo que o próprio hello-world recomenda é rodar o bash de uma imagem de Ubuntu de forma interativa

```bash
docker run -it ubuntu bash
```

Isso permite que você acesse o terminal do container com a imagem do Ubuntu que você acaba de subir. Eu recomendo tentar brincar um pouco, navegue, explore permissões...

<div align="center">
    <a href="./02-containers-e-imagens.md">←Voltar</a>
    <a href="./04-comandos.md">Próximo→</a>
</div>