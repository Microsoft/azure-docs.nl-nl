---
title: Een Windows-VM met Azure Image Builder maken (preview)
description: Een Windows-VM maken met de Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 918cee723bfde69d08532aee6fe4f395dbddb4ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695444"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Voor beeld: een Windows-VM maken met Azure Image Builder

In dit artikel wordt uitgelegd hoe u een aangepaste installatie kopie van Windows kunt maken met behulp van de opbouw functie voor installatie kopieën van Azure VM. In het voor beeld in dit artikel worden [aanpassingen](../linux/image-builder-json.md#properties-customize) gebruikt voor het aanpassen van de installatie kopie:
- Power shell (ScriptUri): down load en voer een [Power shell-script](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)uit.
- Windows opnieuw starten: Hiermee wordt de virtuele machine opnieuw opgestart.
- Power shell (inline)-een specifieke opdracht uitvoeren. In dit voor beeld maakt het een map op de VM met behulp van `mkdir c:\\buildActions` .
- Bestand: Kopieer een bestand van GitHub naar de virtuele machine. In dit voor beeld wordt [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) naar `c:\buildArtifacts\index.html` op de VM gekopieerd.
- buildTimeoutInMinutes: Verhoog een build-tijd zodat er meer builds kunnen worden uitgevoerd. de standaard waarde is 240 minuten en u kunt een buildtijd verhogen zodat er meer builds meer worden uitgevoerd.
- vmProfile: een vmSize en netwerk eigenschappen opgeven
- osDiskSizeGB: u kunt de grootte van de installatie kopie verg Roten
- identiteit: een identiteit bieden voor Azure Image Builder die tijdens de build moet worden gebruikt


U kunt ook een opgeven `buildTimeoutInMinutes` . De standaard waarde is 240 minuten en u kunt een build-tijd verg Roten zodat er meer builds meer worden uitgevoerd.

Er wordt een voor beeld van een JSON-sjabloon gebruikt voor het configureren van de installatie kopie. Het JSON-bestand dat we gebruiken, is hier: [helloImageTemplateWin.jsop](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="register-the-features"></a>De functies registreren

Als u Azure Image Builder wilt gebruiken tijdens de preview, moet u de nieuwe functie registreren.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Controleer de status van de functie registratie.

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

Als ze niet zijn geregistreerd, voert u de volgende handelingen uit:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables"></a>Variabelen instellen

We zullen enkele gegevens herhaaldelijk gebruiken, dus we maken een aantal variabelen om deze informatie op te slaan.


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

Maak een variabele voor uw abonnements-ID. U kunt dit doen met `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Een resourcegroep maken
Deze resource groep wordt gebruikt voor het opslaan van het sjabloon artefact van de installatie kopie en de installatie kopie.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Een door de gebruiker toegewezen identiteit maken en machtigingen instellen voor de resource groep
De opbouw functie voor installatie kopieën gebruikt de door de [gebruiker gedefinieerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) voor het injecteren van de installatie kopie in de resource groep. In dit voor beeld maakt u een Azure-roldefinitie met de gedetailleerde acties waarmee de installatie kopie kan worden gedistribueerd. De roldefinitie wordt vervolgens toegewezen aan de identiteit van de gebruiker.

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



## <a name="download-the-image-configuration-template-example"></a>Het voor beeld van een installatie kopie configuratie sjabloon downloaden

Er is een geparametriseerde afbeeldings configuratie sjabloon gemaakt die u kunt proberen. Down load het bestand example. json en configureer dit met de variabelen die u eerder hebt ingesteld.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

U kunt dit voor beeld wijzigen in de Terminal met een tekst editor zoals `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> Voor de bron installatie kopie moet u altijd [een versie opgeven](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version). u kunt deze niet gebruiken `latest` .
> Als u de resource groep waaraan de afbeelding is gedistribueerd toevoegt of wijzigt, moet u de [machtigingen instellen](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) voor de resource groep.
 
## <a name="create-the-image"></a>De installatiekopie maken

De configuratie van de installatie kopie verzenden naar de VM Image Builder-service

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Als u klaar bent, wordt er een bericht weer gegeven dat de console is voltooid en maakt u een `Image Builder Configuration Template` in de `$imageResourceGroup` . Als u verborgen typen weer geven inschakelt, ziet u deze resource in de resource groep in de Azure Portal.

Op de achtergrond maakt de opbouw functie voor installatie kopieën ook een staging-resource groep in uw abonnement. Deze resource groep wordt gebruikt voor het bouwen van de installatie kopie. Deze heeft de volgende indeling: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> U moet de faserings resource groep niet rechtstreeks verwijderen. Verwijder eerst de afbeeldings sjabloon artefact, waardoor de faserings resource groep wordt verwijderd.

Als de service een fout meldt tijdens het verzenden van de installatie kopie:
-  Bekijk deze [probleemoplossings](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) stappen. 
- U moet de sjabloon verwijderen met behulp van het volgende code fragment voordat u het opnieuw probeert te verzenden.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>De installatie kopie starten
Start het proces voor het bouwen van de installatie kopie met behulp van [AZ resource invoke-Action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Wacht tot het build is voltooid. Dit kan ongeveer 15 minuten duren.

Als er fouten optreden, raadpleegt u deze [probleemoplossings](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) stappen.


## <a name="create-the-vm"></a>De VM maken

Maak de virtuele machine met behulp van de installatie kopie die u hebt gemaakt. Vervang door *\<password>* uw eigen wacht woord voor de `aibuser` op de virtuele machine.

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

Maak een Extern bureaublad verbinding met de virtuele machine met behulp van de gebruikers naam en het wacht woord die u hebt ingesteld tijdens het maken van de virtuele machine. Open een opdracht prompt in de virtuele machine en typ het volgende:

```console
dir c:\
```

Tijdens het aanpassen van de installatie kopie ziet u deze twee directory's:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Opschonen

Wanneer u klaar bent, verwijdert u de resources.

### <a name="delete-the-image-builder-template"></a>De sjabloon voor de opbouw functie voor installatie kopieën verwijderen

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Verwijder de roltoewijzing, roldefinitie en gebruikers identiteit.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>De resource groep voor de installatie kopie verwijderen

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de onderdelen van het JSON-bestand dat in dit artikel wordt gebruikt [Image Builder-sjabloon verwijzing](../linux/image-builder-json.md).
