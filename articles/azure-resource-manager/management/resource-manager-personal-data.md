---
title: Persoonsgegevens
description: Meer informatie over het beheren van persoonsgegevens die zijn gekoppeld Azure Resource Manager bewerkingen.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 9087d3e46f38aab3de7774ea341ebd9cbc2d7d1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785951"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Persoonlijke gegevens beheren die zijn gekoppeld aan Azure Resource Manager

Verwijder persoonlijke gegevens die u mogelijk hebt opgegeven in implementaties, resourcegroepen of tags om te voorkomen dat gevoelige informatie beschikbaar wordt gesteld. Azure Resource Manager biedt bewerkingen voor het beheren van persoonsgegevens die u mogelijk hebt opgegeven in implementaties, resourcegroepen of tags.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Persoonlijke gegevens in de implementatiegeschiedenis verwijderen

Voor implementaties behoudt Resource Manager parameterwaarden en statusberichten in de implementatiegeschiedenis. Deze waarden blijven behouden totdat u de implementatie uit de geschiedenis verwijdert. Als u wilt zien of u persoonsgegevens in deze waarden hebt opgegeven, vermeldt u de implementaties. Als u persoonsgegevens vindt, verwijdert u de implementaties uit de geschiedenis.

Als u **implementaties** in de geschiedenis wilt bekijken, gebruikt u:

* [Lijst op resourcegroep](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list)

Als u **implementaties uit de** geschiedenis wilt verwijderen, gebruikt u:

* [Verwijderen](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Persoonlijke gegevens in resourcegroepnamen verwijderen

De naam van de resourcegroep blijft bestaan totdat u de resourcegroep verwijdert. Als u wilt zien of u persoonsgegevens in de namen hebt opgegeven, vermeldt u de resourcegroepen. Als u persoonsgegevens vindt, [verplaatst u de resources](move-resource-group-and-subscription.md) naar een nieuwe resourcegroep en verwijdert u de resourcegroep met persoonsgegevens in de naam.

Als u **resourcegroepen wilt opslijst,** gebruikt u:

* [List](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az_group_list)

Als u **resourcegroepen wilt verwijderen,** gebruikt u:

* [Verwijderen](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az_group_delete)

## <a name="delete-personal-data-in-tags"></a>Persoonlijke gegevens in tags verwijderen

Tagsnamen en -waarden blijven behouden totdat u de tag verwijdert of wijzigt. Als u wilt zien of u persoonsgegevens hebt opgegeven in de tags, vermeldt u de tags. Als u persoonsgegevens vindt, verwijdert u de tags.

Als u **tags wilt opslijst,** gebruikt u:

* [List](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az_tag_list)

Als u **tags wilt verwijderen,** gebruikt u:

* [Verwijderen](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az_tag_delete)

## <a name="next-steps"></a>Volgende stappen
* Zie Wat is Azure Resource Manager? voor een overzicht [van Resource Manager?](overview.md)