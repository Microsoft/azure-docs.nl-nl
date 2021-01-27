---
title: Een nieuwe installatie kopie versie maken op basis van een bestaande installatie kopie versie met behulp van Azure Image Builder (preview)
description: Maak een nieuwe VM-installatie kopie versie van een bestaande installatie kopie versie met behulp van Azure image builder in Windows.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 4779abfa92876c0d5a9b045963778a9d2440bf3f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878745"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-windows"></a>Voor beeld: een nieuwe VM-installatie kopie maken van een bestaande installatie kopie versie met behulp van Azure image builder in Windows

In dit artikel wordt beschreven hoe u een bestaande versie van een installatie kopie in een [Galerie met gedeelde afbeeldingen](../shared-image-galleries.md)kunt maken, hoe u deze kunt bijwerken en hoe u deze publiceert als een nieuwe installatie kopie versie naar de galerie.

Er wordt een voor beeld van een JSON-sjabloon gebruikt voor het configureren van de installatie kopie. Het JSON-bestand dat we gebruiken, is hier: [helloImageTemplateforSIGfromWinSIG.jsop](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
```

Als ze niet zijn geregistreerd, voert u de volgende handelingen uit:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Variabelen en machtigingen instellen

Als u [een installatie kopie maken hebt gebruikt en naar een galerie met gedeelde installatie kopieën hebt gedistribueerd](image-builder-gallery.md) om uw gedeelde galerie met installatie kopieën te maken, hebt u de variabelen die we nodig hebben, al gemaakt. Als dat niet het geval is, moet u een aantal variabelen instellen die voor dit voor beeld moeten worden gebruikt.

Voor de preview-versie kan de opbouw functie voor afbeeldingen alleen het maken van aangepaste installatie kopieën in dezelfde resource groep als de door de bron beheerde installatie kopie worden ondersteund. Werk de naam van de resource groep in dit voor beeld bij naar dezelfde resource groep als de door de bron beheerde installatie kopie.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Maak een variabele voor uw abonnements-ID. U kunt dit doen met `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Down load de versie van de installatie kopie die u wilt bijwerken.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Een door de gebruiker toegewezen identiteit maken en machtigingen instellen voor de resource groep
Zoals u in het vorige voor beeld de gebruikers identiteit hebt ingesteld, hoeft u alleen de resource-ID op te halen. deze wordt vervolgens toegevoegd aan de sjabloon.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Als u al beschikt over uw eigen gedeelde galerie met installatie kopieën en u het vorige voor beeld niet hebt gevolgd, moet u machtigingen voor de opbouw functie voor installatie kopieën toewijzen om toegang te krijgen tot de resource groep, zodat deze toegang heeft tot de galerie. Raadpleeg de stappen in het voor beeld [een installatie kopie maken en distribueren naar een galerie met gedeelde installatie kopieën](image-builder-gallery.md) .


## <a name="modify-helloimage-example"></a>HelloImage-voor beeld wijzigen
U kunt het voor beeld bekijken dat we graag gebruiken door het JSON-bestand hier te openen: [helloImageTemplateforSIGfromSIG.jsin](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) samen met de [verwijzing naar de afbeeldings Builder-sjabloon](../linux/image-builder-json.md). 


Down load het. json-voor beeld en configureer dit met de variabelen. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>De installatiekopie maken

Verzend de configuratie van de installatie kopie naar de VM Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Start het maken van de installatie kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Wacht totdat de installatie kopie is gemaakt en replicatie voordat u verdergaat met de volgende stap.


## <a name="create-the-vm"></a>De VM maken

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>De aanpassing controleren
Maak een Extern bureaublad verbinding met de virtuele machine met behulp van de gebruikers naam en het wacht woord die u hebt ingesteld tijdens het maken van de virtuele machine. Open een opdracht prompt in de virtuele machine en typ het volgende:

```console
dir c:\
```

Nu ziet u twee mappen:
- `buildActions` die in de eerste versie van de installatie kopie is gemaakt.
- `buildActions2` dat is gemaakt als onderdeel van het bijwerken van de eerste versie van de installatie kopie om de versie van de tweede installatie kopie te maken.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de onderdelen van het JSON-bestand dat in dit artikel wordt gebruikt [Image Builder-sjabloon verwijzing](../linux/image-builder-json.md).