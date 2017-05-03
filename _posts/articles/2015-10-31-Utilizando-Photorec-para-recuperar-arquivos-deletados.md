---
layout: post
title: "Utilizando Photorec para recuperar arquivos deletados"
excerpt: "Recuperando fotos e videos com o Photorec"
categories: articles
tags: [recover, recuperar, arquivos, imagens, 2015]
author:
comments: true
share: true
image:
  feature: recoverImage.jpg
  credit:
  creditlink:
---

**Aviso: Utilizar ou não o software descrito é de inteira responsabilidade do
leitor. Os passos descritos nesse post foram testados pelo autor, no entanto,
existe a possibilidade de não funcionar causando danos ou perdas irreparáveis,
tornando impossível a recuperação dos dados almejados.**

Vez ou outra nos deparamos com eventos dos quais queremos guardar alguma
lembrança em forma de imagem. Com o advento da tecnologia, nos é possível tirar
inúmeras fotos com um único dispositivo e armazená-las em uma pequena peça. à
qual demos o nome de cartão de memória.

No entanto, muitas vezes somos pegos de surpresa por alguma mensagem num idioma
diferente com o qual estamos acostumados a lidar, e não prestamos atenção ao
pressionar o botão "ok".

Foi após uma viagem que minha prima, por acidente, clicou em um desses "ok" e
apagou, acidentalmente todas as fotos do cartão de memória da máquina. Um
verdadeiro caos se instalou na mente de muita gente... As fotos da viagem
estariam perdidas.

Na realidade, quando o cartão de memória chegou em minhas mãos, eu sabia o que
fazer, a única coisa, é que o medo de não conseguir é muito grande, não apenas
não conseguir, mas, estragar o que é dos outros não é agradável, então tomei a
liberdade de efetuar uma cópia do dispositivo em imagem e apenas trabalhar em
cima dela.

# Starting

Criar a imagem foi o passo mais simples possível, utilizando o comando [dd](http://www.vivaolinux.com.br/dica/Usando-o-comando-dd),
encontrado em qualquer sistema UNIX-like, pude efetuar a cópia.

{% highlight bash %}
# dd if=/dev/mmblk0p of=/home/user/cartao.img
{% endhighlight %}

Onde:

* **dd**: é o comando utilizado;
* **if=**: opção de origem;
* **/dev/mmblk0p**: o cartão de memória;
* **of=**: opção de destino;
* **/home/user/cartao.img**: o destino, nesse caso um arquivo chamado
  "cartao.img";

O comando acima, bem como todos os outros que são executados como usuário
**root**, podem ser utilizados com o comando **sudo**.

{% highlight bash %}
$ sudo dd if=/dev/mmblk0p of=/home/user/cartao.img
{% endhighlight %}


Feita a cópia, removi o cartão e guardei-o. Pois não queria danificar a fonte.

**Uma observação**: Quando tentar recuperar arquivos deletados de um dispositivo,
tenha certeza de não sobrescrever as informações e de nunca utilizar o
dispositivo fonte <s>nem montá-lo</s> (pendrive, cartão de memória, hd etc) para
evitar perda de dados ou impossibilidade de recuperação.

# Recuperando

Para que seja possível iniciar a operação, é necessário instalar o pacote [photorec](http://www.cgsecurity.org/wiki/PhotoRec),
que pode ser instalado em DOS, Windows, Mac e Linux. Para maiores informações
acesse o [site](http://www.cgsecurity.org/wiki/TestDisk_Download).

Detalharei o processo em um sistema Linux, mas se o que você procura é
executá-lo no Windows ou Mac, no
[site](http://www.cgsecurity.org/wiki/PhotoRec_Step_By_Step) pode ser encontrado
um passo-a-passo.

A instalação pode ser feita baixando os pacotes no
[site](http://www.cgsecurity.org/wiki/TestDisk_Download) ou através do
gerenciador de pacotes da sua distribuição, no meu caso, usando [Archlinux](https://www.archlinux-br.org/)
instalei com o seguinte comando:

{% highlight bash %}
# pacman -S testdisk
{% endhighlight %}

O pacote [testdisk](http://www.cgsecurity.org/) instala dois programas para
recuperação:

* **[testdisk](http://www.cgsecurity.org/wiki/TestDisk)**: utilizado para recuperar partições perdidas
  arquivos de diferentes extensões e recuperar partições bootaveis;
* **[photorec](http://www.cgsecurity.org/wiki/PhotoRec)**: voltado para
  recuperações de vídeos, documentos e imagens;

Antes de iniciar a recuperação crie uma pasta onde irá armazenar a imagem que
foi gerada do disposittivo, bem como onde serão salvos os arquivos recuperados.
No meu caso, criarei em minha pasta pessoal:

{% highlight bash %}
$ mkdir recupera
$ mv cartao.img recupera
{% endhighlight %}


Com a imagem do dispositivo em "mãos", o comando a ser utilizado é o seguinte:

{% highlight bash %}
# photorec /home/user/recupera/cartao.img
{% endhighlight %}

Sendo:

* **photorec**: o programa que estamos utilizando;
* **/home/user/recupera/**: o caminho onde encontra-se a imagem gerada pelo comando
  dd, do início do post;
* **cartao.img**: a imagem criada com o comando dd;

![Primeira Tela](/images/recover01.png)

Nesta tela é possível selecionar o dispositivo, no caso, como indicamos,
diretamente, o arquivo de imagem, aparecerá apenas ele.

Ao selecionar o dispositivo, com as setas ↑ e ↓ do teclado. E então, selecionar
*"Proceed"* ("Prosseguir" em português <s>eu acho</s>).

![Segunda Tela](/images/recover02.png)

Após selecionar o dispositivo, é necessário selecionar a partição a ser
*scaneada* por arquivos.

![Terceira Tela](/images/recover03.png)

Selecionada a partição desejada, devemos selecionar qual o espaço a ser
analisado:

* **_Free_**: Espaço não alocado apenas;
* **_Whole**: Toda a partição;

![Quarta Tela](/images/recover04.png)

Aqui é preciso selecionar o [sistema de
arquivos](https://pt.wikipedia.org/wiki/Sistema_de_ficheiros), original, de seu
dispositivo. Como pode ser visto na segunda imagem, o tipo de sistema de
arquivos é FAT 32, logo a opção a ser selecionada é *"Other"*. A seleção é
feita com as setas → e ← do teclado.

![Quinta Tela](/images/recover05.png)

Nessa etapa, selecionamos o diretório onde serão salvos os arquivos recuperados,
onde:

* **Arrow**: setas (↑,↓,←,→) do teclado para navegar através das pastas;
* **C**: Seleciona o diretório no qual você se encontra como destino para salvar
  os arquivos.
* **Directory**: é o diretório onde você se encontra;

Ao selecionar a pasta destino, pressionamos **shift+c** <s>C maiúsculo</s> para
selecionar aa pasta e prosseguir à execução do programa

![Sexta Tela](/images/recover06.png)

Na última tela, vemos o andamento da análise, onde acompanhamos a quantidade de
arquivos encontrados e suas
[extensões](https://pt.wikipedia.org/wiki/Extens%C3%A3o_de_nome_de_ficheiro). É
possível cancelar o processo com a tecla *Enter*. Nesse caso, se reiniciar o
processo (da mesma forma) será perguntado se quer prosseguir com a última
escolha feita, se for sim, o processo será retomado de onde parou, caso
contrário, as escolhas deverão ser feitas novamente.

Ao final da análise/recuperação você encontrará um diretório com o nome de
**recup_dir.n**, onde:

* **recup_dir.**: diretório de arquivos recuperados;
* **n**: uma numeração, condizente com quantas vezes você executou o comando,
  evitando que as pastas sejam sobrescritas/substituídas a cada vez que executar
  o programa.

Como resultado, obtive cerca de 5000 arquivos de imagem recuperados, dentre
eles, as fotos tão almejadas. Ou seja, sucesso!

Um ponto contra que pude perceber, foi que, ao recuperar os arquivos, os nomes
não eram condizentes com os originais.

# E aí, o que achou?

Deixe suas comentários, seguestões, críticas e dúvidas logo abaixo.

{% include fb_comment.html %}
