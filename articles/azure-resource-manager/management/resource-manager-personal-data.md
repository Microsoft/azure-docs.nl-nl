---
title: Persoonsgegevens
description: Meer informatie over het beheren van persoonlijke gegevens die zijn gekoppeld aan Azure Resource Manager bewerkingen.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 1e531f7cd9992536bcc191637111761c5bbdefa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693697"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Persoonlijke gegevens beheren die zijn gekoppeld aan Azure Resource Manager

Als u wilt voor komen dat gevoelige informatie zichtbaar wordt, verwijdert u de persoonlijke gegevens die u mogelijk hebt verstrekt in implementaties, resource groepen of tags. Azure Resource Manager biedt bewerkingen waarmee u persoonlijke gegevens kunt beheren die u mogelijk hebt verstrekt in implementaties, resource groepen of tags.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Persoonlijke gegevens verwijderen in de implementatie geschiedenis

Voor implementaties worden in Resource Manager parameter waarden en status berichten in de implementatie geschiedenis bewaard. Deze waarden blijven behouden totdat u de implementatie uit de geschiedenis verwijdert. Als u wilt zien of u persoons gegevens in deze waarden hebt ingevoerd, vermeldt u de implementaties. Als u persoonlijke gegevens vindt, verwijdert u de implementaties uit de geschiedenis.

Als u **implementaties** in de geschiedenis wilt weer geven, gebruikt u:

* [Lijst op resource groep](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [AZ-implementatie groeps lijst](/cli/azure/deployment/group#az_deployment_group_list)

Als u **implementaties** uit de geschiedenis wilt verwijderen, gebruikt u:

* [Verwijderen](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [AZ-implementatie groep verwijderen](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Persoonlijke gegevens verwijderen uit de namen van resource groepen

De naam van de resource groep blijft behouden totdat u de resource groep verwijdert. Als u wilt zien of u persoons gegevens in de namen hebt ingevoerd, vermeldt u de resource groepen. Als u persoonlijke gegevens vindt, [verplaatst u de resources](move-resource-group-and-subscription.md) naar een nieuwe resource groep en verwijdert u de resource groep met persoons gegevens in de naam.

Als u **resource groepen** wilt weer geven, gebruikt u:

* [List](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [AZ Group List](/cli/azure/group#az-group-list)

Als u **resource groepen** wilt verwijderen, gebruikt u:

* [Verwijderen](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Persoonlijke gegevens in Tags verwijderen

Tags namen en waarden blijven behouden totdat u de tag verwijdert of wijzigt. Als u wilt zien of u persoons gegevens in de tags hebt ingevoerd, geeft u de tags weer. Als u persoonlijke gegevens vindt, verwijdert u de tags.

Als u **Tags** wilt weer geven, gebruikt u:

* [List](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [AZ-label lijst](/cli/azure/tag#az-tag-list)

Als u **Tags** wilt verwijderen, gebruikt u:

* [Verwijderen](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [AZ tag Delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Volgende stappen
* Zie voor een overzicht van Azure Resource Manager de functie [Wat is Resource Manager?](overview.md)