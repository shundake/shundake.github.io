---
layout: post
title: "Instalando-CorelX3-no-playonlinux"
excerpt: "Instalando o CorelX3 no Linux"
categories: articles
tags: [wine, playonlinux, corel, 2017]
author:
comments: true
share: true
image:
  feature: playCorel/playCorel.png
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

Outro dia estava eu e um amigo a conversar sobre editores de imagens e vetoriais
para linux, e chegamos à conclusão que, apesar de existirem muitos, eles não
são tão 'mainstream' nem atendem à necessidade da maioria, que é abrir arquivos
do [Photoshop](http://www.adobe.com/br/products/photoshop.html) e [Corel Draw](http://www.corel.com/br/).

Assim, iniciei minhas pesquisas para que meu amigo pudesse usufruir desse tão
fantástico mundo que é o GNU/Linux e ainda conseguir abrir os tais arquivos.

Nesse post cobrirei apenas a instalação do CorelX3.

# Instalação dos programas necessários

Como utilizo a distribuição [Archlinux](https://www.archlinux-br.org/),
colocarei os comandos de instalação referentes a este, caso esteja utilizando
uma outra distribuição, verifique a disponibilidade dos pacotes [aqui](https://www.playonlinux.com/en/download.html).

```bash
$ sudo pacman -S playonlinux wine wine-mono wine_gecko
```

# Instalando o Corel

Não vou colocar print de tudo, pois acho que não é necessário tanto
detalhamento.

1. Baixe o arquivo CorelDraw-X3.exe: [Download](https://drive.google.com/file/d/0B894M0Ei0LchbVlMR3VkaHYwd3c/view)

2. Abra o Playonlinux;

![Item 2.](images/playCorel/playCorel01.png)

3. Clique em ferramentas e, em seguida, clique em gerenciar Versões do Wine;

![Item 3.](images/playCorel/playCorel02.png)

4. Selecione a versão 1.6.2 e clique na seta para a direita para instalar,
   clique em Anvançar e aguarde a instalação ser finalizada.

![Item 4.](images/playCorel/playCorel03.png)

5. Feche o gerenciador de versões do wine e clique em Instalar no Playonlinux;

![Item 5.](images/playCorel/playCorel04.png)

6. Clique em "Instalar um programa não listado";

![Item 6.](images/playCorel/playCorel05.png)

7. Clique em avançar, avançar e digite "CorelX3" e clique em avançar;

<figure class="third">
	<img src="images/playCorel/playCorel06.png" alt="Item 7."></a>
	<img src="images/playCorel/playCorel07.png" alt="Item 7."></a>
	<img src="images/playCorel/playCorel08.png" alt="Item 7."></a>
	<img src="images/playCorel/playCorel09.png" alt="Item 7."></a>
	<img src="images/playCorel/playCorel10.png" alt="Item 7."></a>
	<figcaption>Item 7..</figcaption>
</figure>

8. Marque a opção "Usar outra versão do wine", clique em avançar;

![Item 8.](images/playCorel/playCorel11.png)

9. Selecione a versão 1.6.2 e clique em avançar, selecione a opção 64 bits e
   clique em avançar novamente;

<figure class="half">
	<img src="images/playCorel/playCorel12.png" alt="Item 9.">
	<img src="images/playCorel/playCorel13.png" alt="Item 9.">
	<figcaption>Item 9.</figcaption>
</figure>

10. Caso apareça uma tela solicitando instalar algum item, clique em Instalar;

11. Clique em Navegar, selecione o arquivo CorelDraw-X3.exe baixado
    anteriormente, clique em abrir e clique em avançar;

![Item 11.](images/playCorel/playCorel14.png)

12. Provavelmente dará um erro, mas não há problemas, clique em OK;

![Item 12.](images/playCorel/playCorel15.png)

13. Selecione a primeira opção e clique em avançar;

![Item 13.](images/playCorel/playCorel16.png)

14. Clique em Configurar;

![Item 14.](images/playCorel/playCorel17.png)

15. Selecione o "CorelX3" no menu da esquerda. clique na aba Wine e clique em
    Configurar o wine;

![Item 15.](images/playCorel/playCorel18.png)

16. Clique na aba bibliotecas e, onde é possível digitar, digite "mshtml",
    clique em Adicionar, depois clique em Editar, escolha a opção Nativo
    (Windows) e clique em OK;

![Item 16.](images/playCorel/playCorel19.png)

17. Clique na aba Instalar Componentes, procure a opção mfc42, selecione-a,
    clique em Instalar e ao final clique em Fechar;

![Item 17.](images/playCorel/playCorel20.png)

18. Abra seu gerenciador de arquivos e navegue até a localização: ~/.PlayOnLinux/wineprefix/CorelX3/drive_c/Program Files (x86)/


19. Crie uma pasta chamada CorelX3 e entre nela;


20. Mova o arquivo CorelDraw-X3.exe, que foi baixado, para esta pasta e baixe,
    também o arquivo mfc71u.dll encontrado no [link](https://drive.google.com/file/d/0B894M0Ei0LchUk1HYjVSLTNvaG8/view)

![Item 20.](images/playCorel/playCorel21.png)

21. Abra um editor de texto de sua preferência ([geany](https://pt.wikipedia.org/wiki/Geany), [leafpad](https://pt.wikipedia.org/wiki/Leafpad), [gedit](https://pt.wikipedia.org/wiki/Gedit)...) e copie o seguinte conteúdo:

```bash

#!/usr/bin/env playonlinux-bash
[ "$PLAYONLINUX" = "" ] && exit 0
source "$PLAYONLINUX/lib/sources"
export WINEPREFIX="/home/seuusuario/.PlayOnLinux//wineprefix/CorelX3"
export WINEDEBUG="-all"
cd "/home/seuusuario/.PlayOnLinux//wineprefix/CorelX3/drive_c/./Program Files (x86)/CorelX3"
POL_Wine CorelDRAW-X3.exe "$@"

```

![Item 21.](images/playCorel/playCorel22.png)

22. Troque, no conteúdo colocado no item acima, "seuusuario" pelo seu nome de
    usuario. Salve o arquivo em ~/.PlayOnLinux/shortcuts com o nome de CorelX3.

Obs.: O nome do arquivo não possui extensão, se for salvo como "CorelX3.txt"
remova a extensão ".txt" para que fique apenas "CorelX3"

![Item 22.](images/playCorel/playCorel23.png)

23. Abra um [terminal](https://help.ubuntu.com/kubuntu/desktopguide/pt_BR/terminals.html) e digite o seguinte comando:

```bash
$ chmod +x ~/.PlayOnLinux/shortcuts/CorelX3
```

![Item 23.](images/playCorel/playCorel24.png)

24. Verifique no Playonlinux o programa e dê um duplo clique para que se inicie
    (ou clique em Executar). Caso não funcione, verifique se os passos foram
    seguidos corretamente.

![Item 24.](images/playCorel/playCorel25.png)

25. A instalação está concluída!

![Item 25.](images/playCorel/playCorel26.png)

# E aí, o que achou?

Deixe suas comentários, seguestões, críticas e dúvidas logo abaixo.

{% include fb_comment.html %}
