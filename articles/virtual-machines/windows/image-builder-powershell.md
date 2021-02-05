---
title: Een Windows-VM met Azure Image Builder maken met behulp van Power shell
description: Maak een Windows-VM met de Azure Image Builder Power shell-module.
author: cynthn
ms.author: cynthn
ms.date: 06/17/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e902798284240b55a3b08ea55ab6ee55add2431
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575835"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Voor beeld: een Windows-VM met Azure Image Builder maken met behulp van Power shell

In dit artikel wordt beschreven hoe u een aangepaste Windows-installatie kopie kunt maken met behulp van de Azure VM Image Builder Power shell-module.

> [!CAUTION]
> Azure Image Builder is momenteel beschikbaar als open bare preview. Deze preview-versie wordt geleverd zonder Service Level Agreement. Dit wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell.

> [!IMPORTANT]
> Hoewel de Power shell-modules **AZ. ImageBuilder** en **AZ. ManagedServiceIdentity** in de preview-versie zijn, moet u ze afzonderlijk installeren met de `Install-Module` cmdlet met de `AllowPrerelease` para meter. Zodra deze Power shell-modules algemeen beschikbaar worden, worden ze onderdeel van toekomstige AZ Power shell-module releases en zijn deze standaard beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Functies registreren

Als dit de eerste keer is dat u Azure Image Builder gebruikt tijdens de preview, registreert u de nieuwe **VirtualMachineTemplatePreview** -functie.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Controleer de status van de functie registratie.

> [!NOTE]
> Het **RegistrationState** kan `Registering` enkele minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht totdat de status is **geregistreerd** voordat u doorgaat.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Registreer de volgende resource providers voor gebruik met uw Azure-abonnement als deze nog niet zijn geregistreerd.

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Variabelen definiëren

U gebruikt herhaaldelijk verschillende gegevens. Maak variabelen om de gegevens op te slaan.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Maak een variabele voor uw Azure-abonnements-ID. Als u wilt bevestigen dat de `subscriptionID` variabele uw abonnements-id bevat, kunt u de tweede regel in het volgende voor beeld uitvoeren.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep gemaakt op basis van de naam in de variabele `$imageResourceGroup` in de regio die is opgegeven in de variabele `$location`. Deze resource groep wordt gebruikt voor het opslaan van het sjabloon artefact van de installatie kopie en de installatie kopie.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Gebruikers-id maken en rolmachtigingen instellen

U kunt Azure Image Builder-machtigingen verlenen voor het maken van installatie kopieën in de opgegeven resource groep met behulp van het volgende voor beeld. Zonder deze machtiging wordt het proces voor het bouwen van de installatie kopie niet voltooid.

Variabelen voor de roldefinitie en Identiteits namen maken. Deze waarden moeten uniek zijn.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Een gebruikers-id maken.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Sla de id-resource en Principal-Id's op in variabelen.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Machtigingen voor de identiteit voor het distribueren van afbeeldingen toewijzen

Down load het JSON-configuratie bestand en wijzig dit op basis van de instellingen die in dit artikel zijn gedefinieerd.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

De roldefinitie maken.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Ken de roldefinitie toe aan de service-principal van de installatie kopie Builder.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Als het volgende fout bericht wordt weer gegeven: '_New-AzRoleDefinition: de roldefinitie limiet is overschreden. Er kunnen geen roldefinities meer worden gemaakt._ Zie [problemen met Azure RBAC oplossen](../../role-based-access-control/troubleshooting.md).

## <a name="create-a-shared-image-gallery"></a>Een gedeelde installatiekopiegalerie maken

Maak de galerie.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Maak een galerie definitie.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Een installatiekopie maken

Maak een Azure Image Builder-bron object. Zie [Windows VM-installatie kopieën zoeken in azure Marketplace met Azure PowerShell](./cli-ps-findimage.md) voor geldige parameter waarden.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Maak een Azure Image Builder-distributie object.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Maak een object voor het aanpassen van de Azure Image Builder.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

Maak een tweede object voor het aanpassen van de Azure Image Builder.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Een Azure Image Builder-sjabloon maken.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Wanneer dit is voltooid, wordt er een bericht weer gegeven en wordt er een installatie kopie van de afbeeldings opbouw functie gemaakt in de `$imageResourceGroup` .

U kunt het volgende voor beeld gebruiken om te bepalen of het proces voor het maken van de sjabloon is geslaagd.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

Op de achtergrond maakt Image Builder ook een staging-resource groep in uw abonnement. Deze resource groep wordt gebruikt voor het bouwen van de installatie kopie. Dit is in de volgende indeling: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Verwijder de faserings resource groep niet rechtstreeks. Verwijder de afbeeldings sjabloon artefact, waardoor de faserings resource groep wordt verwijderd.

Als de service een fout meldt tijdens het verzenden van de installatie kopie:

- Zie [problemen met Azure VM-installatie kopieën opbouwen (AIB) oplossen](../linux/image-builder-troubleshoot.md).
- Verwijder de sjabloon met behulp van het volgende voor beeld voordat u het opnieuw probeert.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>De installatie kopie starten

Verzend de configuratie van de installatie kopie naar de VM Image Builder-service.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Wacht tot het buildproces van de installatie kopie is voltooid. Deze stap kan tot een uur duren.

Als er fouten optreden, raadpleegt u [problemen met het oplossen van Azure VM image build (AIB)-](../linux/image-builder-troubleshoot.md)fouten.

## <a name="create-a-vm"></a>Een virtuele machine maken

Sla referenties voor de virtuele machine op in een variabele. Het wachtwoord moet complex zijn.

```azurepowershell-interactive
$Cred = Get-Credential
```

Maak de virtuele machine met behulp van de installatie kopie die u hebt gemaakt.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>De aanpassingen controleren

Maak een Extern bureaublad verbinding met de virtuele machine met behulp van de gebruikers naam en het wacht woord die u hebt ingesteld tijdens het maken van de virtuele machine. Open Power shell in de VM en voer uit `Get-Content` zoals weer gegeven in het volgende voor beeld:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

U ziet uitvoer op basis van de inhoud van het bestand dat tijdens het aanpassings proces van de installatie kopie is gemaakt.

```Output
Azure-Image-Builder-Was-Here
```

Controleer vanuit dezelfde Power shell-sessie of de tweede aanpassing is geslaagd door te controleren of het bestand aanwezig is `c:\buildArtifacts\index.html` , zoals wordt weer gegeven in het volgende voor beeld:

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

Het resultaat moet een mapweergave zijn waarin het bestand wordt weer gegeven dat tijdens het aanpassings proces van de installatie kopie is gedownload.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>Resources opschonen

Als de resources die in dit artikel zijn gemaakt, niet meer nodig zijn, kunt u ze verwijderen door het volgende voorbeeld uit te voeren.

### <a name="delete-the-image-builder-template"></a>De sjabloon voor de opbouw functie voor installatie kopieën verwijderen

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>De resource groep voor de installatie kopie verwijderen

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van dit artikel in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de onderdelen van het JSON-bestand dat in dit artikel wordt gebruikt [Image Builder-sjabloon verwijzing](../linux/image-builder-json.md).
