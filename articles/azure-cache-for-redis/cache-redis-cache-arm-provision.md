---
title: Azure cache voor redis implementeren met behulp van Azure Resource Manager sjabloon
description: Meer informatie over het gebruik van een Azure Resource Manager sjabloon (ARM-sjabloon) voor het implementeren van een Azure-cache voor redis-resource. Er worden sjablonen gegeven voor veelvoorkomende scenario's.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92735976"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Snelstartgids: een Azure-cache maken voor redis met een ARM-sjabloon

Meer informatie over het maken van een Azure Resource Manager sjabloon (ARM-sjabloon) die een Azure-cache voor redis implementeert. De cache kan worden gebruikt met een bestaand opslag account om diagnostische gegevens te bewaren. U leert ook hoe u definieert welke resources worden geïmplementeerd en hoe u para meters definieert die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen. Op dit moment worden de diagnostische instellingen gedeeld voor alle caches in dezelfde regio voor een abonnement. Het bijwerken van een cache in de regio is van invloed op alle andere caches in de regio.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Een opslag account**: zie [een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal)als u er een wilt maken. Het opslag account wordt gebruikt voor diagnostische gegevens.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

De volgende resources zijn gedefinieerd in de sjabloon:

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Micro soft. Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Resource Manager-sjablonen voor de nieuwe [Premium-laag](cache-overview.md#service-tiers) zijn ook beschikbaar.

* [Een Premium-exemplaar van Azure Cache voor Redis maken met clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Premium Azure-cache maken voor redis met gegevens persistentie](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Premium-Redis Cache die zijn geïmplementeerd in een Virtual Network maken](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Zie [Azure Quick](https://azure.microsoft.com/documentation/templates/) start-sjablonen en zoek naar _Azure cache voor redis_ om te controleren of er nieuwe sjablonen zijn.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en de sjabloon te openen.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Typ of selecteer de volgende waarden:

    * **Abonnement**: selecteer een Azure-abonnement dat wordt gebruikt om de gegevensshare en de andere resources te maken.
    * **Resourcegroep**: selecteer **Nieuwe maken** om een nieuwe resourcegroep te maken of een bestaande resourcegroep te selecteren.
    * **Locatie**: selecteer een locatie voor de resourcegroep. Het opslag account en de redis-cache moeten zich in dezelfde regio bevinden. De redis-cache maakt standaard gebruik van dezelfde locatie als de resource groep. Geef dus dezelfde locatie op als het opslag account.
    * **Redis cache naam**: Voer een naam in voor de redis-cache.
    * **Bestaand opslag account voor diagnostische gegevens**: Voer de resource-id in van een opslag account. De syntaxis is `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Gebruik de standaardwaarde voor de overige instellingen.
1. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open de redis-cache die u hebt gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie leert u hoe u een Azure Resource Manager sjabloon maakt waarmee een Azure-cache voor redis wordt geïmplementeerd. Zie voor meer informatie over het maken van een Azure Resource Manager sjabloon waarmee een Azure-web-app wordt geïmplementeerd met Azure cache voor redis [een web-app maken plus Azure cache voor redis met behulp van een sjabloon](./cache-web-app-arm-with-redis-cache-provision.md).
