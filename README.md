# Fedora-custom-install
Creating a minimal and custom Fedora GNU/Linux install

How can you install Fedora with the most performance/security and awesomeness?

Installing the base system

1. Download Fedora server Netinstall image (https://download.fedoraproject.org/pub/fedora/linux/releases/25/Server/x86_64/iso/Fedora-Server-netinst-x86_64-25-1.3.iso)

2. Fllow instructions and choose a minimal install, geen root account maken!
3. Na installatie de volgende commands;

	sudo dnf install @base-x gnome-shell						# grafische omgeving
	sudo dnf copr enable heikoada/terminix 						# terminal
	sudo dnf install terminix bash completion					
	sudo dnf install dejavu-sans-mono-fonts dejavu-sans-fonts dejavu-serif-fonts	# fonts
	sudo dnf install NetworkManager-wifi 						# wifi voor laptop
	sudo dnf install firefox liberation-*-fonts
	sudo dnf install gedit
	sudo dnf install nautilus
	sudo dnf install gnome-system-monitor
	sudo dnf install eog
	sudo dnf install evince
	sudo dnf install gvfs-mtp
	sudo dnf install rhytymbox
	sudo dnf install rfkill
	sudo dnf install xorg-x11-drv-libinput
	sudo dnf install gnome-tweak-tool
	sudo dnf install gnome-disk-utility
	sudo dnf install iwl6000-firmware
	sudo systemctl set-default graphical.target 					# grafische omgeving boot
	sudo systemctl isolate graphical.target 					# start
	sudo dnf install xdg-user-dirs
	xdg-user-dirs-update 
	sudo dnf install pciutils
	sudo dnf install gnome-shell-extension-alternate-tab
	sudo dnf install gnome-shell-extension-workspace-indicator
	sudo dnf install xdg-user-dirs-gtk
	xdg-user-dirs-gtk-update
	sudo dnf install file-roller 
	sudo dnf install libgtop2-devel NetworkManager-glib-devel
	sudo dnf install --nogpgcheck http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
	sudo rpm -ivh http://linuxdownload.adobe.com/adobe-release/adobe-release-x86_64-1.0-1.noarch.rpm
	sudo dnf install flash-plugin alsa-plugins-pulseaudio
	sudo dnf install vlc
	sudo dnf install transmission screenfetch 
	sudo dnf install libva-intel-driver libva-utils
	sudo dnf install wavemon
	sudo dnf install youtube-dl
	sudo dnf install ffmpeg
	sudo dnf install tar
	sudo dnf install libreoffice-writer
	sudo dnf install libreoffice-langpack-nl

3. services uitschakelen
	
	sudo dnf remove plymouth
	systemctl mask plymouth-start.service
	systemctl mask plymouth-quit-wait.service
	systemctl disable firewalld
	systemctl disable bluetooth.service 
	systemctl mask iio-sensor-proxy.service
	systemctl disable sshd.service 
	sudo systemctl reset-failed
	sudo dracut --force
	systemd-analyze time 
	systemd-analyze blame
	
