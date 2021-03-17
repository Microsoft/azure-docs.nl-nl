---
title: 'Image Builder: een installatie kopie van een virtueel Windows-bureau blad maken'
description: Een Azure VM-installatie kopie van Windows virtueel bureau blad maken met behulp van Azure image builder in Power shell.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 01b253747791fc29abf4434bebfd85865099f9ee
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103602015"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Een virtueel-bureaublad installatie kopie van Windows maken met behulp van Azure VM Image Builder en Power shell

In dit artikel wordt beschreven hoe u een virtueel-bureaublad installatie kopie van Windows maakt met de volgende aanpassingen:

* [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1)installeren.
* Een [Windows-optimalisatie script voor virtueel bureau blad](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) uitvoeren vanuit de opslag plaats van de community.
* Installeer [micro soft teams](https://docs.microsoft.com/azure/virtual-desktop/teams-on-wvd).
* [Opnieuw starten](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-restart-customizer)
* [Windows Update](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-update-customizer) uitvoeren

We laten u zien hoe u dit automatiseert met behulp van de opbouw functie voor installatie kopieën van Azure VM en de installatie kopie distribueert naar een [gedeelde galerie met installatie kopieën](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), waar u kunt repliceren naar andere regio's, de schaal beheert en de installatie kopie binnen en buiten uw organisaties deelt.


Voor het vereenvoudigen van de implementatie van een installatie kopie van de opbouw functie wordt in dit voor beeld een Azure Resource Manager sjabloon gebruikt waarbij de afbeelding Builder-sjabloon is genest. Dit biedt u enkele andere voor delen, zoals variabelen en parameter invoer. U kunt ook para meters door geven vanaf de opdracht regel.

Dit artikel is bedoeld als Kopieer-en plak oefening.

> [!NOTE]
> De scripts voor het installeren van de apps bevinden zich op [github](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD). Ze zijn alleen voor afbeeldings-en test doeleinden en niet voor productiewerk belastingen. 

## <a name="tips-for-building-windows-images"></a>Tips voor het bouwen van Windows-installatie kopieën 

- VM-grootte: de standaard VM-grootte is een `Standard_D1_v2` , wat niet geschikt is voor Windows. Gebruik een `Standard_D2_v2` of meer.
- In dit voor beeld worden de [Power shell-aanpassings scripts](../linux/image-builder-json.md)gebruikt. U moet deze instellingen gebruiken of de build reageert niet meer.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Bijvoorbeeld:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Commentaar voor uw code: het AIB-build-logboek (Customization. log) is uitermate uitgebreid, als u een commentaar op uw scripts uitvoert met behulp van write-host, worden deze naar de logboeken verzonden en worden de probleem oplossing eenvoudiger.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Afsluit codes: AIB verwacht alle scripts om een afsluit code van 0 te retour neren, een niet-nul afsluit code leidt ertoe dat de AIB niet kan worden aangepast en dat de build wordt gestopt. Als u complexe scripts hebt, instrumentatie toevoegen en afsluit codes toevoegt, worden deze weer gegeven in de aanpassings. log.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Testen: u kunt uw code testen en testen voordat u op een zelfstandige VM, Controleer of er geen gebruikers prompts zijn, de juiste bevoegdheid gebruiken, enzovoort.

- Netwerken- `Set-NetAdapterAdvancedProperty` . Dit wordt ingesteld in het optimalisatie script, maar de AIB-build mislukt, omdat de verbinding met het netwerk wordt verbroken. Het wordt onderzocht.

## <a name="prerequisites"></a>Vereisten

U moet de nieuwste Azure PowerShell-CmdLets hebben geïnstalleerd. Zie [hier](https://docs.microsoft.com/powershell/azure/overview) voor installatie Details.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Omgeving en variabelen instellen

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Machtigingen, gebruikers-id en rol 


 Een gebruikers-id maken.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Wijs machtigingen toe aan de identiteit om installatie kopieën te distribueren. Met deze opdracht wordt de sjabloon gedownload en bijgewerkt met de para meters die u eerder hebt opgegeven.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Als u deze fout ziet: ' New-AzRoleDefinition: de roldefinitie limiet is overschreden. Er kunnen geen roldefinities meer worden gemaakt. Raadpleeg dit artikel voor het oplossen van: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>De galerie met gedeelde afbeeldingen maken 

Als u nog geen galerie met gedeelde afbeeldingen hebt, moet u er een maken.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>De afbeeldings sjabloon configureren

Voor dit voor beeld hebben we een sjabloon waarmee de sjabloon kan worden gedownload en bijgewerkt met de eerder opgegeven para meters, worden FsLogix, besturingssysteem optimalisaties, micro soft teams en Windows Update aan het einde geïnstalleerd.

Als u de sjabloon opent, kunt u in de bron eigenschap de afbeelding zien die wordt gebruikt. in dit voor beeld wordt een installatie kopie van het bestand Win 10 van meerdere sessies gebruikt. 

### <a name="windows-10-images"></a>Windows 10-installatie kopieën
U moet rekening houden met twee belang rijke typen: multi sessie en één sessie.

Meerdere sessie-installatie kopieën zijn bedoeld voor gepoold gebruik. Hier volgt een voor beeld van de details van de installatie kopie in Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Installatie kopieën met één sessie zijn bedoeld voor individueel gebruik. Hier volgt een voor beeld van de details van de installatie kopie in Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

U kunt ook de beschik bare Win10-installatie kopieën wijzigen:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Sjabloon downloaden en configureren

Nu moet u de sjabloon downloaden en configureren voor uw gebruik.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

Als u de [sjabloon](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)wilt weer geven, kunt u alle code zien.


## <a name="submit-the-template"></a>De sjabloon verzenden

Uw sjabloon moet worden verzonden naar de service. Hierdoor worden alle afhankelijke artefacten (zoals scripts) gedownload, worden de machtigingen gecontroleerd en opgeslagen in de faserings resource groep, vooraf vastgesteld *IT_*.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>De installatiekopie bouwen
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> De opdracht wacht niet totdat de Image Builder-service de installatie kopie-build heeft voltooid. u kunt de onderstaande status opvragen.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Een virtuele machine maken
Nu de build is voltooid, kunt u een VM maken op [basis van de installatie kopie. gebruik](https://docs.microsoft.com/powershell/module/az.compute/new-azvm#examples)hiervoor de voor beelden.

## <a name="clean-up"></a>Opschonen

Verwijder eerst de resource groeps sjabloon, Verwijder niet alleen de hele resource groep, anders wordt de faserings resource groep (*IT_*) die door AIB wordt gebruikt, niet opgeschoond.

Verwijder de afbeeldings sjabloon.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Verwijder de roltoewijzing.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Verwijder de resource groep.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

U kunt meer voor beelden uitproberen [op github](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
