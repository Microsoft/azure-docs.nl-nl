---
title: Kosten plannen en beheren voor Azure API Management
description: Meer informatie over het plannen en beheren van de kosten voor Azure API Management met behulp van kosten analyse in de Azure Portal.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 1ebb89ae318e57f1d4e0708a08019515ca43158d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581326"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Kosten plannen en beheren voor API Management

In dit artikel wordt beschreven hoe u de kosten voor Azure API Management plant en beheert. Eerst gebruikt u de Azure-prijs calculator om te helpen bij het plannen van de kosten van API Management voordat u resources toevoegt voor de service om de kosten te schatten. Nadat u API Management resources hebt gebruikt, gebruikt u Cost Management-functies om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends identificeren om gebieden te identificeren waar u mogelijk wilt handelen. 

Kosten voor API Management zijn slechts een deel van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor API Management plant en beheert, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden.

## <a name="prerequisites"></a>Vereisten

Kosten analyse in Cost Management ondersteunt de meeste typen Azure-accounts, maar niet alle. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Voor het weer geven van kosten gegevens hebt u ten minste lees toegang voor een Azure-account nodig. Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="estimate-costs-before-using-api-management"></a>Kosten schatten vóór het gebruik van API Management

Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voordat u API Management toevoegt. 

1. Zoek naar *API Management* of selecteer **integratie**  >  **API Management**.
1. Selecteer **weer gave** om een standaard kosten raming voor een API Management service-exemplaar toe te voegen.

> [!NOTE]
> De kosten die in dit voor beeld worden weer gegeven, zijn alleen bedoeld voor demonstratie doeleinden. Zie [API Management prijzen](https://azure.microsoft.com/pricing/details/api-management/) voor de meest recente prijs informatie.

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Kosten schatten voor de Developer-laag":::

* De standaard kostprijs raming is gebaseerd op een API Management service-exemplaar in de [servicelaag](api-management-features.md) van de **Developer** -service met één [capaciteits eenheid](api-management-capacity.md). De laag Developer is voor het gebruik van niet-productie doeleinden en evaluaties. Er wordt geen back-up gemaakt van een service overeenkomst.

* Als u de kosten voor extra capaciteits eenheden of een andere servicelaag wilt schatten, selecteert u andere opties in de vervolg keuzelijsten **eenheden** en **laag** .

* Afhankelijk van de beschik baarheid van functies en de servicelaag, kunnen er extra kosten in rekening worden gebracht voor het gebruik van [zelf-hostende gateways](self-hosted-gateway-overview.md).

Zie voor meer informatie over prijzen en functies:

* [API Management prijzen](https://azure.microsoft.com/pricing/details/api-management/)
* [Vergelijking op basis van functies van de Azure API Management-lagen](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Monetair tegoed gebruiken met API Management

U kunt betalen voor API Management kosten met uw Azure-voor uitbetaling (eerder monetaire toezeg ging genoemd). U kunt het tegoed van Azure-betaling echter niet gebruiken om te betalen voor kosten voor producten en services van derden, waaronder die van de Azure Marketplace.

## <a name="monitor-costs"></a>Kosten bewaken

Wanneer u Azure-resources met API Management gebruikt, worden er kosten in rekening gebracht. De kosten voor de Azure resource usage-eenheid variëren per tijds interval (seconden, minuten, uren en dagen) of per eenheids gebruik (bytes, mega bytes, enzovoort). Zodra API Management gebruik begint, worden de kosten in rekening gebracht en kunt u de kosten voor de kosten [analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bekijken.

Wanneer u kosten analyse gebruikt, bekijkt u API Management kosten in grafieken en tabellen voor verschillende tijds intervallen. Enkele voor beelden zijn de dag, de huidige en de vorige maand en het jaar. Daarnaast bekijkt u de kosten voor budgetten en geraamde kosten. U kunt in de loop van de tijd meer weer gaven gebruiken om de uitgaven trends te identificeren. En u kunt zien waar de overuitgave van het probleem is opgetreden. Als u budgetten hebt gemaakt, kunt u ook gemakkelijk zien waar ze worden overschreden.

> [!NOTE]
> De kosten die in dit voor beeld worden weer gegeven, zijn alleen bedoeld voor demonstratie doeleinden. Uw kosten variëren, afhankelijk van het resource gebruik en de huidige prijzen.

API Management kosten voor de kosten analyse weer geven:

1. Meld u aan bij [Azure Portal](https://azure.microsoft.com).
1. Open het venster **Cost Management en facturering** , selecteer **kosten beheer** in het menu en selecteer vervolgens een **facturerings bereik**. Selecteer bijvoorbeeld een abonnement in de lijst.
1. Selecteer **Cost Management** in het menu en selecteer vervolgens **kosten analyse**.
1. Standaard worden de maandelijkse kosten voor alle services weer gegeven in de eerste cirkel diagram. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Maandelijkse kosten voor abonnement":::

1. Als u de kosten voor één service, zoals API Management, wilt beperken, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**. Selecteer vervolgens **API Management**.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Voor beeld van het weer geven van geaccumuleerde kosten voor API Management":::

In het vorige voor beeld ziet u de huidige kosten voor de service. Kosten per Azure-regio (locaties) en API Management kosten per resource groep worden ook weer gegeven. Hier kunt u de kosten zelf bekijken.

## <a name="create-budgets"></a>Budgetten maken

U kunt [budgetten](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in azure als u meer granulariteit in uw bewaking wilt. Met filters kunt u ervoor zorgen dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie [groeps-en filter opties](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie over de filter opties wanneer u een budget maakt.

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u anderen nodig hebt om extra gegevens analyses uit te voeren voor de kosten. Een financieel team kan bijvoorbeeld de gegevens analyseren met behulp van Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Andere manieren om de kosten voor API Management te beheren en te verlagen

### <a name="choose-tier"></a>Laag kiezen

Bekijk de [op functies gebaseerde vergelijking van de Azure API management-lagen](api-management-features.md) om te helpen beslissen welke servicelaag geschikt is voor uw scenario's. De verschillende service lagen ondersteunen combi Naties van functies en mogelijkheden die zijn ontworpen voor verschillende gebruiks scenario's, met verschillende kosten. 

* De service laag **verbruik** biedt een licht gewicht, serverloze optie waarmee geen vaste kosten in rekening worden gebracht. U wordt gefactureerd op basis van het aantal API-aanroepen naar de service boven een bepaalde drempel waarde. Capaciteit wordt ook automatisch geschaald op basis van de belasting van de service.
* De **ontwikkel**-, **basis**-, **standaard**-en **Premium** -API management lagen maken gebruik van maandelijkse kosten en bieden betere door Voer en uitgebreidere functie sets voor evaluatie-en productie-workloads. Voer op elk moment een [upgrade uit](upgrade-and-scale.md) naar een andere servicelaag.

### <a name="scale-using-capacity-units"></a>Schalen met behulp van capaciteits eenheden

Behalve in de servicelaag voor verbruik, API Management ondersteunt schalen door [*capaciteits eenheden*](api-management-capacity.md)toe te voegen of te verwijderen. Naarmate de belasting toeneemt op een API Management-exemplaar, is het toevoegen van capaciteits eenheden mogelijk voordeliger dan het upgraden naar een hogere servicelaag. Het maximum aantal eenheden is afhankelijk van de servicelaag.

Elke capaciteits eenheid heeft een bepaalde functionaliteit voor het verwerken van aanvragen die afhankelijk is van de laag van de service. Een eenheid van de Basic-laag heeft bijvoorbeeld een geschatte maximale door Voer van ongeveer 1.000 aanvragen per seconde. 

Wanneer u eenheden, capaciteit en kosten proportioneel toevoegt of verwijdert. Zo bieden twee eenheden van de laag standaard een geschatte door Voer van ongeveer 2.000 aanvragen per seconde. De werkelijke door Voer kan verschillen vanwege de omvang van aanvragen of antwoorden, verbindings patronen, het aantal clients dat aanvragen doet en andere factoren.

[Bewaak](api-management-howto-use-azure-monitor.md) de capaciteits metriek voor uw API Management-exemplaar om beslissingen te nemen of een API Management exemplaar te schalen of bij te werken om meer belasting te krijgen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kosten analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over hoe u [onverwachte kosten kunt voor komen](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.
- Meer informatie over API Management [capaciteit](api-management-capacity.md).
- Bekijk de stappen voor het schalen en upgraden van API Management met behulp van de [Azure Portal](upgrade-and-scale.md)en meer informatie over automatisch [schalen](api-management-howto-autoscale.md).
