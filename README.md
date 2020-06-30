# Just some of my PowerShell scripts

### hyperv_newvm.ps1

Using standalone Hyper-V in my homelab, this script will just duplicate my base image VHD file and attach to a new VM with specified CPU/Memory. 

* Takes 3 parameters for name, cpu count and memory
* You can hardcode the paths for your specific environment
  * Where VMs are deployed
  * Where your base image is stored
  * Network switch

```powershell
<#
usage example: ./hyperv_newvm.ps1 -Name Ansible -cpuCount 4 -memCount 8GB
will create a VM like Ansible-Ubuntu-8473 with 4 vCPU and 8GB of RAM
#>

param (
	[string]$name = "new",
	[int]$cpuCount = 2,
	[string]$memCount = 4GB
)

<# set environment specific locations & network #>

$hypervRootPath = "D:\Hyper-V\" #set the path for the VM and VHDx
$virtualSwitch = "VirtualSwitch2" #set the virtual switch
$baseVHD = "E:\Templates\Ubuntu-DockerSSH.vhdx" #set your prepped base VHD with OS

<# script execution #>

$vmSuffix = "-Ubuntu-" + (Get-Random 9999)

$newVMName = $name + $vmSuffix

echo "VM Name is $newVMName with $cpuCount vCPUs & $memCount of RAM"

$memInt = ($memCount / 1) #convert string to Int64

New-VM -Name $newVMName -MemoryStartupBytes $memInt -Path $hypervRootPath -Generation 1 -SwitchName $virtualSwitch #create VM

Set-VMProcessor $newVMName -Count $cpuCount #set vCPU

Copy-Item -Path $baseVHD -Destination $hypervRootPath$newVMName #copy VHDx File

Add-VMHardDiskDrive -VMName $newVMName -Path "D:\Hyper-V\$newVMName\Ubuntu-DockerSSH.vhdx" #add disk

Start-VM $newVMName
```

