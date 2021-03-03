---
title: Azure Image Builder gebruiken & galerie met gedeelde afbeeldingen voor Linux-Vm's (preview-versie)
description: Meer informatie over het gebruik van de Azure-functie voor installatie kopieën en de Azure CLI om een installatie kopie versie in een galerie met gedeelde afbeeldingen te maken en de installatie kopie vervolgens globaal te distribueren.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: cf7f5ed4a27772a89d0356c60e6f7135786ca07d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695597"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Voor beeld: een Linux-installatie kopie maken en deze distribueren naar een gedeelde galerie met installatie kopieën 

In dit artikel wordt beschreven hoe u de opbouw functie voor Azure-installatie kopieën en de Azure CLI kunt gebruiken om een installatie kopie versie te maken in een [Galerie met gedeelde afbeeldingen](../shared-image-galleries.md)en vervolgens de installatie kopie wereld wijd te distribueren. U kunt dit ook doen met behulp van [Azure PowerShell](../windows/image-builder-gallery.md).


Er wordt een voor beeld van een JSON-sjabloon gebruikt voor het configureren van de installatie kopie. Het JSON-bestand dat we gebruiken, is hier: [helloImageTemplateforSIG.jsop](https://github.com/azure/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

De sjabloon maakt gebruik van [sharedImage](image-builder-json.md#distribute-sharedimage) als de waarde voor de `distribute` sectie van de sjabloon om de installatie kopie naar een galerie met gedeelde afbeeldingen te distribueren.

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

## <a name="set-variables-and-permissions"></a>Variabelen en machtigingen instellen 

We zullen enkele gegevens herhaaldelijk gebruiken, dus we maken een aantal variabelen om deze informatie op te slaan.

Voor de preview-versie kan de opbouw functie voor afbeeldingen alleen het maken van aangepaste installatie kopieën in dezelfde resource groep als de door de bron beheerde installatie kopie worden ondersteund. Werk de naam van de resource groep in dit voor beeld bij naar dezelfde resource groep als de door de bron beheerde installatie kopie.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Maak een variabele voor uw abonnements-ID. U kunt dit doen met `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Maak de resourcegroep.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Een door de gebruiker toegewezen identiteit maken en machtigingen instellen voor de resource groep
De opbouw functie voor installatie kopieën gebruikt de door de [gebruiker gedefinieerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) voor het injecteren van de installatie kopie in de galerie met gedeelde installatie kopieën van Azure (SIG). In dit voor beeld maakt u een Azure-roldefinitie met de gedetailleerde acties waarmee de installatie kopie kan worden gedistribueerd naar de SIG. De roldefinitie wordt vervolgens toegewezen aan de identiteit van de gebruiker.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Een definitie en galerie voor een installatie kopie maken

Als u de opbouw functie voor afbeeldingen wilt gebruiken met een galerie met gedeelde afbeeldingen, moet u een bestaande afbeeldings galerie en afbeeldings definitie hebben. Met de opbouw functie voor installatie kopieën wordt de installatie kopie galerie en de definitie van de installatie kopie niet voor u gemaakt.

Als u nog geen galerie en afbeeldings definitie hebt om te gebruiken, maakt u deze eerst. Maak eerst een galerie met installatie kopieën.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Maak vervolgens een definitie voor de installatie kopie.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>De json downloaden en configureren

Down load de JSON-sjabloon en configureer deze met de variabelen.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>De installatiekopieversie maken

Met dit volgende deel wordt de installatie kopie versie gemaakt in de galerie. 

Verzend de configuratie van de installatie kopie naar de Azure Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Start het maken van de installatie kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Het maken van de installatie kopie en het repliceren naar beide regio's kan enige tijd duren. Wacht tot dit onderdeel is voltooid voordat u doorgaat met het maken van een virtuele machine.


## <a name="create-the-vm"></a>De VM maken

Maak een virtuele machine op basis van de installatie kopie versie die is gemaakt door de opbouw functie voor installatie kopieën van Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Maak via SSH verbinding met de virtuele machine.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

U ziet dat de installatie kopie is aangepast met een *bericht van de dag* zodra uw SSH-verbinding tot stand is gebracht.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de versie van de installatie kopie nu opnieuw wilt aanpassen om een nieuwe versie van dezelfde installatie kopie te maken, slaat u de volgende stappen over en gaat u verder met het maken van een versie van de installatie kopie met [behulp van Azure Image Builder](image-builder-gallery-update-image-version.md).


Hiermee verwijdert u de installatie kopie die is gemaakt, samen met alle andere bron bestanden. Zorg ervoor dat u klaar bent met deze implementatie voordat u de resources verwijdert.

Bij het verwijderen van de afbeeldingen galerie-resources, moet u alle installatie kopieën verwijderen voordat u de definitie van de installatie kopie kunt verwijderen die wordt gebruikt om ze te maken. Als u een galerie wilt verwijderen, moet u eerst alle definities van de installatie kopieën in de galerie hebben verwijderd.

Verwijder de sjabloon voor de opbouw functie voor installatie kopieën.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Machtigingen voor toewijzingen, rollen en identiteiten verwijderen
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

De afbeeldings versie ophalen die is gemaakt door de opbouw functie voor afbeeldingen, dit wordt altijd gestart met `0.` en vervolgens de versie van de installatie kopie verwijderen

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


De definitie van de installatie kopie verwijderen.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Verwijder de galerie.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Verwijder de resource groep.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Galerie met gedeelde installatie kopieën van Azure](../shared-image-galleries.md).
