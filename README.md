# Aulas sobre ENTREGA e CI(Continuous Integration)

## HEROKU

Utilizando o plano livre limitado.

1. Criar uma conta.
2. Instalar a CLI do Heroku para permitir que trabalhemos via terminal:

```bash
yarn global add heroku
```

ou, se estiver no Linux Ubuntu:

```bash
sudo snap install --classic heroku
```

3. Logar no Heroku:

```bash
heroku login
```

4. Estar em uma aplicação criada com 'create-react-app'.

5. Rodar o comando para criar o projeto no Heroku:

```bash
heroku create goreact-module3 --buildpack https://github.com/mars/create-react-app-buildpack.git
```

NOTA_ESTUDO: O "goreact-module3" é o nome do meu projeto, ele deve ser único no Heroku

6. Criar o arquivo 'static.json' na raiz do nosso projeto.

Conteúdo do arquivo:

```javascript
{
  "root": "build/",
  "routes": {
    "/**": "index.html"
  }
}
```

NOTA_ESTUDO: O Heroku sempre executará o script "build" do package.json

7. Comitar o projeto "GIT"

```bash
git add --all
git commit -m "COMENTÁRIO"
```

8. Push para o heroku:

```bash
git push heroku master
```

**Um log do deploy será exibido**

NOTA_ESTUDO: Tive um erro ao rodar a sequência acima. No log, o Heroku me aconselhou à rodar o seguinte comando (que eu rodei):

```bash
npx @heroku/update-node-build-script
```

Ao terminar ele exibiu um diff entre o script de build anterior e o que estava sendo alterado e me perguntou se eu aceitava as alterações.

Aceitando, foi-me recomendado rodar os comandos:

```bash
git add package.json
git commit -m "Adapt build scripts to Heroku changes"
git push heroku master
```

No final de tudo ele me deu a URL: https://goreact-module3.herokuapp.com/

O mesmo efeito de acessar a URL acima pode se obter com o comando:

```bash
heroku open
```

**CUIDADOS:**

Sempre trabalhar com variáveis de ambiente:

1. Se não tiver, crie o arquivo .env na raiz do projeto e defina as variáveis ali. Exemplo:

```
REACT_APP_API_URL=http://localhost:3000
```

2. Para acessar:

```
process.env.API_URL;
```

A partir daqui eu posso redefinir essa variável de ambiente no Heroku (EXEMPLO):

```bash
heroku config:set REACT_APP_API_URL=http://api.github.com
```

Ou, posso definir essas ENVs pelo painel web do Heroku.

## UBUNTU

1. Instalar o NODEJS.

ATENÇÃO: Os comandos abaixo foram rodados em 02/2019, podem sofrer alterações. Consulte a página "other downloads" do NODEJS para saber como instalar por linha de comando.

```bash
sudo apt-get install -y nodejs
```

2. Instalar bibliotecas de gerenciamento do servidor.

```bash
# npm install --global pm2 serve
```

PM2: Um serviço do node que vai manter a aplicação no ar. Se ela cair ele sobe novamente.
SERVE: Um minu servidor NODE que serve para rodar aplicações estáticas

3. Baixar o projeto GIT, usando o "git clone"

4. Rodar o "npm install" no projeto

5. Rodar o build:

```bash
$ npm run build
```

6. Levantar o servidor:

```bash
$ pm2 serve build
```

**ATENÇÃO: O serve já coloca a aplicação na porta 8080**

NOTA_ESTUDO: "pm2 list" lista os servers upados. "pm2 monit" monitora os servers.

7. Instalar o NGINX para rotear a porta 80

```bash
# apt install nginx
```

8. Editar o arquivo: /etc/nginx/sites-available/default

Deletar todo o conteúdo e inserir o abaixo:

```
upstream node {
  server 127.0.0.1:8080;
  keepalive 64;
}

server {
  listen 80;
  # server_name domain.com;
  root /home/deploy/goreact-module3;

  location / {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection “upgrade”;
    proxy_max_temp_file_size 0;
    proxy_pass http://node/;
    proxy_redirect off;
    proxy_read_timeout 240s;
  }
}
```

NOTA_ESTUDO: O caminho "root /home/deploy/goreact-module3;" tem que ser exato para aonde está instalado o nosso projeto

9. Reiniciar o nginx

```bash
service nginx restart
```
