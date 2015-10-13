---
layout: post
title: Recuperar arquivos corrompidos
modified:
categories: articles
excerpt: "O que fazer quando abrimos um arquivo importante e aparece a mensagem de arquivo corrompido?"
tags: [ddrescue, recovery, corrompido, corrupt, recuperar]
author:
comments: true
share: true
image:
  feature: recoverFiles.jpg
  credit:
  creditlink:
---

{{ post.url }}

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/en_US/all.js#xfbml=1&appId=541394239351629";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>

Há uns dias adquiri um novo HD, pois o meu estava dando sinais de que iria me
deixar na mão, logo logo. Assim, adquiri um novo, e decidi copiar os arquivos
para o novo, fazer um backup, por assim dizer. Qual foi a minha surpresa que ao
iniciar a cópia, muitos dos arquivos estavam corrompidos devido a *bad blocks*.

Frente a esse novo problema, decidi clonar os hd's, foi quando, numa conversa com uns colegas na internet fui apresentado à
ferramenta [**ddrescue**](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html).

Em resumo, é uma ferramenta de recuperação de dados, ela copia os dados de um
arquivo ou dispositivo de bloco (HD, CDROM, Pendrive etc) para outro, tentando
recuperar primeiro as partes boas em caso de erros de leitura.

A ferramenta está disponível apenas para sistemas linux e pode ser baixada no
site [GNU](http://gnu.c3sl.ufpr.br/ftp/ddrescue/) ou através do gerenciador de
pacotes da sua distribuição.

Para os usuários de Debian, ele vem no pacote gddrescue:

{% highlight bash %}
 # apt-get install gddrescue
{% endhighlight %}

O comando para utilizá-lo é simples:

{% highlight bash %}
 $ ddrescue origem destino log.log
{% endhighlight %}

Explicando:

+ **ddrescue**: a ferramenta para efetuar recuperação de dados;
+ **origem**: dispositivo ou arquivo de origem;
+ **destino**: dispositivo ou arquivo de destino;
+ **log.log**: arquivo de log;

Parece complicado, mas é bem simples de ser utilizado, porém, dependendo do
arquivo ou dispositivo pode levar um bom tempo até terminar. Listarei alguns
usos que podem ser realizados como exemplo, para tornar mais simples o
entendimento:

**Caso 1**

Nesse primeiro caso, seria um CD/DVD com problemas simples de leitura, uns
riscos ou arranhões que atrapaplham quando escuto música ou assisto a um vídeo,
coisa simples, não garanto que funcione com mídias super riscadas.

{% highlight bash %}
 $ ddrescue /dev/sr0 /home/user/imagem.img cd.log
{% endhighlight %}

Utilizei o comando **ddrescue** para copiar bloco-a-bloco o **CD/DVD** criando uma
**imagem.img** da mídia e um *arquivo de log* para consulta.

**Caso 2**

Já nesse caso seria a recuperação de um arquivo corrompido em um pendrive ou HD
externo, montado em /mnt.

{% highlight bash %}
 $ ddrescue /mnt/arquivo.origem /home/user/arquivo.destino arquivo.log
{% endhighlight %}

O comando **ddrescue** copia o **arquivo de origem** e salva um **arquivo de
destino**, criando um **arquivo de log** para consulta.

**Caso 3**

Nesse último caso, é a ilustração do procedimento que efetuei para clonar meu HD
com vários arquivos corrompidos.

{% highlight bash %}
 $ ddrescue /dev/sda1 /dev/sdb1 clone.log
{% endhighlight %}

Aqui, o comando **ddrescue** copia os blocos do **primeiro HD** para o **segundo HD**
criando um **log** para consulta.

#E aí, o que acharam?

Deixem suas sugestões, comentários, críticas e dúvidas logo abaixo.

<div class="fb-comments" data-href="http://blog.shundake.xyz{{ post.url }}/"
data-width="600" data-num-posts="2" data-colorscheme="dark"></div>
