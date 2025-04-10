```powershell

#REM v0.5
# i-083aa130eb307785c

#input-from-commandline #instanceid #kmskeyid
param(
     [Parameter(Mandatory=$true)]
     [string]$ec2id,

	 [Parameter(Mandatory=$true)]
     [string]$kmsid
	 )

#import-modules
Import-Module -name AWSPowerShell

#set-default-AWS-region
Set-DefaultAWSRegion eu-central-1

try {
		$instance = Get-EC2Instance -InstanceId $ec2id -ErrorAction stop
		$kmskeystatus = Get-kmskey -KeyId $kmsid
	} 
catch {
		Write-Host "Instance does not exists or CMK is not present/enabled. Enter correct Instance ID. Check CMK key."
		break
	}

#general-variables
$instanceid = $instance.instances.instanceid
$instanceip = $instance.instances.privateipaddress
$instance_state = (Get-EC2InstanceStatus -InstanceId $instanceid -IncludeAllInstance 1).InstanceState.name.value
$instance_name = $instance.Instances.tags | Where-Object {$_.key -eq "Name"}
$hdd = $instance.instances.blockdevicemappings
$volumeid = $hdd.ebs.volumeid

if ($instance_state -eq "Running" -or $instance_state -eq "Pending" -or $instance_state -eq "Stopping") 
{

Write-Host ""	
Write-host "Instance ID:" $instanceid
Write-host "Instance IP:" $instanceip
Write-host "Instance Name:" $instance_name.value
Write-Host ""
Write-Host "Instance is running...stopping the instance."

#stop-the-instance
stop-EC2Instance -InstanceId $instanceid | Out-Null

While($instance_state -ne 'Stopped')
        {
            $instance_state = (Get-EC2InstanceStatus -InstanceId $instanceid -IncludeAllInstance 1).InstanceState.name.value
			Write-Host "Current status:" $instance_state	
            Start-Sleep -s 15
        }
Write-Host "Instance has been stopped."
}
else {	
	Write-Host ""
	Write-host "Instance Name:" $instance_name.value
	Write-host "Instance:" $instanceid "- already stopped."
    Start-Sleep -s 1	
	}
	
#create-snapshots-from-the-volume
foreach ($v in $volumeid) {
    $mnt = (Get-EC2Volume -VolumeId $v).attachment.device
	$az = (Get-EC2Volume -VolumeId $v).AvailabilityZone
	$size = (Get-EC2Volume -VolumeId $v).size
	$voltype = (Get-EC2Volume -VolumeId $v).volumetype
	$voltags = (Get-EC2Volume -VolumeId $v).tags
	Write-Host ""
	Write-Host "Volume ID: $v" - "Mount point: $mnt" - "Availability Zone: $az" - "Size: $size" - "Volume type: $voltype"
	Write-Host ""
	
	#log-info-to-file
	"Old Volume ID: $v - Mount point: $mnt - Size: $size - Volume type: $voltype" | Out-File .\temp\volumes-$instanceid.txt -Append
	#volume-umount
	Dismount-EC2Volume -InstanceId $instanceid -VolumeId $v | Out-Null
	Start-sleep -s 3
	
	#snapshot-create
	$snapshot = New-EC2Snapshot -VolumeId $v
	$snapshotid = $snapshot.snapshotid
	$snapshotstate = (Get-EC2Snapshot -SnapshotId $snapshotid).state.value
	Write-Host "New Snapshot ID: $snapshotid" 
	
	While($snapshotstate -ne 'Completed'){
       	$snapshotstate = (Get-EC2Snapshot -SnapshotId $snapshotid).state.value
		Write-Host "Progress:" (Get-EC2Snapshot -SnapshotId $snapshotid).progress
        Start-Sleep -s 30
    }	
	
	#volume-create
	$newvolume = New-EC2Volume -Size $size -AvailabilityZone $az -VolumeType $voltype -SnapshotId $snapshotid -Encrypted 1 -KmsKeyId $kmsid 
	Start-Sleep -s 3
	$newvolumeid = $newvolume.volumeid
	$newvolumestate = (Get-EC2Volume -VolumeId $newvolumeid).state.value
	$autoenabledio = (Get-EC2VolumeAttribute -VolumeId $v -Attribute autoEnableIO).AutoEnableIO
	#set-attributes-to-volume
	Edit-EC2VolumeAttribute -VolumeId $newvolumeid -AutoEnableIO $autoenabledio
	Write-Host ""
	Write-Host "New (CMK) Volume ID: $newvolumeid"
	
	While($newvolumestate -ne 'Available'){
       	$newvolumestate = (Get-EC2Volume -VolumeId $newvolumeid).state.value
		Write-host "Waiting to finish the process."
        Start-Sleep -s 15
    }
	
	#tag-new-volume
	Start-Sleep -s 1
	Write-Host ""
	Write-Host "Tagging new volume."
	if ($voltags -ne $null) {
		New-EC2Tag -Resource $newvolumeid -tag $voltags
	} 
	else {
			Write-Host "Volume does not have tags. Skipping."
	}
	#volume-mount
	Start-Sleep -s 1
	add-EC2Volume -InstanceId $instanceid -volumeid $newvolumeid -device $mnt | Out-Null
	Start-Sleep -s 1
	Write-Host "New volume attached to the instance."
	Write-Host "Done."	
}
Write-Host ""
Write-Host "Process for $instanceid finished sucessfully."
Start-Sleep -s 1
#start-the-instance
Write-Host ""
Write-Host "Starting the instance... $instanceid"
Start-Sleep -s 1
Start-EC2Instance -InstanceId $instanceid | Out-Null

```
He-he.

```terraform

#start coding

terraform {
  required_version = ">= 1.9.0"
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = ">=3.0.0"
    }
  }

  backend "local" {
    path = "mystate/terraform.tfstate"
  }
}

provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  location = "westeurope"
  name     = "terraform-newsg"
}



resource "random_string" "container_name" {
  length  = 25
  lower   = true
  upper   = false
  special = false
}

```
