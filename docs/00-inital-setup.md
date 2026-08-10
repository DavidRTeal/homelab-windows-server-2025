
## Host Environment 

**Hypervisor:** Proxmox VE 9.2.5 (bare metal, old gaming laptop repurposed as a server)

**Host hardware:**
```
root@pve:~# lshw -short 2>/dev/null | grep -Ei "processor|memory|disk|nvme|network|system|volume"

system         OMEN Laptop 15-ek0xxx (2A137UA#ABA)
/0/0                               memory         64KiB BIOS
/0/16                              memory         16GiB System Memory
/0/16/0                            memory         8GiB SODIMM DDR4 Synchronous 3200 MHz (0.3 ns)
/0/16/1                            memory         8GiB SODIMM DDR4 Synchronous 3200 MHz (0.3 ns)
/0/21                              memory         384KiB L1 cache
/0/22                              memory         1536KiB L2 cache
/0/23                              memory         12MiB L3 cache
/0/24                              processor      Intel(R) Core(TM) i7-10750H CPU @ 2.60GHz
/0/100                             bridge         10th Gen Core Processor Host Bridge/DRAM Registers
/0/100/14.2                        memory         RAM memory
/0/100/14.3        wlo1            network        Comet Lake PCH CNVi WiFi
/0/100/1b.4/0      /dev/nvme0      storage        CT1000P310SSD8
/0/100/1b.4/0/0    hwmon4          disk           NVMe disk
/0/100/1b.4/0/2    /dev/ng0n1      disk           NVMe disk
/0/100/1b.4/0/1    /dev/nvme0n1    disk           1TB NVMe disk
/0/100/1b.4/0/1/1  /dev/nvme0n1p1  volume         931GiB EXT4 volume
/0/100/1d/0        /dev/nvme1      storage        Samsung SSD 970 EVO Plus 500GB
/0/100/1d/0/0      hwmon3          disk           NVMe disk
/0/100/1d/0/2      /dev/ng1n1      disk           NVMe disk
/0/100/1d/0/1      /dev/nvme1n1    disk           500GB NVMe disk
/0/100/1d/0/1/1    /dev/nvme1n1p1  volume         1006KiB BIOS Boot partition
/0/100/1d/0/1/2    /dev/nvme1n1p2  volume         1023MiB Windows FAT volume
/0/100/1d/0/1/3    /dev/nvme1n1p3  volume         464GiB LVM Physical Volume
/0/100/1d.5/0      nic0            network        RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet C
```

**Networking:** Host uses `vmbr0` as the primary Linux bridge, connecting the physical NIC to all VM virtual NICs, acting as a virtual switch so VMs appear as normal devices on the LAN.

**LAN details**:
```
root@pve:~# hostname -I
192.168.88.9 100.89.19.101 fd7a:115c:a1e0::8301:13bf 
```
 - Subnet: `192.168.88.0/24` 
- Gateway: `192.168.88.1` 
- Router DHCP pool: `192.168.88.2 – 192.168.88.249`

--- 