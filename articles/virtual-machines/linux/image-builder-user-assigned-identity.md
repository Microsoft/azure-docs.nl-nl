---
title: Een installatie kopie voor een virtuele machine maken en een door de gebruiker toegewezen beheerde identiteit gebruiken om toegang te krijgen tot bestanden in Azure Storage (preview)
description: Maak een installatie kopie van een virtuele machine met behulp van Azure Image Builder, waarmee u bestanden kunt openen die zijn opgeslagen in Azure Storage met door de gebruiker toegewezen beheerde identiteit.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9bcb7a94cdf1d5478db32a22ba6e612a90c53ed9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695376"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Een installatie kopie maken en een door de gebruiker toegewezen beheerde identiteit gebruiken om toegang te krijgen tot bestanden in Azure Storage 

Azure Image Builder ondersteunt het gebruik van scripts of het kopiëren van bestanden vanaf meerdere locaties, zoals GitHub en Azure Storage, enzovoort. Als u deze wilt gebruiken, moeten ze extern toegankelijk zijn voor Azure Image Builder, maar kunt u Azure Storage-blobs beveiligen met SAS-tokens.

In dit artikel wordt beschreven hoe u een aangepaste installatie kopie maakt met behulp van de opbouw functie voor installatie kopieën van Azure VM, waarbij de service een door de [gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) gebruikt voor toegang tot bestanden in azure Storage voor het aanpassen van de installatie kopie, zonder dat u de bestanden openbaar toegankelijk wilt maken of SAS-tokens kunt instellen.

In het onderstaande voor beeld maakt u twee resource groepen, die worden gebruikt voor de aangepaste installatie kopie, en de andere zal fungeren als host voor een Azure Storage account dat een script bestand bevat. Dit simuleert een scenario voor een echt leven, waar u artefacten kunt bouwen, of afbeeldings bestanden in verschillende opslag accounts, buiten de opbouw functie voor installatie kopieën. U maakt een door de gebruiker toegewezen identiteit en verleent vervolgens die lees machtigingen voor het script bestand, maar u kunt geen open bare toegang tot dat bestand instellen. Vervolgens gebruikt u de shell-aanpassings programma om dat script uit het opslag account te downloaden en uit te voeren.


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


## <a name="create-a-resource-group"></a>Een resourcegroep maken

We zullen enkele gegevens herhaaldelijk gebruiken, dus we maken een aantal variabelen om deze informatie op te slaan.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Maak een variabele voor uw abonnements-ID. U kunt dit doen met `az account show | grep id` .

```console
subscriptionID=<Your subscription ID>
```

Maak de resource groepen voor zowel de installatie kopie als de script opslag.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Maak een door de gebruiker toegewezen identiteit en stel machtigingen in voor de resource groep.

De opbouw functie voor installatie kopieën gebruikt de door de [gebruiker gedefinieerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) voor het injecteren van de installatie kopie in de resource groep. In dit voor beeld maakt u een Azure-roldefinitie met de gedetailleerde acties waarmee de installatie kopie kan worden gedistribueerd. De roldefinitie wordt vervolgens toegewezen aan de identiteit van de gebruiker.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Maak de opslag en kopieer het voorbeeld script naar het bestand vanuit GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

Geef een machtiging voor het maken van de installatie kopie voor de resource groep voor de installatie kopie. De `--assignee` waarde is de id van de gebruikers identiteit.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Het voor beeld wijzigen

Down load het bestand example. json en configureer dit met de variabelen die u hebt gemaakt.

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>De installatiekopie maken

Verzend de configuratie van de installatie kopie naar de Azure Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Start het maken van de installatie kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Wacht tot de compilatie is voltooid. Dit kan ongeveer 15 minuten duren.

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een virtuele machine op basis van de installatie kopie. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Nadat de VM is gemaakt, start u een SSH-sessie met de virtuele machine.

```console
ssh aibuser@<publicIp>
```

U ziet dat de installatie kopie is aangepast met een bericht van de dag zodra uw SSH-verbinding tot stand is gebracht.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Opschonen

Wanneer u klaar bent, kunt u de resources verwijderen als ze niet meer nodig zijn.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Zie [probleem oplossing](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)als u problemen hebt met het werken met Azure Image Builder.
