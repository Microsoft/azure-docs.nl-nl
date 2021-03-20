---
title: Naleving met behulp van Azure Policy
description: Wijs ingebouwde beleids regels toe aan Azure Policy om de naleving van uw resources van de Azure signalerings service te controleren.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 018033d3a6123948191a7261f5a1ee2ae526e25a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89295013"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Controleer de naleving van Azure signalerings service bronnen met behulp van Azure Policy

[Azure Policy](../governance/policy/overview.md) is een service in azure die u gebruikt om beleids regels te maken, toe te wijzen en te beheren. Met deze beleidsregels worden verschillende regels en gedrag afgedwongen voor uw resources, zodat deze resources voldoen aan de standaarden en Service Level Agreements in uw bedrijf.

In dit artikel worden ingebouwde beleids regels (preview-versie) geïntroduceerd voor de Azure signalerings service. Gebruik dit beleid om nieuwe en bestaande signalerings bronnen te controleren op naleving.

Er worden geen kosten in rekening gebracht voor het gebruik van Azure Policy.

## <a name="built-in-policy-definitions"></a>Ingebouwde beleids definities

De volgende ingebouwde beleids definities zijn specifiek voor de Azure signalerings service:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Beleids definities toewijzen

* Beleids definities toewijzen met behulp van de [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure cli](../governance/policy/assign-policy-azurecli.md), een [Resource Manager-sjabloon](../governance/policy/assign-policy-template.md)of de Azure Policy sdk's.
* Bereik een beleids toewijzing aan een resource groep, een abonnement of een [Azure-beheer groep](../governance/management-groups/overview.md). De toewijzingen van seingevings beleid zijn van toepassing op bestaande en nieuwe seingevings bronnen binnen het bereik.
* Het [afdwingen van beleid](../governance/policy/concepts/assignment-structure.md#enforcement-mode) op elk gewenst moment in-of uitschakelen.

> [!NOTE]
> Wanneer u een beleid toewijst of bijwerkt, kan het enige tijd duren voordat de toewijzing wordt toegepast op resources in het gedefinieerde bereik. Zie informatie over [beleids evaluatie triggers](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Naleving van beleid controleren

Toegang tot nalevings gegevens die zijn gegenereerd door uw beleids toewijzingen met behulp van de Azure Portal, Azure-opdracht regel Programma's of de Azure Policy Sdk's. Zie [nalevings gegevens van Azure-resources ophalen](../governance/policy/how-to/get-compliance-data.md)voor meer informatie.

Wanneer een resource niet-compatibel is, zijn er veel mogelijke redenen. Zie [niet-naleving bepalen](../governance/policy/how-to/determine-non-compliance.md)om de reden te bepalen of de verantwoordelijke wijziging te vinden.

### <a name="policy-compliance-in-the-portal"></a>Beleids naleving in de portal:

1. Selecteer **alle services** en zoek naar **beleid**.
1. Selecteer **naleving**.
1. De filters gebruiken om de nalevings status te beperken of om te zoeken naar beleid
   
    [![Naleving van beleid in portal ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Selecteer een beleid om statistische details en gebeurtenissen van de naleving te controleren. Selecteer desgewenst een specifieke Signa lering voor bron compatibiliteit.

### <a name="policy-compliance-in-the-azure-cli"></a>Beleids naleving in de Azure CLI

U kunt ook de Azure CLI gebruiken om compatibiliteits gegevens op te halen. Gebruik bijvoorbeeld de opdracht [AZ Policy Assignment List](/cli/azure/policy/assignment#az-policy-assignment-list) in de CLI om de beleids-id's op te halen van de Azure signalerings service-beleids regels die worden toegepast:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Voorbeelduitvoer:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Voer vervolgens de [lijst AZ policy state](/cli/azure/policy/state#az-policy-state-list) uit om de nalevings status van JSON-indeling te retour neren voor alle resources onder een specifieke resource groep:

```azurecli
az policy state list --g <resourceGroup>
```

Of Voer [AZ policy state List](/cli/azure/policy/state#az-policy-state-list) uit om de nalevings status in JSON-indeling te retour neren van een specifieke signalerings Bron:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure Policy [definities](../governance/policy/concepts/definition-structure.md) en [effecten](../governance/policy/concepts/effects.md)

* Een [aangepaste beleids definitie](../governance/policy/tutorials/create-custom-policy-definition.md) maken

* Meer informatie over de beheer [mogelijkheden](../governance/index.yml) van Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/