## Title and Scenario

This lab focuses on advanced storage management and device handling on Azure-based Linux virtual machines. You will work with Logical Volume Manager (LVM) and NVMe-backed disks, performing controlled modifications to volume groups and logical volumes while preserving filesystem integrity and operational consistency.

The exercises are designed to simulate real-world storage expansion, reduction, device mapping inconsistencies, and cloud-specific disk behaviors. You must approach each task methodically, validating state before and after every operation.

The goal is to strengthen your understanding of:

- LVM volume group and logical volume lifecycle management
- Filesystem integrity considerations during resize operations
- Azure disk device naming and symlink behavior
- NVMe controller enumeration and udev-based device mapping
- Troubleshooting storage layer inconsistencies without data loss

## Deployment

Storage / LVM

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fstorage001.json)

Storage / NVME

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fstorage002.json)

## Skills Required

- Strong understanding of LVM concepts (PV, VG, LV)
- Filesystem resize behavior (extend vs reduce considerations)
- Comfort inspecting block devices and device-mapper output
- Familiarity with Azure disk presentation models
- Understanding of NVMe devices and controller enumeration
- Awareness of udev rule mechanics and persistent device naming

## Recommended Prerequisites

- Experience working with `lvs`, `vgs`, `pvs`, and `lsblk`
- Understanding of safe filesystem resizing principles
- Familiarity with Azure LUN assignment concepts
- Basic understanding of udev rules and device symlink creation

## Objectives

By completing these exercises, you should be able to:

- Safely extend and reduce logical volumes
- Differentiate between resizing a logical volume and resizing a filesystem
- Identify when additional physical volumes are required
- Diagnose device mapping inconsistencies in Azure NVMe environments
- Restore expected Azure disk symlink behavior using udev rules
- Maintain filesystem integrity throughout all storage operations

## Environment Overview

Two separate Azure-based environments are provided:

1. LVM-based VM with multiple volume groups and logical volumes.
2. NVMe-based VM where disks are presented as NVMe devices instead of traditional SCSI.

Both environments simulate common production storage challenges.

## Your Mission

Work through each exercise sequentially.

For LVM:
- Extend, reduce, and validate logical volumes and filesystems.
- Introduce additional disks and integrate them into volume groups.
- Identify and resolve issues related to volume group capacity and filesystem resizing.

For NVMe:
- Inspect device mappings and controllers.
- Identify missing Azure symlinks.
- Apply the appropriate udev rule to restore expected behavior.
- Validate device mapping after reboot.

Perform all steps carefully and validate after each operation.

## Analytical Guidance

When working with LVM:

- Always inspect current layout before modifying it.
- Distinguish between resizing the logical volume and resizing the filesystem.
- Never assume filesystem resizing occurs automatically.
- When reductions are required, ensure filesystem safety before shrinking logical volumes.
- Validate mount points and data consistency after each modification.

When working with NVMe:

- Compare `lsblk` output with Azure symlink paths.
- Confirm NVMe controllers using `nvme list`.
- Understand how Azure device symlinks are generated.
- Apply udev rules carefully and validate after reboot.

## Validation Criteria

LVM Exercise Validation:

- Logical volumes reflect the requested size changes.
- Volume groups properly include added physical volumes.
- Filesystems are consistent and mounted correctly.
- No data loss occurs during extend or reduce operations.
- All mount points (e.g., `/opt/data01`) are accessible and consistent after resizing.

NVMe Exercise Validation:

- NVMe controllers are properly enumerated.
- Azure symlink paths exist under `/dev/disk/azure` after applying udev rules.
- Device mappings persist correctly across reboots.

## Documentation Expectations

Your documentation should include:

- Initial layout inspection output
- Explanation of capacity calculations before resizing
- Observed errors and their interpretation
- High-level explanation of how the issue was resolved
- Validation outputs confirming successful operations
- Observations about Azure disk presentation differences

## What Not To Do

- Do not resize filesystems blindly.
- Do not assume automatic filesystem expansion.
- Do not reduce logical volumes without validating filesystem requirements.
- Do not ignore errors when extending volume groups.
- Do not assume Azure symlinks exist automatically in NVMe scenarios.

## Real-World Context

In production environments:

- Storage expansion is routine but must be performed safely.
- Logical volume reduction is high-risk and requires careful validation.
- Cloud providers may present disks differently depending on instance type.
- Persistent device naming is critical for stable automation.
- NVMe-based disks can disrupt legacy assumptions about device paths.

Understanding these behaviors prevents outages and data corruption.

## Optional Advanced Exploration

- Compare LVM behavior across XFS and ext4 filesystems.
- Investigate how Azure maps LUN IDs to NVMe devices.
- Analyze how udev rules generate `/dev/disk/azure` symlinks.
- Create a repeatable storage validation checklist for production use.

---

# Storage
## Storage / LVM
  Deploy this VM. This will automatically create a VM and set an initial LVM/filesystem structure.
   
  Deployment: See the Deployment section.
  
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
  - Extend ```/dev/vgdata03/lvdata02``` to use all available space on the volume group ```vgdata02``` as well the filesystem.
  - Add ```/dev/disk/azure/scsi1/lun4``` to ```/dev/vgdata02``` and extend all free space on ```/dev/vgdata02/lvdata02```.
    > Got any error? What you need to solve this?
  - Extend ```/dev/vgdata02/lvdata03``` to use all available space on the volume group ```vgdata03``` as well the filesystem.
  - Create and attach **two** new 4GB disks to be used with ```vgdata03```, assign IDs 8 and 9.
    > Another problem? How can you solve this?
  - Add the new LUNs 8 and 9 to ```vgdata03``` and extend  ```/dev/vgdata03/lvdata03```.

## Storage / NVME
  Deploy this VM. This will automatically create a VM which uses NVME for all disks.
   
  Deployment: See the Deployment section.

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
  > You may found the previous symlinks and files are missing
- For this purpose we are going to install a new udev rule available [here](https://raw.githubusercontent.com/Azure/azure-vm-utils/refs/heads/main/udev/80-azure-disk.rules)
  ```bash
  wget https://raw.githubusercontent.com/Azure/azure-vm-utils/refs/heads/main/udev/80-azure-disk.rules -O /etc/udev/rules.d/80-azure-disk.rules
  ```
- Reboot and check again ```/dev/disk/azure```
