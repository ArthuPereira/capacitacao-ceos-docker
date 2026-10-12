# O que é Docker e o que ele resolve?

O Docker é uma ferramenta de virtualização por containers; ele (e outras ferramentas de containerização) resolvem principalmente problemas de configuração de ambientes.

Pense que cada pessoa nova que chega num projeto precisa instalar linguagem, banco de dados, cache, servidor web... e se a pessoa errar as versões de algo? Como instalar Java 17 em um projeto de Java 21. Isso cria uma barreira de infraestrutura para começar o projeto, e é aqui que o Docker brilha.

Utilizando técnicas de isolamento, o Docker consegue empacotar serviços de forma leve e simples de usar. Agora com um ou dois comandos o dev do caso anterior já tem uma aplicação rodando com containers para API, banco de dados, servidor web e o que mais precisar.

> Para saber mais: as técnicas de isolamento são os namespaces como pid, net, mnt e os control groups (cgroups) para criar uma visão isolada, fazendo o container "enxergar" somente ele na máquina, mas sendo um processo isolado e limitado em recursos.

[Material de apoio](https://stack.desenvolvedor.expert/appendix/docker/oquee.html)

<div align="center">
    <a href="../README.md">←Voltar</a>
    <a href="./02-containers-e-imagens.md">Próximo→</a>
</div>