---
title: Azure Image Builder-service machtigingen configureren met Power shell
description: Vereisten voor de Azure VM Image Builder-service configureren met de machtigingen en bevoegdheden met behulp van Power shell
author: danielsollondon
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: b476945677e10c4985d9e16689b109468c9f7c59
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676193"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Azure Image Builder-service machtigingen configureren met Power shell

De Azure Image Builder-service vereist het configureren van machtigingen en bevoegdheden voordat een installatie kopie wordt gemaakt. De volgende secties bevatten informatie over het configureren van mogelijke scenario's met Power shell.

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>De functies registreren

Eerst moet u zich registreren voor de Azure Image Builder-service. Registratie verleent de service toestemming om een tijdelijke resource groep te maken, te beheren en te verwijderen. De service heeft ook rechten om resources toe te voegen aan de groep die is vereist voor het bouwen van de installatie kopie.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken in azure

Voor Azure Image Builder moet u een door de [gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)maken. De Azure Image Builder maakt gebruik van de door de gebruiker toegewezen beheerde identiteit voor het lezen van afbeeldingen, het schrijven van afbeeldingen en toegang tot Azure Storage-accounts. U verleent de identiteits machtiging om specifieke acties in uw abonnement uit te voeren.

> [!NOTE]
> Voorheen gebruikte Azure Image Builder de Azure Image Builder Service Principal Name (SPN) om machtigingen toe te kennen aan de afbeeldings resource groepen. Het gebruik van de SPN wordt afgeschaft. Gebruik in plaats daarvan een door de gebruiker toegewezen beheerde identiteit.

In het volgende voor beeld ziet u hoe u een door de gebruiker toegewezen beheerde identiteit maakt. Vervang de instellingen van de tijdelijke aanduiding om uw variabelen in te stellen.

| Instelling | Beschrijving |
|---------|-------------|
| \<Resource group\> | De resource groep waar de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
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

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Voor beeld van bestaand VNET Azure-rol

In het volgende voor beeld wordt een Azure-rol gemaakt voor het gebruik en de distributie van een bestaande VNET-installatie kopie. Vervolgens kent u de aangepaste rol toe aan de door de gebruiker toegewezen beheerde identiteit voor Azure Image Builder.

Als u de vervanging van waarden in het voor beeld wilt vereenvoudigen, stelt u eerst de volgende variabelen in. Vervang de instellingen van de tijdelijke aanduiding om uw variabelen in te stellen.

| Instelling | Beschrijving |
|---------|-------------|
| \<Subscription ID\> | De id van uw Azure-abonnement |
| \<Resource group\> | VNET-resource groep |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure Image Builder](../image-builder-overview.md)voor meer informatie.