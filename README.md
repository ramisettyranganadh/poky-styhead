README.poky.md

----------------------Add WiFi Recipe in Yocto-----------------------------

1. Add below commands in local.conf file:
	IMAGE_INSTALL:append = " wpa-supplicant"
2. Create wpa-supplicant_%.bbappend (% is same version as wpa-supplicant_%.bb)in /meta/recipes-connectivity/wpa-supplicant/ and add below commands:
	SYSTEMD_AUTO_ENABLE:wpa-supplicant = "enable"
	SYSTEMD_SERVICE:wpa-supplicant = "wpa_supplicant.service"

3. Add below commands in /meta/recipes-connectivity/wpa-supplicant/wpa-supplicant.conf file:
	ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
	update_config=1
	country=<Your_Country_Code>  # e.g., US, IN, etc.
	network={
	    ssid="<YourSSID>"
	    psk="<YourPassword>"
	}
4. Execute below bitbake command:
	bitbake core-image-minimal
5. Add the below commands in local.conf file if systemctl is not found:
	DISTRO_FEATURES:append = " usrmerge"
	DISTRO_FEATURES:append = " systemd"
	VIRTUAL-RUNTIME_init_manager = "systemd"
	VIRTUAL-RUNTIME_initscripts = "systemd-compat-units"
	(OR)
	DISTRO_FEATURES:append = " usrmerge"
	DISTRO_FEATURES:append = " systemd"
	VIRTUAL-RUNTIME_init_manager = "systemd"
	VIRTUAL-RUNTIME_initscripts = ""
	VIRTUAL-RUNTIME_sysvinit = ""
	(OR)
	DISTRO_FEATURES:remove = " systemd"
	VIRTUAL-RUNTIME_init_manager = "sysvinit"
6. sudo apparmor_parser -R /etc/apparmor.d/unprivileged_userns
7. bitbake core-image-minimal -c cleanall
8. bitbake core-image-minimal

----------------------Flash Image on Memory Card-----------------------------

1. lsblk; sudo umount /dev/mmcblk0; sudo mkfs.vfat -F 32 /dev/mmcblk0;
2. bzip2 -d -f core-image-minimal-raspberrypi5.rootfs.wic.bz2
3. cd build/tmp/deploy/images/raspberrypi5/
4. sudo dd if=core-image-minimal-raspberrypi5.rootfs.wic of=/dev/mmcblk0 status=progress bs=4M


----------------------Debug Probe Serial Communication----------------------------

1. minicom -b 115200 -o -D /dev/ttyACM0
