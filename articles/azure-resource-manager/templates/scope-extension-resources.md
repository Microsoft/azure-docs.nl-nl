---
title: Bereik voor uitbreidings resource typen
description: Hierin wordt beschreven hoe u de eigenschap scope gebruikt bij het implementeren van uitbreidings bron typen.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 75c2c8b8409cc9f8e7a8e71965589ece6660607a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179976"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Bereik instellen voor uitbreidings resources in ARM-sjablonen

Een extensie resource is een resource die een andere resource wijzigt. U kunt bijvoorbeeld een rol toewijzen aan een resource. De roltoewijzing is een uitbreidings resource type.

Zie voor een volledige lijst met resource typen voor extensies [resource typen die de mogelijkheden van andere bronnen uitbreiden](../management/extension-resource-types.md).

In dit artikel wordt uitgelegd hoe u het bereik voor een uitbreidings resource type kunt instellen wanneer het wordt geïmplementeerd met een Azure Resource Manager sjabloon (ARM-sjabloon). De eigenschap scope wordt beschreven die beschikbaar is voor uitbreidings resources wanneer deze op een resource wordt toegepast.

> [!NOTE]
> De eigenschap scope is alleen beschikbaar voor uitbreidings resource typen. Als u een ander bereik voor een resource type wilt opgeven dat geen extensie type is, gebruikt u een geneste of gekoppelde implementatie. Zie [implementaties van resource groepen](deploy-to-resource-group.md), [abonnements implementaties](deploy-to-subscription.md), [implementaties van beheer groepen](deploy-to-management-group.md)en [Tenant implementaties](deploy-to-tenant.md)voor meer informatie.

## <a name="apply-at-deployment-scope"></a>Toep assen bij implementatie bereik

Als u een uitbreidings resource type wilt Toep assen op het doel implementatie bereik, voegt u de resource toe aan uw sjabloon, net als bij elk resource type. De beschik bare bereiken zijn [resource groep](deploy-to-resource-group.md), [abonnement](deploy-to-subscription.md), [beheer groep](deploy-to-management-group.md)en [Tenant](deploy-to-tenant.md). Het implementatie bereik moet ondersteuning bieden voor het resource type.

Met de volgende sjabloon wordt een vergren deling geïmplementeerd.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Wanneer het wordt geïmplementeerd in een resource groep, wordt de resource groep vergrendeld.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

In het volgende voor beeld wordt een rol toegewezen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Wanneer het wordt geïmplementeerd in een abonnement, wordt de rol toegewezen aan het abonnement.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Toep assen op resource

Gebruik de eigenschap om een extensie bron toe te passen op een resource `scope` . Stel de eigenschap scope in op de naam van de resource waaraan u de extensie wilt toevoegen. De eigenschap scope is een hoofd eigenschap voor het bron type van de extensie.

In het volgende voor beeld wordt een opslag account gemaakt en wordt er een rol toegepast.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](template-syntax.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
* Zie [problemen met algemene Azure-implementatie fouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatie fouten.
* Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](secure-template-with-sas-token.md).
