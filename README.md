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

If you plan on using virtual network, to save time, run `sudo virsh net-autostart default` to have the virtual network service start on startup.

If you don't want to do this, run `sudo virsh net-start default` everytime you start a VM.

## Configure Virtual Machine
Create a new virtual machine, give it whatever name, whatever storage, tick the box that says `Customize configuration before install`

select your UEFI firmware to be `/usr/share/edk2-ovmf/x64/OVMF_CODE.fd`

Inside of boot options, tick the box that says `enable boot menu`

Begin the windows install. After you make it to the desktop, shutdown.

## Grab and customize ROM
Install hirens boot CD, and dump your rom using gpu-z.

Drag the .ROM into Hexed.it, search for VIDEO.

After you find VIDEO, move behind it until you find the first U. remove everything BEHIND the U.

## Place the ROM.

`sudo mkdir /usr/share/vgabios`

`cp [PathToROm] /usr/share/vgabios/`

`cd /usr/share/vgabios`

`sudo chmod -R 644 [NameOfRom].ROM`

`sudo chown yourusername:yourusername [NameOfRom].ROM`

## Install Hooks

This is an amazing script by RisingPrism

`git clone https://github.com/Drevoes/Single-GPU-Passthrough-KVM/`

`cd Single-GPU-Passthrough-KVM/`

`sudo chmod +x install_hooks.sh`

`sudo ./install_hooks.sh`

Your going to have to edit hooks now. `sudo nano /etc/libvirt/hooks/qemu`

on the If then line, at the end of [[ add `|| [[ $OBJECT == "YourVMName" ]]`

## Pass your GPU through
Press `add hardware` and inside of `PCI Host Device` pass all of your GPU related devices, most likely anything with `NVIDIA CORPORATION` at the start.

Pass through your USB device aswell.

Go to the XML tab of all your GPU devices and add `<rom file='/usr/share/vgabios/[nameofrom].rom'/>`  between the second source and address

## Remove Devices
Remove both USB directors

Sound ich9

Tablet

Serial 1

Channel spice

Remove TPM

## Remove stubborn devices
chances are, you won't be able to remove VideoQXL or Display Spice

Go to the XML tab in your overview, and remove these lines

```
 <graphics type="spice" autoport="yes">
      <listen type="address"/>
      <image compression="off"/>
    </graphics>
    <audio id="1" type="spice"/>
    <video>
      <model type="qxl" ram="65536" vram="65536" vgamem="16384" heads="1" primary="yes"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x01" function="0x0"/>
    </video>
```

## Modify XML further
Go to the <Features> Section of overview and add

``` 
  <features>
    <acpi/>
    <apic/>
    <hyperv>
      <relaxed state='on'/>
      <vapic state='on'/>
      <spinlocks state='on' retries='8191'/>
      <vendor_id state='on' value='123456789123'/>
    </hyperv>
    <kvm>
      <hidden state='on'/>
    </kvm>
    <vmport state='off'/>
    <ioapic driver='kvm'/>
    
 
    
```

  ## Modify topology
 
 go to CPU tab
 
 Tick manually set CPU topology
 
 set sockets to 1, cores to your cpu cores, threads to 1 if HP is OFF and 2 if HP is ON
 
 scroll down to <topology
                          
 add ` <feature policy='disable' name='smep'/>` underneath `<topology sockets='' dies='' cores='' threads=''/>`
 
 ## Finish
 Run your Virtual Machine.
                          
                          
                          
                          
                       
 


