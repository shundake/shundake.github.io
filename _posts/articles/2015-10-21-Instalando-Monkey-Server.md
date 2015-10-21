---
layout: post
title: "Instalando Monkey Server"
excerpt: "Instalar um http server otimizado para servidores Linux, leve e
funcional em sistemas embarcados utilizando."
categories: articles
tags: [linux, web, server]
author:
comments: true
share: true
image:
  feature: installMonkey.jpg
  credit:
  creditlink:
---

Se o que você procura é um http server leve e poderoso, que tenha um baixo
consumo de memória e processamento, você precisa conhecer o [Monkey](http://monkey-project.com/).

O Monkey é um servidor web otimizado para funcionar em sistemas Linux e,
atualmente, no [OSX](http://www.apple.com/br/osx/), desenvolvido para ser
escalável com baixo consumo de memória e CPU, uma ótima opção para [Sistemas
Embarcados](https://pt.wikipedia.org/wiki/Sistema_embarcado) funcionando bem em
arquiteturas ARM, x86 e x86_64.

Seu projeto teve início no ano de 2001, desenvolvido por [Eduardo
Silva](http://edsiper.linuxchile.cl/blog/), tendo como foco sistemas embarcados
utilizando Linux. Possui todas as funcionalidades em seu pacote padrão,
possuindo a possibilidade de utilizar plugins para adicionar novas, como
[SSL/TLS](https://pt.wikipedia.org/wiki/Transport_Layer_Security) etc. Para
maiores informações acessem o [site do projeto](http://monkey-project.com/about/).

# Por onde começar?

A instalação é bem simples, se você possui uma máquina com Debian/Ubuntu, basta,
primeiro, adicionar a chave GPG do servidor em seu chaveiro:

{% highlight bash %}
$ wget -qO - http://apt.monkey-project.com/monkey.key | sudo apt-key add -
{% endhighlight %}

Em seguida adicionar o repositório oficial em seu /etc/apt/sources.list
utilizando o editor de textos que preferir:

**Ubuntu 15.04 (vivid)**

{% highlight bash %}
deb http://apt.monkey-project.com/ubuntu vivid main
{% endhighlight %}

**Debian 8 (jessie)**

{% highlight bash %}
deb http://apt.monkey-project.com/debian jessie main
{% endhighlight %}

Assim, é atualizar a base de dados do repositório e instalar o monkey:

{% highlight bash %}
$ sudo apt-get update
$ sudo apt-get install monkey
{% endhighlight %}

Mas, se você não utiliza nenhum dos dois citados acima, ou prefere compilar,
você pode baixar o pacote mais recente <s>até 2015-10-21T11:53:27</s> [aqui](http://monkey-project.com/releases/1.6/monkey-1.6.3.tar.gz) e prosseguir:

{% highlight bash %}
$ tar zxfv monkey-1.6.3.tar.gz
$ cd monkey-1.6.3
{% endhighlight %}

{% highlight bash %}
$ ./configure --bindir=/usr/bin/ --datadir=/var/www/monkey --logdir=/var/log/monkey --plugdir=/var/www/monkey/plugins --sysconfdir=/etc/monkey
{% endhighlight %}

Onde:

* **/usr/bin/**: é o diretório onde serão colocados os executaveis;

* **/var/www/monkey**: diretório onde estarão as páginas web;

* **/var/log/monkey**: onde se encontrarão os logs;

* **/var/www/monkey/plugins**: local onde estarão os plugins;

* **/etc/monkey**: onde os arquivos de configuração ficarão;

Já tendo executado o comando configure, é necessário efetuar a instalação.

{% highlight bash %}
$ sudo make
$ sudo make install
{% endhighlight %}

Verifique qual a porta está sendo utilizada no arquivo /etc/monkey/monkey.conf:

{% highlight bash %}
$ sudo vim /etc/monkey/monkey.conf

# Monkey HTTP Server - Configuration
# ==================================
# These are the main variables and their descriptions, defined in respect
# to the configuration of the different types of directives.

[SERVER]
    # Listen:
    # -------
    # The Listen directive maps the port and optionally restricts the
    # network interface from where Monkey will be listens for incoming
    # connections. Multiple Listen directives are allowed.
    #
    # The Listen directive requires a port number, preceded by an
    # optional IPv4 or IPv6 address separated by a colon (:).
    # IPv6 addresses must be enclosed in brackets ([]), e.g:
    #
    # Listen 127.0.0.1:2001
    # Listen [::1]:2001

    Listen 80

{% endhighlight %}

No meu caso, coloquei para o servidor funcionar na porta 80, mas fica a seu
critério. *Nota*: desabilite qualquer outro server, caso estejam usando a mesma porta.

Feito isso, é só executar o comando para iniciar o server e testar para saber se está funcionando:

{% highlight bash %}
$ sudo monkey -D
{% endhighlight %}

Assim, você pode acessar através do seu browser http://ip-do-seu-servidor:porta
para ver a tela de apresentação do server.

Caso queira outras opções no configure, verifique a [página
oficial](http://monkey-project.com/documentation/1.6/getting_started/installation.html).

# Configurando o server

Eis a estrutura das configurações do server:

{% highlight bash %}
conf/                                 # diretório base
    /monkey.conf                      # arquivo de configuração do server
    /monkey.mime                      # contém lista de extensões de arquivos associados com o mime type correto
    /plugins.load                     # arquivo para habilitar os plugins
    /sites/                           # diretório contendo os hosts virtuais
          /default                    # arquivo de configuração de um host virtual
    /plugins/                         # diretório contendo os arquivos de configuração dos plugins
            /security                 # diretório com o nome do plugin instalado
            /security/security.conf   # arquivo de configuração do plugin
{% endhighlight %}

Não há muito o que configurar de início, apenas o arquivo default em
/etc/monkey/sites..Nesse arquivo, você somente colocará as informações
necessárias, para que seu site possa ser entregue corretamente:

{% highlight bash %}
$ sudo vim /etc/monkey/sites/default

# Default Host - Configuration
# ============================
# Here the variable principals of the program are defined in respect
# to the configuration of the different types of directives.

[HOST]
    # ServerName:
    # -----------
    # Allow you to set a host and domain name (e.g monkey.linuxchile.cl). If
    # you are working in a local network just set your IP address or if you
    # are working like localhost set your loopback address (127.0.0.1).

    ServerName 127.0.0.1

    # DocumentRoot:
    # -------------
    # This variable corresponds to the location of the main server directory
    # of the web pages, where the files of your site are located.
    #
    # Example:
    #      DocumentRoot /home/krypton/htdocs

    DocumentRoot /usr/share/monkey

    # Redirect:
    # ---------
    # Under specific conditions, you may want the server performs a HTTP
    # redirect when this Virtual Host is reach. If that is the case, append
    # to the Redirect key the value of the address where to redirect the
    # HTTP client.
    #
    # Redirect http://monkey-project.com

[LOGGER]
    # AccessLog:
    # ----------
    # Registration file of correct request.

    AccessLog /var/log/monkey/access.log

    # ErrorLog:
    # ---------
    # Registration file of incorrect request.

    ErrorLog /var/log/monkey/error.log

[ERROR_PAGES]
    404  404.html

[HANDLERS]
    # FastCGI
    # =======
    # Match /.*\.php fastcgi

    # CGI
    # ===
    # Match /cgi-bin/.*\.cgi cgi

{% endhighlight %}

Logo acima temos o exemplo que vem por padrão no monkey, onde podemos
identificar cada item necessário para fazê-lo funcionar:

* **ServerName**: o domínio do seu servidor, podendo ser example.com.br ou, se
  for rede local, o ip que definiu para ele;

* **DocumentRoot**: o local onde os documentos da sua página se encontram,
  geralmente está em /var/www ou /srv/www, no entanto pode ser utilizada uma
  pasta no diretório pessoal de um usuário;

* **Redirect**: utilizado em casos específicos, onde desejamos redirecionar o
  acesso a este site para outro;

* **AccessLog**: localização do arquivo de log de acessos;

* **ErrorLog**: localização do arquivo de log de erros;

* **[ERROR PAGES]**: a numeração dos erros, seguidos pela página a ser
  carregada;

* **[HANDLERS]**: quais as instruções de plugin serão utilizadas;

# Estou começando a configuração de meu server Debian e gostaria de uma forma
mais automatizada

No caso de iniciantes ou até mesmo usuários avançados, que queiram apenas testar
o server em uma máquina limpa <s>recém instalada</s>, podem utilizar o script de
instalação do [EkaatyLinux](https://alexteles.com/) que pode ser encontrado [aqui](https://github.com/alexandreteles/monkeyServer).

Testei o script, trocando o link de download encontrado na linha 125 pela versão
mais recente do pacote, e foi bem simples.

#E aí, o que achou?

Deixe seu comentário, dúvida, crítica ou sugestão aqui embaixo.

{% include fb_comment.html %}
