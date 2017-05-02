---
layout: post
title: "Migrando o blog para o Github"
modified:
categories: articles
excerpt: "Migrando o blog para o Github"
tags: [linux, jekyll, deploy, remote, blog, github]
author:
comments: true
share: true
image:
  feature: migrateBlog.jpg
  credit:
  creditlink:
---
<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/pt_BR/all.js#xfbml=1&version=v2.5&appId=541394239351629";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>


I'm back!!!

Devido a uns problemas pessoais <s>preguiça</s> acabei me distanciando do blog
<s>again</s>, mas percebi que a [vps](https://pt.wikipedia.org/wiki/Servidor_virtual_privado) que tenho
no [Azure](https://azure.microsoft.com/pt-br/?&wt.mc_id=AID_sem_WZsxWCz1) está
para encerrar o tempo contratado.

Assim, comecei a procurar um outro local, gratuito, para hospedar o blog, e
fiquei sabendo que no [Github](https://github.com/) existe essa possibilidade.

Nesse post apenas explicarei meus passos para a migração do blog, num futuro
post explicarei, detalhadamente, o processo para a criação de um blog e quais
[CMS](https://pt.wikipedia.org/wiki/Sistema_de_gerenciamento_de_conte%C3%BAdo)
podem ser utilizados.

Como o CMS que estou utilizando é o [Jekyll](http://jekyllrb.com/docs/home/) vou
deixar o [link]({% post_url 2015-10-12-Configurando-Jekyll-em-Máquina-Local-e-VPS %})
para a configuração local e do server.

# Repositório

Primeiro tive que criar um novo repositório, em minha conta no Github, com o
nome meusite.github.io, em meu caso, coloquei [shundake.github.io](http://shundake.github.io)
que será a [url](https://pt.wikipedia.org/wiki/URL) do blog.

![Criando repositorio](/images/migrateBlog01.png)

# Populando o repositório

Em seguida, executei o push no repositório local apotando para o repositório no
Githhub.

{% highlight bash %}
$ git remote add origin https://github.com/seuusuario/seusite.github.io.git

$ git push -u origin master
{% enhighlight %}

Toda vez que efetuar o push no repositório, será solicitado usuário e senha do
Github.

# Repositório já populado

Aqui, demorou um pouco para popular o repositório, mas é assim que ficou:

![Repositório populado](/images/migrateBlog02.png)

Por enquanto é isso, logo volto com mais informações.

# E aí, o que achou?

Deixe suas comentários, seguestões, críticas e dúvidas logo abaixo.


{% include fb_comment.html %}
