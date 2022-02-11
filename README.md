# ArchLinuxWebtop
Notes on setup of Arch Linux webtop

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

> docker run -d --name=webtop --security-opt seccomp=unconfined -e PULSE_SERVER=host.docker.internal -e PUID=501 -e PGID=20 -e TZ=Europe/London -e SUBFOLDER=/ -e KEYBOARD=en-gb-qwerty -p 3000:3000 -v /Users/\<user\>/webtops/arch-xfce:/config -v /var/run/docker.sock:/var/run/docker.sock -v ~/.config/pulse:/config/.config/pulse --shm-size="1gb" --restart unless-stopped lscr.io/linuxserver/webtop:arch-xfce

Others:

> sudo docker run -d --name=webtop --security-opt seccomp=unconfined -e PUID=1000 -e PGID=1000 -e TZ=Europe/London -e SUBFOLDER=/ -e KEYBOARD=en-gb-qwerty -p 3000:3000 -v /home/\<user\>/webtops/arch-xfce:/config -v /var/run/docker.sock:/var/run/docker.sock --shm-size="1gb" --restart unless-stopped lscr.io/linuxserver/webtop:arch-xfce

notes:

* check host user UID and GID with command "id \<user\>", change -e PUID=501 -e PGID=20 [macOS] or -e PUID=1000 -e PGID=1000 [chomeOS] if necessary.

* /home/\<user\>/webtops is the host local path for storing webtop home directory, change it according your own needs.

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

> sudo pacman -Syu

> sudo pacman -S pulseaudio

> sudo echo 'PULSE_SERVER=host.docker.internal' >> /etc/environment

On host macOS:

> docker restart webtop

# Install basic tools

> sudo pacman -Syu

> sudo pacman -S wget gcc glibc git python-pip python-pyqt5 python-pyqt5-sip python-pyqt5-webengine python-qtpy

# Text Editor

> sudo pacman -S nano geany

# Display of Chinese characters

Manually add "zh_CN.UTF8 UTF8"

> sudo nano /etc/locale.gen

To download language pack:

> sudo locale-gen

To download Traditioanl Chinese fonts:

> sudo pacman -S wqy-bitmapfont wqy-zenhei ttf-arphic-ukai ttf-arphic-uming opendesktop-fonts wqy-microhei wqy-microhei-lite

To download additional Simplified Chinese fonts:

> sudo pacman -S adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts noto-fonts-cjk

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

# Input method fcitx

> sudo pacman -S fcitx-im fcitx-googlepinyin fcitx-configtool opencc

> nano ~/.xinitrc

Manually add the following lines:

> export XMODIFIERS="@im=fcitx"<br>
> export XIM_PROGRAM="fcitx"<br>
> export GTK_IM_MODULE="fcitx"<br>
> export QT_IM_MODULE="fcitx"

Run host terminal:

> sudo docker restart webtop<br>
Note: "docker restart webtop" on macOS

After restart, select "Configure" from keyboard dropdown menu located on top navigation bar.

Add Google PinYin

To switch between GoodPinYin and default English Keyboard, either:

1) Select "Input Method" via keyboard dropdown menu

2) Press on webtop VIRTUAL keyboard, made available by clicking the black dot button on the left, "Ctrl > SPACE > Ctrl"

# Install and run UniqueBibleApp

> git clone https://github.com/eliranwong/UniqueBible

To run UBA:

> python3 UniqueBible/uba.py

Note: Running UBA the first time takes extra time to install essential data.

Edit config.py after UBA ran the first time and closed.

> qtLibrary = 'pyqt5'

If fcitx is installed and you want to use it in UBA,

> fcitx = True
