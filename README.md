# Mentorship exercises

## Storage / LVM
  Deploy this VM. This will automatically create a VM and set an initial LVM/filesystem structure.
   
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fstorage001.json)
  
  For this exercise, it's recommended to use these commands: 
  ```bash
  lvs -o +devices
  ls -l /dev/disk/azure/scsi1
  ```
  - Extend ```/dev/vgdata01/lvdata01``` to use all available space on the volume group ```vgdata01```.
  - Extend ```/dev/vgdata01/lvdata01```, this time adding ```/dev/disk/azure/scsi1/lun1``` to ```vgdata01```.
    > **Don't resize the filesystem yet!**
  - Reduce ```/dev/vgdata01/lvdata01``` by 2GB of the total space assigned.
  - Check the filesystem ```/opt/data01```
  - Extend ```/dev/vgdata01/lvdata01``` to use all available space on the volume group ```vgdata01```. This time extend the filesystem as well.
  - Reduce ```/dev/vgdata01/lvdata01``` to 3GB.
  - Check the filesystem ```/opt/data01```.
  - Extend ```/dev/vgdata02/lvdata02``` to use all available space on the volume group ```vgdata02``` as well the filesystem.
  - Add ```/dev/disk/azure/scsi1/lun4``` to ```/dev/vgdata02``` and extend all free space on ```/dev/vgdata02/lvdata02```.
    > Got any error? What you need to solve this?
  - Extend ```/dev/vgdata02/lvdata03``` to use all available space on the volume group ```vgdata03``` as well the filesystem.
  - Create and attach **two** new 4GB disks to be used with ```vgdata03```, assign IDs 8 and 9.
    > Another problem? How can you solve this?
  - Add the new LUNs 8 and 9 to ```vgdata03``` and extend  ```/dev/vgdata02/lvdata03```.

## Storage / LVM
  Deploy this VM. This will automatically create a VM and set an initial LVM/filesystem structure.
   
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fstorage002.json)

  For this exercise, it's recommended to use these commands: 
  ```bash
  lsblk  -f
  nvme list
  ls -l /dev/disk/azure/scsi1
  ls -l /dev/disk/azure
  ```
- List the block device using ```lsblk -f```
- List all nvme controllers using ```nvme list```
- List the files and symlinks on ```/dev/disk/azure```
  > You may found the previous symlinks are missing
- For this purpose we are going to install a new udev rule available [here](https://raw.githubusercontent.com/Azure/azure-vm-utils/refs/heads/main/udev/80-azure-disk.rules)
  ```bash
  wget https://raw.githubusercontent.com/Azure/azure-vm-utils/refs/heads/main/udev/80-azure-disk.rules -O /etc/udev/rules.d/80-azure-disk.rules
  ```
