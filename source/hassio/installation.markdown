---
title: "Installing Home Assistant"
description: "Instructions on how to install Home Assistant."
---

The following will take you through the steps required to install Home Assistant.

1. Download the appropriate install option:

   - As an image for your device:

     - [Raspberry Pi 3 Model B and B+ 32bit][pi3-32] (recommended)
     - [Raspberry Pi 3 Model B and B+ 64bit][pi3-64]
     - [Raspberry Pi 4 Model B 32bit][pi4-32] (recommended)
     - [Raspberry Pi 4 Model B 64bit][pi4-64]
     - [Tinkerboard][tinker]
     - [Odroid-C2][odroid-c2]
     - [Odroid-N2 (Beta)][odroid-n2]
     - [Odroid-XU4][odroid-xu4]
     - [Intel-Nuc][intel-nuc]

   - As a virtual appliance:
  
     - [VMDK][vmdk] (VMWare Workstation)
     - [VHDX][vhdx]
     - [VDI][vdi]
     - [OVA][Virtual Appliance] (not available at this time!)

   - Not recommended Hardware:

     - [Raspberry Pi][pi1]
     - [Raspberry Pi Zero-W][pi0-w]
     - [Raspberry Pi 2][pi2]

2. Install Home Assistant:

   - Flash the downloaded image to an SD card using [balenaEtcher][balenaEtcher]. If using a Pi, we recommend at least a 32 GB SD card to avoid running out of space. On Virtual machine platforms, provide at least 32 GB of disk space for the VM.
   - Load the appliance image into your virtual machine software. Choose 64-bit Linux and UEFI boot.

3. Optional - set up the Wi-Fi or static IP. There are two possible places for that:
   - on a blank USB stick with a FAT32 partition having partition label `CONFIG`, while in its root directory, create the `network/my-network` file, or
   - on the Home Assistant SD card's first, bootable partition (labeled `hassio-boot`, might not be auto mounted in Linux) create the `CONFIG/network/my-network` file.

   For the content of this file, follow the [Home Assistant Operating System howto][hassos-network].

4. For image-based installs insert the SD card (and optional USB stick) into the device.

5. Turn on your device or virtual appliance. On first boot, it downloads the latest version of Home Assistant which takes around 20 minutes (slower/faster depending on the platform and your Internet connection).

   <img src='/images/hassio/screenshots/first-start.png' style='clear: right; border:none; box-shadow: none; float: right; margin-bottom: 12px;' width='150' />

6. You will be able to reach your installation at `http://homeassistant.local:8123` (if your router supports mDNS, otherwise see below).

7. It is important to provide yourself proper access, including the Home Assistant CLI tools. Both the [Samba add-on][samba] and the [SSH add-on][ssh] should be the first add-ons you should install, before making changes to the configuration in the `/config/` folder. From the UI choose **Supervisor**, which is located in the sidebar and then the add-on store.

<div class='note warning'>

We used `hassio.local` in the past, if you have a system that is installed before this naming change, you might need to use `hassio.local` instead of `homeassistant.local`.

</div>

<div class='note'>

If your router doesn't support mDNS, then you'll have to use the IP address of your Pi instead of `homeassistant.local`. For example, `http://192.168.0.9:8123`. You should be able to find the IP address of your Pi from the admin interface of your router.

</div>

<div class='note warning'>

If you are using a Raspberry Pi please remember to ensure you're using an [appropriate power supply][pi-power] with your Pi. Mobile chargers may not be suitable since some were only designed to provide just enough power to the device it was designed for by the manufacturer. **Do not** try to power the Pi from the USB port on a TV, computer, or similar.

</div>

Now you can [configure][configure] your install.

## Updating a Home Assistant installation

Best practice for updating a Home Assistant installation:

1. Backup your installation, using the snapshot functionality Home Assistant offers.
2. Check the release notes for breaking changes on [Home Assistant release notes](https://github.com/home-assistant/home-assistant/releases). Be sure to check all release notes between the version you are running and the one you are upgrading to. Use the search function in your browser (`CTRL + f`) and search for **Breaking Changes**.
3. Check your configuration using the [Check Home Assistant configuration](/addons/check_config/) add-on.
4. If the check passes, you can safely update. If not, update your configuration accordingly.
5. Select _Dashboard_ from the _Supervisor_ menu, and then select _Update_.

## Run a specific version on Home Assistant

SSH to your Home Assistant system, or connect to the console, and run:

```bash
ha core update --version=0.XX.X
```

## Run the beta version on Home Assistant

If you would like to test next release before anyone else, you can install the beta version released every three weeks:

1. Backup your installation, using the snapshot functionality Home Assistant offers.
2. Check the [Home Assistant Beta release notes](https://rc.home-assistant.io/latest-release-notes/) for breaking changes. Be sure to check all release notes between the version you are running and the one you are upgrading to. Use the search function in your browser (`CTRL + f`) and search for **Breaking Changes**.
3. Select _System_ tab from the _Supervisor_ menu, then select _Join Beta Channel_ under _Supervisor_, then select _Reload_.
4. Select _Dashboard_ tab from the _Supervisor_ menu, and then select _Update_.

## Alternative: install Home Assistant Supervised on a generic Linux host

You can also install Home Assistant on a Linux operating system of choice, called Home Assistant Supervised.

Home Assistant Supervised, will still give you access to most features Home Assistant has to offer, including add-ons.

The packages you need to have available on your system needed to Home Assistant may vary.

### Debian/Ubuntu

- `apparmor-utils`
- `apt-transport-https`
- `avahi-daemon`
- `ca-certificates`
- `curl`
- `dbus`
- `jq`
- `socat`
- `software-properties-common`

Optional:

- `network-manager`

<div class='note warning'>

   Without the NetworkManager, you will be not able to control your host network setup over the UI. The `modemmanager` package will interfere with any Z-Wave or Zigbee stick and should be removed or disabled. Failure to do so will result in random failures of those integrations. For example, you can disable with `sudo systemctl disable ModemManager` and remove with `sudo apt-get purge modemmanager`

</div>

### Arch Linux

- `apparmor`
- `avahi`
- `ca-certificates`
- `curl`
- `dbus`
- `docker`
- `jq`
- `socat`

You also need to have Docker-CE installed. There are well-documented procedures for installing Docker on Ubuntu at [Docker.com](https://docs.docker.com/install/linux/docker-ce/ubuntu/), you can find installation steps for your Linux distribution in the menu on the left.

<div class='note warning'>

  Some distributions, like Ubuntu, have a `docker.io` package available. Using that package will cause issues!
  Be sure to install the official Docker-CE from the above-listed URL.
  
  Docker is not always ready with a release when a new Ubuntu version is out. Check if your version of Ubuntu is supported by Docker [here](https://docs.docker.com/install/linux/docker-ce/ubuntu/).

</div>

### Preparation

To prepare your machine for the Home Assistant installation, run the following commands:

For Ubuntu:

```bash
add-apt-repository universe
```

Debian/Ubuntu:

```bash
sudo -i
apt-get update
apt-get install -y software-properties-common apparmor-utils apt-transport-https avahi-daemon ca-certificates curl dbus jq network-manager socat
systemctl disable ModemManager
curl -fsSL get.docker.com | sh
```

The following script will then install Home Assistant on a variety of operating systems and machine types.

```bash
curl -sL "https://raw.githubusercontent.com/home-assistant/hassio-installer/master/hassio_install.sh" | bash -s
```

Some installation types require flags to identify the computer type, for example, when using a Raspberry Pi 3, the flag `-- -m raspberrypi3` is required. The install script would then look like this:

```bash
curl -sL "https://raw.githubusercontent.com/home-assistant/hassio-installer/master/hassio_install.sh" | bash -s -- -m raspberrypi3
```

#### Other machine types

- `intel-nuc`
- `raspberrypi`
- `raspberrypi2`
- `raspberrypi3`
- `raspberrypi3-64`
- `raspberrypi4`
- `raspberrypi4-64`
- `odroid-c2`
- `odroid-cu2`
- `odroid-xu`
- `tinker`
- `qemuarm`
- `qemuarm-64`
- `qemux86`
- `qemux86-64`

See the [hassio-installer](https://github.com/home-assistant/hassio-installer) GitHub page for an up-to-date listing of supported machine types.

If you can not find your machine type in the list, you should pick the `qemu` release. i.e., `qemux86-64` for a normal 64-bit Linux distribution, or `qemuarm-64` for most modern ARM-based target like Raspberry Pi clones, or TV boxes.

<div class='note'>
When you use this installation method, the core SSH add-on may not function correctly. If that happens, use the community SSH add-on. Some of the documentation might not work for your installation either.
</div>

A detailed guide about running Home Assistant as a virtual machine is available in the [blog][hassio-vm].

[balenaEtcher]: https://www.balena.io/etcher
[Virtual Appliance]: https://github.com/home-assistant/operating-system/blob/dev/Documentation/boards/ova.md
[hassos-network]: https://github.com/home-assistant/operating-system/blob/dev/Documentation/network.md
[pi0-w]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_rpi0-w-3.11.img.gz
[pi1]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_rpi-3.11.img.gz
[pi2]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_rpi2-3.11.img.gz
[pi3-32]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_rpi3-3.11.img.gz
[pi3-64]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_rpi3-64-3.11.img.gz
[pi4-32]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_rpi4-3.11.img.gz
[pi4-64]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_rpi4-64-3.11.img.gz
[tinker]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_tinker-3.11.img.gz
[odroid-c2]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_odroid-c2-3.11.img.gz
[odroid-n2]: https://github.com/home-assistant/operating-system/releases/download/4.3/hassos_odroid-n2-4.3.img.gz
[odroid-xu4]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_odroid-xu4-3.11.img.gz
[intel-nuc]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_intel-nuc-3.11.img.gz
[vmdk]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_ova-3.11.vmdk.gz
[vhdx]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_ova-3.11.vhdx.gz
[vdi]: https://github.com/home-assistant/operating-system/releases/download/3.11/hassos_ova-3.11.vdi.gz
[linux]: https://github.com/home-assistant/hassio-installer
[local]: http://homeassistant.local:8123
[samba]: /addons/samba/
[ssh]: /addons/ssh/
[pi-power]: https://www.raspberrypi.org/help/faqs/#powerReqs
[hassio-vm]: /blog/2017/11/29/hassio-virtual-machine/
[configure]: /getting-started/configuration/

## **Notes for Installing VMDK image on ESXi**

The VMDK files provided are created for VMWare Workstation, these are a different file format than used by ESXi.  Attempting to use them in an ESXi VM will result in an unhelpful error message. ESXi has tools to convert the VMDK to the correct format but unless you have vSphere not though the UI.

To convert the VMDK to the correct format you need to SHH into your ESXi server;

 1. Upload the current VMDK file to you ESXi server data store
 2. From the web interface right click on host, and under "Services" "Enable Secure Shell(SSH)", remember to disable SSH when you have finished using it.
 3. Log into your sever using your preferred SSH client, the ESXi web client provides a web based SSH client from the same "host" menu
 4. Run the vsmkfstool command with -i option for clone disk, in the form "vmkfstools -i path to *.vmdk file path to output file I.E.
> vmkfstools -i /vmfs/volumes/5e10f33f-######-####-002590a7cb74/HASS/hassos_ova-3.11.vmdk
  /vmfs/volumes/5e10f33f-#######-####-002590a7cb74/HASS/hassos-output.vmdk
 5. Create a new VM and replace the default disk with the output *.vmdk
 6. On your newly created VM disable secure boot. "Edit Settings" - "VM Options"- "Boot Options" un-tick "Enable UEFI secure boot"

