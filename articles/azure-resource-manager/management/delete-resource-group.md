---
title: Resourcegroep en resources verwijderen
description: Beschrijft hoe u resourcegroepen en resources verwijdert. In dit artikel wordt beschreven Azure Resource Manager het verwijderen van resources bij het verwijderen van een resourcegroep. De responscodes worden beschreven en hoe Resource Manager verwerkt om te bepalen of de verwijdering is geslaagd.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 3c062c2f775e145347129f24b201748ee517daf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768665"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager resourcegroep en resource verwijderen

In dit artikel wordt beschreven hoe u resourcegroepen en resources verwijdert. Er wordt beschreven hoe Azure Resource Manager het verwijderen van resources bestelt wanneer u een resourcegroep verwijdert.

## <a name="how-order-of-deletion-is-determined"></a>Hoe de volgorde van de verwijdering wordt bepaald

Wanneer u een resourcegroep verwijdert, bepaalt Resource Manager de volgorde van het verwijderen van resources. Hierbij wordt de volgende volgorde gebruikt:

1. Alle onderliggende (geneste) resources worden verwijderd.

2. Resources die andere resources beheren, worden vervolgens verwijderd. Voor een resource kan de `managedBy` eigenschap zijn ingesteld om aan te geven dat een andere resource deze beheert. Wanneer deze eigenschap is ingesteld, wordt de resource die de andere resource beheert, verwijderd vóór de andere resources.

3. De resterende resources worden verwijderd na de vorige twee categorieën.

Nadat de volgorde is bepaald, Resource Manager voor elke resource een DELETE-bewerking uit. Het wacht tot alle afhankelijkheden zijn voltooien voordat u doorgaat.

Voor synchrone bewerkingen zijn de verwachte geslaagde antwoordcodes:

* 200
* 204
* 404

Voor asynchrone bewerkingen is het verwachte geslaagde antwoord 202. Resource Manager de locatieheader of de azure-async-bewerkingskop bij om de status van de asynchrone verwijderbewerking te bepalen.
  
### <a name="deletion-errors"></a>Verwijderingsfouten

Wanneer een verwijderbewerking een fout retourneert, Resource Manager de DELETE-aanroep opnieuw uitgevoerd. Er worden nieuwe nieuwe proberen voor de statuscodes 5xx, 429 en 408. De tijdsperiode voor opnieuw proberen is standaard 15 minuten.

## <a name="after-deletion"></a>Na verwijdering

Resource Manager een GET-aanroep uit voor elke resource die is geprobeerd te verwijderen. Het antwoord van deze GET-aanroep is naar verwachting 404. Wanneer Resource Manager 404 krijgt, wordt de verwijdering als voltooid gezien. Resource Manager verwijdert u de resource uit de cache.

Als de GET-aanroep voor de resource echter een 200 of 201 retourneert, wordt Resource Manager resource opnieuw gemaakt.

Als de GET-bewerking een fout retourneert, Resource Manager get opnieuw voor de volgende foutcode:

* Minder dan 100
* 408
* 429
* Groter dan 500

Voor andere foutcodes mislukt Resource Manager verwijderen van de resource.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden maken.

## <a name="delete-resource-group"></a>Resourcegroep verwijderen

Gebruik een van de volgende methoden om de resourcegroep te verwijderen.

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

1. Selecteer in [de portal](https://portal.azure.com)de resource die u wilt verwijderen.

1. Selecteer **Verwijderen**. In de volgende schermopname ziet u de beheeropties voor een virtuele machine.

   ![Resource verwijderen](./media/delete-resource-group/delete-resource.png)

1. Bevestig de verwijdering als u daarom wordt gevraagd.

---

## <a name="required-access"></a>Vereiste toegang

Als u een resourcegroep wilt verwijderen, hebt u toegang nodig tot de actie Verwijderen voor de resource **Microsoft.Resources/subscriptions/resourceGroups.** U moet ook verwijderen voor alle resources in de resourcegroep.

Zie Bewerkingen voor [Azure-resourceproviders](../../role-based-access-control/resource-provider-operations.md)voor een lijst met bewerkingen. Zie Ingebouwde Azure-rollen voor een lijst [met ingebouwde rollen.](../../role-based-access-control/built-in-roles.md)

Als u de vereiste toegang hebt, maar de aanvraag voor [](lock-resources.md) verwijderen mislukt, kan dit zijn omdat de resourcegroep is vergrendeld.

## <a name="next-steps"></a>Volgende stappen

* Zie overzicht Resource Manager voor meer [Azure Resource Manager Resource Manager concepten.](overview.md)
* Zie [PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup)Azure [CLI](/cli/azure/group#az_group_delete)en REST API voor [verwijderingsopdrachten.](/rest/api/resources/resourcegroups/delete)
