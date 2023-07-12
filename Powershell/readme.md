# Creating an Azure VM using Powershell

### I tried deploying an Azure VM using Powershell. It failed in the last step due to the incorrect version of the OS Image. I intend to work on this and successfuly create a VM this way.
---

```
$location = "Eastus2"

$myResourceGroup = "PurpleTeamRG"
$vmName=PurpleTeamVM
New-AzureRmResourceGroup -Name PurpleTeamRG -Location Eastus2
$newSubnetParams = @{
    'Name'          = 'MySubnet'
    'AddressPrefix' = '10.0.1.0/24'
}
$subnet = New-AzVirtualNetworkSubnetConfig @newSubnetParams

$newVNetParams = @{
    'Name'              = 'MyNetwork'
    'ResourceGroupName' = PurpleTeamRG
    'Location'          = Eastus2
    'AddressPrefix'     = '10.0.0.0/16'
}
$vNet = New-AzVirtualNetwork @newVNetParams -Subnet $subnet


$newVNetParams = @{ Name = 'MyNetwork' ; ResourceGroupName = PurpleTeamRG ; Location = Eastus2 ; AddressPrefix = '10.0.0.0/16' }
$vNet = New-AzVirtualNetwork @newVNetParams -Subnet $subnet

$newStorageAcctParams = @{Name = 'purpleteamstorage' ; ResourceGroupName = 'PurpleTeamRG' ; Type = 'parameters('osDiskType')'; Location = Eastus2
}
$storageAccount = New-AzStorageAccount @newStorageAcctParams

$newPublicIpParams = @{Name = 'MyNIC' ; ResourceGroupName = 'PurpleTeamRG' ; AllocationMethod  = 'Dynamic' ; DomainNameLabel   = 'test-domain' ; Location = 'Eastus2' }

$publicIp = New-AzPublicIpAddress @newPublicIpParams

$newVNicParams = @{ Name = 'MyNic'; ResourceGroupName = 'PurpleTeamRG' ; Location = 'Eastus2'}
$vNic = New-AzNetworkInterface @newVNicParams -SubnetId $vNet.Subnets[0].Id -PublicIpAddressId $publicIp.Id

$newConfigParams = @{'VMName' = PurpleTeamVM; 'VMSize' = 'Standard_B2ms'}
$vmConfig = New-AzVMConfig @newConfigParams

$newVmOsParams = @{'Windows'= $true ; 'ComputerName' = PurpleTeamVM ;'Credential' = (Get-Credential -Message 'Type the name and password of the local administrator account.');'ProvisionVMAgent' = $true; 'EnableAutoUpdate' = $true}
$vm = Set-AzVMOperatingSystem @newVmOsParams -VM $vmConfig

$newSourceImageParams = @{PublisherName = 'MicrosoftWindowsServer'; Version = 'latest' ;Skus = '2019-Datacenter'}

$vm = Set-AzVMSourceImage @newSourceImageParams -VM $vm -Offer 'WindowsServer'
$vm = Add-AzVMNetworkInterface -VM $vm -Id $vNic.Id
$vm = Add-AzVMNetworkInterface -VM $vm -Id $vNic.Id

$osDiskName = 'myDisk'
$osDiskUri = $storageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + 'PurpleTeamVM' + 'myDisk' + ".vhd"

$newOsDiskParams = @{Name = 'OSDisk'; CreateOption = 'fromImage'}

$vm = Set-AzVMOSDisk @newOsDiskParams -VM $vm -VhdUri $osDiskUri

New-AzVM -VM $vm -ResourceGroupName PurpleTeamRG -Location Eastus2 

```

![](https://github.com/AncyThomas-dev/WilldanInterview/tree/main/Powershell/Images)












