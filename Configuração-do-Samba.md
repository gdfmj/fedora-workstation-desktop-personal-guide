<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Raleway:ital,wght@0,100..900;1,100..900&display=swap" rel="stylesheet">
<link rel="stylesheet" href="./style.css">

## [Sumário](#sumário)

* [Ínício](./README.md);
* [Guia #1: Pós-instalação](./Pós-instalação.md);
* [Guia #2: Configuração do Samba](#guia-2-configuração-do-samba);
* [Guia #3: Configuração do Waydroid](./Configuração-do-Waydroid.md);
* [Guia #4: Instalação de Fontes](./Fontes.md).

-------------------

# Guia #2: Configuração do Samba

De início, é necesário [ter o `samba` instalado](./Pós-instalação.md#softwares). Caso ainda não tenha instalado, digitar no terminal:

```
$ sudo dnf install samba -y
```

Também é necessário dar as permissões de usuário na pasta que será compartilhada:

```
$ sudo chmod -R 777 /mnt/local-drive/
```

Fazer um backup do arquivo `/etc/samba/smb.conf` e abrir no editor de texto:

```
$ sudo cp /etc/samba/smb.conf ~/Documentos/ && sudo gnome-text-editor /etc/samba/smb.conf
```

Adicionar no `/etc/samba/smb.conf` as linhas abaixo:

```
#Os caracteres # e ; no início de cada linha deste documento /etc/samba/smb.conf são marcadores de comentário.
#Quando o comentário iniciar com ; ele faz referência a um código de configuração que pode ser habilitado tirando o comentário.
#É recomendável tirar os comentários marcados aqui, quando criar seu /etc/samba/smb.conf, para o código de configuração ficar mais fácil de compreender.
#Outros códigos de configuração além destes listados aqui são possíveis. Checar wikisamba.org nas referências ou o arquivo /etc/samba/smb.conf.example quando disponível em sua instalação.
[global]
#Adicionar o charset:
	unixcharset = UTF-8
#Escolher o nome na rede para o servidor samba:
	netbios name = FEDORASMB
#Escolher o nome do grupo de trabalho do servidor samba:
	workgroup = SAMBA
#Opção de segurança a nível de usuário:
	security = user
#É possível limitar os IPs aceitos para acessar o servidor samba. Retirar o ';' para utilizar o código:
	;hosts allow = IP0 IP1 IP2
#Limitar usuário não identificado:
	map to guest = Bad User

#Nome do Diretório a ser compartilhado:
[SambaDiretório]
#Especificação do diretório compartilhado:
	path = /mnt/local-drive/
#Permitir a navegação no diretório compartilhado:
	browseable = yes
#Permitir ou não que todos os usuários escrevam nesse diretório:
	writable = yes
#Permitir usuário convidado:
	guest ok = no
#Permitir todos os convidados como usuários:
	guest only = no
#Forçar que o usuário do grupo mantenha suas permissões ao criar arquivos e diretórios:
	;force group = sambagroup
#Informar quais usuários ou grupos terão acesso a essas pastas compartilhadas (quando grupos):
	;valid users = @sambagroup
#Informar quais usuários ou grupos terão acesso a essas pastas compartilhadas (quando usuários):
	;valid users = sambauser sambauser0 sambauser1
#Forçar a permissão [chmod 777] quando um arquivo é criado:
	force create mask = 777
#Forçar a permissão [chmod 777] quando um diretório é criado:
	force directory mask = 777
```

Entrar com o superusuário e habilitar o `smb` no sistema:

```
$sudo su
```
```
# systemctl enable --now smb
```

Após toda alteração no arquivo `/etc/samba/smb.conf` é necessário reiniciar o `smb` no sistema como super usuário:

```
# systemctl restart smb
```

Para permitir ao `samba` compartilhar qualquer arquivo/diretório, leitura/escrita, é preciso ativar o booleano `samba_export_all_rw`:

```
# setsebool -P samba_export_all_rw 1 && restorecon -R /mnt/local-drive
```

Também é necessário adicionar e ativar o serviço do samba no firewall:

```
# firewall-cmd --add-service=samba && firewall-cmd --runtime-to-permanent
```

Criar um usuário para acessar os diretórios compartilhados é uma opção para gerenciamento do compartilhamento:

```
# useradd --no-create-home sambauser
```

Uma outra opção é agrupar usuários em grupos específicos:

```
# groupadd sambagroup --users sambauser
```

Os usuários podem receber senhas específicas para acesso ao samba:

```
# smbpasswd -a sambauser
```

Para acessar o servidor, é necessário usar o IP da máquina em que o samba está configurado. Para ver o IP no terminal:

```
$ ip a
```

Com o IP em mãos, basta acessar no explorador o endereço `\\IP` e digitar as credenciais de acesso.

---------------
### Fontes e Referências:
-	Wiki Samba (wikisamba.org)
-	Server World Info (server-world.info)
-	Comunidade Fedora Brasil - Youtube
-	Canal Interface UP

[Voltar ao Topo](#sumário)