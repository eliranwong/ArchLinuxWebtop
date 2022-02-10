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

> sudo docker run -d --name=webtop --security-opt seccomp=unconfined -e PUID=1000 -e PGID=1000 -e TZ=Europe/London -e SUBFOLDER=/ -e KEYBOARD=en-gb-qwerty -p 3000:3000 -v /home/\<user\>/development/webtops/arch-xfce:/config -v /var/run/docker.sock:/var/run/docker.sock --shm-size="1gb" --restart unless-stopped lscr.io/linuxserver/webtop:arch-xfce

notes:

* no need to use "sudo" on macOS.

* check UID and GID with command "id \<user\>", change -e PUID=1000 -e PGID=1000 if necessary.

* /home/\<user\>/development/webtops is the host local path for storing webtop home directory, change it according your own needs.

To open Arch Linux webtop, open "localhost:3000" on a web browser.

# Download and Run UniqueBibleApp

To setup, open Arch Linux terminal and run:

> sudo pacman -Syu

> sudo pacman -S gcc git python-pip python-pyqt5 python-pyqt5-sip python-pyqtwebengine python-qtpy

> git clone https://github.com/eliranwong/UniqueBible

To run UBA:

> python3 UniqueBible/uba.py

Note: Running UBA the first time takes extra time to install essential data.

# Locale
