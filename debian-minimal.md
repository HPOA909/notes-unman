---
layout: doc
title: Debian Minimal Template
permalink: /doc/templates/debian-minimal/
---

Debian - minimal
================

The template only weighs about 200 MB compressed (0.75 GB on disk) and has only the most vital packages installed, including a minimal X and xterm installation.
The minimal template, however, can be easily extended to fit your requirements. The sections below contain the instructions on duplicating the template and provide some examples for commonly desired use cases.

Installation
------------

The Debian minimal template can be installed with the following command:

~~~
[user@dom0 ~]$ sudo qubes-dom0-update qubes-template-debian-9-minimal
~~~

The download may take a while depending on your connection speed.

Duplication and first steps
---------------------------

It is highly recommended that you clone the original template, and make any changes in the clone instead of the original template. The following command clones the template. Replace `your-new-clone` with your desired name.

~~~
[user@dom0 ~]$ qvm-clone debian-9-minimal your-new-clone
~~~

You must start the template in order to customize it.

Customization
-------------

Customizing the template for specific use cases normally only requires installing additional packages.
The following table provides an overview of which packages are needed for which purpose.

As you would expect, the required packages should be installed in the running template with the following command. (Replace "packages` with a space-delimited list of packages to be installed.)

~~~
[user@your-new-clone ~]$ sudo apt install packages
~~~
### Package table for Qubes 4.0

Use case | Description | Required steps
--- | --- | ---
**Standard utilities** | If you need the commonly used utilities | Install the following packages: `pciutils` `vim-minimal` `less` `psmisc` `gnome-keyring`
**Networking** | If you want networking | Install qubes-core-agent-networking
**Audio** | If you want sound from your VM... | Install `pulseaudio-qubes`
CHECK**FirewallVM** | You can use the minimal template as a [FirewallVM](/doc/firewall/), such as the basis template for `sys-firewall` | Install at least `qubes-core-agent-networking`, and also `qubes-core-agent-dom0-updates` if you want to use it as the updatevm (which is normally sys-firewall).
**NetVM** | You can use this template as the basis for a NetVM such as `sys-net` | Install the following packages:  `qubes-core-agent-networking` `qubes-core-agent-network-manager`
**NetVM (extra firmware)** | If your network devices need extra packages for the template to work as a network VM | Use the `lspci` command to identify the devices, then run `apt search firmware` (replace `firmware` with the appropriate device identifier) to find the needed packages and then install them.
**Network utilities** | If you need utilities for debugging and analyzing network connections | Install the following packages: `tcpdump` `telnet` `nmap` `nmap-ncat`
CHECK**USB** | If you want USB input forwarding to use this template as the basis for a [USB](/doc/usb/) qube such as `sys-usb` | Install `qubes-input-proxy-sender`
**VPN** | You can use this template as basis for a [VPN](/doc/vpn/) qube | Use the `apt search "NetworkManager VPN plugin"` command to look up the VPN packages you need, based on the VPN technology you'll be using, and install them. Some GNOME related packages may be needed as well. After creation of a machine based on this template, follow the [VPN howto](/doc/vpn/#set-up-a-proxyvm-as-a-vpn-gateway-using-networkmanager) to configure it.
 
CHECKA comprehensive guide to customizing the minimal template is available [here][GUIDE]


Qubes 4.0
---------

In Qubes R4.0 the minimal template is not configured for passwordless root.  To update or install packages to it, from a dom0 terminal window run:

~~~
[user@dom0 ~]$ qvm-run -u root debian-9-minimal xterm
~~~
to open a root terminal in the template, from which you can use apt tools without sudo. You will have to do this every time you want root access if you choose not to enable passwordless root. 

If you want the usual qubes `sudo ...` commands, open the root terminal using the above command, and in the root xterm window enter

~~~
bash-4.4# apt install qubes-core-agent-passwordless-root polkit
~~~

Optionally check this worked: from the gui open the minimal template's xterm and give the command

~~~
[user@debian-9-minimal ~]$ sudo -l
~~~

which should give you output that includes the NOPASSWD keyword.


In Qubes 4.0, additional packages from the `qubes-core-agent` suite may be needed to make the customized minimal template work properly. These packages are:

- `qubes-core-agent-qrexec`: Qubes qrexec agent. Installed by default.
- `qubes-core-agent-systemd`: Qubes unit files for SystemD init style. Installed by default.
- `qubes-core-agent-passwordless-root`, `polkit`: By default the 'fedora-27-minimal' template doesn't have passwordless root. These two packages enable this feature. (Note from R4.0 a design choice was made that passwordless should be optional, so is left out of the minimal templates)
- `qubes-core-agent-nautilus`: This package provides integration with the Nautilus file manager (without it things like "copy to VM/open in disposable VM" will not be shown in Nautilus).
- `qubes-core-agent-sysvinit`: Qubes unit files for SysV init style or upstart.
- `qubes-core-agent-networking`: Networking support. Required for general network access and particularly if the template is to be used for a `sys-net` or `sys-firewall` VM.
- `qubes-core-agent-network-manager`: Integration for NetworkManager. Useful if the template is to be used for a `sys-net` VM.
- `network-manager-applet`: Useful (together with `dejavu-sans-fonts` and `notification-daemon`) to have a system tray icon if the template is to be used for a `sys-net` VM.
- `qubes-core-agent-dom0-updates`: Script required to handle `dom0` updates. Any template which the VM responsible for 'dom0' updates (e.g. `sys-firewall`) is based on must contain this package.
- `qubes-usb-proxy`: Required if the template is to be used for a USB qube (`sys-usb`) or for any destination qube to which USB devices are to be attached (e.g `sys-net` if using USB network adapter).
- `pulseaudio-qubes`: Needed to have audio on the template VM.

