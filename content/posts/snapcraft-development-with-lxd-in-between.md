---
Categories: ["ubuntu"]
Description: ""
Keywords: [code]
Tags: [snappy, snapcraft, development]
date: 2017-08-23T13:29:02-03:00
title: snapcraft development with LXD in bewteen
draft: true
---

To work on the [snapcraft](https://snapcraft.io) [codebase](https://github.com/snapcore/snapcraft.git)
I first install my development tools

# Install
```
$ sudo snap install vscode
$ sudo snap install lxd
$ sudo apt install git
```

# Setup

Cloning the code is rather simple:
```
$ mkdir ~/source
git clone https://github.com/snapcore/snapcraft.git ~/source/snapcraft
```

## LXD setup
LXD would need some setup, to initialise,

```
$ sudo lxd init
```

To run LXD as a user, you need to be in the `lxd` group,
```
$ sudo adduser $(whoami) lxd
```

And finally, to just keep on working from the same terminal without a need
to logout and back in you can,
```
$ newgrp lxd
```

Set the system up for correct uid/gid mapping,
```
$ printf "lxd:$(id -u):1\nroot:$(id -u):1\n" | sudo tee -a /etc/subuid
$ printf "lxd:$(id -g):1\nroot:$(id -g):1\n" | sudo tee -a /etc/subgid
$ sudo snap restart lxd
```

Create a container and set it up,
```
$ lxc launch ubuntu:16.04 snapcraft-dev
$ printf "uid $(id -u) 1000\ngid $(id -g) 1000" | lxc config set snapcraft-dev raw.idmap -
$ lxc restart snapcraft-dev
$ lxc config device add snapcraft-dev snapcraft-source disk source=~/source/snapcraft path=/home/ubuntu/source/snapcraft
```

Verify the permissions are correct,
```
lxc exec snapcraft-dev -- sudo -iu ubuntu ls -l source/snapcraft | head -2
total 52232
-rw-rw-r--  1 ubuntu ubuntu     1245 Oct  7 20:40 CODE_STYLE.md
```

Now enter and setup,
```
$ lxc exec snapcraft-dev -- sudo -iu ubuntu
$ sudo apt update
$ sudo apt dist-upgrade
```

Then setup the development environment inside the container as instructed on `HACKING.md`

Back on your host create these two scripts:
```
mkdir ~/bin
cat > ~/bin/snapcraft-mypy <<'EOF'
#!/bin/sh

user="$(whoami)"
reflected_pwd=$(echo $PWD|sed -e "s|$user|ubuntu|")
lxc exec snapcraft-dev -- su ubuntu -c "cd \"$reflected_pwd\";/home/ubuntu/venv/snapcraft/bin/mypy $@"
EOF
chmod +x ~/bin/snapcraft-mypy

cat > ~/bin/snapcraft-flake8 <<'EOF'
#!/bin/sh

user="$(whoami)"
reflected_pwd=$(echo $PWD|sed -e "s|$user|ubuntu|")
lxc exec snapcraft-dev -- su ubuntu -c "cd \"$reflected_pwd\";/home/ubuntu/venv/snapcraft/bin/flake8 $@"
EOF
chmod +x ~/bin/snapcraft-flake8
```

## Visual Studio Code setup
Then I install a [python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python) for [code](https://code.visualstudio.com/).
After that comes the tricky part to setup everything so the host does not need anymore
tampering with than necessary.

```
$ 