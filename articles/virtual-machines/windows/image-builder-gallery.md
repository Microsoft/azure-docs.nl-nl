---
title: Azure Image Builder gebruiken met een galerie met installatie kopieën voor Windows-Vm's (preview-versie)
description: Maak installatie kopieën van de gedeelde Azure-galerie met behulp van Azure Image Builder en Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: e8caf9f742217161c60ce90351989999f18adabb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694084"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Voor beeld: een Windows-installatie kopie maken en deze distribueren naar een gedeelde installatie kopie galerie 

In dit artikel wordt uitgelegd hoe u de opbouw functie voor installatie kopieën van Azure en Azure PowerShell kunt gebruiken om een installatie kopie versie te maken in een [Galerie met gedeelde afbeeldingen](../shared-image-galleries.md)en vervolgens de installatie kopie wereld wijd te distribueren. U kunt dit ook doen met behulp van de [Azure cli](../linux/image-builder-gallery.md).

Er wordt een JSON-sjabloon gebruikt om de installatie kopie te configureren. Het JSON-bestand dat we gebruiken, is hier: [armTemplateWinSIG.jsop](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). We gaan een lokale versie van de sjabloon downloaden en bewerken, zodat dit artikel wordt geschreven met behulp van een lokale Power shell-sessie.

De sjabloon maakt gebruik van [sharedImage](../linux/image-builder-json.md#distribute-sharedimage) als de waarde voor de `distribute` sectie van de sjabloon om de installatie kopie naar een galerie met gedeelde afbeeldingen te distribueren.

Azure Image Builder voert automatisch Sysprep uit om de installatie kopie te generaliseren. Dit is een generieke Sysprep-opdracht, die u zo nodig kunt [overschrijven](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully) . 

Houd er rekening mee dat het aantal keren dat u de laag wilt aanpassen. U kunt de Sysprep-opdracht Maxi maal 8 keer uitvoeren op één Windows-installatie kopie. Nadat u Sysprep acht keer hebt uitgevoerd, moet u de Windows-installatie kopie opnieuw maken. Zie [limieten voor het aantal keren dat u Sysprep kunt uitvoeren](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)voor meer informatie. 

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="register-the-features"></a>De functies registreren
Als u Azure Image Builder wilt gebruiken tijdens de preview, moet u de nieuwe functie registreren.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Controleer de status van de functie registratie.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Wacht tot `RegistrationState` de bewerking is gestart `Registered` voordat u verdergaat met de volgende stap.

Controleer uw provider registraties. Zorg ervoor dat elke keer wordt geretourneerd `Registered` .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

Als ze niet retour neren `Registered` , gebruikt u het volgende om de providers te registreren:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

Power shell-modules installeren:
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>Variabelen maken

We zullen enkele gegevens herhaaldelijk gebruiken, dus we maken een aantal variabelen om deze informatie op te slaan. Vervang de waarden voor de variabelen, zoals `username` en `vmpassword` , door uw eigen gegevens.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Een door de gebruiker toegewezen identiteit maken en machtigingen instellen voor de resource groep
De opbouw functie voor installatie kopieën gebruikt de door de [gebruiker gedefinieerde identiteit](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) voor het injecteren van de installatie kopie in de galerie met gedeelde installatie kopieën van Azure (SIG). In dit voor beeld maakt u een Azure-roldefinitie met de gedetailleerde acties waarmee de installatie kopie kan worden gedistribueerd naar de SIG. De roldefinitie wordt vervolgens toegewezen aan de identiteit van de gebruiker.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Machtigingen voor de identiteit voor het distribueren van afbeeldingen toewijzen

Met deze opdracht wordt een Azure Role definition-sjabloon gedownload en wordt de sjabloon bijgewerkt met de para meters die u eerder hebt opgegeven.

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>De galerie met gedeelde afbeeldingen maken

Als u de opbouw functie voor afbeeldingen wilt gebruiken met een galerie met gedeelde afbeeldingen, moet u een bestaande afbeeldings galerie en afbeeldings definitie hebben. Met de opbouw functie voor installatie kopieën wordt de installatie kopie galerie en de definitie van de installatie kopie niet voor u gemaakt.

Als u nog geen galerie en afbeeldings definitie hebt om te gebruiken, maakt u deze eerst. Maak eerst een galerie met installatie kopieën.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>De sjabloon downloaden en configureren

Down load de JSON-sjabloon en configureer deze met de variabelen.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Uw sjabloon moet worden verzonden naar de service. Hierdoor worden alle afhankelijke artefacten, zoals scripts, gedownload en opgeslagen in de staging-resource groep, voorafgegaan door *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -apiversion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Als u de installatie kopie wilt maken, moet u uitvoeren op de sjabloon.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

Het maken van de installatie kopie en het repliceren naar beide regio's kan enige tijd duren. Wacht tot dit onderdeel is voltooid voordat u doorgaat met het maken van een virtuele machine.

Zie [Leesmij] voor informatie over opties voor het automatiseren van het ophalen van de installatie kopie status.
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>De VM maken

Maak een virtuele machine op basis van de installatie kopie versie die is gemaakt door de opbouw functie voor installatie kopieën van Azure.

Haal de versie van de installatie kopie op die u hebt gemaakt.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Maak de virtuele machine in de tweede regio die de afbeelding had gerepliceerd.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>De aanpassing controleren
Maak een Extern bureaublad verbinding met de virtuele machine met behulp van de gebruikers naam en het wacht woord die u hebt ingesteld tijdens het maken van de virtuele machine. Open een opdracht prompt in de virtuele machine en typ het volgende:

```console
dir c:\
```

Als het goed is, ziet u een map met de naam `buildActions` die is gemaakt tijdens het aanpassen van de installatie kopie.


## <a name="clean-up-resources"></a>Resources opschonen
Als u de installatie kopie nu opnieuw wilt aanpassen om een nieuwe versie van dezelfde installatie kopie te maken, slaat u **deze stap over** en gaat u verder met het [gebruik van Azure Image Builder om een andere versie van de installatie kopie te maken](image-builder-gallery-update-image-version.md).


Hiermee verwijdert u de installatie kopie die is gemaakt, samen met alle andere bron bestanden. Zorg ervoor dat u klaar bent met deze implementatie voordat u de resources verwijdert.

Verwijder eerst de sjabloon voor de resource groep, anders wordt de staging-resource groep (*IT_*) die door AIB wordt gebruikt, niet opgeschoond.

Haal ResourceID van de afbeeldings sjabloon op. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

Verwijder de afbeeldings sjabloon.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Roltoewijzing verwijderen

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

definities verwijderen

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

identiteit verwijderen

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

Verwijder de resource groep.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Image Builder gebruiken om een andere versie](image-builder-gallery-update-image-version.md)van de installatie kopie te maken voor meer informatie over het bijwerken van de versie van de installatie kopie die u hebt gemaakt.
