---
title: Planning voor het beheren van de kosten voor Azure ExpressRoute
description: Meer informatie over het plannen en beheren van de kosten voor Azure ExpressRoute met behulp van kosten analyse in de Azure Portal.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 38b6aa35c1b12e3fdaed4a60cd6e241a05d42efe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501437"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Kosten voor Azure ExpressRoute plannen en beheren

In dit artikel wordt beschreven hoe u de kosten voor ExpressRoute plant en beheert. Eerst gebruikt u de prijs calculator van Azure om u te helpen bij het plannen van de kosten voor ExpressRoute voordat u resources voor de service toevoegt om de kosten te schatten. Wanneer u Azure-resources toevoegt, controleert u vervolgens de geschatte kosten. 

Nadat u ExpressRoute-resources hebt gebruikt, gebruikt u Cost Management-functies om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends identificeren om gebieden te identificeren waar u mogelijk wilt handelen. 

Houd er rekening mee dat de kosten voor ExpressRoute slechts een deel van de maandelijkse kosten in uw Azure-factuur zijn. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor ExpressRoute plant en beheert, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden.

## <a name="prerequisites"></a>Vereisten

Kosten analyse in Cost Management ondersteunt de meeste typen Azure-accounts, maar niet alle. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Voor het weer geven van kosten gegevens hebt u ten minste lees toegang voor een Azure-account nodig. 

Zie [Toegang tot gegevens toewijzen](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="local-vs-standard-vs-premium"></a>Lokaal versus standaard versus Premium

ExpressRoute heeft drie verschillende SKU voor circuits: [*lokaal*](./expressroute-faqs.md#expressroute-local), *standaard* en [*Premium*](./expressroute-faqs.md#expressroute-premium). De manier waarop uw ExpressRoute-gebruik in rekening wordt gebracht, varieert tussen deze drie SKU-typen. Met de lokale SKU worden automatisch een onbeperkt data-abonnement in rekening gebracht. Met de Standard-en Premium-SKU kunt u kiezen tussen een data-abonnement met een Data limiet of een onbeperkt. Alle ingangs gegevens zijn gratis, behalve wanneer de Global Reach-invoeg toepassing wordt gebruikt. Het is belang rijk om te begrijpen welke SKU'S en welk gegevens abonnement het meest geschikt zijn voor uw workload om de kosten en het budget te optimaliseren.

## <a name="estimate-costs"></a>Kosten schatten

Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voordat u een ExpressRoute-circuit maakt. 

1. Selecteer aan de linkerkant **netwerken** en selecteer vervolgens **Azure ExpressRoute** om te beginnen. 

1. Selecteer de juiste *zone* , afhankelijk van de locatie van uw peering. Raadpleeg [ExpressRoute-connectiviteits providers](./expressroute-locations-providers.md#partners) om de juiste *zone* te selecteren in de vervolg keuzelijst. 

1. Selecteer vervolgens de *SKU*, de snelheid van het *circuit* en het *data-abonnement* waarvoor u een schatting wilt maken. 

1. Voer in de *extra uitgaande gegevens overdracht* een schatting in van de hoeveelheid uitgaande gegevens die u in de loop van een maand kunt gebruiken. 

1. Ten slotte kunt u de *invoeg toepassing Global Reach* toevoegen aan de schatting.

De volgende scherm afbeelding toont de kosten raming door gebruik te maken van de Calculator:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Kosten raming voor ExpressRoute in azure Calculator":::

Zie [prijzen voor Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/)voor meer informatie.

### <a name="expressroute-gateway-estimated-cost"></a>Geschatte kosten van ExpressRoute-gateway

Als u een ExpressRoute-gateway gebruikt om een virtueel netwerk aan het ExpressRoute-circuit te koppelen, gebruikt u de volgende stappen om de kosten voor het gebruik van de gateway te schatten.

1. Selecteer aan de linkerkant **netwerken** en selecteer vervolgens **VPN gateway** om te beginnen. 

1. Selecteer de *regio* voor de gateway en wijzig *type* in **ExpressRoute-gateways**.

1. Selecteer het *Gateway type* in de vervolg keuzelijst.

1. Voer de *Gateway-uren* in. (720 uur = 30 dagen)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Meer informatie over het volledige facturerings model voor ExpressRoute

ExpressRoute wordt uitgevoerd op de Azure-infra structuur, waardoor de kosten samen met ExpressRoute toenemen wanneer u de nieuwe resource implementeert. Het is belang rijk om te begrijpen dat extra infra structuur kosten kan toenemen. U moet deze kosten beheren wanneer u wijzigingen aanbrengt in geïmplementeerde resources. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Kosten die normaal gesp roken toenemen met ExpressRoute

Wanneer u een ExpressRoute-circuit maakt, kunt u ervoor kiezen om een ExpressRoute-gateway te maken om uw virtuele netwerk aan het circuit te koppelen. Gateways worden in rekening gebracht tegen een uurtarief en de kosten van een ExpressRoute-circuit. Bekijk de [prijzen voor ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute) en selecteer ExpressRoute-gateways om tarieven voor verschillende gateway-sku's te bekijken.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Kosten kunnen toenemen na het verwijderen van resources

Als u een ExpressRoute-gateway hebt nadat u het ExpressRoute-circuit hebt verwijderd, worden er nog steeds kosten in rekening gebracht tot u deze hebt verwijderd.

### <a name="using-monetary-credit"></a>Monetair tegoed gebruiken

U kunt betalen voor ExpressRoute-kosten met uw EA monetaire toezeg ging-tegoed. U kunt het tegoed van EA monetaire toezeg ging echter niet gebruiken om te betalen voor de kosten van producten en services van derden, waaronder die van de Azure Marketplace.

## <a name="monitor-costs"></a>Kosten bewaken

Wanneer u Azure-resources met ExpressRoute gebruikt, worden er kosten in rekening gebracht. De kosten voor de Azure resource usage-eenheid variëren per tijds interval (seconden, minuten, uren en dagen) of per eenheids gebruik (bytes, mega bytes, enzovoort). Zodra ExpressRoute wordt gestart, worden de kosten in rekening gebracht en kunt u de kosten voor de kosten [analyse](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bekijken.

Wanneer u kosten analyse gebruikt, bekijkt u de kosten voor ExpressRoute-circuits in grafieken en tabellen voor verschillende tijds intervallen. Enkele voor beelden zijn de dag, de huidige en de vorige maand en het jaar. Daarnaast bekijkt u de kosten voor budgetten en geraamde kosten. U kunt in de loop van de tijd meer weer gaven gebruiken om de uitgaven trends te identificeren. En u kunt zien waar de overuitgave van het probleem is opgetreden. Als u budgetten hebt gemaakt, kunt u ook gemakkelijk zien waar ze worden overschreden.

Kosten voor ExpressRoute weer geven in kosten analyse:

1. Meld u aan bij Azure Portal.

1. Ga naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens  **kosten analyse** in het menu. Selecteer **bereik** om over te scha kelen naar een ander bereik in cost analysis. Kosten voor services worden standaard weer gegeven in de eerste cirkel diagram.

    De werkelijke maandelijkse kosten worden weer gegeven wanneer u de kosten analyse voor het eerst opent. Hier volgt een voor beeld waarin alle maandelijkse gebruiks kosten worden weer gegeven.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Voor beeld van het weer geven van geaccumuleerde kosten voor een abonnement":::
    

1.  Als u de kosten voor één service, zoals Expressroute, wilt beperken, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**. Selecteer vervolgens **ExpressRoute**.

    Hier volgt een voor beeld met de kosten voor ExpressRoute.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Voor beeld van het weer geven van geaccumuleerde kosten voor ExpressRoute":::

In het vorige voor beeld ziet u de huidige kosten voor de service. Kosten per Azure-regio's (locaties) en ExpressRoute kosten per resource groep worden ook weer gegeven. Hier kunt u de kosten zelf bekijken.

## <a name="create-budgets-and-alerts"></a>Maak budgetten en waarschuwingen

U kunt [budgetten](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in azure als u meer granulariteit in uw bewaking wilt. Met filters kunt u ervoor zorgen dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie voor meer informatie over de filter opties voor het maken van een budget [groeps-en filter opties](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u of anderen extra gegevens analyse voor kosten moeten uitvoeren. Een financieel team kan bijvoorbeeld de gegevens analyseren met behulp van Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de werking van prijzen met Azure ExpressRoute. Zie de [prijzen van Azure ExpressRoute Overview](https://azure.microsoft.com/en-us/pricing/details/expressroute/)(Engelstalig).
- Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kosten analyse](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over hoe u [onverwachte kosten kunt voor komen](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Neem de [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.
