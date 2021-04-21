---
title: Naleving met Azure Policy
description: Ingebouwde beleidsregels toewijzen in Azure Policy om de naleving van uw Azure-containerregisters te controleren
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 62a1fd8d3c996fd3a0bac3cadf77fc7e7ace0ce3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784169"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Naleving van Azure-containerregisters controleren met Azure Policy

[Azure Policy](../governance/policy/overview.md) is een service in Azure die u gebruikt voor het maken, toewijzen en beheren van beleid. Met deze beleidsregels worden verschillende regels en gedrag afgedwongen voor uw resources, zodat deze resources voldoen aan de standaarden en Service Level Agreements in uw bedrijf.

In dit artikel worden ingebouwde beleidsregels voor Azure Container Registry. Gebruik deze beleidsregels om nieuwe en bestaande registers te controleren op naleving.

Er worden geen kosten in rekening gebracht voor het gebruik Azure Policy.

## <a name="built-in-policy-definitions"></a>Ingebouwde beleidsdefinities

De volgende ingebouwde beleidsdefinities zijn specifiek voor Azure Container Registry:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Beleid toewijzen

* Wijs beleid toe met [behulp Azure Portal,](../governance/policy/assign-policy-portal.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md)een [Resource Manager sjabloon](../governance/policy/assign-policy-template.md)of de Azure Policy-SDK's.
* Bereik van een beleidstoewijzing voor een resourcegroep, een abonnement of [een Azure-beheergroep.](../governance/management-groups/overview.md) Beleidstoewijzingen voor containerregisters zijn van toepassing op bestaande en nieuwe containerregisters binnen het bereik.
* Het afdwingen van [beleid op elk](../governance/policy/concepts/assignment-structure.md#enforcement-mode) moment in- of uitschakelen.

> [!NOTE]
> Nadat u een beleid hebt toegewezen of bijgewerkt, duurt het enige tijd voor de toewijzing wordt toegepast op resources in het gedefinieerde bereik. Zie informatie over [beleidsevaluatietriggers.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Naleving van beleid controleren

Krijg toegang tot nalevingsinformatie die wordt gegenereerd door uw beleidstoewijzingen met behulp van de Azure Portal, Azure-opdrachtregelprogramma's of de Azure Policy-SDK's. Zie Nalevingsgegevens [van Azure-resources op halen voor meer informatie.](../governance/policy/how-to/get-compliance-data.md)

Wanneer een resource niet-compatibel is, zijn er veel mogelijke redenen. Zie Niet-naleving bepalen om te bepalen waarom of om de verantwoordelijke [wijziging te vinden.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Naleving van het beleid in de portal:

1. Selecteer **Alle services** en zoek naar **Beleid.**
1. Selecteer **Naleving.**
1. Gebruik de filters om nalevingsvereisten te beperken of om te zoeken naar beleidsregels.

    ![Naleving van het beleid in de portal](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Selecteer een beleid om samengevoegde nalevingsdetails en -gebeurtenissen te controleren. Selecteer desgewenst een specifiek register voor resource-naleving.

### <a name="policy-compliance-in-the-azure-cli"></a>Naleving van het beleid in de Azure CLI

U kunt ook de Azure CLI gebruiken om nalevingsgegevens op te halen. Gebruik bijvoorbeeld de opdracht [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) in de CLI om de beleids-ID's op te halen van de Azure Container Registry die worden toegepast:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Voorbeelduitvoer:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Voer vervolgens [az policy state list uit om](/cli/azure/policy/state#az_policy_state_list) de nalevingstoestand in JSON-indeling te retourneren voor alle resources onder een specifieke beleids-id:

```azurecli
az policy state list \
  --resource <policyID>
```

Of voer [az policy state list uit om](/cli/azure/policy/state#az_policy_state_list) de nalevingstoestand in JSON-indeling van een specifieke registerresource te retourneren, zoals *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure Policy [definities](../governance/policy/concepts/definition-structure.md) en [effecten](../governance/policy/concepts/effects.md).

* Maak een [aangepaste beleidsdefinitie](../governance/policy/tutorials/create-custom-policy-definition.md).

* Meer informatie over [governancemogelijkheden](../governance/index.yml) in Azure.
