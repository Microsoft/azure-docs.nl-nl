---
title: Functies en capaciteit omhoog schalen
description: Meer informatie over het opschalen van een app in Azure App Service. Haal meer CPU, geheugen, schijf ruimte en extra functies op.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: eb0ce5cfadbab247299cd3c22bf6ee618bf19731
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582480"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Een app omhoog schalen in Azure App Service

Dit artikel laat u zien hoe u uw app kunt schalen in Azure App Service. Er zijn twee werk stromen voor schalen, omhoog schalen en uitschalen, en in dit artikel wordt de werk stroom voor omhoog schalen uitgelegd.

* [Omhoog schalen](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Profiteer van meer CPU, geheugen, schijf ruimte en extra functies zoals toegewezen virtuele machines (vm's), aangepaste domeinen en certificaten, staging-sleuven, automatisch schalen en meer. U kunt omhoog schalen door de prijs categorie te wijzigen van het App Service plan waartoe uw app behoort.
* [Uitschalen](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Verhoog het aantal VM-exemplaren waarop uw app wordt uitgevoerd.
  U kunt uitschalen naar Maxi maal 30 exemplaren, afhankelijk van uw prijs categorie. [App service omgevingen](environment/intro.md) in de **geïsoleerde** laag verg Roten het aantal uitschalen tot 100 exemplaren. Zie [aantal exemplaren hand matig of automatisch schalen](../azure-monitor/autoscale/autoscale-get-started.md)voor meer informatie over uitschalen. Daar vindt u meer informatie over het gebruik van automatisch schalen, waarmee u het aantal exemplaren op basis van vooraf gedefinieerde regels en planningen kunt schalen.

De schaal instellingen duren slechts enkele seconden en zijn van invloed op alle apps in uw [app service-abonnement](../app-service/overview-hosting-plans.md).
U hoeft uw code niet te wijzigen of uw toepassing opnieuw te implementeren.

Zie [app service prijs informatie](https://azure.microsoft.com/pricing/details/web-sites/)voor meer informatie over de prijzen en functies van afzonderlijke app service plannen.  

> [!NOTE]
> Voordat u een App Service plan van de **gratis** laag overschakelt, moet u eerst de [bestedings limieten](https://azure.microsoft.com/pricing/spending-limits/) voor uw Azure-abonnement verwijderen. Zie [Microsoft Azure abonnementen][azuresubscriptions]om opties voor uw Microsoft Azure app service-abonnement weer te geven of te wijzigen.
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Uw prijs categorie omhoog schalen

> [!NOTE]
> Als u omhoog wilt schalen naar de laag **PremiumV3** , raadpleegt u [PremiumV3 laag configureren voor app service](app-service-configure-premium-tier.md).
>

1. Open [Azure Portal][portal] in uw browser.

1. Selecteer op de pagina App Service-app in het menu links **Omhoog schalen (app service plan)**.
   
3. Kies uw laag en selecteer vervolgens **Toep assen**. Selecteer de verschillende categorieën (bijvoorbeeld **productie**) en **Zie ook aanvullende opties** om meer lagen weer te geven.
   
    ![Navigeer om uw Azure-app omhoog te schalen.][ChooseWHP]

    Wanneer de bewerking is voltooid, ziet u een pop-upvenster met een melding over een groen resultaat.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Gerelateerde resources schalen
Als uw app afhankelijk is van andere services, zoals Azure SQL Database of Azure Storage, kunt u deze resources afzonderlijk schalen. Deze resources worden niet beheerd door het App Service plan.

1. Selecteer op de pagina **overzicht** voor uw app de koppeling **resource groep** .
   
    ![De gerelateerde resources van uw Azure-app omhoog schalen](./media/web-sites-scale/RGEssentialsLink.png)

2. Selecteer een resource die u wilt schalen in het **overzichts** gedeelte van de pagina **resource groep** . De volgende scherm afbeelding toont een SQL Database resource.
   
    ![Ga naar de pagina van de resource groep om uw Azure-app omhoog te schalen](./media/web-sites-scale/ResourceGroup.png)

    Zie de documentatie voor het specifieke resource type om de gerelateerde resource omhoog te schalen. Als u bijvoorbeeld één SQL Database wilt opschalen, raadpleegt u de [resources van één data base schalen in Azure SQL database](../azure-sql/database/single-database-scale.md). Zie [MySQL-resources schalen](../mysql/concepts-pricing-tiers.md#scale-resources)voor meer informatie over het omhoog schalen van een Azure database for MySQL resource.

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Prijs categorieën vergelijken

Zie [app service prijs informatie](https://azure.microsoft.com/pricing/details/app-service)voor gedetailleerde informatie, zoals VM-grootten voor elke prijs categorie.

Zie [app service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor een tabel met Service limieten, quota's en beperkingen en ondersteunde functies in elke laag.

<a name="Next Steps"></a>

## <a name="more-resources"></a>Meer bronnen

[Het aantal exemplaren handmatig of automatisch schalen](../azure-monitor/autoscale/autoscale-get-started.md)  
[PremiumV3-laag configureren voor App Service](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png