---
title: Planning voor het beheren van de kosten voor Azure Cognitive Services
description: Meer informatie over het plannen en beheren van de kosten voor Azure Cognitive Services met behulp van kosten analyse in de Azure Portal.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 48e111ac8b2b4fd3c1e2fee568e20699896dfff3
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705772"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Kosten plannen en beheren voor Azure Cognitive Services

In dit artikel wordt beschreven hoe u de kosten voor Azure Cognitive Services plant en beheert. Eerst gebruikt u de Azure-prijs calculator om te helpen bij het plannen van de kosten van Cognitive Services voordat u resources toevoegt voor de service om de kosten te schatten. Bekijk vervolgens de geschatte kosten wanneer u Azure-resources toevoegt. Nadat u Cognitive Services-resources (bijvoorbeeld speech, Computer Vision, LUIS, Text Analytics, Translator enzovoort) hebt gebruikt, gebruikt u Cost Management functies om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends identificeren om gebieden te identificeren waar u mogelijk wilt handelen. Kosten voor Cognitive Services zijn slechts een deel van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Cognitive Services plant en beheert, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden.

## <a name="prerequisites"></a>Vereisten

Kosten analyse in Cost Management ondersteunt de meeste typen Azure-accounts, maar niet alle. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Voor het weer geven van kosten gegevens hebt u ten minste lees toegang voor een Azure-account nodig. Zie [Toegang tot gegevens toewijzen](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Kosten schatten vóór het gebruik van Cognitive Services

Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voordat u Cognitive Services toevoegt.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Azure-prijs calculator voor Cognitive Services" border="true":::

Wanneer u nieuwe resources aan uw werk ruimte toevoegt, keert u terug naar deze reken machine en voegt u hier dezelfde resource toe om uw kosten ramingen bij te werken.

Zie [prijzen van Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)voor meer informatie.

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Het volledige facturerings model voor Cognitive Services begrijpen

Cognitive Services wordt uitgevoerd op een Azure-infra structuur die de [kosten](https://azure.microsoft.com/pricing/details/cognitive-services/) toeneemt wanneer u de nieuwe resource implementeert. Het is belang rijk om te begrijpen dat extra infra structuur kosten kan toenemen. U moet deze kosten beheren wanneer u wijzigingen aanbrengt in geïmplementeerde resources. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Kosten die normaal gesp roken toenemen met Cognitive Services

Normaal gesp roken worden de kosten na het implementeren van een Azure-resource bepaald door de prijs categorie en de API-aanroepen die u naar uw eind punt maakt. Als de service die u gebruikt een toezeg ging-laag heeft, kan het uitvoeren van de toegewezen aanroepen in uw laag een overschrijding kosten.

Aanvullende kosten kunnen toenemen wanneer u deze services gebruikt:

#### <a name="qna-maker"></a>QnA Maker

Wanneer u resources voor QnA Maker maakt, kunnen er ook resources voor andere Azure-Services worden gemaakt. Deze omvatten:

- [Azure App Service (voor de runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure-Cognitive Search (voor de gegevens)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Kosten die kunnen toenemen na het verwijderen van resources

#### <a name="qna-maker"></a>QnA Maker

Nadat u QnA Maker resources hebt verwijderd, kunnen de volgende resources blijven bestaan. Ze blijven kosten voor samen voegen totdat u ze verwijdert.

- [Azure App Service (voor de runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure-Cognitive Search (voor de gegevens)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-monetary-credit-with-cognitive-services"></a>Monetair tegoed gebruiken met Cognitive Services

U kunt betalen voor Cognitive Services kosten met uw EA monetaire toezeg ging-tegoed. U kunt het tegoed van EA monetaire toezeg ging echter niet gebruiken om te betalen voor de kosten van producten en services van derden, waaronder die van de Azure Marketplace.

## <a name="create-budgets"></a>Budgetten maken

U kunt [budgetten](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in azure als u meer granulariteit in uw bewaking wilt. Met filters kunt u ervoor zorgen dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie [groeps-en filter opties](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie over de filter opties wanneer u een budget maakt.

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u of anderen extra gegevens analyse voor kosten moeten uitvoeren. Zo kunnen financiële teams de gegevens analyseren met Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kosten analyse](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over hoe u [onverwachte kosten kunt voor komen](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Neem de [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.
