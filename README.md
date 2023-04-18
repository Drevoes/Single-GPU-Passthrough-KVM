# Single-GPU-Passthrough-KVM
dont follow this guide. its for me. If your specs are like mine then do it idgaf

## Credits:
BigAntEater

RisingPrism

## Get Windows ISO:
go to https://www.microsoft.com/en-us/software-download/windows10ISO and download the latest ISO (DO NOT DOWNLOAD WINDOWS 11)

## Enable virtualization in BIOS
ENABLE: Vt-d and Intel VMX

## Enable IOMMU in bootloader
`sudo nano /boot/loader/entries/arch.conf`

add `intel_iommu=on` and `iommu=pt` at the end of options

## Install virt manager
`sudo pacman -S virt-manager qemu vde2 ebtables iptables-nft nftables dnsmasq bridge-utils ovmf`

if asked to uninstall IPTables, select YES

## Edit virt manager
`sudo nano /etc/libvirt/libvirtd.conf`

uncomment `unix_sock_group = "libvirt"` and `unix_sock_rw_perms = "0770"`

put `log_filters="3:qemu 1:libvirt"` and `log_outputs="2:file:/var/log/libvirt/libvirtd.log"` at the end of the config.

assign yourself to the libvirt group - `sudo usermod -a -G kvm,libvirt $(whoami)`

enable and start the libvirtd service - `sudo systemctl enable libvirtd` and `sudo systemctl start libvirtd`

`sudo nano /etc/libvirt/qemu.conf`

uncomment `user = "root"` and `group = "root"` and replace them with your username.

restart the libvirt daemon `sudo systemctl restart libvirtd`

