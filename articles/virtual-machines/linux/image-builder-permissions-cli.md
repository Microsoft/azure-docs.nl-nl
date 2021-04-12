---
title: Service machtigingen van Azure Image Builder configureren met behulp van Azure CLI
description: Vereisten configureren voor de Azure VM Image Builder-service, inclusief machtigingen en bevoegdheden met Azure CLI
author: cynthn
ms.author: danis
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: eb4fe102407bf519c9253ac7da39178ad8cacb0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607514"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Service machtigingen van Azure Image Builder configureren met behulp van Azure CLI

Wanneer u zich registreert voor de (AIB), verleent dit de AIB-service toestemming voor het maken, beheren en verwijderen van een staging-resource groep (IT_ *), en hebt u rechten om resources toe te voegen, die vereist zijn voor het bouwen van de installatie kopie. Dit wordt gedaan door een AIB-SPN (Service Principal Name) die tijdens een geslaagde registratie beschikbaar wordt gesteld in uw abonnement.

Als u wilt dat Azure VM Image Builder installatie kopieën naar de beheerde installatie kopieën of naar een galerie met gedeelde installatie kopieën kan distribueren, moet u een door de gebruiker toegewezen Azure-identiteit maken die machtigingen heeft voor het lezen en schrijven van installatie kopieën. Als u toegang hebt tot Azure Storage, hebt u machtigingen nodig om persoonlijke of open bare containers te lezen.

U moet machtigingen en bevoegdheden instellen voordat u een installatie kopie bouwt. In de volgende secties vindt u informatie over het configureren van mogelijke scenario's met Azure CLI.

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>De functies registreren

Eerst moet u zich registreren voor de Azure Image Builder-service. Registratie verleent de service toestemming om een tijdelijke resource groep te maken, te beheren en te verwijderen. De service heeft ook rechten om resources toe te voegen aan de groep die is vereist voor het bouwen van de installatie kopie.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken in azure

Voor Azure Image Builder moet u een door de [gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)maken. De Azure Image Builder maakt gebruik van de door de gebruiker toegewezen beheerde identiteit voor het lezen van afbeeldingen, het schrijven van afbeeldingen en toegang tot Azure Storage-accounts. U verleent de identiteits machtiging om specifieke acties in uw abonnement uit te voeren.

> [!NOTE]
> Voorheen gebruikte Azure Image Builder de Azure Image Builder Service Principal Name (SPN) om machtigingen toe te kennen aan de afbeeldings resource groepen. Het gebruik van de SPN wordt afgeschaft. Gebruik in plaats daarvan een door de gebruiker toegewezen beheerde identiteit.

In het volgende voor beeld ziet u hoe u een door de gebruiker toegewezen beheerde identiteit maakt. Vervang de instellingen van de tijdelijke aanduiding om uw variabelen in te stellen.

| Instelling | Beschrijving |
|---------|-------------|
| \<Resource group\> | De resource groep waar de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Zie voor meer informatie over door de gebruiker toegewezen identiteiten de door de [gebruiker toegewezen beheerde identiteits](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) documentatie over het maken van een identiteit.

## <a name="allow-image-builder-to-distribute-images"></a>Opbouw functie voor afbeeldingen toestaan om afbeeldingen te distribueren

Voor Azure Image Builder voor het distribueren van installatie kopieën (Managed images/gedeelde afbeeldingen galerie) moet de Azure Image Builder-service de installatie kopieën in deze resource groepen kunnen injecteren. Als u de vereiste machtigingen wilt verlenen, moet u een door de gebruiker toegewezen beheerde identiteit maken en deze rechten verlenen aan de resource groep waar de installatie kopie is gebouwd. Azure Image Builder heeft **geen** machtiging voor toegang tot resources in andere resource groepen in het abonnement. U moet expliciete acties uitvoeren om te voor komen dat uw builds mislukken.

U hoeft de door de gebruiker toegewezen beheerde identiteits bijdrage niet toe te kennen aan de resource groep voor het distribueren van installatie kopieën. De door de gebruiker toegewezen beheerde identiteit heeft echter de volgende Azure- `Actions` machtigingen nodig in de distributie resource groep:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Als u naar een galerie met gedeelde installatie kopieën wilt distribueren, hebt u ook het volgende nodig:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Machtiging voor het aanpassen van bestaande installatie kopieën

Voor Azure Image Builder om installatie kopieën te bouwen op basis van aangepaste installatie kopieën (beheerde installatie kopieën/gedeelde afbeeldingen galerie), moet de Azure Image Builder-service de installatie kopieën in deze resource groepen kunnen lezen. Als u de vereiste machtigingen wilt verlenen, moet u een door de gebruiker toegewezen beheerde identiteit maken en deze rechten verlenen aan de resource groep waar de installatie kopie zich bevindt.

Bouwen op basis van een bestaande aangepaste installatie kopie:

```Actions
Microsoft.Compute/galleries/read
```

Bouwen op basis van een bestaande versie van de galerie met gedeelde afbeeldingen:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Machtiging voor het aanpassen van installatie kopieën in uw VNETs

Azure Image Builder biedt de mogelijkheid om een bestaand VNET te implementeren en gebruiken in uw abonnement, zodat u met aanpassing toegang krijgt tot verbonden bronnen.

U hoeft de door de gebruiker toegewezen beheerde identiteits bijdrage niet toe te kennen aan de resource groep om een virtuele machine te implementeren in een bestaand VNET. De door de gebruiker toegewezen beheerde identiteit vereist echter de volgende Azure- `Actions` machtigingen voor de VNET-resource groep:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Een Azure-functie definitie maken

In de volgende voor beelden wordt een Azure-functie definitie gemaakt op basis van de acties die in de vorige secties zijn beschreven. De voor beelden worden toegepast op het niveau van de resource groep. Evalueer en test of de voor beelden nauw keurig genoeg zijn voor uw vereisten. Voor uw scenario moet u het mogelijk verfijnen tot een specifieke galerie met gedeelde afbeeldingen.

Met de afbeeldings acties is lezen en schrijven toegestaan. Bepaal wat geschikt is voor uw omgeving. U kunt bijvoorbeeld een rol maken waarmee Azure Image Builder installatie kopieën kan lezen uit de resource groep *voor beeld-RG-1* en installatie kopieën schrijven naar voor beeld van een resource groep *-RG-2*.

### <a name="custom-image-azure-role-example"></a>Voor beeld van Azure Role voor aangepaste installatie kopie

In het volgende voor beeld wordt een Azure-rol gemaakt voor het gebruik en de distributie van een aangepaste bron afbeelding. Vervolgens kent u de aangepaste rol toe aan de door de gebruiker toegewezen beheerde identiteit voor Azure Image Builder.

Als u de vervanging van waarden in het voor beeld wilt vereenvoudigen, stelt u eerst de volgende variabelen in. Vervang de instellingen van de tijdelijke aanduiding om uw variabelen in te stellen.

| Instelling | Beschrijving |
|---------|-------------|
| \<Subscription ID\> | De id van uw Azure-abonnement |
| \<Resource group\> | Resource groep voor aangepaste installatie kopie |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Voor beeld van bestaand VNET Azure-rol

In het volgende voor beeld wordt een Azure-rol gemaakt voor het gebruik en de distributie van een bestaande VNET-installatie kopie. Vervolgens kent u de aangepaste rol toe aan de door de gebruiker toegewezen beheerde identiteit voor Azure Image Builder.

Als u de vervanging van waarden in het voor beeld wilt vereenvoudigen, stelt u eerst de volgende variabelen in. Vervang de instellingen van de tijdelijke aanduiding om uw variabelen in te stellen.

| Instelling | Beschrijving |
|---------|-------------|
| \<Subscription ID\> | De id van uw Azure-abonnement |
| \<Resource group\> | VNET-resource groep |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Beheerde identiteit gebruiken voor toegang tot Azure Storage

Als u de verificatie met Azure Storage wilt uitvoeren en persoonlijke containers wilt gebruiken, moet Azure Image Builder een door de gebruiker toegewezen beheerde identiteit aan een Azure-naadloos. Azure Image Builder maakt gebruik van de identiteit voor verificatie met Azure Storage.

> [!NOTE]
> Azure Image Builder gebruikt alleen de identiteit bij het verzenden van de afbeeldings sjabloon. De build-VM heeft geen toegang tot de identiteit tijdens het bouwen van de installatie kopie.

Gebruik Azure CLI om de door de gebruiker toegewezen beheerde identiteit te maken.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

In de afbeeldings Builder-sjabloon moet u de door de gebruiker toegewezen beheerde identiteit opgeven.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Vervang de volgende instellingen voor de tijdelijke aanduiding:

| Instelling | Beschrijving |
|---------|-------------|
| \<Region\> | Sjabloon regio |
| \<Resource group\> | Resourcegroep |
| \<Storage account container\> | Containernaam voor het opslagaccount |
| \<Subscription ID\> | Azure-abonnement |

Voor meer informatie over het gebruik van een door de gebruiker toegewezen beheerde identiteit, zie de een [aangepaste installatie kopie maken die gebruikmaakt van een Azure User-Assigned beheerde identiteit voor het naadloos van toegang tot bestanden Azure Storage](./image-builder-user-assigned-identity.md). In de Snelstartgids wordt uitgelegd hoe u de door de gebruiker toegewezen beheerde identiteit kunt maken en configureren voor toegang tot een opslag account.

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure Image Builder](../image-builder-overview.md)voor meer informatie.