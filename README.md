# ArchLinuxWebtop
Notes on setup of Arch Linux webtop

These notes are integrated into one of our projects: https://github.com/eliranwong/uniquebibleapp-webtop

# Setup docker

On Linux [Debian / Ubuntu]:

> sudo apt update & sudo apt dist-upgrade

> sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common

> curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

> sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"

> sudo apt update & sudo apt dist-upgrade

> sudo apt-get install docker-ce docker-ce-cli containerd.io

> sudo docker run hello-world

On ChromeOS:

https://dvillalobos.github.io/2020/How-to-install-and-run-Docker-on-a-Chromebook/

On macOS:

https://docs.docker.com/desktop/mac/install/

On Windows:

https://docs.docker.com/desktop/windows/install/

# Setup Arch Linux Webtop

For macOS users:

> docker run -d --name=webtop --security-opt seccomp=unconfined -e PULSE_SERVER=host.docker.internal -e PUID=501 -e PGID=20 -e TZ=Europe/London -e SUBFOLDER=/ -e KEYBOARD=en-gb-qwerty -p 3000:3000 -v ~/webtops/arch-xfce:/config -v /var/run/docker.sock:/var/run/docker.sock -v ~/.config/pulse:/config/.config/pulse --shm-size="1gb" --restart unless-stopped lscr.io/linuxserver/webtop:arch-xfce

Others:

> sudo docker run -d --name=webtop --security-opt seccomp=unconfined -e PUID=1000 -e PGID=1000 -e TZ=Europe/London -e SUBFOLDER=/ -e KEYBOARD=en-gb-qwerty -p 3000:3000 -v ~/webtops/arch-xfce:/config -v /var/run/docker.sock:/var/run/docker.sock --shm-size="1gb" --restart unless-stopped lscr.io/linuxserver/webtop:arch-xfce

notes:

* check host user UID and GID with command "id \<user\>", change -e PUID=501 -e PGID=20 [macOS] or -e PUID=1000 -e PGID=1000 [chomeOS] if necessary.

* ~/webtops is the host local path for storing webtop home directory, change it according your own needs.

# Open Arch Linux webtop

Open "localhost:3000" with a web browser.

# Setup audio [macOS users ONLY]

On host macOS:

> brew install pulseaudio

> pulseaudio --load=module-native-protocol-tcp --exit-idle-time=-1 --daemon

(One may use the following line to check which shell is being used. recent macOS versions use zsh by default, older versions use bash by default):

> echo $SHELL

(For zsh users)

> echo 'pulseaudio --load=module-native-protocol-tcp --exit-idle-time=-1 --daemon' >> ~/.zshrc

(For bash users)

> echo 'pulseaudio --load=module-native-protocol-tcp --exit-idle-time=-1 --daemon' >> ~/.bash_profile

On webtop:

> sudo pacman -Syu --noconfirm

> sudo pacman -S --noconfirm pulseaudio

> export PULSE_SERVER=host.docker.internal

> sudo sh -c 'echo "PULSE_SERVER=host.docker.internal" >> /etc/environment'

# Install basic tools

> sudo pacman -Syu --noconfirm 

> sudo pacman -S --noconfirm wget gcc glibc git python-pip python-pyqt5 python-pyqt5-sip python-pyqt5-webengine python-qtpy

> pip install --upgrade pip

# Add PATH

> echo 'export PATH="${PATH}:/config/.local/bin"' >> ~/.bashrc

# Text Editor

> sudo pacman -S --noconfirm nano geany

# Display of Chinese characters [optional]

> sudo sh -c 'echo "zh_CN.UTF-8 UTF-8" >> /etc/locale.gen'

To download language pack:

> sudo locale-gen

To download Traditioanl Chinese fonts:

> sudo pacman -S --noconfirm wqy-bitmapfont wqy-zenhei ttf-arphic-ukai ttf-arphic-uming opendesktop-fonts wqy-microhei wqy-microhei-lite

To download additional Simplified Chinese fonts:

> sudo pacman -S --noconfirm adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts noto-fonts-cjk

To install user fonts [optional]:
[It is optional, but you may consider it for WPS office to display characters properly.]<br>
For example, put all fonts in a folder "MyFonts" in "Downloads", then enter in terminal:

> mkdir ~/.fonts<br>
> cp -r /mnt/chromeos/MyFiles/Downloads/MyFonts/ ~/.fonts/<br>
> fc-cache -f -v<br>

Check all installed font list

> fc-list

Check installed Chinese font list

> fc-list :lang=zh | cut -d: -f2<br>
> fc-list :lang=zh | cut -d: -f1<br>
> fc-list -f '%{family}\n' :lang=zh

Further reference:

https://wiki.archlinux.org/title/Localization_(正體中文)/Traditional_Chinese_(正體中文)

https://wiki.archlinux.org/title/Localization_(简体中文)/Simplified_Chinese_(简体中文)

# Input method fcitx [optional]

> sudo pacman -S --noconfirm fcitx-im fcitx-googlepinyin fcitx-configtool opencc

> echo $'export XMODIFIERS="@im=fcitx"\nexport XIM_PROGRAM="fcitx"\nexport GTK_IM_MODULE="fcitx"\nexport QT_IM_MODULE="fcitx"' >> ~/.xinitrc

Run host terminal:

> sudo docker restart webtop<br>
Note: "docker restart webtop" on macOS

After restart, select "Configure" from keyboard dropdown menu located on top navigation bar.

Add Google PinYin

To switch between GoodPinYin and default English Keyboard, either:

1) Select "Input Method" via keyboard dropdown menu

2) Press on webtop VIRTUAL keyboard, made available by clicking the black dot button on the left, "Ctrl > SPACE > Ctrl"

# Text-to-speech

> pip install gTTS

> sudo pacman -S --noconfirm sox mpg123

To test:

> gtts-cli 'hello' | play -t mp3 -

> gtts-cli --lang zh --nocheck '你好' | play -t mp3 -

> gtts-cli 'hello' --output hello.mp3

> mpg123 hello.mp3

Documents: https://gtts.readthedocs.io/en/latest/

# Install media player

For example, vlc player:

> sudo pacman -S --noconfirm vlc

# Install and run UniqueBibleApp

> git clone https://github.com/eliranwong/UniqueBible

To run UBA, either:

* Open terminal and run:

> python3 UniqueBible/uba.py

* or open from menu "Applications" > "Other" > "Unique Bible App"

Note: Running UBA the first time takes extra time to install essential data.

Edit config.py after UBA ran the first time and closed.

> qtLibrary = 'pyqt5'

> gTTS = True

If fcitx is installed and you want to use it in UBA,

> fcitx = True

# Install AUR Helper - yay

> git clone https://aur.archlinux.org/yay.gi

> cd yay

> makepkg -si

To update available AUR packages:

> yay -Syu

To search for AUR package(s):

> yay -Ss [string]

To install an AUR package:

> yay -S [package]

To uninstall an AUR package:

> yay -Rns [package]

To remove extra packages and any remnants from past installations:

> yay -Yc

# Uninstall pacman package(s)

To search for package name(s) associated with an app, e.g. libreoffice:

> sudo pacman -Ss libreoffice | grep -i installed

> sudo pacman -Rs [package]

# Useful command line tools

https://bbs.archlinux.org/viewtopic.php?id=50475
