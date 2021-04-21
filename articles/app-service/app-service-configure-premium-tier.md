---
title: PremiumV3-laag configureren
description: Leer hoe u betere prestaties kunt leveren voor uw web-app, mobiele app en API-app in Azure App Service door te schalen naar de nieuwe Prijscategorie PremiumV3.
keywords: App Service, Azure App Service, schaal, schaalbaar, App Service-abonnement, kosten App Service
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fbd841626e2a074bc0a35cd1b4ac094e267b34a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833342"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>PremiumV3-laag configureren voor Azure App Service

De nieuwe **prijscategorie PremiumV3** biedt snellere processors, SSD-opslag en vier keer zoveel geheugen-naar-core-verhouding van de bestaande prijscategorie (dubbele prijscategorie **PremiumV2).** Met het prestatievoordeel kunt u geld besparen door uw apps op minder exemplaren uit te werken. In dit artikel leert u hoe u een app maakt in de **PremiumV3-laag** of hoe u een app omhoog schaalt naar de **PremiumV3-laag.**

## <a name="prerequisites"></a>Vereisten

Als u een app wilt opschalen naar **PremiumV3,** moet u een Azure App Service-app hebben die wordt uitgevoerd in een prijscategorie die lager is dan **PremiumV3.** De app moet worden uitgevoerd in een App Service-implementatie die Ondersteuning biedt voor PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3-beschikbaarheid

De **PremiumV3-laag** is beschikbaar voor systeemeigen apps en container-apps, waaronder Windows-containers en Linux-containers.

> [!NOTE]
> Alle Windows-containers die tijdens de **preview-periode** in de Premium-containerlaag worden uitgevoerd, blijven werken, maar de **Premium-containerlaag** blijft in preview. De **PremiumV3-laag** is de officiële vervanging voor de **Premium Container-laag.** 

**PremiumV3** is beschikbaar in sommige Azure-regio's en beschikbaarheid in extra regio's wordt voortdurend toegevoegd. Als u wilt zien of deze beschikbaar is in uw regio, moet u de volgende Azure CLI-opdracht uitvoeren in [de Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Een app maken in de PremiumV3-laag

De prijscategorie van een App Service-app wordt gedefinieerd in [het App Service-abonnement](overview-hosting-plans.md) waarin deze wordt uitgevoerd. U kunt een App Service zelf of als onderdeel van het maken van de app maken.

Bij het configureren van App Service abonnement in <a href="https://portal.azure.com" target="_blank">Azure Portal</a>selecteert u **Prijscategorie**. 

Selecteer **Productie,** selecteer **vervolgens P1V3,** **P2V3** of **P3V3** en klik vervolgens op **Toepassen.**

![Schermopname met de aanbevolen prijscategorie voor uw app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Als u **P1V3,** **P2V3** en **P3V3** niet als opties ziet, of als de opties grijs zijn, is **PremiumV3** waarschijnlijk niet beschikbaar in de onderliggende App Service-implementatie die het App Service-abonnement bevat. Zie [Omhoog schalen vanuit een niet-ondersteunde combinatie van resourcegroep en regio](#unsupported) voor meer informatie.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Een bestaande app omhoog schalen naar PremiumV3-laag

Voordat u een bestaande app schaalt naar **de PremiumV3-laag,** moet u ervoor zorgen dat **PremiumV3** beschikbaar is. Zie [PremiumV3-beschikbaarheid voor meer informatie.](#availability) Zie Omhoog schalen vanuit een niet-ondersteunde combinatie van resourcegroep en regio als deze [niet beschikbaar is.](#unsupported)

Afhankelijk van uw hostingomgeving zijn voor omhoog schalen mogelijk extra stappen nodig. 

Open in <a href="https://portal.azure.com" target="_blank">Azure Portal</a>de pagina van App Service app.

Selecteer omhoog schalen (App Service abonnement) in de linkernavigatiebalk van App Service **app-pagina.**

![Schermopname die laat zien hoe u uw App Service-plan omhoog kunt schalen.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecteer **Productie,** selecteer **vervolgens P1V3,** **P2V3** of **P3V3** en klik vervolgens op **Toepassen.**

![Schermopname met de aanbevolen prijscategorie voor uw app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Als de bewerking is voltooid, ziet u op de overzichtspagina van uw app dat deze zich nu in een **PremiumV3-laag.**

![Schermopname van de prijscategorie PremiumV3 op de overzichtspagina van uw app.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Als er een foutmelding wordt weergegeven

Sommige App Service kunnen niet omhoog worden geschaald naar de PremiumV3-laag als de onderliggende App Service-implementatie geen ondersteuning biedt voor PremiumV3. Zie [Omhoog schalen vanuit een niet-ondersteunde combinatie van resourcegroep en regio](#unsupported) voor meer informatie.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Omhoog schalen vanuit een niet-ondersteunde combinatie van resourcegroep en regio

Als uw app wordt uitgevoerd in een App Service-implementatie waar **PremiumV3** niet beschikbaar is, of als uw app wordt uitgevoerd in een regio die momenteel geen ondersteuning biedt **voor PremiumV3,** moet u uw app opnieuw implementeren om te profiteren van **PremiumV3.**  U hebt hiervoor twee opties:

- Maak een app in een nieuwe resourcegroep en met een nieuw App Service abonnement. Wanneer u het App Service maakt, selecteert u een **PremiumV3-laag.** Deze stap zorgt ervoor dat het App Service wordt geïmplementeerd in een implementatie-eenheid die ondersteuning biedt **voor PremiumV3.** Vervolgens kunt u uw toepassingscode opnieuw in de zojuist gemaakte appployeren. Zelfs als u de App Service naar een lagere laag schaalt om kosten te besparen, kunt u altijd weer omhoog schalen naar **PremiumV3,** omdat de implementatie-eenheid dit ondersteunt.
- Als uw app al  wordt uitgevoerd in een bestaande Premium-laag, kunt u uw app met alle app-instellingen, verbindingsreeksen en implementatieconfiguratie klonen in een nieuwe resourcegroep in een nieuw App Service-plan dat gebruikmaakt van **PremiumV3.**

    ![Schermopname die laat zien hoe u uw app kloont.](media/app-service-configure-premium-tier/clone-app.png)

    Op de **pagina App** klonen kunt u een App Service-abonnement maken met behulp van **PremiumV3** in de 9e regio en de app-instellingen en configuratie opgeven die u wilt klonen.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Over van Premium Container naar Premium V3 SKU

Als u een app hebt die gebruik maakt van de Preview Premium Container SKU en u wilt over gaan naar de nieuwe Premium V3-SKU, moet u uw app opnieuwployeren om te profiteren van **PremiumV3.** Zie hiervoor de eerste optie in Omhoog schalen vanuit een niet-ondersteunde [combinatie van resourcegroep en regio](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt het maken van apps in de **PremiumV3-laag** automatiseren met behulp van de [Azure CLI](/cli/azure/install-azure-cli) [of Azure PowerShell.](/powershell/azure/)

### <a name="azure-cli"></a>Azure CLI

Met de volgende opdracht maakt u een App Service-abonnement in _P1V3._ U kunt deze uitvoeren in de Cloud Shell. De opties voor `--sku` zijn P1V3, _P2V3_ en _P3V3._

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de volgende opdracht maakt u een App Service-abonnement in _P1V3._ De opties voor `-WorkerSize` zijn _Klein,_ _Gemiddeld_ en _Groot._

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Meer bronnen

[Een app omhoog schalen in Azure](manage-scale-up.md) 
 [Het aantal exemplaren handmatig of automatisch schalen](../azure-monitor/autoscale/autoscale-get-started.md)
