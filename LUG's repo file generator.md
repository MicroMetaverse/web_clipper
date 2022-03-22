# LUG's repo file generator
#### Tips: 可通过 URL 的形式直接下载配置. 例如:

[https://mirrors.ustc.edu.cn/repogen/conf/debian-http-6-sid](https://mirrors.ustc.edu.cn/repogen/conf/debian-http-6-sid)
[https://mirrors.ustc.edu.cn/repogen/conf/archlinux-https-4](https://mirrors.ustc.edu.cn/repogen/conf/archlinux-https-4)

#### Archlinux (/etc/pacman.d/mirrorlist):

```
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch

```

#### Debian (/etc/apt/sources.list):

Tips: Remember to install package <apt-transport-https>

```
deb https://mirrors.ustc.edu.cn/debian/ bullseye main contrib non-free
deb-src https://mirrors.ustc.edu.cn/debian/ bullseye main contrib non-free

deb https://mirrors.ustc.edu.cn/debian/ bullseye-updates main contrib non-free
deb-src https://mirrors.ustc.edu.cn/debian/ bullseye-updates main contrib non-free

deb https://mirrors.ustc.edu.cn/debian/ bullseye-backports main contrib non-free
deb-src https://mirrors.ustc.edu.cn/debian/ bullseye-backports main contrib non-free

deb https://mirrors.ustc.edu.cn/debian-security/ bullseye-security main contrib non-free
deb-src https://mirrors.ustc.edu.cn/debian-security/ bullseye-security main contrib non-free

```

#### Ubuntu (/etc/apt/sources.list):

```
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-security main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

## Not recommended
# deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse

```

Send mirror related questions to mirrors@ustc.edu.cn. 
 [https://mirrors.ustc.edu.cn/repogen/](https://mirrors.ustc.edu.cn/repogen/)
