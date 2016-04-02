---
layout: post
title: "Instalando o Arch Linux"
date: 2016-03-25T12:00:25(-03:00)
last_modified_at:
excerpt: Passo a passo da instalação do Arch Linux.
categories: articles
tags: [linux, arch, instalacao, passo]
author:
comments: true
share: true
image:
  feature: installArch.jpg
  credit:
  creditlink:
---

"Fui instalar um tal de Arch Linux, mas o [Guia do Iniciante](https://wiki.archlinux.org/index.php/Beginners'_guide_%28Portugu%C3%AAs%29) é muito extenso e complicado."

Se você já passou por isso, esse é o local certo. Tentarei, da forma mais
simples e direta executar o passo a passo da instalação.

# 1. Baixando a imagem ISO

O melhor lugar para baixar a iso é no
[site](https://www.archlinux-br.org/download/).

Baixe o arquivo, que provavelmente não terá mais que 800Mb e grave em um CD/DVD
ou crie um pendrive bootavel.

# 2. Instalação

Depois de inicializar o CD/DVD, lhe será apresentado o terminal para instalação.

O que muitas pessoas estranham é a "falta" de um instalador em modo gráfico, mas
lhes garanto que não é de todo difícil.

Se você possuir uma placa mãe UEFI (o que é muito comum, atualmente, devido ao
Windows 8/8.1/10) podemos ter certeza verificando o conteúdo do diretório
/sys/firmware/efi/efivars. Se houverem arquivos, quer dizer que o Arch Linux foi
inicializado em modo UEFI.

{% highlight bash %}
# ls /sys/firmware/efi/efivars
{% endhighlight %}

**2.1 Teclado**

Em seguida, vamos 'setar' o layout de nosso teclado:

{% highlight bash %}
# loadkeys br-abnt2
{% endhighlight %}

Caso o layout de seu teclado não seja esse, você poderá encontrar os layouts:

{% highlight bash %}
# ls /usr/share/kbd/keymaps/ -R
{% endhighlight %}

**2.2 Conexão com internet**

Geralmente, quando vamos efetuar alguma instalação de
[SO](https://pt.wikipedia.org/wiki/Sistema_operativo) a rede cabeada se conecta
de forma 'automática'. Para ter certeza que esteja conectado execute um ping:

{% highlight bash %}
# ping -c 3 google.com
PING www.l.google.com (74.125.132.105) 56(84) bytes of data.
64 bytes from wb-in-f105.1e100.net (74.125.132.105): icmp_req=1 ttl=50 time=17.0
ms
64 bytes from wb-in-f105.1e100.net (74.125.132.105): icmp_req=2 ttl=50 time=18.2
ms
64 bytes from wb-in-f105.1e100.net (74.125.132.105): icmp_req=3 ttl=50 time=16.6
ms

--- www.l.google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 16.660/17.320/18.254/0.678 ms
{% endhighlight %}

Se seu comando trouxe algo muito diferente, deveremos conectar manualmente. Para
isso utilize o comando 'ip' para identificar a interface de rede e o comando
'dhcpcd' para conectar.

{% highlight bash %}
# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp7s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 00:09:00:00:00:00 brd ff:ff:ff:ff:ff:ff
3: wlp8s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 00:09:00:00:00:00 brd ff:ff:ff:ff:ff:ff

# dhcpcd enp7s0

{% endhighlight %}

No meu caso, **enp7s0** é o nome da minha interface de rede cabeada. Se eu fosse
efetuar conexão em rede sem fio seria:

{% highlight bash %}
# ip link set wlp8s0 up

# wifi-menu wlp9s0
{% endhighlight %}

Caso o primeiro comando (ip link set wlp8s0 up) retorne algo como:

{% highlight bash %}
# ip link set wlp8s0 up

SIOCSIFFLAGS: No such file or directory
{% endhighlight %}

É necessário efetuar a instalação do driver para sua placa de rede sem fio.
Recomendo que a instalação seja feita utilizando uma conexão cabeada para que
não existam muitos problemas.

**2.3 Preparando os Discos**

Aqui vamos efetuar o paritionamento do hd utillizando o utilitário 'cfdisk':

{% highlight bash %}
# cfdisk /dev/sda
{% endhighlight %}

Darei o exemplo de um particionamento:

EFI: (Obs: Se o seu sistema não for EFI essa partição não é necessária)
		Escolha Nova (ou pressione N) - Enter para Primaria - digite "500"  - Enter para "No início".
		Escolha o tipo (ou pressione T) - Enter em "EFI System".

Raíz:
    Escolha Nova (ou pressione N) - Enter para Primaria - digite "15360" - Enter para "No início" - Enter para Bootável.

Swap:
    Pressione seta para baixo para mover a seleção para o "espaço livre" no disco rígido.
    Escolha Nova (ou pressione N) - Enter para Primaria - digite "1024" – Enter para "No início".
    Escolha o tipo (ou pressione T) - Pressione a seta para baixo - Enter em "Linux swap"

Home:
    Pressione seta para baixo para mover a seleção para o "espaço livre" no disco rígido.
    Escolha Nova (ou pressione N) – Enter para Primaria - Enter para usar o restante do espaço em disco.
    Escolha o tipo (ou pressione T) - Pressione a seta para baixo - Enter em "Linux filesystem"

Selecione Gravar (ou pressione Shift+W) e digite "yes" (sem aspas) e selecione
Sair (ou pressione Q).

Agora vamos formatar as partições:

{% highlight bash %}
# mkfs.ext4 /dev/sda2

# mkfs.ext4 /dev/sda4

# mkfs.vfat /dev/sda1

# mkswap /dev/sda3

# swapon /dev/sda3
{% endhighlight %}

Obs: cada linha com '#' é um comando a ser executado no terminal.

**2.4 Montando as partições**

Preste atenção na ordem de montagem, pois ela é importante.

Primeiro, monte a partição raíz em /mnt. Seguindo o exemplo abaixo (em seu sistema, pode ser diferente) seria algo como:

{% highlight bash %}
# mount /dev/sda2 /mnt
{% endhighlight %}

Monte então a partição destinada ao /home e outras separadas para o /boot, /var, etc, caso desejar:

{% highlight bash %}
# mkdir /mnt/home

# mount /dev/sda4 /mnt/home
{% endhighlight %}

Monte a partição EFI da seguinte maneira:

{% highlight bash %}
# mkdir /mnt/boot/efi

# mount /dev/sda1 /mnt/boot/efi
{% endhighlight %}

** 2.5 Selecionando um repositório **

Antes de instalar, você pode desejar configurar seu arquivo mirrorlist para
apontar pra um repositório de seu interesse. O repositório é onde o comando
'pacman' buscará as atualizações e programas a serem instalados.

Para selecionar um mirror, apenas recorte-o de onde estiver e cole-o no topo da
lista, faça isso utilizando seu editor de texto preferido. No meu caso, utilizei
o vi e coloquei em primeiro o servidor do Brasil.

{% highlight bash %}
# vi /etc/pacman.d/mirrolist

##
## Arch Linux repository mirrorlist
## Sorted by mirror score from mirror status page
## Generated on 2015-03-01
##

## Score: 1.7, Brazil
Server = http://pet.inf.ufsc.br/mirrors/archlinux/$repo/os/$arch
## Score: 0.3, France
Server = http://archlinux.polymorf.fr/$repo/os/$arch
## Score: 0.4, Denmark
Server = http://mirror.one.com/archlinux/$repo/os/$arch
{% endhighlight %}

** 2.6 Instalação base **

A instalação do sistema base é feita pelo comando 'pacstrap'.

A maioria dos programas poderão ser instalados posteriormente utilizando o
comando 'pacman' ou podem ser adicionados no comando abaixo:

{% highlight bash %}
# pacstrap -i /mnt base base-devel
{% endhighlight %}

** 2.7 FSTAB **

O arquivo ["fstab"](https://wiki.archlinux.org/index.php/Fstab) contém as partições e seus respectivos pontos de montagem, que
são executados quando o sistema está sendo carregado.

{% highlight bash %}
# genfstab -U /mnt >> /mnt/etc/fstab
{% endhighlight %}

** 2.8 Chroot e configuração do sistema base **

O ['chroot'](https://pt.wikipedia.org/wiki/Chroot) nos permitirá interagir com
nosso sistema recém instalado efetuando as modificações necessárias para que
possamos utilizá-lo.

{% highlight bash %}
# arch-chroot /mnt
{% endhighlight %}

Agora que estamos em nosso sistema, poderemos configurá-lo à nossa vontade, o
primeiro passo é configurar a "linguagem":

{% highlight bash %}
# mv /etc/locale.gen /etc/locale.gen-bkp

# echo "pt_BR.UTF-8 UTF-8" > /etc/locale.gen

# locale-gen

# echo LANG=pt_BR.UTF-8 > /etc/locale.conf

# export LANG=pt_BR.UTF-8
{% endhighlight %}

Em seguida vamos tornar permanente a configuração de layout de teclado:

{% highlight bash %}
# loadkeys br-abnt2

# echo "KEYMAP=br-abnt2" > /etc/vconsole.conf
{% endhighlight %}

Para que o fuso horário fique correto é necessário que façamos a configuração:

{% highlight bash %}
# ln -s /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
{% endhighlight %}

Se o seu fuso for outro, você poderá encontrá-lo em "/usr/share/zoneinfo/", e
então é só substituir o caminho "/usr/share/zoneinfo/America/Sao_Paulo" pelo de
sua localização.

Para que o relógio mostre as horas corretas é necessário configurar, também:

{% highlight bash %}
# hwclock --systohc --utc
{% endhighlight %}

Depois de efetuada as mudanças de hora, teclado e fuso, vamos configurar o nome
da máquina (hostname).

{% highlight bash %}
# echo myhostname > /etc/hostname

# vi /etc/hosts

#
# /etc/hosts: static lookup table for host names
#

#<ip-address>			<hostname.domain.org>			<hostname>
127.0.0.1					localhost.localdomain			localhost myhostname
::1								localhost.localdomain			localhost myhostname
{% endhighlight %}

Onde vemos "myhostname" você deve colocar o nome do seu computador.

Na configuração de redes vou passar o mais simples e fácil:

{% highlight bash %}
# pacman -S wireless_tools wpa_supplicant wpa_actiond wicd wicd-gtk

# systemctl enable wicd

{% endhighlight %}

Se você instalou o Arch Linux x86_64, fique sabendo que muitos programas
precisam de bibliotecas 32 bits que não são encontradas no repositório padrão,
pra poder instalá-las é necessário habilitar o repositório [multilib], removendo
o '#' no início da linha, como pode ser visto logo abaixo:

{% highlight bash %}
# vi /etc/pacman.conf





# If you want to run 32 bit applications on your x86_64 system,
# enable the multilib repositories as required here.

#[multilib-testing]
#Include = /etc/pacman.d/mirrorlist

[multilib]
Include = /etc/pacman.d/mirrorlist
{% endhighlight %}

Agora, vamos definir a senha de root e adicionar um novo usuário:

{% highlight bash %}
# passwd

# useradd -m -g users -s /bin/bash meuusuario

# passwd meuusuario
{% endhighlight %}

Em seguida, vamos gerar a imagem ramdisk inicial, que conterá nossas
modificações no sistema:

{% highlight bash %}
# mkinitcpio -p linux
{% endhighlight %}

Para que nosso sistema possa ser iniciado, é necessário que esteja instalado um
gerenciador de boot, nesse casoi, mostrarei a instalação do
[GRUB](https://pt.wikipedia.org/wiki/GNU_GRUB).

{% highlight bash %}
# pacman -S grub-efi-x86_64 efibootmgr

# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch_grub --recheck

# cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo

# efibootmgr -c -g -d /dev/sdX -p Y -w -L "Arch Linux (GRUB)" -l '\\EFI\\arch_grub\\grubx64.efi'
{% endhighlight %}

Após a instalação do GRUB, vamos atualizar o sistema:

{% highlight bash %}
# pacman -Syu
{% endhighlight %}

E instalar algumas coisas necessárias para a utilização:

{% highlight bash %}
# pacman -S libreoffice-fresh libreoffice-fresh-pt-BR gimp guvcview pulseaudio
alsa-firmware alsa-plugins alsa-lib pavucontrol flashplugin lame cdrkit
dvd+rw-tools
{% endhighlight %}

Como opção, deixarei o modo simples de instalação de 2 modos gráficos:

** [KDE](https://wiki.archlinux.org/index.php/KDE) **
{% highlight bash %}
# pacman -S plasma-desktop gdm kde-applications kde-l10n-pt_br xorg-server
xorg-xinit xorg-server-utils

# systemctl enable gdm.service
{% endhighlight %}

** [Mate](https://wiki.archlinux.org/index.php/MATE) **
{% highlight bash %}
# pacman -S mate mate-extra lightdm xorg-server xorg-xinit xorg-server-utils

# systemctl enable lightdm.service
{% endhighlight %}

Após finalizar as instalações só precisamos sair do ambiente chroot e reiniciar.

{% highlight bash %}
# exit

# reboot
{% endhighlight %}


E é isso. Agora você tem um Arch Linux, instalado e configurado para seu uso.

# E aí, o que achou?

Deixe suas comentários, seguestões, críticas e dúvidas logo abaixo.

{% include fb_comment.html %}
