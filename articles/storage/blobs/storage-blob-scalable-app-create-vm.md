---
title: Een VM en een opslagaccount maken voor een schaalbare toepassing in Azure
description: Informatie over hoe u een VM implementeert die wordt gebruikt om een schaalbare toepassing uit te voeren met behulp van Azure Blob-opslag
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b8c52b03cb6dec6096565e9eac26b7b2c4a30e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89073247"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Een virtuele machine en een opslagaccount maken voor een schaalbare toepassing

Deze zelfstudie is deel één van een serie. In deze zelfstudie leert u hoe u een toepassing kunt implementeren waarmee grote hoeveelheden willekeurige gegevens worden geüpload en gedownload met een Azure-opslagaccount. Wanneer u klaar bent, hebt u een consoletoepassing die wordt uitgevoerd op een virtuele machine waarmee u grote hoeveelheden gegevens downloadt van en uploadt naar een opslagaccount.

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Create a storage account
> * Een virtuele machine maken
> * Een aangepaste scriptextensie configureren

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie versie 0.7 of hoger van de Azure PowerShell-module Az vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Create a storage account
 
Met het voorbeeld worden 50 grote bestanden geüpload naar een blobcontainer in een Azure Storage-account. Een opslagaccount biedt een unieke naamruimte voor het opslaan en openen van uw Azure Storage-gegevensobjecten. Maak een opslagaccount in de resourcegroep die u hebt gemaakt met behulp van de opdracht [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount).

Vervang in de volgende opdracht het Blob Storage-account in de tijdelijke aanduiding `<blob_storage_account>` door uw eigen unieke naam.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele-machineconfiguratie. Deze configuratie bevat de instellingen die worden gebruikt bij het implementeren van de virtuele machine, zoals een installatiekopie van de virtuele machine, de grootte en de verificatieconfiguratie. Als deze stap wordt uitgevoerd, wordt u gevraagd referenties op te geven. De waarden die u invoert, worden geconfigureerd als de gebruikersnaam en het wachtwoord voor de virtuele machine.

Maak de virtuele machine met [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Configuratie implementeren

Voor deze zelfstudie moeten bepaalde onderdelen op de virtuele machine worden geïnstalleerd. De aangepaste scriptextensie wordt gebruikt om een PowerShell-script uit te voeren dat de volgende taken voltooit:

> [!div class="checklist"]
> * .NET Core 2.0 installeren
> * Chocolatey installeren
> * Git installeren
> * De voorbeeldopslagplaats klonen
> * NuGet-pakketten herstellen
> * 50 bestanden van 1 GB met willekeurige gegevens maken

Voer de volgende cmdlet uit om de configuratie van de virtuele machine te voltooien. Deze stap neemt 5 tot 15 minuten in beslag.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Volgende stappen

In deel één van de reeks hebt u geleerd hoe u een opslagaccount maakt, een virtuele machine implementeert en de virtuele machine configureert met de juiste vereisten. U hebt onder meer het volgende gedaan:

> [!div class="checklist"]
> * Create a storage account
> * Een virtuele machine maken
> * Een aangepaste scriptextensie configureren

Ga naar deel twee van de reeks om grote hoeveelheden gegevens te uploaden naar een opslagaccount met behulp van exponentiële gelijktijdige uitvoering en opnieuw proberen.

> [!div class="nextstepaction"]
> [Grote aantallen grote bestanden gelijktijdig uploaden naar een opslagaccount](storage-blob-scalable-app-upload-files.md)
