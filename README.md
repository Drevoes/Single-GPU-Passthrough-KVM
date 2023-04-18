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

## Configure Virt manager
`git clone https://github.com/BigAnteater/KVM-GPU-Passthrough/ && cd KVM-GPU-Passthrough`
`chmod +x libvirt_configuration.sh'
`sudo ./libvirt_configuration.sh'
