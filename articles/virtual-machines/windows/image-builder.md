---
title: Een windows-VM maken met Azure Image Builder (preview)
description: Maak een Windows-VM met de Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: b93d236d0b716bfaf7dfb45b21c9524ece75fcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764563"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Preview: Een windows-VM maken met Azure Image Builder

In dit artikel wordt beschreven hoe u een aangepaste Windows-afbeelding kunt maken met behulp van de Azure VM Image Builder. In het voorbeeld in dit artikel wordt [gebruikgemaakt van aanpasbare toepassingen](../linux/image-builder-json.md#properties-customize) voor het aanpassen van de afbeelding:
- PowerShell (ScriptUri) - download en voer een [PowerShell-script uit.](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)
- Windows Opnieuw opstarten: start de VM opnieuw op.
- PowerShell (inline) - voer een specifieke opdracht uit. In dit voorbeeld wordt een map op de VM gemaakt met behulp van `mkdir c:\\buildActions` .
- Bestand: kopieer een bestand van GitHub naar de VM. In dit voorbeeld worden [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) gekopieerd `c:\buildArtifacts\index.html` naar op de VM.
- buildTimeoutInMinutes: verhoog de buildtijd zodat langerlopende builds mogelijk zijn. De standaardwaarde is 240 minuten en u kunt de buildtijd langer maken om langere builds toe te staan.
- vmProfile: een vmSize- en netwerkeigenschappen opgeven
- osDiskSizeGB: u kunt de grootte van de afbeelding vergroten
- identiteit: een identiteit bieden voor Azure-Image Builder te gebruiken tijdens de build


U kunt ook een `buildTimeoutInMinutes` opgeven. De standaardwaarde is 240 minuten en u kunt de buildtijd langer maken om langere builds toe te staan.

We gebruiken een .json-voorbeeldsjabloon om de afbeelding te configureren. Het JSON-bestand dat we gebruiken, is hier: [helloImageTemplateWin.jsop](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="register-the-features"></a>De functies registreren

Als u Azure Image Builder tijdens de preview, moet u de nieuwe functie registreren.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Controleer de status van de functieregistratie.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Controleer uw registratie.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Als ze niet geregistreerde zeggen, voer het volgende uit:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables"></a>Variabelen instellen

We zullen herhaaldelijk enkele stukjes informatie gebruiken, dus maken we enkele variabelen om die informatie op te slaan.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Maak een variabele voor uw abonnements-id. U kunt dit doen met behulp van `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Een resourcegroep maken
Deze resourcegroep wordt gebruikt voor het opslaan van het sjabloonartefact voor de configuratie van de installatie afbeelding en de afbeelding.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Een door de gebruiker toegewezen identiteit maken en machtigingen instellen voor de resourcegroep
Image Builder gebruikt de opgegeven [gebruikersidentiteit](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) om de afbeelding in de resourcegroep te injecteren. In dit voorbeeld maakt u een Azure-roldefinitie die de gedetailleerde acties heeft om de afbeelding te distribueren. De roldefinitie wordt vervolgens toegewezen aan de gebruikersidentiteit.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Door de gebruiker toegewezen beheerde identiteit maken en machtigingen verlenen 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Voorbeeld van de configuratiesjabloon voor afbeeldingen downloaden

Er is een geparameteriseerde sjabloon voor het configureren van afbeeldingen gemaakt die u kunt proberen. Download het JSON-voorbeeldbestand en configureer het met de variabelen die u eerder hebt ingesteld.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

U kunt dit voorbeeld in de terminal wijzigen met behulp van een teksteditor zoals `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> Voor de bronafbeelding moet u altijd [een versie opgeven.](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)U kunt niet `latest` gebruiken.
> Als u de resourcegroep toevoegt of wijzigt waarin de afbeelding is gedistribueerd, moet u ervoor zorgen dat de [machtigingen zijn ingesteld](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) voor de resourcegroep.
 
## <a name="create-the-image"></a>De installatiekopie maken

De configuratie van de installatie afbeelding verzenden naar de VM-Image Builder service

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Wanneer dit is voltooid, wordt er een succesbericht teruggezonden naar de console en wordt een `Image Builder Configuration Template` in de `$imageResourceGroup` weergegeven. U kunt deze resource in de resourcegroep in de Azure Portal als u Verborgen typen tonen inschakelen.

Op de achtergrond maakt Image Builder ook een faseringsresourcegroep in uw abonnement. Deze resourcegroep wordt gebruikt voor het bouwen van de afbeelding. Deze heeft deze indeling: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> U mag de faseringsresourcegroep niet rechtstreeks verwijderen. Verwijder eerst het artefact van de afbeeldingssjabloon. Hierdoor wordt de faseringsresourcegroep verwijderd.

Als de service een fout rapporteert tijdens het indienen van de configuratiesjabloon voor afbeeldingen:
-  Bekijk deze stappen [voor probleemoplossing.](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) 
- U moet de sjabloon verwijderen met behulp van het volgende codefragment voordat u het opnieuw kunt indienen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>De build van de afbeelding starten
Start het proces voor het bouwen van de afbeelding [met az resource invoke-action](/cli/azure/resource#az_resource_invoke_action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Wacht totdat de build is voltooid. Dit kan ongeveer 15 minuten duren.

Als er fouten optreden, raadpleegt u deze stappen [voor probleemoplossing.](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors)


## <a name="create-the-vm"></a>De VM maken

Maak de VM met behulp van de door u gebouwde afbeelding. Vervang *\<password>* door uw eigen wachtwoord voor de op de `aibuser` VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>De aanpassing controleren

Maak een Extern bureaublad verbinding met de VM met behulp van de gebruikersnaam en het wachtwoord die u hebt ingesteld tijdens het maken van de VM. Open in de VM een cmd-prompt en typ het volgende:

```console
dir c:\
```

Als het goed is, ziet u de volgende twee directories die zijn gemaakt tijdens het aanpassen van de afbeelding:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Opschonen

Wanneer u klaar bent, verwijdert u de resources.

### <a name="delete-the-image-builder-template"></a>De sjabloon image builder verwijderen

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Verwijder de roltoewijzing, roldefinitie en gebruikersidentiteit.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>De resourcegroep voor de afbeelding verwijderen

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Volgende stappen

Zie Sjabloonverwijzing voor Image Builder voor meer informatie over de onderdelen van het JSON-bestand dat in dit artikel [wordt gebruikt.](../linux/image-builder-json.md)
