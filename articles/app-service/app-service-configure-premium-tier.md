---
title: PremiumV3-laag configureren
description: Meer informatie over het verbeteren van de prestaties van uw web-, mobiele en API-app in Azure App Service door te schalen naar de nieuwe prijs categorie PremiumV3.
keywords: App Service, Azure App Service, schaal, schaalbaar, App Service-abonnement, kosten App Service
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c187a1521762c0b69d42c81f464694729d3c02f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100594230"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>PremiumV3-laag configureren voor Azure App Service

De nieuwe prijs categorie **PremiumV3** biedt snellere processors, SSD-opslag en de verhouding tussen de geheugen en kernen van de bestaande prijs categorieën (dubbele de **PremiumV2** -laag). Met het prestatie voordeel kunt u geld besparen door uw apps op minder exemplaren uit te voeren. In dit artikel leert u hoe u een app maakt in **PremiumV3** -laag of een app omhoog schaalt naar **PremiumV3** -laag.

## <a name="prerequisites"></a>Vereisten

Als u een app wilt schalen naar **PremiumV3**, moet u beschikken over een Azure app service-app die wordt uitgevoerd in een lagere prijs categorie dan **PremiumV3**. de app moet worden uitgevoerd in een app service-implementatie die PremiumV3 ondersteunt.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3-Beschik baarheid

De **PremiumV3** -laag is beschikbaar voor zowel native als container-apps, met inbegrip van zowel Windows-containers als Linux-containers.

> [!NOTE]
> Windows-containers die worden uitgevoerd in de **Premium-container** laag tijdens de preview-periode blijven functioneren zoals is, maar de laag van de **Premium-container** blijft in de preview-fase. De **PremiumV3** -laag is de officiële vervanging van de **eersteklas container** -laag. 

**PremiumV3** is beschikbaar in sommige Azure-regio's en de beschik baarheid in extra regio's wordt doorlopend toegevoegd. Als u wilt zien of deze beschikbaar is in uw regio, voert u de volgende Azure CLI-opdracht uit in de [Azure Cloud shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Een app maken in de PremiumV3-laag

De prijs categorie van een App Service-app wordt gedefinieerd in het [app service plan](overview-hosting-plans.md) waarop het wordt uitgevoerd. U kunt een App Service plan maken op zichzelf of als onderdeel van het maken van een app.

Selecteer bij het configureren van het App Service-abonnement in de <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **prijs categorie**. 

Selecteer **productie**, selecteer **P1V3**, **P2V3** of **P3V3** en klik vervolgens op **Toep assen**.

![Scherm afbeelding met de aanbevolen prijs categorieën voor uw app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Als u **P1V3**, **P2V3** en **P3V3** niet als opties ziet, of als de opties grijs worden weer gegeven, is **PremiumV3** waarschijnlijk niet beschikbaar in de onderliggende app service implementatie die het app service plan bevat. Zie [Omhoog schalen vanuit een niet-ondersteunde resource groep en regio combinatie](#unsupported) voor meer informatie.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Een bestaande app omhoog schalen naar PremiumV3-laag

Voordat u een bestaande app naar **PremiumV3** -laag schaalt, moet u ervoor zorgen dat **PremiumV3** beschikbaar is. Zie [PremiumV3 Availability](#availability)(Engelstalig) voor meer informatie. Als deze niet beschikbaar is, raadpleegt [u omhoog schalen op basis van een niet-ondersteunde combi natie van resource groep en regio](#unsupported).

Afhankelijk van uw hostomgeving moet u mogelijk extra stappen voor het omhoog schalen. 

Open uw App Service app-pagina in de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

Selecteer in de linkernavigatiebalk van uw App Service-app-pagina **Omhoog schalen (app service plan)**.

![Scherm afbeelding die laat zien hoe u uw app service-plan kunt schalen.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecteer **productie**, selecteer **P1V3**, **P2V3** of **P3V3** en klik vervolgens op **Toep assen**.

![Scherm afbeelding met de aanbevolen prijs categorieën voor uw app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Als uw bewerking is voltooid, ziet u in de overzichts pagina van uw app dat deze nu in een **PremiumV3** -laag is.

![Scherm opname van de PremiumV3 prijs categorie op de overzichts pagina van uw app.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Als er een fout optreedt

Sommige App Service-abonnementen kunnen niet naar de PremiumV3-laag worden geschaald als de onderliggende App Service implementatie geen ondersteuning biedt voor PremiumV3. Zie [Omhoog schalen vanuit een niet-ondersteunde resource groep en regio combinatie](#unsupported) voor meer informatie.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Opschalen van een niet-ondersteunde combi natie van resource groep en regio

Als uw app wordt uitgevoerd in een App Service implementatie waarbij **PremiumV3** niet beschikbaar is, of als uw app wordt uitgevoerd in een regio die momenteel geen ondersteuning biedt voor **PremiumV3**, moet u uw app opnieuw implementeren om gebruik te maken van **PremiumV3**.  U hebt hiervoor twee opties:

- Een app maken in een nieuwe resource groep en met een nieuw App Service plan. Wanneer u het App Service plan maakt, selecteert u een **PremiumV3** -laag. Met deze stap zorgt u ervoor dat het App Service plan wordt geïmplementeerd in een implementatie-eenheid die ondersteuning biedt voor **PremiumV3**. Implementeer vervolgens uw toepassings code opnieuw in de zojuist gemaakte app. Zelfs als u het App Service plan naar een lagere laag schaalt om kosten te besparen, kunt u altijd een back-up naar **PremiumV3** maken, omdat de implementatie-eenheid dit ondersteunt.
- Als uw app al in een bestaande **Premium** -laag wordt uitgevoerd, kunt u uw app klonen met alle app-instellingen, verbindings reeksen en implementatie configuratie in een nieuwe resource groep op een nieuw app service-plan dat gebruikmaakt van **PremiumV3**.

    ![Scherm afbeelding die laat zien hoe u uw app kunt klonen.](media/app-service-configure-premium-tier/clone-app.png)

    U kunt op de pagina **app klonen** een app service plan maken met behulp van **PremiumV3** in de gewenste regio en de app-instellingen en configuratie opgeven die u wilt klonen.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Overstappen van Premium-container naar Premium v3-SKU

Als u een app hebt die de preview-SKU voor Premium-containers gebruikt en u wilt overstappen op de nieuwe Premium v3-SKU, moet u uw app opnieuw implementeren om gebruik te kunnen maken van **PremiumV3**. Zie de eerste optie in [Omhoog schalen vanuit een niet-ondersteunde resource groep en regio combi natie](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt het maken van apps in de **PremiumV3** -laag automatiseren met scripts, met behulp van [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Azure CLI

Met de volgende opdracht maakt u een App Service plan in _P1V3_. U kunt deze in de Cloud Shell uitvoeren. De opties voor `--sku` zijn P1V3, _P2V3_ en _P3V3_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de volgende opdracht maakt u een App Service plan in _P1V3_. De opties voor `-WorkerSize` zijn _kleine_, _middel_ grote en _grote_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Meer bronnen

[Een app omhoog schalen in azure](manage-scale-up.md) 
 [Aantal exemplaren hand matig of automatisch schalen](../azure-monitor/autoscale/autoscale-get-started.md)
