Hoe moet je Fedora minimal install doen?					     Versie 2.5


    A) Basis Installeren

    (1) Download Fedora Server Netinstall image of Everything (netinstall)
    (2) Installeren met minimal install (UEFI of BIOS, wel/geen LVM, wel/geen root account, ext4)
    (3) Na installatie de volgende commands;

sudo dnf clean all && sudo dnf check-update
sudo dnf remove plymouth*
(optional) sudo dnf group list -v hidden
(optional) sudo dnf group info base-x
sudo dnf install --exclude="plymouth-system-theme xorg-x11-drv-*" @base-x		
sudo dnf install xorg-x11-drv-libinput (*1)

Essential programs
sudo dnf install gnome-shell bash-completion sqlite libnm-gtk	  		# gnome en dnf
sudo dnf install tilix nano							# terminal install
sudo dnf install dejavu-sans-*-fonts dejavu-serif-fonts liberation-*-fonts  	# fonts
sudo dnf install NetworkManager-wifi iwl6000-firmware(*2) pciutils		# wifi voor laptop
sudo dnf install firefox google-roboto-mono-fonts xorg-x11-fonts-misc          	# webbrowser
sudo dnf install htop smartmontools msr-tools(*3)				# system usage
sudo dnf install tlp(*4)							# powersaving
sudo dnf install eog gnome-calculator xlockmore					# image viewer etc.
sudo dnf install alsa-plugins-pulseaudio					# audio extra	
sudo dnf install gnome-tweak-tool dconf-editor gnome-disk-utility file-roller	# gnome tweaks
sudo dnf install youtube-dl							# media
sudo dnf install evince gedit							# document viewer
*1 mss. ook andere xorg-x11-drv? 
*2 anders voor elke wifi card
*3 voor Dell Bios
*4 voor laptop

Optional programs
sudo dnf install nautilus xdg-user-dirs xdg-user-dirs-gtk gvfs-mtp		# file browser
sudo dnf install tracker-preferences						# nautilus tweak
sudo dnf install libreoffice-writer libreoffice-calc libreoffice-langpack-nl	# office
sudo dnf install gimp								# image editor
	
    B) Post Installatie

sudo systemctl set-default graphical.target					# grafische omgeving boot
sudo systemctl enable gdm.service						# display manager
sudo systemctl isolate graphical.target						# start
xdg-user-dirs-update (*5)
xdg-user-dirs-gtk-update (*6)
*5 indien home directories niet zijn aangemaakt
*6 zelfde als boven

RPM Fusion 
sudo dnf install --nogpgcheck http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install mpv libva-intel-driver ffmpeg

Papirus Icons
sudo dnf copr enable dirkdavidis/papirus-icon-theme
sudo dnf install papirus-icon-theme

Adobe Flash
sudo rpm -ivh http://linuxdownload.adobe.com/adobe-release/adobe-release-x86_64-1.0-1.noarch.rpm
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-adobe-linux
sudo dnf install flash-plugin libcurl

    C) Services aanpassen/uitschakelen (optional)

systemctl list-unit-files --type=service | grep enabled				# see all enabled
sudo systemctl reset-failed							# reset failed
  
sudo systemctl enable fstrim.timer (voor ssd)		sudo systemctl disable dnf-makecache.timer
sudo systemctl disable firewalld.service		sudo systemctl disable dbxtool.service
sudo systemctl disable sssd.service			sudo systemctl mask systemd-rfkill.service		
sudo systemctl disable switcheroo-control.service	sudo systemctl mask systemd-rfkill.socket
sudo systemctl disable atd.service					
sudo systemctl disable auditd.service		
sudo systemctl disable bluetooth.service
sudo systemctl disable mdmonitor.service
sudo systemctl disable multipathd.service

sudo dracut -fv									# initramfs maken
systemd-analyze time								# boot time 
systemd-analyze blame								# boot services

    D) Misc tweaks (optional)

Algemene tweaks
echo "echo \"options iwlwifi led_mode=1\" >> /etc/modprobe.d/iwlwifi.conf" | sudo bash	# wifi led

gsettings set org.gnome.shell.window-switcher current-workspace-only false	# gnome

sudo gedit /etc/sysctl.conf							# disk 
vm.swappiness=10
vm.dirty_background_ratio = 2
vm.dirty_ratio = 3
sudo sysctl -p

sudo gedit /etc/fstab								# fstab 
noatime bij ext4
noauto,x-systemd.automount bij /home

swapon -s									# hibernate
resultaat toevoegen aan GRUB_CMDLINE_LINUX (/etc/default/grub) dus resume=/path/to/swap
in tweaktool of power settings, powerbutton hibernate

xlock -mode rain -dpmsoff 1 -timeelapsed -echokeys -echokey "*" +description -font "fixed"

edit /etc/systemd/logind.conf							# powerbutton remap
HandlePowerKey=suspend i.p.v. poweroff

Dell CPU fix
sudo rdmsr -a 0x19a 								# indien 1c dan
sudo wrmsr -a 0x19a 0x0								# reset naar 0
sudo gedit /usr/lib/systemd/system-sleep/cpu_clock_fix 
			
			#!/bin/sh
			wrmsr -a 0x19a 0x0

sudo chmod +x /usr/lib/systemd/system-sleep/cpu_clock_fix

Benchmark
time echo "scale=5000; 4*a(1)" | bc -l -q ; cat /proc/cpuinfo | grep "model name"; cat /proc/meminfo  | grep MemTotal

Pulseaudio
rm -rf /tmp/pulse* ~/.pulse* ~/.config/pulse
pulseaudio -k
pulseaudio --start

mv /etc/pulse/default.pa /etc/pulse/default.pa.bak
comment alles met bluetooth en module-esound-protocol-unix

Refresh System (zonder reboot)
sudo systemctl isolate multi-user.target
sudo systemctl emergency

pkill -u latitude
echo 3 > /proc/sys/vm/drop_caches
swapoff -a
swapon -a
systemctl daemon-reload
systemctl daemon-reexec
htop
Control + d

Reset Gnome
dconf reset -f /org/gnome/shell/extensions/
dconf dump / > my_settings
dconf reset -f /
dconf load / < my_settings

Eject Esata
sync
hdparm -y /dev/sdb
echo offline > /sys/block/sdb/device/state
echo 1 > /sys/block/sdb/device/delete
