---
layout: post
title: "Formatando pen drive no linux"
excerpt: "Conhecendo como se formata pen drive no sistema Linux"
categories: articles
tags: [mkfs, pendrive, drive, pen, formatar, usb, 2017]
author:
comments: true
share: true
image:
  feature: format/format.png
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


Olá!

Percebi que muitas pessoas possuem dificuldade ao formatar um pendrive, hd
externo ou cartão de memória no linux, por isso resolvi fazer esse post.

# Disclaimer

Os comandos a seguir serão executados com permissão de [superusuário](https://pt.wikipedia.org/wiki/Superusu%C3%A1rio), ou seja, com permissoes de root.

Use por sua conta e risco, e mantenha em mente que *com grandes poderes vêm grandes
responsabilidades*.

# Preparação

Agora, vamos instalar o [ntfs-3g](http://www.tuxera.com/community/open-source-ntfs-3g/) que será necessário para formatar HD's externos.

* Debian/Ubuntu e derivados

  ```bash
    usuario@domain:~ $ sudo apt-get update && sudo apt-get install ntfs-3g dosfstools
  ```

* Arch e derivados

  ```bash
    usuario@domain:~ $ sudo pacman -Syy  && sudo pacman -S ntfs-3g dosfstools
  ```

* RedHat/Suse e derivados

  ```bash
    usuario@domain:~ $ sudo yum install epel-release && yum install ntfs-3g dosfstools
  ```

# Ação

Primeiro, vamos identificar nosso dispositivo com o comando fdisk:

```bash
  usuario@domain:~ $ sudo fdisk -l
Disk /dev/sda: 465,8 GiB, 500107862016 bytes, 976773168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: C470E9B2-2B4E-4EC0-B708-1372BDBF8B31

Device         Start       End   Sectors   Size Type
/dev/sda1       2048   1001471    999424   488M EFI System
/dev/sda2    1001472 105859071 104857600    50G Linux filesystem
/dev/sda3  105859072 122636287  16777216     8G Linux filesystem
/dev/sda4  122636288 976773134 854136847 407,3G Linux filesystem


Disk /dev/sdb: 60 MiB, 62914560 bytes, 122880 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x00000000

Device     Boot Start    End Sectors Size Id Type
/dev/sdb1  *       32 122879  122848  60M  4 FAT16 <32M
```

Onde:
* *fdisk*: é o comando para verificar as unidades de discos disponíveis

O disco *sda*, geralmente, é o disco primário, ou seja, seu HD onde está
instalado o Sistema Operacional. Assim, o que vamos formatar (no meu caso) é o
disco *sdb*.

Note que as partições também são mostradas no comando, que é nosso foco para a
formatação.

# Criando partição no pendrive/HD

Caso seu dispositivo a ser formatado não possua partição, você pode criar uma
utilizando os passos abaixo:

1. Execute o comando abaixo trocando *sdb* pelo seu dispositivo.
   ```bash
    usuario@domain:~ $ sudo cfdisk /dev/sdb
   ```

2. Dê enter em *New*.
   ![Cfdisk1](/images/format/format1.png)

3. Dê enter novamente, a não ser que queira definir um tamanho diferente de
   partição.
   ![Cfdisk2](/images/format/format2.png)

4. Selecione *primary* e dê enter.
   ![Cfdisk3](/images/format/format3.png)

5. Selecione *Write* e dê enter.
   ![Cfdisk4](/images/format/format4.png)

6. Digite *yes* e dê enter.
   ![Cfdisk5](/images/format/format5.png)

7. Selecione *Quit* e dê enter.
   ![Cfdisk6](/images/format/format6.png)

# Direto ao ponto

Aqui, vamos falar sobre o caso de pendrives e HD's, pois cada um possui um
[Sistema de Arquivos](http://www.uniriotec.br/~morganna/guia/sistemas_de_arquivos.html) diferente para se trabalhar.

1. Formatando pendrives menores que 16G.
   ```bash
    usuario@domain:~ $ sudo mkfs.vfat -n <nome_do_pendrive> -F 32 /dev/sdb1
   ```
   Onde:
   * *mkfs.vfat*: comando para formatação de discos;
   * *-n*: opção para colocar um nome no dispositivo;
   * *-F*: tamanho do fat, sendo 16 ou 32;
   * */dev/sdb1*: partição a ser formatada.

2. Formatando pendrives ou HD's de 16G ou maiores.
   ```bash
    usuario@domain:~ $ sudo mkfs.ntfs -L <nome_do_pendrive> /dev/sdb1
   ```
   Onde:
   * *mkfs.ntfs*: comando para formatação de discos;
   * *-L*: opção para colocar um nome no dispositivo;
   * */dev/sdb1*: partição a ser formatada.


# E aí, o que achou?

Deixe suas comentários, seguestões, críticas e dúvidas logo abaixo.

{% include fb_comment.html %}
