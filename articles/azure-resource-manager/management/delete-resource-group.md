---
title: Resource groep en-resources verwijderen
description: Hierin wordt beschreven hoe u resource groepen en-resources verwijdert. Hierin wordt beschreven hoe Azure Resource Manager het verwijderen van resources ordent bij het verwijderen van een resource groep. Hierin worden de antwoord codes beschreven en hoe Resource Manager deze verwerkt om te bepalen of het verwijderen is geslaagd.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 244d59ffc096b5e219e27fd376b07baecde3670e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587658"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager resource groep en verwijderen van resources

In dit artikel wordt uitgelegd hoe u resource groepen en-resources kunt verwijderen. Hierin wordt beschreven hoe Azure Resource Manager het verwijderen van resources ordent bij het verwijderen van een resource groep.

## <a name="how-order-of-deletion-is-determined"></a>Hoe volg orde van verwijderen wordt bepaald

Wanneer u een resource groep verwijdert, bepaalt Resource Manager de volg orde voor het verwijderen van resources. Er wordt gebruikgemaakt van de volgende volg orde:

1. Alle onderliggende (geneste) resources worden verwijderd.

2. Resources die andere resources beheren, worden vervolgens verwijderd. Een resource kan de `managedBy` eigenschap instellen om aan te geven dat een andere resource deze beheert. Wanneer deze eigenschap is ingesteld, wordt de resource die de andere resource beheert, verwijderd vóór de andere resources.

3. De overige resources worden verwijderd na de vorige twee categorieën.

Nadat de order is vastgesteld, geeft Resource Manager een Verwijder bewerking uit voor elke resource. Er wordt gewacht tot alle afhankelijkheden zijn voltooid voordat u doorgaat.

Voor synchrone bewerkingen zijn de verwachte geslaagde antwoord codes:

* 200
* 204
* 404

Voor asynchrone bewerkingen is de verwachte geslaagde reactie 202. Resource Manager houdt de locatie header of de Azure-async-bewerkings header bij om de status van de asynchrone verwijderings bewerking te bepalen.
  
### <a name="deletion-errors"></a>Verwijderingsfouten

Wanneer een Verwijder bewerking een fout retourneert, probeert Resource Manager de aanroep DELETE opnieuw. Er gebeuren nieuwe pogingen voor de status codes 5xx, 429 en 408. De tijds periode voor opnieuw proberen is standaard 15 minuten.

## <a name="after-deletion"></a>Na verwijdering

Resource Manager geeft een GET-aanroep op voor elke resource die wordt geprobeerd te verwijderen. Het antwoord van deze GET-aanroep wordt verwacht 404. Wanneer Resource Manager een 404-waarde krijgt, beschouwt dit de verwijdering als voltooid. Resource Manager verwijdert de resource uit de cache.

Als de GET-aanroep voor de resource echter een 200 of 201 retourneert, wordt de resource door Resource Manager opnieuw gemaakt.

Als de GET-bewerking een fout retourneert, probeert Resource Manager de volgende fout code op te halen:

* Minder dan 100
* 408
* 429
* Groter dan 500

Voor andere fout codes mislukt de Resource Manager het verwijderen van de resource.

> [!IMPORTANT]
> Het verwijderen van de resource groep is onomkeerbaar.

## <a name="delete-resource-group"></a>Resourcegroep verwijderen

Gebruik een van de volgende methoden om de resource groep te verwijderen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer de resourcegroep die u wilt verwijderen in de [portal](https://portal.azure.com).

1. Selecteer **Resourcegroep verwijderen**.

   ![Resourcegroep verwijderen](./media/delete-resource-group/delete-group.png)

1. Typ de naam van de resourcegroep om het verwijderen te bevestigen

---

## <a name="delete-resource"></a>Resource verwijderen

Gebruik een van de volgende methoden om een resource te verwijderen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de [Portal](https://portal.azure.com)de resource die u wilt verwijderen.

1. Selecteer **Verwijderen**. Op de volgende scherm afbeelding ziet u de beheer opties voor een virtuele machine.

   ![Resource verwijderen](./media/delete-resource-group/delete-resource.png)

1. Bevestig de verwijdering als u daarom wordt gevraagd.

---

## <a name="required-access"></a>Vereiste toegang

Als u een resource groep wilt verwijderen, moet u toegang hebben tot de actie verwijderen voor de resource **micro soft. resources/abonnementen/resourceGroups** . U moet ook verwijderen voor alle resources in de resource groep.

Zie [bewerkingen voor Azure-resource providers](../../role-based-access-control/resource-provider-operations.md)voor een lijst met bewerkingen. Zie [ingebouwde rollen in azure](../../role-based-access-control/built-in-roles.md)voor een lijst met ingebouwde rollen.

Als u de vereiste toegang hebt, maar de aanvraag voor het verwijderen mislukt, kan dat zijn omdat er een [vergren deling](lock-resources.md) is voor de resource groep.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager-overzicht](overview.md)voor meer informatie over de concepten van Resource Manager.
* Zie [Power shell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure cli](/cli/azure/group#az-group-delete)en [rest API](/rest/api/resources/resourcegroups/delete)voor verwijderings opdrachten.
