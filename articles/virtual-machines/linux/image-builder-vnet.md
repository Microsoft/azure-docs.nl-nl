---
title: Azure Image Builder voor Linux-Vm's gebruiken om toegang te krijgen tot een bestaand Azure VNET (preview-versie)
description: Linux VM-installatie kopieën maken met Azure Image Builder toegang tot een bestaand Azure VNET
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 500ddec9b84f9d73db45ddb4b7f5a8486a48d3e5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565305"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Azure Image Builder voor Linux-Vm's gebruiken om toegang te krijgen tot een bestaand Azure VNET

In dit artikel leest u hoe u de Azure Image Builder kunt gebruiken om een aangepaste Linux-installatie kopie te maken die toegang heeft tot bestaande resources op een VNET. De build-VM die u maakt, wordt geïmplementeerd in een nieuw of bestaand VNET dat u in uw abonnement hebt opgegeven. Wanneer u een bestaand Azure VNET gebruikt, is voor de Azure Image Builder-service geen open bare netwerk verbinding vereist.

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>De functies registreren

Eerst moet u zich registreren voor de Azure Image Builder-service. Registratie verleent de service toestemming om een tijdelijke resource groep te maken, te beheren en te verwijderen. De service heeft ook rechten om resources toe te voegen aan de groep die is vereist voor het bouwen van de installatie kopie.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Variabelen en machtigingen instellen 

U zult een aantal gegevens herhaaldelijk gebruiken. Maak enkele variabelen om deze informatie op te slaan.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Maak de resourcegroep.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Netwerken configureren

Als u geen bestaande VNET\Subnet\NSG hebt, gebruikt u het volgende script om er een te maken.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Regel voor netwerk beveiligings groep toevoegen

Met deze regel kan de Azure Image Builder-verbinding worden load balancer naar de proxy-VM. Poort 60001 is voor Linux OSs en poort 60000 is voor Windows OSs. De proxy-VM maakt verbinding met de build-VM met behulp van poort 22 voor Linux OSs of poort 5986 voor Windows OSs.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Beleid voor privé-Services uitschakelen op subnet

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Zie [Azure Image Builder service-netwerk opties](image-builder-networking.md)voor meer informatie over het gebruik van Image Builder-netwerken.

## <a name="modify-the-example-template-and-create-role"></a>De voorbeeld sjabloon wijzigen en een rol maken

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Machtigingen voor de resource groep instellen

De opbouw functie voor installatie kopieën gebruikt de door de [gebruiker gedefinieerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) voor het injecteren van de installatie kopie in de galerie met gedeelde installatie kopieën van Azure (SIG). In dit voor beeld maakt u een Azure-roldefinitie met de gedetailleerde acties waarmee de installatie kopie kan worden gedistribueerd naar de SIG. De roldefinitie wordt vervolgens toegewezen aan de identiteit van de gebruiker.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

In plaats van de opbouw functie voor installatie kopieën te verlenen lagere granulatie en verhoogde bevoegdheden, kunt u twee rollen maken. Een voor beeld van de Builder-machtigingen voor het maken van een installatie kopie, het andere is de mogelijkheid om de build-VM te koppelen en te load balancer aan uw VNET.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Zie [Azure Image Builder-service machtigingen configureren met Azure cli](image-builder-permissions-cli.md) of [Azure Image Builder-service machtigingen configureren met Power shell](image-builder-permissions-powershell.md)voor meer informatie over machtigingen.

## <a name="create-the-image"></a>De installatiekopie maken

Verzend de configuratie van de installatie kopie naar de Azure Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Start het maken van de installatie kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

Het maken van de installatie kopie en het repliceren naar beide regio's kan enige tijd duren. Wacht tot dit onderdeel is voltooid voordat u doorgaat met het maken van een virtuele machine.


## <a name="create-the-vm"></a>De VM maken

Maak een virtuele machine op basis van de installatie kopie versie die is gemaakt door de opbouw functie voor installatie kopieën van Azure.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Maak via SSH verbinding met de virtuele machine.

```bash
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

Als u de versie van de installatie kopie nu wilt aanpassen om een nieuwe versie van dezelfde afbeelding te maken, slaat u de volgende stappen over en gaat u verder met het [gebruik van Azure Image Builder om een andere versie van de installatie kopie te maken](image-builder-gallery-update-image-version.md).


In het volgende voor beeld wordt de installatie kopie die is gemaakt, samen met alle andere bron bestanden verwijderd. Zorg ervoor dat u klaar bent met deze implementatie voordat u de resources verwijdert.

Bij het verwijderen van de afbeeldingen galerie-resources, moet u alle installatie kopieën verwijderen voordat u de definitie van de installatie kopie kunt verwijderen die wordt gebruikt om ze te maken. Als u een galerie wilt verwijderen, moet u eerst alle definities van de installatie kopieën in de galerie hebben verwijderd.

Verwijder de sjabloon voor de opbouw functie voor installatie kopieën.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Machtigingen voor toewijzingen, rollen en identiteiten verwijderen
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Verwijder de resource groep.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Als u een VNET hebt gemaakt voor deze Quick Start, kunt u het VNET verwijderen als het niet meer wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Galerie met gedeelde installatie kopieën van Azure](../shared-image-galleries.md).
