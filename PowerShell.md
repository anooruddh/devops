# POWERSHELL

#Powershell Remoting

**If you want to manage Windows computers remotely with PowerShell, you first have to enable PowerShell remoting on the remote machine. You can then use the Invoke-Command and Enter-PSsession cmdlets to execute PowerShell commands on the remote machine. The commands described in this article also work in PowerShell 6 and PowerShell 7**

# On a local computer with Enable-PSRemoting

To enable PowerShell remoting on a single machine, you can log on to this computer locally or via Remote Desktop and then execute Enable-PSRemoting at a PowerShell prompt with administrator rights.

To avoid the conformation prompts, you can use the -Force parameter:

    Enable-PSRemoting -Force

If the computer's current connection type is set to public, the above command will produce an error message because by default PowerShell remoting is only enabled for private and domain connection types. See this blog post for more details about this issue. To avoid the error message and enable PowerShell remoting on a public network, you can use the ‑SkipNetworkProfileCheck parameter:

    Enable-PSRemoting -Force -SkipNetworkProfileCheck
    
# On workgroup group computers    

PowerShell remoting works best in an Active Directory environment. If you want to enable remoting for workgroup or standalone computers you have to consider a few more settings.

In case your network connection type is set to public, you have to use the ‑SkipNetworkProfileCheck parameter as explained above.

    Enable-PSRemoting -Force -SkipNetworkProfileCheck
    
Authentication in PowerShell remoting relies on Active Directory. By default, only computers that are domain members can connect via PowerShell remoting. In a workgroup environment, you have to add the IP addresses of the computers to the TrustedHosts list manually:

    Set-Item WSMan:\localhost\Client\TrustedHosts -Value "10.0.2.33" -Force
    
You also have to ensure that Windows Firewall is opened for Windows Remote Management on the remote computer. On the remote computer, type "firewall" after clicking **Start**, and click **Advanced settings** in the Control Panel **firewall app**. Right-click Inbound Rules and then select New Rule. In the Predefined field select Windows Remote Management and then follow the wizard.    

To enable PowerShell remoting with PsExec, open a command prompt with admin rights in the folder where you copied PsExec and then execute this command:
    
        psexec.exe \\RemoteComputerName -s powershell Enable-PSRemoting -Force
        
# Via PowerShell Direct        

If you want to enable remoting in virtual machine on a Hyper-V host, you can also use PowerShell Direct if the guest OS is Windows 10, Windows Server 2016 or Windows Server 2019 (see comment below). This is the PowerShell command for the task:

        Invoke-Command -VMName <VM name> -ScriptBlock {Enable-PSRemoting -Force} -Credential Administrator
        
# Testing PowerShell remoting        

To test you have enabled PowerShell remoting correctly, you can enter this command

        Enter-PSSession -ComputerName <hostname>
        
This will open an interactive session with a remote computer where you can then enter PowerShell commands to execute on the remote machine.

If you want to connect with a different account than the one you logged on the local machine with, you can use this command:

        Enter-PSSession -Computername "host" –Credential "host\administrator"
        
We can now start the SSH service (sshd) with this PowerShell command:

        Start-Service sshd
        
To automatically star the OpenSSH service sshd, you need this command

        Set-Service sshd -StartupType Automatic
        
**To connect to the remote host, you have to use the HostName parameter instead of  ComputerName parameter:**        

        Enter-PSsession -HostName <computer name>

# Powershell / AZ CLI

| POWERSHELL  | AZ CLI  |
|---|---|
| New-AzResourceGroup -Name 'myResourceGroup' -Location 'EastUS' | az group create --name myResourceGroup --location eastus |
| Remove-AzResourceGroup -Name 'myResourceGroup' | az group delete --name myResourceGroup |
| Get-AzVM -ResourceGroupName "ResourceGroup11" -Name "VirtualMachine07" | az vm list | 
| Set-AzVM -ResourceGroupName "ResourceGroup11" -Name "VirtualMachine07" -Generalized | az vm update -n name -g group --remove tags.tagName |



# Create Windows VM images with Azure PowerShell

## Get the VM

    $sourceVM = Get-AzVM `
       -Name sourceVM `
       -ResourceGroupName myResourceGroup
   
## Create a resource group   

    $resourceGroup = New-AzResourceGroup `
       -Name 'myGalleryRG' `
       -Location 'EastUS'
   
## Create a gallery   

    $gallery = New-AzGallery `
       -GalleryName 'myGallery' `
       -ResourceGroupName $resourceGroup.ResourceGroupName `
       -Location $resourceGroup.Location `
       -Description 'Azure Compute Gallery for my organization'
   
## Create an image definition   

    $galleryImage = New-AzGalleryImageDefinition `
       -GalleryName $gallery.Name `
       -ResourceGroupName $resourceGroup.ResourceGroupName `
       -Location $gallery.Location `
       -Name 'myImageDefinition' `
       -OsState specialized `
       -OsType Windows `
       -Publisher 'myPublisher' `
       -Offer 'myOffer' `
       -Sku 'mySKU'
   
## Create an image version

    $region1 = @{Name='South Central US';ReplicaCount=1}
       $region2 = @{Name='East US';ReplicaCount=2}
       $targetRegions = @($region1,$region2)

    New-AzGalleryImageVersion `
       -GalleryImageDefinitionName $galleryImage.Name`
       -GalleryImageVersionName '1.0.0' `
       -GalleryName $gallery.Name `
       -ResourceGroupName $resourceGroup.ResourceGroupName `
       -Location $resourceGroup.Location `
       -TargetRegion $targetRegions  `
       -Source $sourceVM.Id.ToString() `
       -PublishingProfileEndOfLifeDate '2030-12-01'
   
## Create a VM

    # Create some variables for the new VM.
    $resourceGroup = "myResourceGroup"
    $location = "South Central US"
    $vmName = "mySpecializedVM"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroup -Location $location

    # Create the network resources.
    $subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
    $vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
      -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
    $pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
      -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
    $nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
      -DestinationPortRange 3389 -Access Deny
    $nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
      -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
    $nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
      -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration using $imageVersion.Id to specify the image version.
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
    Set-AzVMSourceImage -Id $galleryImage.Id | `
    Add-AzVMNetworkInterface -Id $nic.Id

    # Create a virtual machine
    New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
    
## Share the gallery

    # Get the object ID for the user
    $user = Get-AzADUser -StartsWith alinne_montes@contoso.com
    # Grant access to the user for our gallery
    New-AzRoleAssignment `
       -ObjectId $user.Id `
       -RoleDefinitionName Reader `
       -ResourceName $gallery.Name `
       -ResourceType Microsoft.Compute/galleries `
       -ResourceGroupName $resourceGroup.ResourceGroupName
       
       
## Generalize a VM

    {
      "builders": [{
        "type": "azure-arm",

        "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
        "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
        "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

        "managed_image_resource_group_name": "myPackerGroup",
        "managed_image_name": "myPackerImage",

        "os_type": "Windows",
        "image_publisher": "MicrosoftWindowsServer",
        "image_offer": "WindowsServer",
        "image_sku": "2016-Datacenter",

        "communicator": "winrm",
        "winrm_use_ssl": true,
        "winrm_insecure": true,
        "winrm_timeout": "5m",
        "winrm_username": "packer",

        "azure_tags": {
            "dept": "Engineering",
            "task": "Image deployment"
        },

        "build_resource_group_name": "myPackerGroup",
        "vm_size": "Standard_D2_v2"
      }],
      "provisioners": [{
        "type": "powershell",
        "inline": [
          "Add-WindowsFeature Web-Server",
          "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
          "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
          "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
          "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
        ]
      }]
    }

# Generalize Command - sysprep.exe /oobe /generalize /mode:vm /shutdown
