<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Raleway:ital,wght@0,100..900;1,100..900&display=swap" rel="stylesheet">
<link rel="stylesheet" href="./style.css">

## [Sumário](#sumário)

* [Ínício](./README.md);
* [Guia #1: Pós-instalação](./Pós-instalação.md);
* [Guia #2: Configuração do Samba](./Configuração-do-Samba.md);
* [Guia #3: Configuração do Waydroid](#guia-3-configuração-do-waydroid);
* [Guia #4: Instalação de Fontes](./Fontes.md).

-------------------

# Guia #3: Configuração do Waydroid

Índice:

* [Primeiros Passos](#primeiros-passos);
* [Ativar/Desativar a entrada de Joystick](#ativardesativar-a-entrada-de-joystick);
* [Instalando uma loja de aplicativos](#instalando-uma-loja-de-aplicativos);
* [Compartilhamento de pastas e arquivos com o Host Linux](#compartilhamento-de-pastas-e-arquivos-com-o-host-linux)
* [Desinstalando e corrigindo falhas](#desinstalando-e-corrigindo-falhas).

-------------------
## Primeiros Passos

Antes de tudo é necessário [ter instalado o `waydroid`](./Pós-instalação.md#softwares). Caso ainda não tenha, digite no terminal:

```
$ sudo dnf install -y waydroid
```
	
Após a instalação, entrar no terminal como superusuário:

```
$ sudo su
```
	
Abrir o waydroid com a janela de configuração inicial:

```
# waydroid first-launch
```
	
Preencher as seguintes configurações:

```
	System OTA: https://ota.waydro.id/system
	Vendor OTA: https://ota.waydro.id/vendor
	Android Type: Minimal Android
```
	
O sistema será baixado e instalado em um container. Ao terminar, clicar em Done, sair do terminal e inicializar o Waydroid pelo Menu.

Configurar o Waydroid na primeira inicialização:
* Adotar o tema escuro;
* Trocar o fuso horário;
* Escolher o teclado adequado, idioma português brasileiro e desativar o teclado virtual.

Fechar a janela do sistema e iniciar a sessão do Waydroid via terminal:

```
$ waydroid session start
```
	
Rodar o comando para ativar o modo multi-windows:

```
$ waydroid prop set persist.waydroid.multi_windows true
```

Terminar a sessão do Waydroid e reiniciar pela interface gráfica com as novas configurações de janela:

```
$ waydroid session stop
```

## Ativar/Desativar a entrada de Joystick

Para o controle [8bitdo Ultimate](./Pós-instalação.md#adicionar-a-compatibilidade-do-controle-8bitdo-ultimate) ou outras entradas de joystick serem reconhecidos no WayDroid (ainda não funciona bem em todos os apps), rodar no terminal ao iniciar o programa:

```
$ waydroid prop set persist.waydroid.uevent true
```

```
$ waydroid prop set persist.waydroid.udev true
```

Devemos desativar essa configuração quando não utilizarmos os controles para não ter interferência das entradas dos botões em outros usos do Waydroid:

```
$ waydroid prop set persist.waydroid.uevent false
```

```
$ waydroid prop set persist.waydroid.udev false
```

Rodar sempre o comando para encerrar a sessão e depois reiniciar pela interface gráfica com as novas configurações:

```
$ waydroid session stop
```

## Instalando uma loja de aplicativos

Para a instalação de aplicativos, podem ser instalados qualquer um com a extensão `.apk` . No entanto é recomendado a instalação de uma loja, no caso a [Aurora Store](https://auroraoss.com/downloads/AuroraStore/Release/)
<!--
##### OBS.: Devido a um bug, a melhor versão para instalação do Aurora OSS é a 4.2.5, não mais disponível no repositório oficial do site. Portanto, até que este bug seja corrigido, sugere-se utilizar a [versão 47](https://web.archive.org/web/20230730213239/https://f-droid.org/repo/com.aurora.store_47.apk)

No terminal, rodamos:

```
$ wget -O ~/Downloads/aurora.apk https://web.archive.org/web/20230730213239/https://f-droid.org/repo/com.aurora.store_47.apk
```
-->
Após baixar a imagem da Aurora Store, instalamos:

```
$ waydroid app install ~/Downloads/aurora.apk
```

Podemos instalar alguns aplicativos como:

* Termux;
* Gerenciador de Arquivos +;
* WhatsApp.

## Compartilhamento de pastas e arquivos com o Host Linux

<!-- Método #1 FAILED -->
<!--
Para linkar os diretórios do usuário host com o usuário do Waydroid, podemos começar entrando no terminal como super usuário:

```
$ sudo su
```

Verificar se o terminal está na pasta /home/$USER e linkar os diretórios de usuário local dentro do container android.

```
# rm -r ./.local/share/waydroid/data/media/0/Documents ./.local/share/waydroid/data/media/0/Pictures ./.local/share/waydroid/data/media/0/Music ./.local/share/waydroid/data/media/0/Movies ./.local/share/waydroid/data/media/0/Download && cd ./.local/share/waydroid/data/media/0/ && ln -s /mnt/local-drive/Documentos/ -T Documents && ln -s /mnt/local-drive/Imagens/ -T Pictures && ln -s /mnt/local-drive/Músicas/ -T Music && ln -s /mnt/local-drive/Vídeos/ -T Movies && ln -s /home/$USER/Downloads/ -T Download && cd
```
-->

<!-- Método #2 -->

Para criar um atalho para o diretório em que os arquivos de usuário do Waydroid são salvos no host do Linux, criar o diretório onde vai ser montado o atalho:

```
$ mkdir ~/Waydroid
```

Instalar o `bindfs` para montar o atalho:

```
$ sudo dnf install bindfs
```

Para possibilitar a leitura e escrita nos arquivos na pasta montada, é necessário espelhar com o UID (User ID) correspondente. Para descobrir o UID do Host, rodar no terminal o seguinte comando e anotar a saída:

```
$ id -u
```

O comando correspondente para fazer a montagem da pasta com `bindfs`, substituindo o UID padrão do exemplo (`1000`) pelo correspondente caso seja necessário é:

```
$ sudo bindfs  --mirror=1000 ~/.local/share/waydroid/data/media/0/ ~/Waydroid
```

Porém, podemos automatizar a montagem desse atalho quando a sessão de usuário for iniciada. Para isso modificamos o arquivo `/etc/fstab`:

```
$ sudo gnome-text-editor /etc/fstab
```

O arquivo deve conter as instruções para a montagem de algumas partições. Devemos adicionar uma linha ao final contendo os seguintes parâmetros, lembrando de substituir os diretórios de usuário do exemplo (`/home/user/`) e a UID (`1000`) para o correspondente:

```
/home/user/.local/share/waydroid/data/media/0 /home/user/Waydroid fuse.bindfs mirror=1000,allow_other,x-gvfs-hide,noatime 0 0
```

Agora precisamos recarregar o daemon do `systemd`:

```
$ systemctl daemon-reload
```

## Desinstalando e corrigindo falhas

Para desinstalar o Waydroid e corrigir bugs, seguir as instruções:

1. Pare o Waydroid com superusuário `$ sudo su`:

```
# systemctl stop waydroid-container.service
```

2. Saia do superusuário com `# exit` e limpe os arquivos instalados no linux:

```
$ sudo rm -rf /var/lib/waydroid /home/.waydroid ./waydroid ./.share/waydroid ./.local/share/applications/*waydroid* ./.local/share/waydroid
```
	
3. Desinstalar o pacote com o dnf:

```
$ sudo dnf remove -y waydroid
```

Se quiser apenas resetar para solução geral de problemas:

1. Assegure-se de que sua instalação do Waydroid está atualizada;

2. Faça uma atualização da imagem mais recente do Waydroid rodando como superusuário `$ sudo su`:

```
# waydroid upgrade;
```

3. Pare o Waydroid:

```
# systemctl stop waydroid-container.service
```

4. Limpe os arquivos instalados no linux:

```
# rm -rf /var/lib/waydroid /home/.waydroid ./waydroid ./.share/waydroid ./.local/share/applications/*waydroid* ./.local/share/waydroid
```

5. Reinicie o serviço no sistema:

```
# systemctl start waydroid-container.service
```
	
6. Inicialize com um dos comandos abaixo:

```
# waydroid init -f
```

```
# waydroid init -f -i /usr/share/waydroid-extra/images
```

```
# waydroid first-launch
```
	
7. Se utilizar o última opção, preencher novamente a configuração inicial:

```
	System OTA: https://ota.waydro.id/system
	Vendor OTA: https://ota.waydro.id/vendor
	Android Type: Minimal Android
```
----------------
### [Fontes e Referências](https://docs.waydro.id)

[Voltar ao Topo](#sumário)