---
title: 'Quickstart: Gebeurtenissen Azure Cache voor Redis naar een web-eindpunt met PowerShell'
description: Gebruik Azure Event Grid u te abonneren op Azure Cache voor Redis gebeurtenissen, de gebeurtenissen naar een webhook te verzenden en de gebeurtenissen in een webtoepassing te verwerken.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fa1ae9a7a2200944bd5da0211be88ba4955e3d03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833882"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Quickstart: Gebeurtenissen Azure Cache voor Redis naar een web-eindpunt met PowerShell

Azure Event Grid is een gebeurtenisservice voor de cloud. In deze quickstart gebruikt u Azure PowerShell om u te abonneren op Azure Cache voor Redis gebeurtenissen, een gebeurtenis te activeren en de resultaten weer te geven. 

Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Ter vereenvoudiging van deze quickstart verzendt u echter gebeurtenissen naar een web-app waarmee de berichten worden verzameld en weergegeven. Wanneer u de stappen in deze quickstart hebt voltooid, ziet u dat de gebeurtenisgegevens naar de web-app zijn verzonden.

## <a name="setup"></a>Instellen

Voor deze quickstart moet u de nieuwste versie van de Azure PowerShell. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps) als u de toepassing nog moet installeren of een upgrade moet uitvoeren.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht en volg de instructies op `Connect-AzAccount` het scherm om te verifiëren.

```powershell
Connect-AzAccount
```

In dit voorbeeld wordt **westus2 gebruikt** en wordt de selectie op een variabele op opslag voor gebruik in de rest van de tijd.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Event Grid worden geïmplementeerd als afzonderlijke Azure-resources en moeten worden ingericht onder een Azure-resourcegroep. Een resourcegroep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

In het volgende voorbeeld wordt een resourcegroep met de naam **gridResourceGroup** gemaakt op de locatie **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Een instantie van Azure Cache voor Redis maken 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Zie voor meer informatie over het maken van een cache-exemplaar in PowerShell de [Azure PowerShell naslaginformatie.](/powershell/module/az.rediscache/new-azrediscache) 

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op het onderwerp, gaan we het eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

Vervang `<your-site-name>` door een unieke naam voor de web-app. De naam van de web-app moet uniek zijn omdat deze deel uitmaakt van de DNS-vermelding.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

Op de site zouden momenteel geen berichten moeten wijn weergeven.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Lege Event Grid Viewer-site.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Abonneren op uw Azure Cache voor Redis gebeurtenis

In deze stap abonneert u zich op een onderwerp om aan te geven Event Grid welke gebeurtenissen u wilt bijhouden. In het volgende voorbeeld ziet u hoe u zich abonneert op het cache-exemplaar dat u hebt gemaakt en hoe u de URL van uw web-app door geeft als eindpunt voor gebeurtenismeldingen. Het eindpunt voor uw web-app moet het achtervoegsel `/api/updates/` bevatten.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Een gebeurtenis activeren vanuit Azure Cache voor Redis

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Zie de naslaginformatie voor Azure PowerShell [](/powershell/module/az.rediscache/import-azrediscache)importeren in PowerShell. 

U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Bekijk uw web-app om de gebeurtenis te zien die u zojuist hebt verzonden.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent om door te gaan met Azure Cache voor Redis-exemplaar en gebeurtenisabonnement, moet u de resources die u in deze quickstart hebt gemaakt, niet ops schonen. Als u niet wilt doorgaan, gebruikt u de volgende opdracht om de resources te verwijderen die u in deze quickstart hebt gemaakt.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u onderwerpen en gebeurtenisabonnementen maakt, kunt u meer leren over Azure Cache voor Redis gebeurtenissen en wat Event Grid u kunt doen:

- [Reageren op Azure Cache voor Redis gebeurtenissen](cache-event-grid.md)
- [Over Event Grid](../event-grid/overview.md)
