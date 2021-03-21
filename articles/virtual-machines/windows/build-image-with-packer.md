---
title: Power shell-VM-installatie kopieën maken met Packer
description: Meer informatie over het gebruik van Packer en Power shell voor het maken van installatie kopieën van virtuele machines in azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: a2cd6152f9c8e75223fcf1aab77fee7700223979
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561004"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>Power shell: met behulp van de module Packer kunt u installatie kopieën voor virtuele machines maken in azure
Elke virtuele machine (VM) in azure wordt gemaakt op basis van een installatie kopie die de Windows-distributie-en besturingssysteem versie definieert. Installatie kopieën kunnen vooraf geïnstalleerde toepassingen en configuraties bevatten. De Azure Marketplace biedt veel kopieën van de eerste en derde partij voor het meest voorkomende besturings systeem en de toepassingen omgevingen, of u kunt uw eigen aangepaste installatie kopieën maken die zijn afgestemd op uw behoeften. In dit artikel wordt beschreven hoe u met behulp van de open source tool [Packer](https://www.packer.io/) aangepaste installatie kopieën in azure kunt definiëren en bouwen.

Dit artikel is voor het laatst getest op 8/5/2020 met [Packer](https://www.packer.io/docs/install) versie 1.6.1.

> [!NOTE]
> Azure heeft nu een service, Azure Image Builder (preview), voor het definiëren en maken van uw eigen aangepaste installatie kopieën. Azure Image Builder is gebaseerd op Packer, dus u kunt zelfs uw bestaande scripts voor de inrichtings functie van de pakket shell gebruiken. Zie [een Windows-VM maken met Azure Image Builder](image-builder.md)om aan de slag te gaan met Azure Image Builder.

## <a name="create-azure-resource-group"></a>Azure-resourcegroep maken
Tijdens het bouw proces maakt verpakker tijdelijke Azure-resources tijdens het maken van de bron-VM. Als u wilt dat de bron-VM als een installatie kopie wordt gebruikt, moet u een resource groep definiëren. De uitvoer van het pakket voor het maken van pakketten wordt opgeslagen in deze resource groep.

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep met de naam *myPackerGroup* gemaakt op de locatie *eastus* :

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure-referenties maken
De verpakker wordt geverifieerd met Azure met behulp van een service-principal. Een Azure-Service-Principal is een beveiligings identiteit die u kunt gebruiken met apps, services en Automation-hulpprogram ma's, zoals Packer. U beheert en definieert de machtigingen voor de bewerkingen die de Service-Principal in azure kan uitvoeren.

Een service-principal maken met [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). De waarde voor `-DisplayName` moet uniek zijn; Vervang deze indien nodig door uw eigen waarde.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

Voer vervolgens het wacht woord en de toepassings-ID uit.

```powershell
$plainPassword
$sp.ApplicationId
```


Als u zich bij Azure wilt verifiëren, moet u ook de Id's van uw Azure-Tenant en-abonnement verkrijgen met [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Pakket sjabloon definiëren
Als u installatie kopieën wilt maken, maakt u een sjabloon als een JSON-bestand. In de sjabloon definieert u bouwers en inrichtings die het werkelijke bouw proces uitvoeren. Verpakker heeft een [Builder voor Azure](https://www.packer.io/docs/builders/azure.html) waarmee u Azure-resources kunt definiëren, zoals de referenties van de service-principal die in de voor gaande stap zijn gemaakt.

Maak een bestand met de naam *windows.jsop* en plak de volgende inhoud. Voer uw eigen waarden in voor het volgende:

| Parameter                           | Waar u kunt verkrijgen |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Service-Principal-ID weer geven met `$sp.applicationId` |
| *client_secret*                     | Het automatisch gegenereerde wacht woord weer geven met `$plainPassword` |
| *tenant_id*                         | Uitvoer van `$sub.TenantId` opdracht |
| *subscription_id*                   | Uitvoer van `$sub.SubscriptionId` opdracht |
| *managed_image_resource_group_name* | De naam van de resource groep die u in de eerste stap hebt gemaakt |
| *managed_image_name*                | Naam voor de beheerde schijf installatie kopie die wordt gemaakt |

```json
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
```

Met deze sjabloon maakt u een virtuele machine met Windows Server 2016, installeert u IIS en generaliseert u vervolgens de virtuele machine met Sysprep. De IIS-installatie laat zien hoe u de Power shell-inrichtings programma kunt gebruiken om extra opdrachten uit te voeren. De uiteindelijke installatie kopie van de verpakker bevat vervolgens de vereiste software-installatie en-configuratie.

De Windows-gast agent neemt deel aan het Sysprep-proces. De agent moet volledig zijn geïnstalleerd voordat de virtuele machine kan worden sysprep'ed. Om ervoor te zorgen dat dit waar is, moeten alle agent services worden uitgevoerd voordat u sysprep.exe uitvoert. In het voor gaande JSON-fragment ziet u een manier om dit te doen in de Power shell-inrichting. Dit fragment is alleen vereist als de virtuele machine is geconfigureerd om de agent te installeren. Dit is de standaard waarde.


## <a name="build-packer-image"></a>Installatie kopie van Builder
Als u Packer nog niet op uw lokale computer hebt geïnstalleerd, [volgt u de installatie-instructies van de verpakking](https://learn.hashicorp.com/packer/getting-started/install).

Bouw de installatie kopie door een opdracht prompt te openen en het pakket sjabloon bestand als volgt op te geven:

```
./packer build windows.json
```

Een voor beeld van de uitvoer van de voor gaande opdrachten is als volgt:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Het duurt enkele minuten voordat de virtuele machine door de pakket functie wordt gebouwd, de inrichtingen worden uitgevoerd en de implementatie is opschoont.


## <a name="create-a-vm-from-the-packer-image"></a>Een VM maken op basis van de installatie kopie van de Packer
U kunt nu een virtuele machine maken op basis van uw installatie kopie met [New-AzVM](/powershell/module/az.compute/new-azvm). De ondersteunende netwerk bronnen worden gemaakt als ze nog niet bestaan. Wanneer u hierom wordt gevraagd, voert u een beheerders naam en-wacht woord in om te maken op de VM. In het volgende voor beeld wordt een VM gemaakt met de naam *myVM* van *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Als u Vm's wilt maken in een andere resource groep of regio dan uw pakket afbeelding, geeft u de installatie kopie-ID op in plaats van de naam van de installatie kopie. U kunt de afbeeldings-ID verkrijgen met [Get-AzImage](/powershell/module/az.compute/get-azimage).

Het duurt enkele minuten om de virtuele machine te maken op basis van de installatie kopie van uw Packer.


## <a name="test-vm-and-webserver"></a>VM en webserver testen
Haal het openbare IP-adres van uw virtuele machine op met [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). In het volgende voorbeeld wordt het IP-adres opgehaald voor het eerder gemaakte *myPublicIP*:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Als u uw VM wilt zien, met inbegrip van de IIS-installatie van de pakket inrichting, voert u in actie het open bare IP-adres in in een webbrowser.

![Standaardsite van IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Volgende stappen
U kunt ook bestaande pakket inrichtings scripts gebruiken met [Azure Image Builder](image-builder.md).
