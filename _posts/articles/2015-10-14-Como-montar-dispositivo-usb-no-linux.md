---
layout: post
title: "Como montar dispositivo usb no linux"
excerpt: "Instalei uma nova distribuição linux no pc e preciso montar um pendrive... O que
fazer?"
categories: articles
tags: [linux, usb, montar]
author:
comments: true
share: true
image:
  feature: mountUSB.jpg
  credit:
  creditlink:
---

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/pt_BR/sdk.js#xfbml=1&version=v2.5&appId=541394239351629";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>


Pensemos na seguinte situação:

*Acabei de instalar uma nova distribuição linux no meu computador, coisa bonita,
coisa nova. Eu, todo empolgado, vendo quais são as características que a torna
diferenciada, me lembro que não havia terminado um relatório, no writer, a ser
entregue dentro de 1 hora. Mais do que depressa pego o pendrive no qual salvo os arquivos referentes ao meu
trabalho, e coloco na porta usb do meu computador. Qual a minha surpresa, o
pendrive não montou automaticaticamente.*

A *história* que contei logo acima acontece muitas vezes, na maioria delas,
porque estamos acostumados com ações automáticas por parte do SO. O que
facilita a vida do usuário, mas pode tornar-se um risco de segurança para a sua
máquina.

Não que seja uma <s>ameaça terrorista</s> deixar o usb montar automaticamente, no
entanto, caso seja sua máquina no trabalho, uma que mais pessoas tenham acesso,
ou até mesmo a da sua casa, montar automaticamente, pendrives desconhecidos
pode não ser uma boa escolha.

É certo que os sistemas linux são 'conhecidos' por serem mais seguros, não
possuirem tantos problemas com virus e malwares, mas um
[script](http://lwn.net/Articles/427135/) bem feito pode lhe
deixar em maus bocados.

Existem outros exemplos, mas meu foco, neste post, não é segurança <s>também é,
mas não entremos em detalhes</s>. Nosso foco é sobre **como montar o
dispositivo de armazenamento usb de forma manual**.

Pois bem, acabei me desviando um bocado do tema, e futuramente farei um post
sobre esse outro assunto.

# Continuando

Aqui temos duas opções para utilizar:

+ [mount](http://www.gnu.org/software/libc/manual/html_node/Mount_002dUnmount_002dRemount.html): que é utilizado apenas como root

+ [udisks](http://udisks.freedesktop.org/docs/latest/udisksctl.1.html): a ser
  utilizado como usuário normal.

# Utilizando *mount*

O comando *mount* vem instalado, por padrão, no linux, então não há necessidade
de se preocupar com downloads ou atualizações. Sua sintaxe de uso é bem simples:

{% highlight bash %}
# mount /dev/sdX /mnt
{% endhighlight %}

Sendo:

+ **mount**: a ferramenta que possibilita a montagem de sistemas de arquivos;

+ **/dev/sdX**: é o dispositivo a ser montado;

+ **/mnt**: é o diretório onde será montado o dispositivo;

Lembrando que somente o [Super
User](https://pt.wikipedia.org/wiki/Superusu%C3%A1rio) poderá utilizá-lo.

Para identificar os dispositivos o comando
[fdisk](http://dev.man-online.org/man8/fdisk/):

{% highlight bash %}
# fdisk -l
Disk /dev/sda: 465,8 GiB, 500107862016 bytes, 976773168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: A21D5F1F-8D96-4456-8930-D9AA054FB8C2

Device         Start       End   Sectors   Size Type
/dev/sda1       2048   1001471    999424   488M EFI System
/dev/sda2    1001472  89843711  88842240  42,4G Linux filesystem
/dev/sda3   89843712 105467903  15624192   7,5G Linux swap
/dev/sda4  105467904 976773119 871305216 415,5G Linux filesystem


Disk /dev/sdb: 60 MiB, 62914560 bytes, 122880 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x00000000

Device     Boot Start    End Sectors Size Id Type
/dev/sdb1  *       32 122879  122848  60M  4 FAT16 <32M
{% endhighlight %}

Ao executar o comando **fdisk** com a opção **-l** obtive uma listagem dos
dispositivos e partições disponíveis. Note que foram identificados os dispositivos
**/dev/sda** com 4 partições e **/dev/sdb** com uma partição.

Por padrão, /dev/sda é o HD de seu computador, ou o dispositivo no qual o sistema
operacional está instalado. Já /dev/sdb, provavelmente é o dispositivo que foi
plugado por último, caso insira um outro em uma porta usb, provavelmente na saída
do comando utilizado veríamos um /dev/sdc.

Uma coisa que devemos nos atentar é que nós montamos as partições e não o dispositivo
em si. Assim, iremos montar a partição 1 do dispositivo /dev/sdb, ou seja, **/dev/sdb1**.

Após identificar o dispositivo a ser montado, podemos, utilizar o comando para tal:

{% highlight bash %}
# mount /dev/sdb1 /mnt
{% endhighlight %}

O comando irá montar a partição **sdb1** no diretório **/mnt**.

No entanto, se sua intenção é montar um HD externo, com partição NTFS, é necessário que
tenha instalado o pacote [ntfs-3g](https://en.wikipedia.org/wiki/NTFS-3G) que pode ser
encontrado no repositório da distribuição que você utiliza.

O comando a ser utilizado será quase o mesmo, tendo como diferença a opção **-t
ntfs-3g**, onde ele define que o tipo **-t**  de sistema da partição é
**ntfs-3g**.

{% highlight bash %}
# mount -t ntfs-3g /dev/sdX /mnt
{% endhighlight %}

E para desmontar o dispositivo:

{% highlight bash %}
# umount /mnt
{% endhighlight %}

+ **umount**: comando para desmontar a partição;
+ **/mnt**: o diretório onde foi montada a partição, podendo ser, trocado pela
  partição em si (/dev/sdb1, por exemplo);

# Utilizando udisks (udisksctl)

O **udisks** possui um daemon, udisksd, que implementa interfaces de D-Bus
bem definidos que podem ser usados para consultar e manipular os dispositivos de
armazenamento, fornecendo uma ferramenta de linha de comando, **udisksctl**, que
pode ser utilizada para interagir com o daemon.

Parece algo difícil, mas é bem simples na realidade. Antes de utilizá-lo, é
necessário instalar o pacote pelo gerenciador da sua distribuição, pois nem
todas o possuem por padrão. Como uso, atualmente, Arch Linux, utilizarei o
**pacman** para instalar o pacote.

{% highlight bash %}
# pacman -S udisks2
{% endhighlight %}

Caso sua distribuição não possua o pacote ntfs-3g instalado, é necessário que
faça a instalação, pois é necessário para montar partições do tipo NTFS.

Quando instalado poderemos utilizar o comando abaixo para verificar quais os
dispositivos disponíveis para montagem.

{% highlight bash %}
$ udisksctl status
{% endhighlight %}

O que nos traria uma saída semelhante a essa:

{% highlight bash %}
$ udisksctl status
MODEL                     REVISION  SERIAL               DEVICE
--------------------------------------------------------------------------
WDC WD5000LPVX-75V0TT0    01.01A01  WX81AB43R1KC         sda
GENERIC USB FLASH DISK    0201      0212090000001        sdb
{% endhighlight %}

No entanto, podemos ver que não nos mostra, exatamente, as partições de cada
dispositivo. Se for um pendrive, como no meu caso, não há tanto problema, pois
geralmente temos apenas uma partição, ficando, então, **/dev/sdb1**, nesse caso.

{% highlight bash %}
$ udisksctl mount -b /dev/sdb1
{% endhighlight %}

Onde:

+ **udisksctl**: ferramenta provida pelo pacote udisks;

+ **mount**: opção que demonstra a ação a ser efetuada, que é 'montar'. Uma outra opção,
  nesse caso seria unmount, para desmontar;

+ **-b**: opção para designar dispositivos de blocos;

+ **/dev/sdb1**: o dispositivo/partição que deseja montar;

Diferentemente do **mount** o **udisksctl** não precisa de um ponto para
montagem, ou seja, não precisa que você especifique um diretório para montar a
partição desejada, ela será montada em uma pasta encontrada em
**/run/media/seuUser/nomeParticao**. Mas não se preocupe, quando executar o
comando será mostrado o local onde foi montada a partição.

{% highlight bash %}
$ udisksctl mount -b /dev/sdb1
Mounted /dev/sdb1 at /run/media/usuario/PENDRIVE.
{% endhighlight %}

No entanto, se o que pretende é montar um HD externo, é melhor recorrer à
ferramenta **fdisk** como foi comentado logo mais acima.

Quando for necessário desmontar a partição o comando será:

{% highlight bash %}
$ udisksctl unmount -b /dev/sdb1
{% endhighlight %}

Apenas lembrando:

+ **udisksctl**: a ferramenta que estamos utilizando;

+ **umount**: opção que demonstra a ação 'desmontar';

+ **-b**: opção para designar o dispositivo de blocos;

+ **/dev/sdb1**: a partição que deseja desmontar;

A saída obtida ao executar o comando seria semelhante a:

{% highlight bash %}
$ udisksctl unmount -b /dev/sdb1
Unmounted /dev/sdb1.
{% endhighlight %}

#E aí, o que acharam?

Deixem suas sugestões, comentários, críticas e dúvidas logo abaixo.

<div class="fb-comments" data-href="http://blog.shundake.xyz{{ page.url }}/"
  data-width="600" data-numposts="5"></div>

