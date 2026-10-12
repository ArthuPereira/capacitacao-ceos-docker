# Boas práticas e Próximos passos
Olá, parabéns por ter chegado até aqui! De agora em diante você já é capaz de ver vantagens de usar Docker para desenvolvimento e usar o básico para o dia a dia. Para finalizarmos com chave de ouro, vamos reforçar as melhores práticas a se manter e próximos passos caso você queira aprofundar seus conhecimentos sobre Docker.

## Use imagens leves e versionadas
Durante a aula 4 foi citado que algumas imagens podem ser bem pesadas; isso é um problema real e recorrente. Durante nossos labs a imagem mais pesada deve ter pesado cerca de 300MB, mas é comum vermos imagens com mais de 700MB, às vezes até mais de 1GB.

Existem vários jeitos de contornar isso, um deles é buscar por imagens com a tag *alpine*, ela define que a imagem é feita com base no Alpine Linux, uma distribuição muito leve. Além disso, procurar por imagens alternativas também é uma ótima opção, bem como montar Dockerfiles melhores que deixem nossas imagens mais leves.

Além disso, usar *latest* é um comportamento a ser evitado. A tag *latest* atrela a imagem à versão mais recente disponível, o que é um problema por dois motivos. O primeiro deles é que a imagem nova pode conter alterações ou erros imprevisíveis; além disso, como a versão não é fixa, podemos ter situações de baixar uma nova imagem inteira em momentos em que não era necessário.

## O que vem depois?
Tudo o que vimos aqui é a ponta do iceberg do mundo da conteinerização. Podemos no futuro fazer uma nova capacitação inteira sobre conceitos que ficaram de fora e aprofundar em outras tecnologias que fazem parte do ecossistema Docker. Se você gostou do que viu aqui, existem vários tópicos a se explorar depois daqui, alguns deles são:
- Multi-stage builds: usar mais de uma imagem base para criar Dockerfiles mais eficientes
- Redes em Docker: como os containers conversam entre si e com o mundo exterior
- Segurança de imagens: gerenciando usuários e secrets e analisar vulnerabilidades
- CI/CD: automatizar desde build e push de imagens para o Docker Hub até deploy
- Orquestração: quando nem mesmo com compose é possível gerir o ambiente, é hora de Kubernetes
- Podman: Docker não é a única tecnologia de containers usada, existem outras tecnologias para outros casos

<div align="center">
    <a href="../README.md">Início</a>
</div>