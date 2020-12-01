---
title: Kosten plannen en beheren voor Azure Cosmos DB
description: Meer informatie over het plannen en beheren van de kosten voor Azure Cosmos DB met behulp van kosten analyse in Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 3632c098f865b1e5c4e76709a83176035be7abc2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351824"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Kosten plannen en beheren voor Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In dit artikel wordt beschreven hoe u de kosten voor Azure Cosmos DB kunt plannen en beheren. Eerst gebruikt u de Calculator Azure Cosmos DB capaciteit om de kosten van uw werk belasting te schatten voordat u resources maakt. Later kunt u de geschatte kosten bekijken en uw resources gaan maken.

Nadat u Azure Cosmos DB resources hebt gebruikt, gebruikt u de Cost Management functies om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends identificeren om gebieden te identificeren waar u mogelijk wilt handelen. De kosten voor Azure Cosmos DB zijn slechts een deel van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Azure Cosmos DB plant en beheert, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden.

## <a name="prerequisites"></a>Vereisten

### <a name="provisioned-throughput-or-serverless"></a>Ingerichte door Voer of serverloos

Azure Cosmos DB ondersteunt twee typen capaciteits modi: [ingerichte door Voer](set-throughput.md) en [serverloos](serverless.md). De manier waarop u kosten in rekening brengt voor uw Azure Cosmos DB gebruik is afhankelijk van de twee modi. het is dus belang rijk dat u de optie kiest die geschikt is voor uw werk belasting. Zie de keuze [tussen ingerichte door Voer en serverloos](throughput-serverless.md) artikel voor richt lijnen en aanbevelingen voor het maken van deze keuze.

### <a name="cost-analysis"></a>Kostenanalyse

Kosten analyse in Cost Management ondersteunt de meeste typen Azure-accounts, maar niet alle. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Voor het weer geven van kosten gegevens hebt u ten minste lees toegang voor een Azure-account nodig. Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>De ingerichte doorvoer kosten schatten voordat u Azure Cosmos DB gebruikt

Als u van plan bent Azure Cosmos DB te gebruiken in de ingerichte doorvoer modus, gebruikt u de [Azure Cosmos DB capaciteit Calculator](https://cosmos.azure.com/capacitycalculator/) voor het schatten van de kosten voordat u de resources maakt in een Azure Cosmos-account. De capaciteits calculator wordt gebruikt om een schatting te krijgen van de vereiste door Voer en kosten van uw workload. Het configureren van uw Azure Cosmos-data bases en containers met de juiste hoeveelheid ingerichte door Voer of [aanvraag eenheden (ru/s)](request-units.md)voor uw werk belasting is essentieel om de kosten en prestaties te optimaliseren. U moet gegevens invoeren, zoals het API-type, het aantal regio's, de item grootte, lees/schrijf aanvragen per seconde, de totale hoeveelheid gegevens die is opgeslagen om een kosten raming op te halen. Zie het artikel [estimate](estimate-ru-with-capacity-planner.md) voor meer informatie over de capaciteits Calculator.

In de volgende scherm afbeelding ziet u de schatting van de door Voer en de kosten met behulp van de capaciteits Calculator:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Kosten raming in Azure Cosmos DB capaciteits Calculator":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> Serverloze kosten schatten voordat u Azure Cosmos DB gebruikt

Als u van plan bent Azure Cosmos DB te gebruiken in de serverloze modus, moet u een schatting maken van het aantal [aanvraag eenheden](request-units.md) en GB aan opslag dat u per maand kunt gebruiken. U kunt de vereiste hoeveelheid aanvraag eenheden schatten door het aantal database bewerkingen te evalueren dat in een maand zou worden uitgegeven en de hoeveelheid te vermenigvuldigen met de bijbehorende RU-kosten. De volgende tabel geeft een overzicht van de geschatte RU-kosten voor veelvoorkomende database bewerkingen:

| Bewerking | Geschatte kosten | Notities |
| --- | --- | --- |
| Een item maken | 5 RUs | Gemiddelde kosten voor een 1 KB-item met minder dan 5 eigenschappen om te indexeren |
| Een item bijwerken | Tien aanvraageenheden | Gemiddelde kosten voor een 1 KB-item met minder dan 5 eigenschappen om te indexeren |
| Een afzonderlijk item lezen met de ID en de partitie sleutel (punt-lezen) | 1 RU | Gemiddelde kosten voor een 1 KB-item |
| Een item verwijderen | 5 RUs | |
| Een query uitvoeren | Tien aanvraageenheden | De gemiddelde kosten voor een query die optimaal gebruik maakt van [indexering](index-overview.md) en 100 resultaten of minder levert |

> [!IMPORTANT] 
> Let op de opmerkingen in de bovenstaande tabel. Voor een nauw keurigere schatting van de werkelijke kosten van uw bewerkingen kunt u de [Azure Cosmos DB-emulator](local-emulator.md) gebruiken en [de exacte ru-kosten van uw bewerkingen meten](find-request-unit-charge.md). Hoewel de Azure Cosmos DB-emulator geen serverloos ondersteunt, wordt een standaard-RU-kosten voor database bewerkingen gerapporteerd en kan deze worden gebruikt voor deze schatting.

Zodra u het totale aantal aanvraag eenheden en GB aan opslag ruimte hebt berekend dat u in de loop van een maand waarschijnlijk wilt gebruiken, wordt uw kosten raming door de volgende formule geretourneerd: **([aantal aanvraag eenheden]/1.000.000 * $0,25) + ([GB opslag ruimte] * $0,25)**.

> [!NOTE]
> De kosten die in het vorige voor beeld worden weer gegeven, zijn alleen bedoeld voor demonstratie doeleinden. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de meest recente prijs informatie.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

Wanneer u begint met het gebruik van Azure Cosmos DB resources van Azure Portal, worden de geschatte kosten weer geven. Gebruik de volgende stappen om de kosten raming te controleren:

1. Meld u aan bij de Azure Portal en navigeer naar uw Azure Cosmos-account.
1. Ga naar de sectie **overzicht** .
1. Controleer de **kosten** grafiek onderaan. Dit diagram toont een schatting van uw huidige kosten gedurende een Configureer bare tijds periode:
1. Maak een nieuwe container, zoals een grafiek container.
1. Voer de door Voer die vereist is voor uw werk belasting, zoals 400 RU/s. Nadat u de doorvoer waarde hebt ingevoerd, ziet u de prijs schatting zoals weer gegeven op de volgende scherm afbeelding:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Kosten raming in Azure Portal":::

Als uw Azure-abonnement een bestedings limiet heeft, voor komt u dat u kunt bestedingen over uw tegoed. Wanneer u Azure-resources maakt en gebruikt, worden uw tegoeden gebruikt. Wanneer u de krediet limiet bereikt, worden de resources die u hebt geïmplementeerd, uitgeschakeld voor de rest van die facturerings periode. U kunt uw krediet limiet niet wijzigen, maar wel verwijderen. Zie [Azure bestedings limiet](../cost-management-billing/manage/spending-limit.md)voor meer informatie over bestedings limieten.

U kunt betalen voor Azure Cosmos DB kosten met uw Azure Enterprise Agreement monetaire toezeg ging-tegoed. U kunt het tegoed van de monetaire toezeg ging echter niet gebruiken om te betalen voor de kosten van producten en services van derden, waaronder die van de Azure Marketplace.

## <a name="monitor-costs"></a>Kosten bewaken

Als u resources met Azure Cosmos DB gebruikt, worden er kosten in rekening gebracht. De kosten voor de resource gebruiks eenheid variëren per tijds interval (seconden, minuten, uren en dagen) of per aanvraag eenheids gebruik. Zodra het gebruik van Azure Cosmos DB gestart, worden de kosten in rekening gebracht en kunt u ze zien in het deel venster [kosten analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in de Azure Portal.

Wanneer u kosten analyse gebruikt, kunt u de kosten voor de Azure Cosmos DB in grafieken en tabellen weer geven voor verschillende tijds intervallen. Enkele voor beelden zijn dag, actueel, voor gaande maand en jaar. U kunt ook kosten weer geven op basis van budgetten en geraamde kosten. Door over te scha kelen naar langere weer gaven kunt u uitgaven trends helpen identificeren en zien waar overuitgave mogelijk is gebeurd. Als u budgetten hebt gemaakt, kunt u ook eenvoudig zien waar ze zijn overschreden. 

Azure Cosmos DB kosten voor de kosten analyse weer geven:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Open het bereik in de Azure Portal en selecteer **kosten analyse** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens  **kosten analyse** in het menu. Selecteer **bereik** om over te scha kelen naar een ander bereik in cost analysis.

1. Kosten voor alle services worden standaard weer gegeven in de eerste cirkel diagram. Selecteer het gebied in de grafiek met het label Azure Cosmos DB.

1. Als u de kosten voor één service, zoals Azure Cosmos DB, wilt beperken, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**. Kies vervolgens **Azure Cosmos DB** in de lijst. Hier volgt een voor beeld van de kosten voor alleen Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Het deel venster kosten bewaken met kosten analyse":::

In het vorige voor beeld ziet u de huidige kosten voor Azure Cosmos DB voor de maand feb. De grafieken bevatten ook Azure Cosmos DB kosten per locatie en per resource groep.

## <a name="create-budgets"></a>Budgetten maken

U kunt [budgetten](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in azure als u meer granulariteit in uw bewaking wilt. Met filters kunt u ervoor zorgen dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie [groeps-en filter opties](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie over de filter opties wanneer u een budget maakt.

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u of anderen extra gegevens analyse voor kosten moeten uitvoeren. Een financiële teams kunnen de gegevens bijvoorbeeld analyseren met Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over de werking van prijzen in Azure Cosmos DB:

* [Prijsmodel in Azure Cosmos DB](how-pricing-works.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [De opslag kosten in Azure Cosmos DB optimaliseren](optimize-cost-storage.md)
* Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Meer informatie over het beheren van kosten met [kosten analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Meer informatie over hoe u [onverwachte kosten kunt voor komen](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.