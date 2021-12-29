![manjaro_logo_text.png](pics/manjaro_logo_text.png)

Ce tuto est à la base une doc personnelle.

Néanmoins, j'ai tenté autant que possible de la rédiger afin qu'elle soit utilisable par d'autres.

Les commentaires sont évidement les bienvenus ;)

# Post Installation

Bon... normalement, à ce stade, vous venez de terminer l'installation de base de __Manjaro Gnome__ ;)

> __Remarque__ : hormis quelques points propres à Gnome, cette doc est valable pour d'autres variantes de Manjaro.

## Outils de base

On va installer ce package, qui sera nottament nécessaire dans la construction de paquets `AUR`.

```shell
sudo pacman -S base-devel
```

## Changement de Shell

Je sais...certains mes diront pourquoi `bash` au lieu de `zsh` ? Bah ... question de goût ;)

```shell
chsh -s /bin/bash
```

## Configuration de base de Pacman

### Méthode 1

On configure le gestionnaire de paquet pacman pour utiliser un mirroir belge pour les dépots

```shell
nano /etc/pacman.d/mirrorlist
```

Et on garde

```shell
Server = http://archlinux.cu.be/$repo/os/$arch
```

### Méthode 2 (conseillée)

Passer via le gestionaire de paquet de Manjaro

![mirrors.jpeg](/pics/pamac_mirrors.jpeg)

## Ajout de dépôts additionnels dans Pacman

> __Remarque__ : c'est le même principe que pour n'importe quel dépot de n'importe quelle distro, il faut __impérativement__ n'ajouter que des dépots dans lesquels on a __confiance__. Et c'est idem ++ pour les paquets `AUR`.

Dans `/etc/pacman.conf`

On ajoute le support du 32bits en décommentant :

```shell
[multilib]
Include = /etc/pacman.d/mirrorlist
```

Et on ajoute les dépôts suivants :

```shell
[archstrike]
Server = https://mirror.archstrike.org/$arch/$repo

[herecura]
Server = https://repo.herecura.be/$repo/$arch

[sergej-repo]
Server = http://repo.p5n.pp.ru/$repo/os/$arch

[archlinuxfr]
SigLevel = Never
Server = https://repo.archlinux.fr/$arch
```

On met les DBs des paquets à jour

```shell
sudo pacman -Syy
```

On importe la clé de Archstrike

> __Remarque__ : je vous invite à vérifier ces clés sur le site de Archstrike et ne pas bêtement copier/coller ;)

```shell
sudo pacman-key --init
sudo dirmngr < /dev/null
sudo pacman-key -r 9D5F1C051D146843CDA4858BDE64825E7CBC0D51
sudo pacman-key --lsign-key 9D5F1C051D146843CDA4858BDE64825E7CBC0D51
```

On installe les paquets suivants (procédure spcifique à Archstrike)

```shell
sudo pacman -S archstrike-keyring
sudo pacman -S archstrike-mirrorlist
```

Et toujours dans `/etc/pacman.conf`, on remplace :

```shell
[archstrike]
Server = https://mirror.archstrike.org/$arch/$repo
```

Par

```shell
[archstrike]
Include = /etc/pacman.d/archstrike-mirrorlist
```

Et on met les DBs des paquets à jours à nouveau

```shell
sudo pacman -Syy
```

## Yaourt ou Trizen

> __Remarque__ : étant sous Manjaro, l'outil maison `Pamac` gère également `AUR` (ainsi que `Snap` et `Flatpak`), mais `trizen` / `yourt` restent intéressant, surtout pour les Archlinuxien qui veulent rester en terrain connu ;)

On installe `yaourt` ou `trizen` pour épauler `pacman` avec les paquets `AUR`.

Ce sont en fait des wrapper pour `pacman` qui ajoutent des fonctions en plus, comme la possibilité d'installer des paquets depuis `AUR`.

> __Remarque 1__ : utilisez de préférence `trizen` qui est plus sécurisé
> __Remarque 2__ : n'oubliez pas de lire les scripts d'installation à chaque fois, même lors de mises à jours !

Pour `yaourt`

```shell
sudo pacman -S yaourt
```

Pour `trizen`

```shell
sudo pacman -S git
cd /tmp
git clone https://aur.archlinux.org/trizen.git
cd trizen
makepkg -si
```

## Polices de caractères

On rajoute quelques polices de caractères utiles/manquantes.

```shell
trizen -S ttf-bitstream-vera gnu-free-fonts
```

Depuis AUR

Malheureusement nécessaires...

```shell
trizen -S aur/ttf-ms-fonts
```

Et pour le japonais ;)

```shell
trizen -S noto-fonts-cjk noto-fonts-emoji noto-fonts
```

## Impression

On ajoute ce qui manque à l'installation initiale.

```shell
trizen -S foomatic-{db-ppds,db-nonfree,db-nonfree-ppds}
```

## Bluetooth

On ajoute ce qui manque au support du bluetooth, nottament `Blueman` qui est nettement mieux fichu ;)

```shell
trizen -S bluez-tools bluez-plugins bluez-utils blueman
```

## Sources du Kernel

On installe le package contenant les sources du kernel indispensable pour pouvoir compiler des modules addtionnels avec `DKMS` (Virtualbox, ....)

> __Rermaque__ : les sources à installer doivent bien entenu "matcher" avec le noyau installé

```shell
trizen -S linux512-headers
```

# Package Managers additionnels

On va maintenant installer des package manager alternatifs, mais avant, passons par les paramètres de `Pamac`.

## Paramètres de Pamac

Histoire de garder un système propre, je vous conseille de cocher `Déinstaller les dépendances inutiles`

![pamac_settings.jpeg](/pics/pamac_settings.jpeg)

## AUR

Bon... même si vous avez installé `trizen` ou `yaourt`, si vous voulez intégrer les paquets AUR dans Pamac et donc bénéficier de la gestion unifiée que celui-ci fourni, vous devez activer les paquets `AUR` dans celui-ci.

> __Remarque__ : les paquets `AUR` viennent en droite ligne de `AUR` (mouarf) et contrairement à `Flatpak` ou `Snap`, peuvent entrer en conflit avec les paquets de Manjaro. Je vous conseille de ne cocher `Vérifier les mises à jour` si et seulement si vous vous sentez l'âme de gérer les éventuels conflits. Sinon, effectuez les mises à jour via `trizen` seulement quand c'est nécessaire et pour un paquet en particulier.

> __Remarque 2__ : AUR (Arch User Repository) est, comme son nom l'indique, prévu pour Arch. Et bien que Manjaro soit basée sur Arch, elle fonctionne avec ses propres dépots (instable, test et stable), lesquelles ajoutent une "couche de stabilité" par rapport à Arch, l'instable de Manjaro correspondant au stable de Arch et les releases stables de Manjaro se faisant sous forme de "Snapshots" à intervals courts (excepté les MAJ de sécu qui peuvent être poussées en priorité). Ce qui du coup, peut entrainer des soucis (c'est rare, mais ça arrive) avec la mise à jours de certains packages AUR, lesquels reposent sur une version stable de Arch mais pas encore disponible dans le dépot stable de Manjaro. La plupart du temps, ça se passe très bien, et dans le cas pré-cité, cela se résume à une erreur de construction du paquet (donc l'ancienne version reste installée) et à simplement attendre le suivant snapshot stable de manjaro.

> __Remarque 2 bis__ : et donc dans un idéal "safe", je recommanderai de ne PAS cocher l'option __Vérifier les mises à jours__, afin d'éviter qu'une telle erreur se produise lors d'une mise à jours complète du système.

![pamac_aur.jpeg](/pics/pamac_aur.jpeg)

## Flatpak

Ce fait via l'activation des paquets `Flatpak` dans `Pamac`.

Je vous invite à cocher `Vérifier les mises à jour` pour que celles soient effectuées via `Pamac`.

> __Remarque__ : cela équivaut à un `flatpak update` et devrait être 100% safe pour votre système.

![pamac_flatpak.jpeg](/pics/pamac_flatpak.jpeg)

### Installation manuelle du plugin Flatpak

> __Remarque__ : le plugin `pamac-flatpak-plugin` à flatpak comme dépendence, donc l'installation manuelle de `flatpak` n'est nécessaire QUE si vous n'installer pas le support `Flatpak` dans `Pamac`

```shell
trizen -S flatpak
```

## Snap

Ce fait via l'activation des paquets `Snap` dans `Pamac`.

> __Remarque__ : `Snap` fonctionne avec un Store centralisé et géré par Canonical. D'un point de vue d'un libriste, je vous conseil fortement d'éviter autant que possible et privilégier `AUR` ou `Flatpak`.

![pamac_snap.jpeg](/pics/pamac_snap.jpeg)

### Installation manuelle du plugin Snap

> __Remarque__ : le plugin `pamac-snap-plugin` à `snapd` comme dépendence, donc l'installation manuelle de `snapd` n'est nécessaire QUE si vous n'installer pas le support `Snap` dans `Pamac`

```shell
trizen -S snapd
```

## AppImage

Les `AppImage` ne sont pris en charge par `pamac` et bien qu'il existe un `AppImage` hub, cela ne repose pas sur un store ou des dépôts comme `Snap` ou `Flatpak`.

Du coup, ce petit utilitaire permet d'installer une `AppImage` dans `~/Applications` (default) et d'ajouter un raccourci dans le menu de façon simple.

On install donc

```shell
trizen -S appimagelauncher
```

## NodeJS et NPM

On installe NodeJS + le package manager NodeJS

```shell
trizen -S nodejs npm
```

On définit où seront installé les packages nodejs avec l'option `-g` de `npm`

```shell
npm config set prefix /home/<YOUR_USER_NAME>/.local
```

Dans `~/.bashrc`, on ajoute :

```shell
PATH=~/.local/bin/:$PATH
```

## PIP

On installe le package manager de python

```shell
trizen -S python-pip
```

# Matos

Dans cette section, on va s'occuper de notre matos.

## Dongle USB Bluetooth basé sur Realtek RTL8761B

> **Remarque (Juillet 2021)** : cette partie n'est plus nécessaire, rtl8761b ayant été ajouté dans linux-firmware dans les dépôts officiels
>
> Installer le module depuis `AUR`, suivi d'un branchement/débranchement du dongle ;)
>
>```shell
>trizen -S rtl8761b-fw
>```

## Support LDAC dans Pulseaudio pour les Casques Bluetooth

Nous avons besoin de remplacer `pulseaudio-bluetooth` par `pulseaudio-modules-bt` pour avoir plus de choix niveau Codecs Bluetooth, dont __LDAC__, qui est un Codec Lossless.

```shell
trizen -S pulseaudio-modules-bt
```

> __Remarque__ : le paquet `pulseaudio-modules-bt` sur Manjaro est présent dans le dépôt `community` ET `AUR`, mais n'existe que dans `AUR` sous Arch

Suivi d'un petit

```shell
pulseaudio -k
```

### Vérifier son utilisation

#### Via l'extension Gnome `Sound Input & Output Device Chooser`

![pulseaudio_bt_ldac.jpg](/pics/pulseaudio_bt_ldac.jpg)

#### Via `pactl list`

Il est possible de vérifier s'il est pris en compte via la commande : `pactl list`.

Exemple de sortie

```shell
pactl list

[...]

Destination #2
	État : RUNNING
	Nom : bluez_sink.94_DB_56_9D_4C_38.a2dp_sink
	Description : WH-1000XM4
	Pilote : module-bluez5-device.c
	Spécification de l'échantillon : s16le 2ch 44100Hz
	Plan des canaux : front-left,front-right
	Module du propriétaire : 26
	Sourdine : non
	Volume : front-left: 37933 /  58% / -14,25 dB,   front-right: 37933 /  58% / -14,25 dB
	        balance 0,00
	Volume de base : 65536 / 100% / 0,00 dB
	Source du moniteur : bluez_sink.94_DB_56_9D_4C_38.a2dp_sink.monitor
	Latence : 31109 usec, configuré 30804 usec
	Marqueurs : HARDWARE DECIBEL_VOLUME LATENCY
	Propriétés :
		bluetooth.protocol = "a2dp_sink"
		bluetooth.a2dp_codec = "LDAC"
		device.description = "WH-1000XM4"
		device.string = "94:DB:56:9D:4C:38"
		device.api = "bluez"
		device.class = "sound"
		device.bus = "bluetooth"
		device.form_factor = "headset"
		bluez.path = "/org/bluez/hci0/dev_94_DB_56_9D_4C_38"
		bluez.class = "0x240404"
		bluez.alias = "WH-1000XM4"
		device.icon_name = "audio-headset-bluetooth"
		device.intended_roles = "phone"
	Ports :
		headset-output: Headset (type: Unknown, priority: 0, available)
	Port actif : headset-output
	Formats :
		pcm

[...]

Carte #3
	Nom : bluez_card.94_DB_56_9D_4C_38
	Pilote : module-bluez5-device.c
	Module propriétaire : 26
	Propriétés :
		device.description = "WH-1000XM4"
		device.string = "94:DB:56:9D:4C:38"
		device.api = "bluez"
		device.class = "sound"
		device.bus = "bluetooth"
		device.form_factor = "headset"
		bluez.path = "/org/bluez/hci0/dev_94_DB_56_9D_4C_38"
		bluez.class = "0x240404"
		bluez.alias = "WH-1000XM4"
		device.icon_name = "audio-headset-bluetooth"
		device.intended_roles = "phone"
	Profils :
		headset_head_unit: Headset Head Unit (HSP/HFP) (sinks: 1, sources: 1, priority: 30, available: oui)
		a2dp_sink_sbc: High Fidelity Playback (A2DP Sink: SBC) (sinks: 1, sources: 0, priority: 40, available: oui)
		a2dp_sink_aac: High Fidelity Playback (A2DP Sink: AAC) (sinks: 1, sources: 0, priority: 40, available: oui)
		a2dp_sink_aptx: High Fidelity Playback (A2DP Sink: aptX) (sinks: 1, sources: 0, priority: 40, available: non)
		a2dp_sink_aptx_hd: High Fidelity Playback (A2DP Sink: aptX HD) (sinks: 1, sources: 0, priority: 40, available: non)
		a2dp_sink_ldac: High Fidelity Playback (A2DP Sink: LDAC) (sinks: 1, sources: 0, priority: 40, available: oui)
		off: Off (sinks: 0, sources: 0, priority: 0, available: oui)
	Profil actif : a2dp_sink_ldac
	Ports :
		headset-output: Headset (type: Unknown, priority: 0, latency offset: 0 usec, available)
			Partie du(des) profil(s) : headset_head_unit, a2dp_sink_sbc, a2dp_sink_aac, a2dp_sink_aptx, a2dp_sink_aptx_hd, a2dp_sink_ldac
		headset-input: Headset (type: Unknown, priority: 0, latency offset: 0 usec, availability unknown)
			Partie du(des) profil(s) : headset_head_unit
```

## Udev

Certains périphériques USB nécessitent des règles `udev` spécifiques pour qu'ils soient accessibles.

### Périphs Android

Afin de pouvoir utiliser les outils du SDK Android, comme `adb` ou `fastboot`, on doit donner un accès au smartphone via USB.

Vous avez 2 possibilités :

1) Manuellement, on créer le fichier `/etc/udev/rules.d/99-android.rules` et on ajoute

```shell
SUBSYSTEM=="usb", ATTR{idVendor}=="0502", MODE="0666", OWNER="shakasan" # Acer
SUBSYSTEM=="usb", ATTR{idVendor}=="0b05", MODE="0666", OWNER="shakasan" # Asus
SUBSYSTEM=="usb", ATTR{idVendor}=="413c", MODE="0666", OWNER="shakasan" # Dell
SUBSYSTEM=="usb", ATTR{idVendor}=="0489", MODE="0666", OWNER="shakasan" # Foxconn
SUBSYSTEM=="usb", ATTR{idVendor}=="04c5", MODE="0666", OWNER="shakasan" # Fujitsu
SUBSYSTEM=="usb", ATTR{idVendor}=="04c5", MODE="0666", OWNER="shakasan" # Fujitsu-Toshiba
SUBSYSTEM=="usb", ATTR{idVendor}=="091e", MODE="0666", OWNER="shakasan" # Garmin-Asus
SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", OWNER="shakasan" # Google-Nexus
SUBSYSTEM=="usb", ATTR{idVendor}=="201E", MODE="0666", OWNER="shakasan" # Haier
SUBSYSTEM=="usb", ATTR{idVendor}=="109b", MODE="0666", OWNER="shakasan" # Hisense
SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", MODE="0666", OWNER="shakasan" # HTC
SUBSYSTEM=="usb", ATTR{idVendor}=="12d1", MODE="0666", OWNER="shakasan" # Huawei
SUBSYSTEM=="usb", ATTR{idVendor}=="8087", MODE="0666", OWNER="shakasan" # Intel
SUBSYSTEM=="usb", ATTR{idVendor}=="24e3", MODE="0666", OWNER="shakasan" # K-Touch
SUBSYSTEM=="usb", ATTR{idVendor}=="2116", MODE="0666", OWNER="shakasan" # KT Tech
SUBSYSTEM=="usb", ATTR{idVendor}=="0482", MODE="0666", OWNER="shakasan" # Kyocera
SUBSYSTEM=="usb", ATTR{idVendor}=="17ef", MODE="0666", OWNER="shakasan" # Lenovo
SUBSYSTEM=="usb", ATTR{idVendor}=="1004", MODE="0666", OWNER="shakasan" # LG
SUBSYSTEM=="usb", ATTR{idVendor}=="22b8", MODE="0666", OWNER="shakasan" # Motorola
SUBSYSTEM=="usb", ATTR{idVendor}=="0e8d", MODE="0666", OWNER="shakasan" # MTK
SUBSYSTEM=="usb", ATTR{idVendor}=="0409", MODE="0666", OWNER="shakasan" # NEC
SUBSYSTEM=="usb", ATTR{idVendor}=="2080", MODE="0666", OWNER="shakasan" # Nook
SUBSYSTEM=="usb", ATTR{idVendor}=="0955", MODE="0666", OWNER="shakasan" # Nvidia
SUBSYSTEM=="usb", ATTR{idVendor}=="2257", MODE="0666", OWNER="shakasan" # OTGV
SUBSYSTEM=="usb", ATTR{idVendor}=="10a9", MODE="0666", OWNER="shakasan" # Pantech
SUBSYSTEM=="usb", ATTR{idVendor}=="1d4d", MODE="0666", OWNER="shakasan" # Pegatron
SUBSYSTEM=="usb", ATTR{idVendor}=="0471", MODE="0666", OWNER="shakasan" # Philips
SUBSYSTEM=="usb", ATTR{idVendor}=="04da", MODE="0666", OWNER="shakasan" # PMC-Sierra
SUBSYSTEM=="usb", ATTR{idVendor}=="05c6", MODE="0666", OWNER="shakasan" # Qualcomm
SUBSYSTEM=="usb", ATTR{idVendor}=="1f53", MODE="0666", OWNER="shakasan" # SK Telesys
SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", MODE="0666", OWNER="shakasan" # Samsung
SUBSYSTEM=="usb", ATTR{idVendor}=="04dd", MODE="0666", OWNER="shakasan" # Sharp
SUBSYSTEM=="usb", ATTR{idVendor}=="054c", MODE="0666", OWNER="shakasan" # Sony
SUBSYSTEM=="usb", ATTR{idVendor}=="0fce", MODE="0666", OWNER="shakasan" # Sony Ericsson
SUBSYSTEM=="usb", ATTR{idVendor}=="0fce", MODE="0666", OWNER="shakasan" # Sony Mobile Communications
SUBSYSTEM=="usb", ATTR{idVendor}=="2340", MODE="0666", OWNER="shakasan" # Teleepoch
SUBSYSTEM=="usb", ATTR{idVendor}=="0930", MODE="0666", OWNER="shakasan" # Toshiba
SUBSYSTEM=="usb", ATTR{idVendor}=="19d2", MODE="0666", OWNER="shakasan" # ZTE
```

2) Ou de manière automatique, via un paquet :

```shell
trizen -S android-udev
```

### Clé FIDO-U2F Key-ID

Idem, pour avoir accès à la clé FIDO U2F via USB.

On créer le fichier `/etc/udev/rules.d/70-u2f.rules` et on ajoute

```shell
# this udev file should be used with udev 188 and newer\nACTION!="add|change", GOTO="u2f_end"

# Key-ID FIDO U2F
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="096e", ATTRS{idProduct}=="0850|0880", TAG+="uaccess"

LABEL="u2f_end"
```

### Ledger Nano S

Et toujours idem... ^^ pour le Ledger Nano S

> __Remarque__ : si vous installez Ledger Live (vois après), les règles `udev` sont installées automatiquement

On créer le fichier `/etc/udev/rules.d/20-hw1.rules` et on ajoute

```shell
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="2b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="3b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="4b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1807", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1808", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2c97", ATTRS{idProduct}=="0000", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2c97", ATTRS{idProduct}=="0001", MODE="0660", TAG+="uaccess", TAG+="udev-acl”
#KERNEL=="hidraw*", SUBSYSTEM=="hidraw", MODE="0660", GROUP="plugdev", ATTRS{idVendor}=="2c97"
#KERNEL=="hidraw*", SUBSYSTEM=="hidraw", MODE="0660", GROUP="plugdev", ATTRS{idVendor}=="2581"
```

### Recharger les règles udev

Afin que ces règles `udev` soit prises en compte directement

```shell
sudo udevadm control --reload
```

## Apps liées au matos

### Logitech Unifying

Solaar permet de configurer les périphériques Logitech compatible Unifying.

```shell
trizen -S solaar
```

### Logitech Gaming Mouses

Piper permet d'avoir accès aux différents paramètres des souris Gaming Logitech.

> __Remarque__ : encore quelques bugs, mais vaut le détour et fait le taf ;)

```shell
trizen -S piper
```

### Lecteur de carte

Le nécessaire pour utiliser les cartes à puces (carde d'identité, ...)

```shell
trizen -S pcsc-tools ccid
```

### eID Middleware

Nécessaire pour les services fédéraux, etc en lignes en Belgique, le middleware eID.

```shell
trizen -S eid-mw
```

### Ledger-Live pour Nano S

On installe la nouvelle application de gestion/config du wallet hardware Ledger Nano S.

> __Remarque__ : c'est valable pour n'importe quel dépôt tier ou `AUR`, il faut __toujours__ vérifier minitieusement ce que vous installez. Et dans le cas de `AUR`, vérifier les sources. Il s'agit d'un Wallet matériel pour Crypto-monnaies, c'est d'autant plus sensible !!

Depuis `AUR`

```shell
trizen -S aur/ledger-udev aur/ledger-live-bin
```

### Corsair

`ckb-next` permet de gérer les périphériques Corsair (touches spécials, rgb, ...)

Depuis AUR

```shell
trizen -S ckb-next
```

### Iscan for EPSON V500 Photo

Application + 'driver' pour le scanner Epson V500 Photo

```shell
trizen -S iscan-for-epson-v500-photo
```

# Apps

L'installation de "base" étant terminée, passons à l'ajout des applications.

> **Astuce** : si vous voulez des infos sur un paquet en particulier, en résumé `trizen -Ss <nom_du_paquet>` et en détaillé `trizen -Si <nom_du_paquet>`.

## Utilitaires

Depuis les dépôts

```shell
trizen -S detox qt5-styleplugins clamav clamtk acetoneiso2 alltray aria2 htop bmon screen bleachbit copyq dialog dosbox fcrackzip fdupes figlet guake idle3-tools mupdf mupdf-tools pdfcrack pdfgrep psensor pv screenfetch neofetch ulauncher terminator tilda pandoc stress odt2txt strace tilix etcher lm_sensors pdfmixtool
```

Depuis `AUR`

```shell
trizen -S tmsu xcalib qtqr woeusb spectre-meltdown-checker inxi cryptkeeper s-tui linpack nano-syntax-highlighting-git kdocker rarcrack
```

Depuis `Flatpak`

```shell
flatpak install com.github.tchx84.Flatseal
```

Depuis `NPM`

```shell
npm i -g coinmon nativefier
```

Depuis `PIP`

```shell
sudo -H pip3 install tldr cheat md2pdf
```

## Multimédia

Depuis des dépôts

```shell
trizen -S quodlibet vlc mpv xsane shotwell simplescreenrecorder lib32-simplescreenrecorder asunder audacious audacious-plugins audacity avidemux-cli avidemux-qt blender cheese cuetools darktable peek shotcut flacon gpick gpicview guvcview handbrake handbrake-cli hugin inkscape kodi krita krita-plugin-gmic milkytracker mkvtoolnix-cli mkvtoolnix-gui moc openshot picard pitivi rawtherapee shntool smplayer smplayer-{skins,themes} soundconverter id3v2 youtube-dl mac libdvdcss celluloid vidcutter font-manager blanket
```

Depuis `AUR`

```shell
trizen -S gmusicbrowser radiotray-ng gyazo green-recorder xnviewmp-system-libs deadbeef
```

Depuis `Flatpak`

```shell
flatpak install flathub com.spotify.Client com.github.marinm.songrec org.gnome.gitlab.YaLTeR.VideoTrimmer org.flozz.yoga-image-optimizer
```

## Rhythmbox + plugins

Depuis les dépôts

```shell
trizen -S rhythmbox
```

Depuis `AUR`

```shell
trizen -S rhythmbox-{llyrics,plugin-alternative-toolbar-git,plugin-hide-git,plugin-radio-browser-git,plugin-tray-icon-git}
```

## Gimp + plugins

Depuis les dépôts

```shell
trizen -S gimp gimp-{help-fr,nufraw,plugin-gmic}
```

Depuis `AUR`

```shell
trizen -S gimp-{plugin-resynthesizer,plugin-refocusit,plugin-pandora,plugin-layers-to-divs,plugin-export-layers}
```

## OBS

Depuis les dépôts

```shell
trizen -S obs-studio
```

Et quelques plugins depuis AUR

```shell
trizen -S obs-motion-effect-git obs-websocket obs-cli
```

## eBook

Depuis les dépôts

```shell
trizen -S calibre sigil fbreader gnome-epub-thumbnailer bookworm foliate
```

## Graveur CD/DVD/BR

Depuis les dépôts

```shell
trizen -S brasero k3b xfburn
```

## Bureautique

Depuis les dépôts

```shell
trizen -S libreoffice-still libreoffice-still-fr dia scribus
```

## Internet, Web

Depuis les dépôts

```shell
trizen -S firefox firefox-i18n-fr chromium slack-desktop thunderbird thunderbird-i18n-fr nextcloud-client digikam discord filezilla google-chrome hexchat icedtea-web links mumble teamspeak3 mypaint opera opera-ffmpeg-codecs pidgin pidgin-{libnotify,otr} purple-{facebook,plugin-pack,skypeweb} quiterss syncthing syncthing-gtk-python3 telegram-{desktop,qt} vivaldi vivaldi-ffmpeg-codecs skypeforlinux-stable zoom liferea rambox-bin cawbird signal-desktop caprine
```

Depuis `AUR`

```shell
trizen -S whalebird-bin tootle birdtray megasync-bin newsflash-git telegram-purple
```

Depuis `Flatpak`

```shell
flatpak install flathub com.microsoft.Teams
```

## Réseau

Depuis les dépôts

```shell
trizen -S aircrack-ng ngrep networkmanager-openvpn dsniff wireshark-qt wireshark-cli iftop iptraf-ng nethogs whois keychain x11-ssh-askpass nmap bind-tools bluez vinagre remmina traceroute
```

Depuis `AUR`

```shell
trizen -S remmina-plugin-rdesktop
```

Depuis `NPM`

```shell
npm i -g whatismyip
```

Depuis `PIP`

```shell
sudo -H pip3 install speedtest-cli
```

## Nautilus + plugins

Depuis les dépôts

```shell
trizen -S nautilus-{image-converter,sendto,share}
```

## Thunar + plugins

Depuis les dépôts

```shell
trizen -S thunar-gtk3 thunar-{archive-plugin-gtk3,media-tags-plugin,volman-gtk3}
```

## Wine

Depuis les dépôts

```shell
trizen -S wine winetricks playonlinux
```

## CAD

Depuis les dépôts

```shell
trizen -S kicad kicad-library kicad-library-3d librecad
```

## Jeux

### Jeux - Utilitaires

Depuis les dépôts

```shell
trizen -S joyutils lutris gnutls lib32-gnutls
```

Pour les joueurs de WoW

```shell
trizen -S wowup
```

### Quelques Jeux

Depuis les dépôts

```shell
trizen -S 0ad
```

Depuis `Flatpak`

```shell
flatpak install flathub org.srb2.SRB2
```

## Gadgets

Depuis `AUR`

```shell
trizen -S no-more-secrets-git
```

## Perso

Depuis `AUR`

```shell
trizen -S wpfind
```

# Customization

Un système qui a du style, ça n'est pas le plus fondamental, mais quand même... ça fais du bien ;)

## Thèmes et icones

On ajoute quelques thèmes GTK et icones

Depuis les dépôts

```shell
trizen -S arc-{gtk-theme,solid-gtk-theme}
```

Depuis `AUR`

```shell
trizen -S numix-{gtk-theme-git,icon-theme-git} arc-icon-theme-full-git
```

## Wallpapers

### HydraPaper

Cet utilitaire permet de configurer des wallpapers différents en configuration multi-écran OU de configurer un wallpaper couvrant l'ensemble de ceux-ci. Contrairement à `Nitrogen`, on ne doit pas sacrifier ses icones de bureau ;)

On installe

```shell
trizen -S hydrapaper-git
```

## Docks

### Plank

Dock léger et bien fini en provenance de ElementaryOS.

```shell
trizen -S plank
```

# Configuration / Customization

Il est maintenant temps d'un peu "fine tuner" comme dirait certains ;)

## SSD

On va activer le TRIM pour les disques SSD

```shell
sudo systemctl enable fstrim.timer
```

## Swappiness

Si l'on dispose de suffisament de RAM, on peut limiter l'usage du swap et donc ménager un peu le SSD

Dans `/etc/sysctl.d/99-sysctl.conf`, on ajoute :

```shell
vm.swappiness=10
```

## Mise à jours du Microcode du CPU

> __Remarque__ : Cette étape est optionnelle, mais fortement recommandée (failles spectre, meltdown, ...) car elle corrige des failles majeures.

### On installe le nécessaire : INTEL

```shell
trizen -S iucode-tool intel-ucode
```

Et on met à jour la configuration de `GRUB` pour qu'il en tienne compte

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### On installe le nécessaire : AMD

```shell
trizen -S iucode-tool amd-ucode
```

Et on met à jour la configuration de `GRUB` pour qu'il en tienne compte

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Firewall

On installe les outils graphique/cli

```shell
trizen -S ufw gufw
```

Si vous utilisez `syncthing`, on ouvre les ports pour celui-ci

```shell
sudo ufw allow syncthing
```

De même si vous faites l'install depuis une connexion SSH, on ouvre les ports SSH également. Et c'est important de le faire __avant__ d'activer le firewall sous peine de se retrouver à la porte ;)

```shell
sudo ufw allow ssh
```

Et on active le firewall au boot.

```shell
sudo systemctl start ufw
sudo systemctl enable ufw
```

## Unbound (cache DNS)

Pour accélérer la résolution DNS, on installe un cache DNS local

```shell
trizen -S unbound
```

Et on active le service au boot

```shell
sudo systemctl start unbound.service
sudo systemctl enable unbound.service
```

## .bashrc

Dans `~/.bashrc`, on ajoute :

Un theme plus sympa pour `mocp`

```shell
alias mocp='mocp -T "darkdot_theme"'
```

Des couleurs et un affichage plus complet de la commande `ls`

```shell
alias ll='ls -lah --color'
```

On affiche `neofetch` à l'ouverture d'un terminal

```shell
neofetch
```

On configure `nano` comme éditeur cli par défaut

```shell
export EDITOR=nano
```

## Terminal par défaut

> __Remarque__ : nécessaire pour certaines applications comme `rclonebrowser` par exemple

Dans `/etc/environment`, on ajoute

```shell
TERMINAL=/usr/bin/tilix
```

## Partages SMB/CIFS dans /etc/fstab

Exemple de configuration dans `/etc/fstab` pour monter automatiquement au boot un dossier partagé sur son NAS avec smb/cifs

```shell
//<VOTRE_ADRESSE_IP>/<NOM_DU_DOSSIER_PARTAGE>     /mnt/<NOM_DU_POINT_DE_MONTAGE_LOCAL>          cifs   vers=3.0,x-systemd.automount,x-systemd.idle-timeout=1min,_netdev,credentials=/etc/smbcreds,rw,iocharset=utf8,uid=1000,gid=1000 0 0
```

> **Remarque** : le `uid` et le `gid` sont a adapter si besoin à ceux de votre utilisateur

Et on regénère les règles `systemd` pour le nouveau point de montage smb/cifs

```shell
sudo systemctl daemon-reload
sudo systemctl restart remote-fs.target
sudo systemctl restart local-fs.target
```

## Grappe Raid 0 pour Logithèque Steam

Cet exemple décrit un RAID 0 (logiciel, via mdadm) de 2 disques : /dev/sdb et /dev/sdc, pour un volume monté via fstab.

### Considération Performance et Sécurité

> __Remarque__ : la taille de chunk est de 512 par défaut et correspondais à mon besoin. La taille de celui-ci + les caractéristiques stride/stripe du système de fichier ext4 vont influencer fortement les performances en fonction de l'usage que vous aller faire de la grappe : jeux, documents, bases de données, .....
>
> __Astuce__ : lisez cette section raid 0 avant de vous lancer pour l'adapter à vos besoins.
>
> __Doc Arch__ : je vous invite à checker la doc de Arch pour plus de détails : [Arch Wiki : RAID](https://wiki.archlinux.org/index.php/RAID)
>
> __Remarque 2__ : sans que ça devienne une doc sur le raid, je rappel que le Raid 0, c'est plus de performance mais aucune sécurité des données. Dans mon cas, il s'agit de mes jeux Steam, au pire, je dois simplement les re-downloader ;-)

### Préparation des disques

Pour éviter certains soucis un peu étranges... je vous conseil de faire un petit nettoyage des 2 disques

> __Remarque__ : suivant la taille des disques et le type (HDD / SSD), cela peut prendre un certain temps

```shell
sudo dd if=/dev/zero of=/dev/sdb
sudo dd if=/dev/zero of=/dev/sdb
```

### Config du raid

On modifie `/etc/mkinitcpio.conf` et on ajoute `mdadm_udev` dans `HOOKS` juste avant `filesystems`:

```shell
HOOKS="base udev autodetect modconf block keyboard keymap plymouth plymouth-encrypt openswap resume mdadm_udev filesystems"
```

On créer le raid 0

> __Remarque__ : cfr la partie performance et sécurité pour le paramètre `chunk`

```shell
sudo mdadm --create --verbose --level=0 --raid-devices=2 /dev/md0 /dev/sdb /dev/sdc
```

On créer la config en l'ajoutant à `/etc/mdadm.conf`

```shell
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
```

Ensuite, on créer un système de fichier sur le raid fraîchement créé

> __Remarque__ : cfr la partie performance et sécurité pour les paramètres `stride` et `stripe`

```shell
sudo mkfs.ext4 -v -L monVolRaid -b 4096 -E stride=128,stripe-width=256 /dev/md0
```

On ajoute un point de montage dans `/etc/fstab`

```shell
/dev/md0  /mnt/monVolRaid  ext4  defaults,noatime 0 2
```

On monte le point de montage

```shell
sudo mount /mnt/monVolRaid
```

On re-genère le `mkinitpcio`

```shell
sudo mkinitpcio -P
```

### Vérification : raid, montage, [...]

```shell
$ lsblk
NAME                                          MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sdb                                             8:16   1   1,8T  0 disk
└─md0                                           9:0    0   3,6T  0 raid0 /mnt/monVolRaid
sdc                                             8:32   1   1,8T  0 disk
└─md0                                           9:0    0   3,6T  0 raid0 /mnt/monVolRaid
```

```shell
$ cat /proc/mounts
[...]
/dev/md0 /mnt/mySSDRaid4T ext4 rw,noatime,stripe=256 0 0
[...]
```

```shell
$ sudo mdadm --detail /dev/md0
/dev/md0:
           Version : 1.2
     Creation Time : Fri Jan  8 18:42:20 2021
        Raid Level : raid0
        Array Size : 3906764800 (3725.78 GiB 4000.53 GB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Fri Jan  8 18:42:20 2021
             State : clean 
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

        Chunk Size : 512K

Consistency Policy : none

              Name : manjaro:0  (local to host manjaro)
              UUID : b71cae0e:5d8cc9ed:f56af4ee:eaf9a8b6
            Events : 0

    Number   Major   Minor   RaidDevice State
       0       8       16        0      active sync   /dev/sdb
       1       8       32        1      active sync   /dev/sdc
```

```shell
$ cat /proc/mdstat
Personalities : [raid0]
md0 : active raid0 sdb[0] sdc[1]
      3906764800 blocks super 1.2 512k chunks

unused devices: <none>
```

```shell
$ df -h
Sys. de fichiers Taille Utilisé Dispo Uti% Monté sur
[...]
/dev/md0           3,6T    984G  2,5T  29% /mnt/monVolRaid
[...]
```

## Environnement QT5

Utilisation de l'utilitaire Kvantum

![kvantum.jpeg](/pics/kvantum.jpeg)

> __Remarque__ : en lieu et place de `qt5ct` + variable `QT_QPA_PLATFORMTHEME=qt5ct`

# Outils de Dev

Pour cette partie, je passe certaines explications étant donné le publique cible ^^

## Outis de base

```shell
trizen -S gdb ghex jq meld
```

## Langages

### Javascript

#### NVM

```shell
trizen -S nvm
```

#### Yarn

```shell
trizen -S yarn
```

Ou bien

```shell
sudo npm i -g yarn
```

#### Gulp

```shell
trizen -S gulp
```

#### Angular CLI

```shell
sudo npm i -g @angular/cli
```

Et utiliser `yarn` à la place de `npm` (AngularCLI >= 6)

```shell
ng config -g cli.packageManager yarn
```

#### Ionic & Cordova

```shell
sudo npm i -g ionic cordova
```

> A besoin d'être mis à jour ;) :')

### GO

```shell
trizen -S go
```

### Lua

```shell
trizen -S lua luajit
```

### PHP

```shell
trizen -S composer php
```

### Python

```shell
trizen -S python-{pyqt5,pipenv} tk
```

```shell
sudo -H pip3 install pylint autopep8
```

### QT5

```shell
trizen -S qtcreator qt5-tools
```

### Ruby

```shell
trizen -S ruby
```

Dans `~/.bashrc`, ajouter

```shell
PATH=$PATH:~/.gem/ruby/2.7.0/bin
```

## Base de données

### MongoDB + Compass

```shell
trizen -S mongodb-bin mongodb-tools-bin mongodb-compass
```

Pour démarrer le serveur

```shell
sudo systemctl start mongodb.service
```

Et si besoin, le démarrer au boot

```shell
sudo systemctl enable mongodb.service
```

### MariaDB

#### Serveur

On install MariaDB

```shell
trizen -S mariadb
sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

On démarre le service

```shell
sudo systemctl start mariadb.service
```

On effectue la post installation

```shell
sudo mysql_secure_installation
```

Et éventuellement on démarre MariaDB au boot si besoin

```shell
sudo systemctl enable mariadb.service
```

#### Client

```shell
trizen -S mysql-workbench
```

### SQLite + SQLiteBrower

```shell
trizen -S sqlite sqlitebrowser
```

## IDE

### Android Studio

```shell
trizen -S android-tools android-studio
```

OU utiliser l'app Toolbox (conseillé)

```shell
trizen -S jetbrains-toolbox
```

#### SDK Tools

Pour avoir accès aux outils du SDK : adb, fastboot, ... (installés via Android Studio et le SDK Manager)

Ajouter cette ligne à votre `.bashrc`

```shell
PATH=$PATH:/home/shakasan/Android/Sdk/platform-tools
```

### Arduino IDE

```shell
trizen -S arduino arduino-docs arduino-avr-core
sudo usermod -a -G uucp shakasan
sudo usermod -a -G lock shakasan
```

### Atom

```shell
trizen -S atom apm
```

### Brackets

```shell
trizen -S brackets-bin
```

### Bluefish

```shell
trizen -S bluefish
```

### Codeblocks

```shell
trizen -S codeblocks
```

### Eric

```shell
trizen -S eric
```

### Geany

```shell
trizen -S geany geany-plugins
```

### Glade

```shell
trizen -S glade
```

### Intellij-idea (CE)

```shell
trizen -S intellij-idea-community-edition
```

OU utiliser l'app Toolbox (conseillé)

```shell
trizen -S jetbrains-toolbox
```

### Lazarus

```shell
trizen -S lazarus-qt5 gdb
```

### Notepadqq

```shell
trizen -S notepadqq
```

### PyCharm (CE)

```shell
trizen -S pycharm-community-edition
```

OU utiliser l'app Toolbox (conseillé)

```shell
trizen -S jetbrains-toolbox
```

### Visual Studio Code

```shell
trizen -S visual-studio-code-bin
```

### BlueJ

```shell
trizen -S bluej
```

## Outils

### Ansible

```shell
trizen -S ansible ansible-lint
```

### Boostnote

```shell
trizen -S boost-note-local-bin
```

### Joplin

```shell
trizen -S joplin-desktop
```

### DBeaver

```shell
trizen -S dbeaver
```

### Docker

```shell
trizen -S docker docker-compose
```

On créer un groupe `docker` et on y ajoute son user

> **Remarque** : après cette étape, il est fortement recommandé de logout/login complètement

```shell
sudo groupadd
sudo usermod -aG docker <votre_user>
```

On check si `docker` tourne

```shell
sudo systemctl status docker
```

Et éventuellement on lance `docker` et on l'ajoute au boot si nécessaire

```shell
sudo systemctl start docker
sudo systemctl enable docker
```

### Portainer CE

```shell
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

### git

```shell
trizen -S git
```

### packagecloud cli tool

```shell
gem install package_cloud
```

### Postman

```shell
trizen -S postman-bin
```

### Umbrello

```shell
trizen -S umbrello
```

### Vagrant

```shell
trizen -S vagrant
```

### VirtualBox

```shell
trizen -S virtualbox virtualbox-host-dkms
```

Et en fonction de votre Kernel

```shell
trizen -S linux512-headers linux512-virtualbox-host-modules
```

Suivi d'un reboot ou bien

```shell
sudo modprobe vboxdrv
```

### Wordpress

```shell
trizen -S wpscan
```

### Zeal

```shell
trizen -S zeal
```

# Info & Licence

- Doc réalisée par Francois B (Makoto)
- Publiée sous les termes de la licence Creative Commons [CC-BY-NC-SA](https://creativecommons.org/licenses/by-nc-sa/2.0/be/)

[![cc-by-nc-sa](https://licensebuttons.net/l/by-nc-sa/2.0/be/88x31.png)](https://creativecommons.org/licenses/by-nc-sa/2.0/be/)
