# Ansible Arch

These ansible playbooks is used to setup an openionated workstation in archlinux.
That i use for my daily driver.

# How to execute

* install ansible and git<br>
  `sudo pacman -S ansible git`
* install ansible aur dependencies
  `ansible-galaxy collection install -r requirements.yml`
*  clone this repo<br>
  `git clone https://github.com/Mane-Pal/ansible-arch.git`
* enter the directory<br>
  `cd ansible-arch`
* run the playbooks you want
    * `ansible-playbook -u $USER -K playbook_core.yml`
    * `ansible-playbook -u $USER -K playbook_zsh.yml`
    * `ansible-playbook -u $USER -K playbook_docker.yml`
    * `ansible-playbook -u $USER -K playbook_dev_tools.yml`

yes, you write `$USER` there, which puts in the user you are logged in <br>
the `-K` is short for `--ask-become-pass` which will prompt for password

**Removal (optional)**<br>
After running playbooks it be good to remove ansible package
and bunch of its dependancies. Saves \~400MB and noise during updating.

* `sudo pacman -Rns ansible`

# Playbooks


### playbook_core.yml

useful terminal progams, settings, maintance services 

* arch update/upgrade, equivalent of `pacman -Syu`
* install:<br>
  man-db, git, curl, wget, rsync, exa, fd, fzf, bat, tree,
  unarchiver, duf, ncdu, htop, iotop, glances, nmap, gnu-netcat, tcpdump,
  net-tools, iproute2, bind, nload, sysfsutils, lsof, borg, fuse,
  python-llfuse, python-pip, python-setuptools, python-pexpect, sqlite
* install yay to have access to AUR<br>
  set - remove make dependencies, always clean builds, cleanup after
* in pacman.conf enable color and enable parallel downloads
* in makepkg.conf disable compression and enable parallel compilation
* `noatime` set in fstab to avoid unnecessary writes of `relatime`
* increase allowed failed login attempts to 10 before lock out
* enable members of wheel group to sudo
* services to install and enable
    * ssh - remote access
    * plocate - file search locate
    * cronie - cron time scheduler
    * archlinux-keyring - weekly update
    * fstrim - weekly ssd trim
    * trash-cli - delete to trash
    * paccache - weekly clearing of pacman cache
    * reflector - weekly update of mirrorlist
    * logrotate - if need to prevent logs from growing
* install neofetch

### playbook_terminal_zsh.yml

* install wezterm
* install zsh shell
* copy bash history in to .zhistory
* change the default shell from bash to zsh for the user
* install zimfw using its own script
* change the theme to `bira`  [other themes zimfw](https://zimfw.sh/docs/themes/)

### playbook_docker.yml

* install docker, docker-compose, ctop
* enable and start docker service
* add the current user to the docker group to avoid need for sudo
* set default max logs size to 250MB and set logs rotation


# Useful Notes

bunch of linux commands

* `journalctl -p 3 -xb`
* `journalctl -b -r`
* `systemctl --failed`
* `systemctl list-units --type=service --state=active`
* `systemctl list-units --type=timer --state=active`
* `systemctl list-timers`
* `journalctl -r -u borg.timer`
* `systemctl list-units --type=mount`
* `systemctl list-units --type=automount`
* `findmnt`
* `cat /proc/cmdline`
* `lsmod`
* `lspci -k`
* `rsync -ah --info=progress2 ./minecraft /mnt/bigdisk/backup`
* `sudo dd bs=4M if=arch.iso of=/dev/sdX status=progress oflag=direct`
* `sudo nethogs` - realtime traffic per process
* `sudo ss -tulpn` - shows what uses which port
* `curl ipinfo.io` - get current public IP
* `sudo nc -vv -l -p 8789` - netcat starts tiny server listening at port 8789,<br>
   do port forwarding on router/firewall, then test on
   [https://www.grc.com/x/portprobe=8789](https://www.grc.com/x/portprobe=8789)
* `sudo tcpdump -n udp port 21116` - see udp traffic on a port
* `pacman -F <path to a file>` - which package owns that file
* `grep -i upgraded /var/log/pacman.log | tac | less` - last upgraded packages

# Encountered issues

* check - `sudo journalctl -p 3 -xb` and `lsmod | grep i2c` for trouble shooting as first step
* **Weekly hang-up because swap was off**. Archlinux VM docker host experienced
  [huge spike of constant disk use](https://i.imgur.com/2NWXpu8.png)
  which was cause by the lack of SWAP. After adding 6GB swap file it was rock solid.
* If **running arch without update for a long time** - `sudo pacman -Sy archlinux-keyring`
  before updating everything else with `pacman -Syu`.<br>
  Enabling `archlinux-keyring-wkd-sync.timer` will update the package weekly.
  It's part of the core playbook.
* To **update zim** zsh framework- `zimfw upgrade` and `zimfw update`.

