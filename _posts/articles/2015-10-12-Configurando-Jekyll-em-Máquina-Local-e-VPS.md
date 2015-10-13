---
layout: post
title: "Configurando Jekyll em Máquina Local e VPS"
modified:
categories: articles
excerpt: "Configuração rápida para iniciar um blog/site com o jekyll e efetuar o
deploy usando git"
tags: [jekyll, deploy, remote, blog]
author:
comments: true
share: true
image:
  feature:
  credit:
  creditlink:
---
<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/en_US/all.js#xfbml=1&appId=541394239351629";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>


Há alguns meses, decidi voltar a produzir um blog. Pensei em reviver um dos meus
blogs antigos, mas eu queria algo novo e que refletisse, realmente, minha
vontade, durante a configuração.

Assim, fui apresentado ao [Jekyll](https://jekyllrb.com/), um gerador de sites
estáticos, que utiliza arquivos de texto, contendo as postagens, a serem
convertidas através de [markdown](https://pt.wikipedia.org/wiki/Markdown).
Para saber mais sobre o Jekyll, acesse o [site](http://jekyllrb.com/docs/home/).

# Configuração da Máquina Local

Primeiramente atualizar o pacote do ruby, que pode ser encontrado no
[Site Oficial](http://rubyinstaller.org/downloads/), mas é aconselhável que
efetue a instalação/atualização através do gerenciador de pacotes de sua distro,
caso esteja utilizando MAC, siga
[essas instruções](http://digitalshore.io/development/2015/03/22/how-to-install-jekyll-mac-osx-yosemite/) e, caso queria tentar utilizá-lo no Windows, [siga essas](http://jekyll-windows.juthilo.com/).
Na minha situação, utilizando arch, apenas atualizei o pacote com o comando:

{% highlight bash %}
# pacman -S ruby
{% endhighlight %}

Após a instalação/atualização do ruby, efetuaremos a instalação do jekyll
através das gems do ruby.

{% highlight bash %}
$ gem install jekyll
{% endhighlight %}

Para finalizar, instalaremos/atualizaremos o pacote git, que nos possibilitará
fazer o deploy do nosso blog.

{% highlight bash %}
# pacman -S git
{% endhighlight %}

# Criando o Blog (local)

No site da [ferramenta](https://jekyllrb.com/) podemos encontrar um guia para
utilizar, bem como o básico e coisas sobre a customização.

Para iniciarmos o blog é necessário navegar até onde deseja guardar os arquivos
relacionados ao blog e executar o comando abaixo:

{% highlight bash %}
$ jekyll new myblog
{% endhighlight %}

Na linha acima, vemos a utilização da ferramenta **jekyll** seguido do comando
**new** e por último o **nome do diretório** contendo os arquivos do blog. Feito
isso, podemos mudar para esse diretório e verificar no browser como está o blog.

{% highlight bash %}
$ cd myblog
$ jekyll serve
{% endhighlight %}

Note que demorará um pouco para construir o blog e aparecer "*Server address:
http://127.0.0.1:4000/*" que é o endereço a ser acessado para ver pelo
navegador.

Dê CTRL+C no terminal para encerrar o server e vamos inializar o repositório git
no diretório do blog, para que as alterações possar ser providas ao server.

{% highlight bash %}
$ git init
$ git add .
$ git commit -m "Commit Inicial"
{% endhighlight %}

# Configuração do servidor/VPS

Nesse caso, considerarei que já tenha um server configurado com
[LAMP](https://wiki.debian.org/LaMp),
[LEMP](http://www.unixmen.com/how-to-install-lemp-stack-on-debian-8/) ou outro
serviço web. Também, irei considerar que o diretório público html esteja em
/var/www.

No meu caso, minha VPS está com Debian Jessie, então a linha para instalar o git
é:

{% highlight bash %}
# apt-get update && apt-get install git-core
{% endhighlight %}

Agora vamos instalar o ruby e o jekyll, basicamente da mesma forma como na
máquina local.

{% highlight bash %}
# apt-get install ruby-full
{% endhighlight %}

{% highlight bash %}
$ gem install jekyll
{% endhighlight %}

Após as instalações vamos criar a pasta do repositório git.

{% highlight bash %}
$ cd ~/
$ mkdir repo && cd repo
$ mkdir myblog.git && cd myblog.git
$ git init --bare
{% endhighlight %}

Após inicializado, precisamos configurar a ação (hook) post-receive, um shell
script que o git executará quando os arquivos forem 'enviados' pela máquina
local.

{% highlight bash %}
$ vim repo/myblog.git/hooks/post-receive
{% endhighlight %}

Aqui usei o [vim](https://pt.wikipedia.org/wiki/Vim) mas poderá ser utilizado o
editor de texto que melhor se adequa ao seu uso, por exemplo:
[nano](https://pt.wikipedia.org/wiki/GNU_nano_%28editor_de_texto%29), [emacs](https://pt.wikipedia.org/wiki/Emacs) etc.

Cole o conteúdo abaixo no arquivo post-receive, encontrado no diretório hooks.

{% highlight bash %}
#!/bin/bash -l

###
# Faça as alterações que melhor lhe atenderem

### GIT_REPO - o diretório que se tornará o repositório git
GIT_REPO=$HOME/repo/myblog.git

### TMP_GIT_CLONE - diretório temporário para para a construção do site/blog
TMP_GIT_CLONE=$HOME/tmp/git/myblog

### PUBLI_WWW - diretório www público
PUBLIC_WWW=/var/www/myblog

git clone $GIT_REPO $TMP_GIT_CLONE
jekyll build --source $TMP_GIT_CLONE --destination $PUBLIC_WWW
rm -Rf $TMP_GIT_CLONE
exit
{% endhighlight %}

Após salvar o arquivo daremos permissão de execução:

{% highlight bash %}
$ chmod +x repo/myblog.git/hooks/post-receive
{% endhighlight %}

Finalizando colocaremos uma configuração no arquivo do [Nginx](http://nginx.org/):

{% highlight bash %}
# vim /etc/nginx/sites-available/site.example.com
{% endhighlight %}

{% highlight bash %}
server {
    listen       80;

    root /var/www/myblog/;
    index index.html;

    server_name site.example.com;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;


    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
         root /usr/share/nginx/www;
    }

    # do not serve pages starting with .
    location ~ /\. {
         log_not_found off;
          deny all;
    }

    ### This location definition prevents Nginx from serving any files which begin
    ### with a dollar sign, so Nginx will refuse to serve out a temp file if you
    ### are doing any editing inside a web-available directory
    location ~ ~$ {
        access_log off;
        log_not_found off;
        deny all;
    }

    ### These next two locations simply prevent Nginx from logging every time the
    ### favicon & robots.txt files are accessed, to keep the logs clean
    location = /robots.txt {
      access_log off;
      log_not_found off;
    }

    #don't need these for static site
    location ~ \.(aspx|php|jsp|cgi)$ {
            return 410;
            access_log off;
    }

    location = /favicon.ico {
      access_log off;
      log_not_found off;
    }

    location ~ \.js {
      access_log off;
      log_not_found off;
    }

    location ~ \.css {
      access_log off;
      log_not_found off;
    }
}
{% endhighlight %}

Após salvar o arquivo, crie um link simbólico em sites-enabled e é só reiniciar o nginx:

{% highlight bash %}
# ln -s /etc/nginx/sites-available/site.example.com /etc/nginx/sites-enable/site.example.com
# systemctl restart nginx
{% endhighlight %}

Caso você utilize apache:

{% highlight bash %}
# vim /etc/apache2/sites-available/000-default.conf
{% endhighlight %}

{% highlight bash %}
<VirtualHost *:80>
ServerName site.example.com
DocumentRoot /var/www/myblog
<Directory /var/www/myblog>
	Options Indexes FollowSymLinks MultiViews
	AllowOverride All
	Order allow,deny
	allow from all
</Directory>

RewriteEngine On
RewriteCond %{REQUEST_FILENAME}.html -f
RewriteRule (/.+) $1.html [L,QSA]
</VirtualHost>
{% endhighlight %}

E insira seu servername no arquivo hosts:

{% highlight bash %}
# echo 127.0.0.1 site.example.com >> /etc/hosts
{% endhighlight %}

Nota: Não testei com o servidor apache, por isso não dou 100% de certeza que funcione a configuração.

# Finalizando a máquina local

Acessando o diretório local (o atual repositório git do bloig/site), executaremos os comandos:

{% highlight bash %}
$ cd myblog
$ git remote add droplet user@site.example.com:repo/myblog.git
$ git push droplet master
{% endhighlight %}

Caso em sua VPS a porta para acesso ssh não seja a padrão, 22, utilize os comandos abaixo:

{% highlight bash %}
$ cd myblog
$ git remote add droplet ssh://user@site.example.com:porta/$HOME/repo/myblog.git
$ git push droplet master
{% endhighlight %}

Lembrando que onde encontra-se **user** é o seu usuário na VPS, **site.example.com** é o ip ou domain name e **porta** a porta de acesso ssh.

Assim, qualquer postagem, mudança de layout, remoção de conteúdo será atualizado, pouucos segundos após a execução dos comandos básicos:

{% highlight bash %}
$ git add .
$ git commit -m "Meu Commit"
$ git push droplet master
{% endhighlight %}

#E aí, o que acharam?

Deixem suas sugestões, comentários, críticas e dúvidas logo abaixo.

<div class="fb-comments" data-href="http://blog.shundake.xyz{{ post.url }}/"
data-width="600" data-num-posts="2" data-colorscheme="dark"></div>
