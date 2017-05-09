---
layout: post
title: "Criando apelidos para o bash (bash aliases)"
excerpt: Como criar apelidos para comandos longos no bash?
categories: articles
tags: [bash, aliases, linux, apelidos, utilitarios, 2015]
author:
comments: true
share: true
image:
  feature: bashAliases/bashAliases.jpg
  credit:
  creditlink:
---

Quantas vezes já não nos pegamos a procurar de forma enlouquecida aquela linha
de comando, quilométrica, que utilizamos uma única vez?

Muitas vezes, para não perdermos algum comando que pensemos ser importante, ou
que iremos utilizar posteriormente, salvamos em arquivo de texto ou em alguma
nota ([Tomboy](https://pt.wikipedia.org/wiki/Tomboy_%28software%29), por
exemplo).

Então, porque não colocar essas linhas complexas de comandos, de uma forma
intuitiva e como se fosse um comando nativo do linux?

Por exemplo, quando efetuo download de imagens de camera de segurança (gealmente
2 ou 3 por vez), eu preciso juntá-los para entregar a meus superiores, a linha que
utilizo é a seguinte:

{% highlight bash %}
$ mencoder 001_M_03052015030300.mp4 001_M_03052015030324.mp4
001_M_03052015030443.mp4 -ovc copy -of lavf -o out.mp4
{% endhighlight %}

Após criar o alias, ficou dessa forma:

{% highlight bash %}
$ juntavideo 001_M_03052015030300.mp4 001_M_03052015030324.mp4
001_M_03052015030443.mp4 out.mp4
{% endhighlight %}

Está certo que não diminuiu tanto o comando, no entanto, já me economizou tempo,
pois coloquei um nome que vou me lembrar e não preciso ficar lembrando, toda
vez, quais as opções a serem colocadas.

# Por onde começar?

A primeira coisa a fazermos é localizar o arquivo bashrc ou bash.bashrc:

* Pode ser que exista um no home de cada usuário: **$HOME/.bashrc**

* ... que esteja no etc: **/etc/bashrc**

* ... ou então em: **/etc/bash.bashrc**

A grande diferença, nesses casos, é que o arquivo em /etc é para todo o sistema
e o encontrado na $HOME é apenas para aquele usuário.

O que vou passar aqui é *system wide*, ou seja, abrange todo o sistema, sendo
assim, qualquer usuário poderá utilizar os *aliases* criados.

Depois de encontrar o arquivo, no meu caso **/etc/bash.bashrc**, vamos editá-lo
utilizando o seu editor de textos preferido, e adicionar as linhas referentes à
Definição dos *aliases*.

{% highlight bash %}
# vim /etc/bash.bashrc

#
# ~/.bashrc
#

# If not running interactively, don't do anything
[[ $- != *i* ]] && return

alias ls='ls --color=auto'
PS1='[\u@\h \W]\$ '

# Definição dos aliases
if [ -f /etc/bash_aliases ]; then # verifica se o arquivo /etc/bash_aliases
  . /etc/bash_aliases             # existe, se existir ele será carregado no
fi                                # momennto do login

{% endhighlight %}

E após adicionar as linhas acima, vamos criar nosso arquivo **bash_aliases**,
que é onde colocaremos nossos "comandos personalizados". Caso você esteja
fazendo isso no seu **$HOME/bashrc**, você poderá colocar os *aliases* direto no
arquivo, sem depender de um arquivo externo, até mesmo no **/etc/bash.bashrc**
isso pode ser feito, mas, em minha opnião, é muito mais organizado manter um
arquivo externo para isso.

{% highlight bash %}
# touch /etc/bash_aliases

# vim /etc/bash_aliases

#
# ~/.bash_aliases
#

# habilita o suporte a cores no ls e outros comandos úteis
if [ -x /usr/bin/dircolors ]; then
  test -r /etc/dircolors && eval "$(dircolors -b /etc/dircolors)" || eval "$(dircolors -b)"
  alias ls='ls --color=auto'
  alias dir='dir --color=auto'
  alias vdir='vdir --color=auto'
  alias grep='grep --color=auto'
  alias fgrep='fgrep --color=auto'
  alias egrep='egrep --color=auto'
fi

# utilizando comandos de modo seguro
 # Obs. é perigoso utilizar o "rm -i" no alias, pois como é uma personalização,
 # caso nos acostumemos a utilizá-lo, quando pegarmos uma distro ou conta de usuário
 # sem esse alias, podemos deletar coisas erradas sem querer. Use por sua conta e risco.
 alias rm='rm -i' # habilita o modo interativo
 alias cp='cp -i' # nos comandos, perguntando a
 alias mv='mv -i' # cada arquivo se deve proceder

# comandos personalizados
 alias gravadvd='growisofs -speed=2 -dvd-compat -Z /dev/sr0=$1' # comando para gravar iso em dvd e cd
 alias monta='udisksctl mount -b $1' # monta pendrive/sdcard como usuário normal
 alias desmonta='udisksctl unmount -b $1' # desmonta pendrive/sdcard como usuário normal

{% endhighlight %}

A sintaxe para a criação de *aliases* é simples: **alias alias_name='command'**.
Podendo ser utilizado aspas simples (') ou  duplas (") para definir o *alias*.
Sendo assim:

* **alias**: comando que permite a criação de "apelidos";

* **alias_name**: o apelido que vamos dar ao comando;

* **command**: o comando a ser executado quando digitado o "apelido";

O *aliases* que coloquei no arquivo **/etc/bash_aliases** que está logo acima,
contém alguns dos comandos que mais uso e que achei bacana compartilhar, sem
contar que os comandos de montagem de pendrive podem ser encontrados [aqui](http://blog.shundake.xyz/articles/Montar-manualmente-usb-no-linux/).

Vale notar, que utilizo as aspas simples (') para definir os comandos, isso
porque, alguns comandos mais complexos necessitam do uso de ambas as aspas para
que funcione de forma correta, a única forma de conseguir fazê-los funcionar, é
testando a melhor combinação.

Eu estava colocando um *alias* no servidor squid que monitoro, para ver os logs
de forma mais amigável. No final, quando terminei de colocar no arquivo a linha
ficou dessa forma:

{% highlight bash %}
#
#  Aliases
#

# enable color support of ls and also handy aliases
if [ -x /usr/bin/dircolors ]; then
  #test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
  test -r /etc/dircolors && eval "$(dircolors -b /etc/dircolors)" || eval "$(dircolors -b)"
  alias ls='ls --color=auto'
  alias dir='dir --color=auto'
  alias vdir='vdir --color=auto'
  alias grep='grep --color=auto'
  alias fgrep='fgrep --color=auto'
  alias egrep='egrep --color=auto'
fi

alias squidlog='tail -f /var/log/squid/access.log | awk '"'"'{print strftime("\033[1;31m%a %d/%m/%Y %H:%M:%S\033[0m",$1) " " $3 " \033[1;35m" $8 "\033[0m " $4 " \033[1;32m" $6 "\033[0m \033[1;33m" $7 "\033[0m"}'"'"

{% endhighlight %}

Um pouco complexo para se compreender, mas funcionou, isso é o que importa <s>ou
não</s>. Mas, na realidade, nesse caso, cada aspa simples (') foi colocada entre
aspas duplas ("), para que funcionasse corretamente.

Caso seja necessário remover algum *alias* é só editar o arquivo
**/etc/bash_aliases** e comentar com # ou apagar. Uma pequena observação para
quando efetuar modificações no arquivo citado, para que os apelidos funcionem
corretamente, é necessário carregar o arquivo **/etc/bash.bashrc**, para isso
basta executar:

{% highlight bash %}
$ source /etc/bash.bashrc
{% endhighlight %}

E pronto! Você já pode utilizar os comandos personalizados com o "apelido" mais
indutivo que foi criado.

# E aí, o que achou?
 Deixem suas sugestões, comentários, críticas e dúvidas logo abaixo.

{% include fb_comment.html %}
