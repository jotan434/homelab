# 1 – Installation

Proxmox VE is installed directly on the hardware ("bare metal"). It replaces any existing
operating system on the target disk.

## Step 1 – Create a bootable USB stick

1. Download the Proxmox VE ISO from the [official download page](https://www.proxmox.com/en/downloads).
2. Write it to a USB stick with **Rufus** (Windows) or **balenaEtcher** (Windows, Linux, macOS).
   This erases the stick.

## Step 2 – Boot from the stick

Plug the stick into the mini PC, open the boot menu while it starts (on HP machines usually
`F9`) and choose the USB stick. My machine boots in **UEFI** mode.

## Step 3 – The installer

The graphical installer asks for a few things. These are the decisions that matter:

| Screen                 | What it asks                         | What to think about                                       |
|------------------------|--------------------------------------|-----------------------------------------------------------|
| Target harddisk        | Which disk to install on             | The whole disk gets erased. I used the default layout (LVM) |
| Location and time zone | Country, time zone, keyboard         | Keyboard layout matters for the root password             |
| Password and email     | Password for `root`, admin email     | Use a strong password, it controls the whole server       |
| Management network     | Network port, hostname, IP, gateway, DNS | **Static IP** from the server range of my [address scheme](../network/01-address-scheme.md). The hostname must be a full name like `proxmox.home.arpa` |

After the installation, remove the stick and reboot.

## Step 4 – First login

Open `https://<proxmox-ip>:8006` in a browser.

| What you see | Why |
|--------------|-----|
| Browser warning about the certificate | Proxmox uses a self-signed certificate. Expected in a homelab, accept it once |
| Login: user `root`, realm **Linux PAM** | The root account of the Proxmox host itself |
| Popup *"No valid subscription"* after login | Normal without a paid subscription. Proxmox works fully anyway, just click OK |

## Step 5 – Fix the update repositories

A fresh install only has the **enterprise** update repository enabled, which needs a subscription.
Without changing that, updates fail. See [Updates & repositories](04-updates-and-repositories.md).
