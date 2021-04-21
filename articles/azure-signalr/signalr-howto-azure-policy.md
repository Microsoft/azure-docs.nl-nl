---
title: Naleving met Azure Policy
description: Wijs ingebouwde beleidsregels toe in Azure Policy om de naleving van uw Azure SignalR Service controleren.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: c8776102602f5bdcf29139d808a6f603cc5c7473
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784569"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Naleving van Azure SignalR Service resources controleren met behulp van Azure Policy

[Azure Policy](../governance/policy/overview.md) is een service in Azure die u gebruikt voor het maken, toewijzen en beheren van beleid. Met deze beleidsregels worden verschillende regels en gedrag afgedwongen voor uw resources, zodat deze resources voldoen aan de standaarden en Service Level Agreements in uw bedrijf.

In dit artikel worden ingebouwde beleidsregels (preview) voor Azure SignalR Service. Gebruik deze beleidsregels om nieuwe en bestaande SignalR-resources te controleren op naleving.

Er worden geen kosten in rekening gebracht voor het gebruik Azure Policy.

## <a name="built-in-policy-definitions"></a>Ingebouwde beleidsdefinities

De volgende ingebouwde beleidsdefinities zijn specifiek voor Azure SignalR Service:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Beleidsdefinities toewijzen

* Wijs beleidsdefinities [toe met behulp Azure Portal](../governance/policy/assign-policy-portal.md), Azure [CLI,](../governance/policy/assign-policy-azurecli.md)een [Resource Manager sjabloon](../governance/policy/assign-policy-template.md)of de Azure Policy-SDK's.
* Bereik van een beleidstoewijzing voor een resourcegroep, een abonnement of [een Azure-beheergroep.](../governance/management-groups/overview.md) SignalR-beleidstoewijzingen zijn van toepassing op bestaande en nieuwe SignalR-resources binnen het bereik.
* Het afdwingen van [beleid op elk](../governance/policy/concepts/assignment-structure.md#enforcement-mode) moment in- of uitschakelen.

> [!NOTE]
> Nadat u een beleid hebt toegewezen of bijgewerkt, duurt het enige tijd voor de toewijzing wordt toegepast op resources in het gedefinieerde bereik. Zie informatie over [beleidsevaluatietriggers.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Naleving van beleid controleren

Krijg toegang tot nalevingsinformatie die wordt gegenereerd door uw beleidstoewijzingen met behulp van de Azure Portal, Azure-opdrachtregelprogramma's of de Azure Policy-SDK's. Zie Nalevingsgegevens [van Azure-resources op halen voor meer informatie.](../governance/policy/how-to/get-compliance-data.md)

Wanneer een resource niet-compatibel is, zijn er veel mogelijke redenen. Zie Niet-naleving bepalen om de reden of te bepalen waarom de wijziging [verantwoordelijk is.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Naleving van het beleid in de portal:

1. Selecteer **Alle services** en zoek naar **Beleid.**
1. Selecteer **Naleving.**
1. De filters gebruiken om nalevingsvereisten te beperken of om te zoeken naar beleidsregels
   
    [![Naleving van het beleid in de portal ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Selecteer een beleid om samengevoegde nalevingsdetails en gebeurtenissen te controleren. Selecteer desgewenst een specifieke SignalR voor resource-naleving.

### <a name="policy-compliance-in-the-azure-cli"></a>Naleving van beleid in de Azure CLI

U kunt ook de Azure CLI gebruiken om nalevingsgegevens op te halen. Gebruik bijvoorbeeld de opdracht [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) in de CLI om de beleids-ID's op te halen van de Azure SignalR Service beleidsregels die worden toegepast:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Voorbeelduitvoer:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Voer vervolgens [az policy state list uit om](/cli/azure/policy/state#az_policy_state_list) de nalevingstoestand met JSON-indeling te retourneren voor alle resources onder een specifieke resourcegroep:

```azurecli
az policy state list --g <resourceGroup>
```

Of voer [az policy state list uit om](/cli/azure/policy/state#az_policy_state_list) de nalevingstoestand met JSON-indeling van een specifieke SignalR-resource te retourneren:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure Policy [en](../governance/policy/concepts/definition-structure.md) [effecten](../governance/policy/concepts/effects.md)

* Een aangepaste [beleidsdefinitie maken](../governance/policy/tutorials/create-custom-policy-definition.md)

* Meer informatie over [governancemogelijkheden](../governance/index.yml) in Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/