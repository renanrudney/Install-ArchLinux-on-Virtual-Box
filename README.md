# ArchLinux-on-Virtual-Box
## Instalação e configuração do Arch Linux na Virtual Box

Abra o VirtualBox, clique em Novo e escolha um nome para o SO, em versão, selecione **Arch Linux x64 ou x86**<br>
Deixe a memória em **1024M** ou **2048M**<br>
Escolha a opção de **VDI (VirtualBox Disk Image)**<br>
Selecione **Dinamicamente Alocado** e em cerca de 20G ou **50G** (Não será utilizado todo o espaço)<br>
Dê Start/Iniciar e selecione a ISO do ArchLinux<br>
Ao iniciar, escolha a opção **Boot Arch Linux.**

 _Ao bootar a mídia de instalação do Arch, você estará logado como # (root) em automatic login._<br>
 _*Será utilizado o editor de texto nano ao decorrer do tutorial (para salvar: "ctrl+o", sair: "ctrl+x"), fica a seu critério qual editor utilizar!_<br><br>
1- Definir um layout de teclado (PT-BR)
> loadkeys br-abnt2 

2- Mudar a fonte do terminal (tty)
> setfont lat0-16

3- Configurar localização/idioma
> nano /etc/locale.gen
>>**descomentar essa linha:**
>>> pt_BR.UTF-8 UTF-8

4- Criar o arquivo de configuração de idioma
> locale-gen
>>export LANG=pt_BR.UTF-8

5- Testar a Conexão
> ping -c 3 www.google.com

6- Listar partições existentes:
> fdisk -l

**7- Configurar as partições no disco ( _fica a seu critério, um exemplo para 20gb:_ )**<br>
**Você será responsável pelos seus dados ,perdas poderão ocorrer caso não seja cuidadoso.**<br>
> cfdisk /dev/sda 
**Selecione o tipo 'dos'**<br>
Para a primeira partição tecle enter, digite o tamanho para **15G**,<br> selecione **primary**,<br> escolha a opção **bootable** e depois **write** e digite yes. <br><br>
Para a segunda partição tecle enter no espaço free, defina o tamanha para **2048M** e selecione **primary**, dê write e quit.<br>

8- Formatar as partições com o sistema de arquivos ext4:<br>
> mkfs.ext4 /dev/sda1

9- Formatar e ativar swap:
>mkswap /dev/sda2
>>swapon /dev/sda2

**10- Montar partições** 
> mount /dev/sda1 /mnt

_*Partições adicionais são montadas da mesma maneira._

11- Visualizar o particionamento atual:
>lsblk /dev/sda

Então nesse tutorial ficou: **/ (root)** no sda1, **swap** no sda2.<br>

**12- Instalar o sistema base**
>pacstrap /mnt base base-devel
 
13- Gerar arquivo fstab (FSTAB: File System Table):
>genfstab /mnt/ >> /mnt/etc/fstab

14- Utilizar o ambiente chroot (Chroot: Change Root)
> arch-chroot /mnt /bin/bash

15- Configurar Localização novamente:
> nano /etc/locale.gen
>>**descomentar essa linha:**
>>> pt_BR.UTF-8 UTF-8

16- Gerar arquivo de localização
>locale-gen

17- Criar o arquivo de configuração de idioma novamente:
>echo LANG=pt_BR.UTF-8 > /etc/locale.conf
>>export LANG=pt_BR.UTF-8

18- Recarregue as configurações, pois o ambiente mudou:
>loadkeys br-abnt2
>>setfont lat0–16

19- Para que tais configurações fiquem guardadas, edite o arquivo vconsole.conf:
>nano /etc/vconsole.conf

Escreva e depois salve:
>KEYMAP=br-abnt2
>>FONT=lat0–16
>>>FONT_MAP=

20- Configurar o fuso-horário
>ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime

21- Sincronizar o relógio do hardware com o do sistema
>hwclock  --systohc  --utc

22- Definir um nome para o SO:
**Aqui você entra com o grr**<br>
> echo grr2017XXXX > /etc/hostname

23- Adicionando entrada em hosts
>nano /etc/hosts

Deixe parecido com isso:
>127.0.0.1 localhost.localdomain localhost
>>::1 localhost.localdomain localhost
>>>127.0.1.1 grr2017XXXX.localdomain nomedopc

24- Habilitando a Internet ao inicializar
>systemctl enable dhcpdc

25- Baixar Sudo e Grub
> pacman -S sudo grub os-prober

**26- Instalar o GRUB**
>grub-install /dev/sda

27- Criar um ambiente ramdisk inicial
> mkinitcpio -p linux

28- Gerar arquivo de configuração do GRUB
> grub-mkconfig -o /boot/grub/grub.cfg

29- Criar usuario e definir senha
**Aqui você pode colocar o nome de usuário a sua escolha**
>useradd -m -g users -G wheel,storage,power -s /bin/bash seuuser 
>>passwd seuuser

30- Definir a senha de root
>passwd root

31- Editar o arquivo sudoers:
>nano /etc/sudoers

_**Descomente a opção e salve**_
> %wheel ALL=(ALL) ALL

**Pronto para reiniciar o sistema!**
>exit
>>reboot

**Após reiniciar selecionar "Boot Existing OS"**<br>
Logue com seu usuário e senha e digite os comandos:<br>
>hostname
>>uname -r
>>>sudo pacman -S lsb-release
>>>>lsb_release -a
