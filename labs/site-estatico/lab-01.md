# Laboratório 1: Hospedando um Site Estático com Docker

Bem-vindo! Aqui você vai colocar em prática os conceitos das aulas 4, 5 e 6: construindo sua primeira imagem Docker, executando containers com mapeamento de portas e experimentando a mágica do **Bind Mount com Live Reload**.

## Estrutura do Laboratório

Dentro de `/labs/site-estatico`, você encontrará:
```
/site-estatico
├── Dockerfile      # Receita que define como montar a imagem
├── lab-01.md       # Roteiro deste laboratório
└── src/            # Código-fonte do site estático
    ├── index.html
    ├── script.js
    └── style.css
```

---

## Etapa 1: Construindo e Rodando a Imagem

O `Dockerfile` já está pronto nesta pasta. Ele usa o Nginx como servidor web e copia a pasta `./src` para `/usr/share/nginx/html`.

1. **Construa a imagem:**  
   Certifique-se de estar dentro da pasta `labs/site-estatico` no seu terminal e execute:
   ```bash
   docker build -t site:ceos .
   ```

2. **Verifique se a imagem foi criada:**
   ```bash
   docker images
   ```

3. **Inicie o container:**  
   Vamos rodar o container em segundo plano (`-d`), com um nome amigável (`--name`) e mapeando a porta `80` do container para a porta `8080` do seu computador:
   ```bash
   docker run -d --name meu_site -p 8080:80 site:ceos
   ```

4. **Teste no navegador:**  
   Abra no seu navegador: `http://localhost:8080`  
   Você deverá ver a página da Ceos Jr. rodando diretamente de dentro do container!

5. **Acompanhe os logs:**  
   Dê um refresh (F5) na página algumas vezes e veja os acessos registrados no servidor:
   ```bash
   docker logs meu_site
   ```

---

## Etapa 2: A Mágica do Bind Mount (Live Reload sem Rebuild)

Imagine que você precisa alterar uma cor no CSS ou um texto no HTML.  
No fluxo tradicional da Etapa 1, você precisaria:
1. Parar o container.
2. Rodar `docker build -t site:ceos .` novamente para copiar os arquivos novos.
3. Subir outro container com `docker run`.

Isso seria muito lento! Em desenvolvimento, usamos **Bind Mount** para espelhar a pasta local diretamente para dentro do container:

1. **Pare e remova o container antigo:**
   ```bash
   docker stop meu_site && docker rm meu_site
   ```

2. **Inicie um container com Bind Mount:**  
   Repare na flag `-v`:
   ```bash
   docker run -d --name site_dev -p 8080:80 -v $(pwd)/src:/usr/share/nginx/html nginx:alpine
   ```
   > **Note o detalhe:** Nem precisamos usar a imagem `site:ceos` aqui! Usamos a imagem oficial `nginx:alpine` diretamente, porque os arquivos do site vêm em tempo real da sua pasta `./src` local!

3. **Faça uma alteração em tempo real:**  
   Abra o arquivo [`src/index.html`](./src/index.html) no seu editor e altere o título principal ou adicione seu nome na página. Salve o arquivo.

4. **Recarregue a página:**  
   Atualize o navegador (`http://localhost:8080`). A alteração aparece instantaneamente, sem precisar rebuildar nenhuma imagem! Esse é o poder que você usará diariamente nos projetos da Ceos.

---

## Etapa 3: Desafio Prático

Agora é com você:
1. Altere o estilo no [`src/style.css`](./src/style.css) (por exemplo, mude a cor de fundo do botão para `#22c55e`).
2. Entre no container ativo para inspecionar os arquivos diretamente lá dentro:
   ```bash
   docker exec -it site_dev sh
   ```
3. Dentro do container, navegue até a pasta do servidor e confira o conteúdo do HTML:
   ```bash
   cd /usr/share/nginx/html
   cat index.html
   exit
   ```

---

## Limpeza

Ao finalizar seus testes, encerre e remova o container e a imagem de teste para manter sua máquina limpa:

```bash
docker stop site_dev && docker rm site_dev
docker rmi site:ceos
```

<div align="center">
    <a href="../../docs/05-criando-imagens.md">←Voltar</a>
    <a href="../../docs/06-volumes.md">Próximo→</a>
</div>