# Gladys-Backup_to_Freebox

Bonjour à tous,

j’ai vu qu’un certain nombre de personnes se demandaient comment sauvegarder leur carte SD.

Pendant un certain temps, j’ai utilisé la méthode :

- Arrêt du Raspberry;
- Sortir la carte SD;
- Outil de copie (etcher);
- Remettre la carte SD puis rallumer le tout
- Plutôt lourd et ennuyeux :crazy_face:

Et en cherchant un peu j’ai trouvé une autre méthode (copie de/dev/mmcblk0) , qui permet de sauvegarder sur un partage pendant la nuit sans avoir à sortir cette :floppy_disk: :gun: carte SD du raspberry.

- accéder à gladys en ssh (ssh pi@192.168.xxx.xxx)

- modifier le fstab (```sudo nano /etc/fstab```)

Ajouter le partage, le NAS, par exemple la freebox
```//192.168.0.254/Disque\040dur/ /media/freebox cifs netdev,rw,users,iocharset=utf8,uid=1000,sec=none,file_mode=0777,dir_mode=0777 0 3```

Créer le répertoire sur le rpi sur lequel on a lié le NAS
```sudo mkdir -p /media/freebox```
et installer les paquets **cifs-utils** et **samba-common**

```sudo mount -a```

Créer un répertoire pour vos scripts
```sudo mkdir -p /home/pi/scripts```

Ajouter le script de sauvegarde
```sudo nano /home/pi/scripts/sauvegarde.sh```

Exemple de script bash :
```
#!/bin/bash
datejour=$(date +'%Y%m%d')
#SAUVEGARDE CARTE SD GLADYS
sudo dd if=/dev/mmcblk0 bs=4M of=/media/freebox/Téléchargements/$datejour"_gladys.img"
tar -czf /media/freebox/Téléchargements/$datejour"_gladys.tar.gz" /media/freebox/Téléchargements/$datejour"_gladys.img"
rm -f /media/freebox/Téléchargements/$datejour"_gladys.img"
Modifier les droits des répertoires et rendre script exécutable
sudo chown -Rf pi: /home/pi/scripts
sudo chmod +x /home/pi/scripts/sauvegarde.sh
sudo chown pi:4242 /media/freebox
```

Créer le crontab pour lancer le script 
```sudo nano /etc/crontab

00 23 * * * root cd /home/pi/scripts/sauvegarde/ && ./sauvegarde.sh
```

Je l’utilise depuis quelques semaines. Je viens de replaquer une sauvegarde sur une nouvelle carte SD (gladys 3.8.0) et je viens de la passer en 3.8.1.
Et tout fonctionne nickel :grinning:

-----
[Source] : https://community.gladysassistant.com/t/automatiser-la-sauvegarde-de-la-carte-sd/2835
-----

D'autres idées :
================

https://wiki.archlinux.org/index.php/Rsync#As_a_backup_utility

https://tech.korben.info/t/effacer-et-reinstaller-un-linux-via-ssh-sans-avoir-a-redemarrer/5522/5

https://community.gladysassistant.com/t/sauvegarde-de-gladys/1364/26

https://github.com/C4rlit0/gladys-backup

https://www.youtube.com/watch?v=lT-30q_jmHQ

https://www.debian.org/releases/stable/i386/apds03.html.en

https://www.debian.org/releases/stable/i386/apds03.html.fr

http://www.underhanded.org/papers/debian-conversion/remotedeb.html

https://github.com/mhf-ir/ubuntu-overssh-reinstallation

https://help.ubuntu.com/community/Grub2/ISOBoot

