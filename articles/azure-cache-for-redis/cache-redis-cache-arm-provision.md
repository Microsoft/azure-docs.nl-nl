---
title: Een Azure Cache voor Redis implementeren met behulp van Azure Resource Manager sjabloon
description: Meer informatie over het gebruik van een Azure Resource Manager sjabloon (ARM-sjabloon) voor het implementeren van een Azure Cache voor Redis resource. Er zijn sjablonen beschikbaar voor algemene scenario's.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 08/18/2020
ms.openlocfilehash: 81940d23ebfcc017455974981649023351b6eeb3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835052"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Quickstart: Een Azure Cache voor Redis maken met behulp van een ARM-sjabloon

Meer informatie over het maken van een Azure Resource Manager sjabloon (ARM-sjabloon) die een Azure Cache voor Redis. De cache kan worden gebruikt met een bestaand opslagaccount om diagnostische gegevens te behouden. U leert ook hoe u definieert welke resources worden geïmplementeerd en hoe u parameters definieert die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen. Momenteel worden diagnostische instellingen gedeeld voor alle caches in dezelfde regio voor een abonnement. Het bijwerken van één cache in de regio is van invloed op alle andere caches in de regio.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Een opslagaccount:** zie Een opslagaccount maken om [er een Azure Storage maken.](../storage/common/storage-account-create.md?tabs=azure-portal) Het opslagaccount wordt gebruikt voor diagnostische gegevens.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

De volgende resources zijn gedefinieerd in de sjabloon:

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Resource Manager voor de nieuwe [Premium-laag](cache-overview.md#service-tiers) zijn ook beschikbaar.

* [Een Premium-exemplaar van Azure Cache voor Redis maken met clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Premium-Azure Cache voor Redis met gegevens persistentie](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Premium-Redis Cache geïmplementeerd in een Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Als u wilt controleren op de nieuwste sjablonen, raadpleegt u [Azure-quickstartsjablonen](https://azure.microsoft.com/documentation/templates/) en zoekt u naar _Azure Cache voor Redis_.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en de sjabloon te openen.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Typ of selecteer de volgende waarden:

    * **Abonnement**: selecteer een Azure-abonnement dat wordt gebruikt om de gegevensshare en de andere resources te maken.
    * **Resourcegroep**: selecteer **Nieuwe maken** om een nieuwe resourcegroep te maken of een bestaande resourcegroep te selecteren.
    * **Locatie**: selecteer een locatie voor de resourcegroep. Het opslagaccount en de Redis-cache moeten zich in dezelfde regio hebben. De Redis-cache gebruikt standaard dezelfde locatie als de resourcegroep. Geef dus dezelfde locatie op als het opslagaccount.
    * **Redis Cache:** voer een naam in voor de Redis-cache.
    * **Bestaand opslagaccount voor diagnostische gegevens:** voer de resource-id van een opslagaccount in. De syntaxis is `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Gebruik de standaardwaarde voor de overige instellingen.
1. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open de Redis-cache die u hebt gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Azure Resource Manager maakt die een Azure Cache voor Redis. Zie Een [web-app](./cache-web-app-arm-with-redis-cache-provision.md)maken plus Azure Cache voor Redis met behulp van een sjabloon voor meer informatie over het maken van een Azure Resource Manager-sjabloon waarmee een Azure-web-app wordt geïmplementeerd met Azure Cache voor Redis.
