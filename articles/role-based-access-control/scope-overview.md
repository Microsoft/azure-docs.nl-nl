---
title: Bereik voor Azure RBAC
description: Meer informatie over de reik wijdte van Azure op rollen gebaseerd toegangs beheer (Azure RBAC) en het bepalen van het bereik van een resource.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: deee42c46c9b08bb265c972695b9319413d4fcb1
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555916"
---
# <a name="understand-scope-for-azure-rbac"></a>Bereik voor Azure RBAC

*Scope* is de set resources waarop toegang van toepassing is. Wanneer u een rol toewijst, is het belang rijk om inzicht te krijgen in de reik wijdte van het bereik, zodat u alleen een beveiligings-principal kunt toewijzen die echt nodig is. Door het bereik te beperken, kunt u beperken welke bronnen risico lopen als de beveiligings-principal ooit is aangetast.

## <a name="scope-levels"></a>Bereik niveaus

In azure kunt u een bereik op vier niveaus opgeven: [beheer groep](../governance/management-groups/overview.md), abonnement, [resource groep](../azure-resource-manager/management/overview.md#resource-groups)en resource. Bereiken zijn gestructureerd in een bovenliggende/onderliggende relatie. Met elk hiërarchie niveau wordt het bereik specifiek gemaakt. U kunt rollen toewijzen aan elk van deze bereikniveaus. Het niveau dat u selecteert, bepaalt hoe breed de rol wordt toegepast. Lagere niveaus nemen rolmachtigingen over van hogere niveaus. 

![Bereik voor een roltoewijzing](./media/scope-overview/rbac-scope-no-label.png)

Beheer groepen zijn een niveau van scope boven abonnementen, maar beheer groepen ondersteunen complexere hiërarchieën. Het volgende diagram toont een voor beeld van een hiërarchie van beheer groepen en abonnementen die u kunt definiëren. Zie [Wat zijn Azure-beheer groepen?](../governance/management-groups/overview.md)voor meer informatie over beheer groepen.

![Beheer groep en abonnements hiërarchie](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Bereik indeling

Als u rollen toewijst met behulp van de opdracht regel, moet u het bereik opgeven. Voor opdracht regel Programma's is bereik een mogelijk lange teken reeks die het exacte bereik van de roltoewijzing identificeert. In de Azure Portal wordt deze scope doorgaans vermeld als de *resource-id*.

Het bereik bestaat uit een reeks id's gescheiden door het slash-teken (/). U kunt deze teken reeks beschouwen als uitdrukken van de volgende hiërarchie, waarbij tekst zonder tijdelijke aanduidingen een `{}` vaste id is:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` is de ID van het abonnement dat moet worden gebruikt (een GUID).
- `{resourcesGroupName}` is de naam van de resource groep die de container bevat.
- `{providerName}` is de naam van de [resource provider](../azure-resource-manager/management/azure-services-resource-providers.md) die de resource verwerkt `{resourceType}` en `{resourceSubType*}` identificeert vervolgens meer niveaus binnen die resource provider.
- `{resourceName}` is het laatste deel van de teken reeks waarmee een specifieke resource wordt geïdentificeerd.

Beheer groepen zijn een niveau boven abonnementen en hebben het breedste (minst specifieke) bereik. Roltoewijzingen op dit niveau zijn van toepassing op abonnementen in de beheer groep. Het bereik van een beheer groep heeft de volgende indeling:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Voor beelden van scopes

> [!div class="mx-tableFixed"]
> | Bereik | Voorbeeld |
> | --- | --- |
> | Beheergroep | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Abonnement | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resourcegroep | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Resource | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Het bereik van een resource bepalen

Het is tamelijk eenvoudig om het bereik voor een beheer groep, abonnement of resource groep te bepalen. U hoeft alleen de naam en de abonnements-ID te weten. Het bepalen van het bereik voor een resource duurt echter iets meer werk. Hier volgen enkele manieren waarop u het bereik voor een resource kunt bepalen.

- Open de resource in het Azure Portal en Bekijk de eigenschappen. De resource moet de **resource-id** vermelden waar u het bereik kunt vaststellen. Dit zijn bijvoorbeeld de resource-Id's voor een opslag account.

    ![Resource-Id's voor een opslag account in Azure Portal](./media/scope-overview/scope-resource-id.png)

- Een andere manier is met de Azure Portal een rol tijdelijk toe te wijzen aan het resource bereik en vervolgens [Azure PowerShell](role-assignments-list-powershell.md) of [Azure cli](role-assignments-list-cli.md) te gebruiken om de roltoewijzing weer te geven. In de uitvoer wordt het bereik weer gegeven als een eigenschap.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Volgende stappen

- [Stappen voor het toewijzen van een Azure-rol](role-assignments-steps.md)
- [Resource providers for Azure services](../azure-resource-manager/management/azure-services-resource-providers.md) (Resourceproviders voor Azure-services)
- [Wat zijn Azure-beheergroepen?](../governance/management-groups/overview.md)
