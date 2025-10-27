<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Raleway:ital,wght@0,100..900;1,100..900&display=swap" rel="stylesheet">
<link rel="stylesheet" href="./style.css">

## [Sumário](#sumário)

* [Ínício](./README.md);
* [Guia #1: Pós-instalação](#guia-1-o-que-fazer-após-instalar-o-fedora-workstation);
* [Guia #2: Configuração do Samba](./Configuração-do-Samba.md);
* [Guia #3: Configuração do Waydroid](./Configuração-do-Waydroid.md);
* [Guia #4: Instalação de Fontes](./Fontes.md).

-------------------

# Guia #1: O QUE FAZER APÓS INSTALAR O [FEDORA WORKSTATION](https://fedoraproject.org/workstation/)

Índice:

* [Softwares](#softwares);
* [Instalar Temas e Alterar Aparência](#instalar-temas-e-alterar-aparência);
* [Adicionar/Remover Aplicativos no Menu](#adicionarremover-ícones-de-aplicativos-do-menu)
* [Configurar o OneDrive](#configurar-o-one-drive);
* [Sofwares Extras](#sofwares-extras);
* [Compatibilidade do 8Bitdo Ultimate](#adicionar-a-compatibilidade-do-controle-8bitdo-ultimate);
* [Configurar a partição de arquivos locais](#configurar-a-partição-de-arquivos-locais);
* [Instalar a IA Local](#instalar-a-ia-local-com-ollamaramalama)
* [Desligar/Reiniciar via Terminal](#desligarreiniciar-via-terminal).

-------------------
  
## Softwares

Vamos rodar no terminal um código para limpar os aplicativos que não serão usados, pois serão substituidos por outros de nossa preferência:

```
$ sudo dnf remove -y gnome-contacts gnome-clocks gnome-maps rhythmbox totem gnome-tour baobab
```

Após a limpeza de aplicativos, vamos atualizar os aplicativos restantes:

```
$ sudo dnf update -y --allowerasing --best
```

Instalar os repositórios [RPM Fusion](https://rpmfusion.org/) Free & Nonfree:

```
$ sudo dnf install -y --allowerasing --best https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm fedora-workstation-repositories
```

Agora vamos fazer a instalação dos pacotes rpm de aplicativos que serão melhor aproveitados em nossa curadoria:

```
$ sudo dnf install -y --allowerasing --best ffmpeg openssl playonlinux mangohud vkmark goverlay samba ostree libappstream-glib waydroid ramalama alpaca nvtop
```
```
$ sudo dnf install -y --allowerasing --best gnome-extensions-app gnome-tweak-tool kpat vlc libreoffice-langpack-pt-BR projectM-pulseaudio gparted telegram-desktop chromium onedrive cpu-x cura blender inkscape prusa-slicer micropython mu thonny evolution quodlibet brasero audacity soundkonverter gimp kolourpaint foliate libreoffice-draw transmission gimagereader-gtk obs-studio retroarch steam discord openshot godot
```
```
$ sudo flatpak install -y org.gtk.Gtk3theme.Adwaita-dark flathub com.vscodium.codium flathub com.wps.Office
```

## Instalar Temas e Alterar Aparência

### Alterando o tamanho do mouse

Rode no terminal:

```
$ gsettings set org.gnome.desktop.interface cursor-size 48
```

### Temas

Baixar e instalar temas:

```
$ cd ~/Downloads && sudo git clone https://github.com/PapirusDevelopmentTeam/papirus-icon-theme.git /usr/share/icons/Papirus && sudo git clone https://github.com/kouros17/Adwaita-Maia-Dark.git /usr/share/themes/Adwaita-Maia-Dark && sudo git clone https://github.com/axxapy/Adwaita-dark-gtk2.git /usr/share/themes/Adwaita-Dark
```

#### Ícones: 

* [Papirus](https://github.com/PapirusDevelopmentTeam/papirus-icon-theme)

#### Widgets:

* [Adwaita-dark](https://github.com/axxapy/Adwaita-dark-gtk2) (GTK-2 vem por padrão, mas falta o GTK-3);
* [Adwaita-Maia-dark](https://github.com/kouros17/Adwaita-Maia-Dark) (GTK-3 & GTK-2)

Usando a interface gráfica do Gnome Ajustes, mudar o tema para Adwaita-dark e verificar o funcionamento nos aplicativos (flatpak inclusive). Caso não tenha surtido efeito, baixar o repositório no GitHub 'stylepak':

```
$ cd ~/Downloads && git clone https://github.com/refi64/stylepak
```

Rodar para instalação:

```
$ ./stylepak install-system && ./stylepak install-user && cd
```

### Extensões GNOME Shell

Instalar as extensões do GNOME, [baixando do site](https://extensions.gnome.org/):

* Lock Keys; 
* Blur my Shell;
* Weather O'Clock;
* No overview at start-up;
* GSConnect;
* Removable Drive Menu;
* Replace Activities text with username.

## Adicionar/Remover Aplicativos no Menu

Aplicativos como WPS Office e LibreOffice criam muitos ícones para os diversos tipos de aplicações, o que pode acabar poluindo o menu do Gnome.

Para adicionar ou remover esses ícones, é preciso alterar os arquivos `.desktop` ou `Desktop Entries` nos diretórios padrão. Os mais comuns são `/usr/share/applications/`, `/usr/local/share/applications/`, `~/.local/share/applications/` e `~/Área\ de\ Trabalho` .

Usando algum editor de texto, é necessário alterar ou adicionar o parâmetro `NoDisplay=` alternando os valores booleanos entre `false` ou `true`, conforme a preferência desejada.

No caso do LibreOffice, utilizando o `gnome-text-editor` digitaremos no terminal:

```
$ sudo gnome-text-editor /usr/share/applications/libreoffice-startcenter.desktop
```

Procurando a linha de entrada com o parâmetro `NoDisplay=true`, alternaremos para `NoDisplay=false`. Quando não houver nenhuma linha de entrada com o mesmo parâmetro é só adicionar manualmente.

## Configurar o One Drive

Rode no terminal:

```
$ onedrive --sync
```

Seguir as instruções impressas no terminal para vinculação da conta e esperar o download dos arquivos e diretórios do servidor. Ao finalizar, é necessário configurar a sincronização automática ao iniciar uma nova sessão com o usuário. Para isso, rode no terminal:

```
$ sudo mkdir ~/.config/autostart/ && sudo leafpad ~/.config/autostart/OneDrive-autostart-sync.desktop
```

Na tela do leafpad, escreva o seguinte conteúdo (é recomendada a [instalação dos ícones Papirus](#ícones)):

```
[Desktop Entry]

Type=Application
Exec=onedrive --sync
Name=OneDrive Autostart Sync
Comment=Execute a command to run an OneDrive synchronization at autostart session
Icon=/usr/share/icons/Papirus/Papirus/128x128/apps/ms-onedrive.svg
```

## Sofwares Extras

No navegador principal, entrar nos site oficiais para baixar os seguintes programas: 'Arduino IDE' e 'Pokerstars'.

### Arduino IDE

Baixar a versão para linux em formato `.zip` e descompactar no diretório `/opt/`. [Criar o ícone de inicialização no menu](#adicionarremover-ícones-de-aplicativos-do-menu) por meio do arquivo `.desktop` no diretório `/usr/share/applications` :

```
$ sudo gnome-text-editor /usr/share/applications/arduino.desktop
```

Editar o arquivo com as seguintes informações:

```
[Desktop Entry]
Name=Arduino IDE
Comment=IDE para programação de microcontroladores.
Icon=/usr/share/icons/Papirus/Papirus/128x128/apps/arduino.svg
Type=Application
Categories=Software;

Exec=/opt/arduino-ide/arduino-ide # Escolher o arquivo de acordo com o diretório correto em /opt/
StartupNotify=false
Terminal=false
```

É necessário incluir o usuário no grupo `dialout` para conectar aos microcontroladores no Arduino IDE. Rodar no Terminal:

```
$ sudo usermod -a -G dialout $USER
```

### Pokerstars

No navegador, instale a extensão: 'User Agent Switcher'.

Ela vai trocar o reconhecimento do site do PokerStars do navegador utilizado, de seu padrão para o Internet Explorer ou outro compatível com Windows, para permitir o download.

Após a instalação da extensão, [acessar o site](http://www.pokerstars.com/poker/download/).

Isso vai baixar a versão `.exe` do instalador. A seguir inicie o arquivo baixado e prossiga com a instalação com o Wine ou o PlayOnLinux, com as configurações recomendadas.

### JDownloader 2

Alguns Softwares ainda precisam ser instalados de modo alternativo. É o caso do [JDownloader 2](https://jdownloader.org/jdownloader2), o qual pode ser instalado via terminal, a partir da pasta `~/Downloads`:

```
$ cd Downloads
```

Após entrar na pasta, execute:

```
$ wget -c http://installer.jdownloader.org/JD2Setup_x64.sh
```

Após o download, dê permissão de execução ao executável:

```
$ chmod +x JD2Setup_x64.sh
```

E execute-o com:

```
$ sh JD2Setup_x64.sh
```

Será exibido o instalador do JDownloader 2. É só seguir os passos e instalar o programa.

## Adicionar a compatibilidade do controle 8Bitdo Ultimate

Digite no terminal o seguinte comando:

```
$ sudo gnome-text-editor /etc/udev/rules.d/99-8bitdo-xinput.rules
```

No `gnome-text-editor`, copie e cole o seguinte:

```
ACTION=="add"
ATTRS{idVendor}=="2dc8"
ATTRS{idProduct}=="3106"
RUN+="/sbin/modprobe xpad"
RUN+="/bin/sh -c 'echo 2dc8 3106 > /sys/bus/usb/drivers/xpad/new_id'"
```

De volta no terminal, finalize:

```
$ sudo udevadm control --reload
```

## Configurar a partição de arquivos locais

### Montar no boot a partição de arquivos locais

* Entrar no `gnome-disks` e selecionar o HDD de 500GB na lista do lado esquerdo.

* Clicar no botão de engrenagens 'Opções adicionais de partição' e selecionar a opção 'Editar opções de montagem'.

* Desativar a opção 'Padrões de sessão de usuário' e confirmar se está ativa a caixa de seleção 'Montar ao inicializar o sistema'.

* Com a caixa ativa, escrever 'local-drive' em nome de exibição, definir um ponto de montagem e escolher a opção 'LABEL=Local Drive' em 'Identificar como'.

* Deslogar e logar novamente para conferir as modificações.

No terminal, definir permissões de leitura e escrita a todos usuários:
```
$ sudo chmod -R 777 /mnt/local-drive/
```

### Criar links simbólicos das pastas de usuário

Rodar no Terminal:

```
$ rm -r ~/Documentos ~/Imagens ~/Músicas ~/Vídeos && ln -s /mnt/local-drive/Documentos/ ~/ && ln -s /mnt/local-drive/Games/ ~/ && ln -s /mnt/local-drive/Imagens/ ~/ && ln -s /mnt/local-drive/Músicas/ ~/ && ln -s /mnt/local-drive/OneDrive/ ~/ && ln -s /mnt/local-drive/Vídeos/ ~/
```

Para o compartilhamento dos arquivos locais na rede, [configurar o Samba](./Configuração-do-Samba.md). Mas antes, finalizar as primeiras configurações.

## Instalar a IA Local com Ollama/Ramalama

Rodar no Terminal:

```
$ ramalama run deepseek-r1
```

## Desligar/Reiniciar via Terminal

Para cada finalização é recomendado o uso do terminal. Para desligar o PC verificando atualizações, rodar sempre:

```
$ sudo dnf upgrade -y --allowerasing --best && onedrive --sync && poweroff
```

Para reboot, rode no terminal:

```
$ sudo dnf upgrade -y --allowerasing --best && onedrive --sync && reboot
```

[Voltar ao Topo](#sumário)