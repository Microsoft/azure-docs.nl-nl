---
title: Planning voor het beheren van de kosten voor Azure Synapse Analytics
description: Meer informatie over het plannen en beheren van de kosten voor Azure Synapse Analytics met behulp van kosten analyse in de Azure Portal.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: ab772043c681684836e3c488419584d94dd0b45a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220640"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Kosten voor Azure Synapse Analytics plannen en beheren

In dit artikel wordt beschreven hoe u de kosten voor Azure Synapse Analytics plant en beheert. Eerst gebruikt u de Azure-prijs calculator om te helpen bij het plannen van de kosten van Azure Synapse voordat u resources voor de service toevoegt om de kosten te schatten. Bekijk vervolgens de geschatte kosten als u Azure Synapse-resources toevoegt.

Nadat u bent begonnen met het gebruik van Azure Synapse-resources, gebruikt u Cost Management-functies om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends identificeren om gebieden te identificeren waar u mogelijk wilt handelen. De kosten voor Azure Synapse zijn slechts een deel van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Azure Synapse plant en beheert, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden.

## <a name="prerequisites"></a>Vereisten

Kosten analyse in Cost Management ondersteunt de meeste typen Azure-accounts, maar niet alle. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Voor het weer geven van kosten gegevens hebt u ten minste lees toegang voor een Azure-account nodig. Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Kosten schatten voordat u Azure Synapse Analytics gebruikt

Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voordat u Azure Synapse Analytics toevoegt.

Azure Synapse heeft verschillende bronnen die verschillende kosten hebben, zoals wordt weer gegeven in de kosten raming hieronder. 

![Voor beeld van de geschatte kosten in de Azure-prijs calculator](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Meer informatie over het volledige facturerings model voor Azure Synapse Analytics

Azure Synapse wordt uitgevoerd op een Azure-infra structuur die de kosten samen met de Azure-Synapse samenvoegt wanneer u de nieuwe resource implementeert. Het is belang rijk om te begrijpen dat extra infra structuur kosten kan toenemen. U moet deze kosten beheren wanneer u wijzigingen aanbrengt in geïmplementeerde resources. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Kosten die normaal gesp roken toenemen met Azure Synapse Analytics

Wanneer u resources voor Azure Synapse maakt, worden er ook resources voor andere Azure-Services gemaakt. Deze omvatten:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Kosten kunnen toenemen na het verwijderen van resources

Nadat u Azure Synapse-resources hebt verwijderd, kunnen de volgende resources blijven bestaan. Ze blijven kosten voor samen voegen totdat u ze verwijdert.

- Data Lake Storage Gen2

### <a name="using-monetary-credit-with-azure-synapse"></a>Monetair tegoed gebruiken met Azure Synapse 

U kunt betalen voor Azure Synapse-kosten met uw EA monetaire toezeg ging-tegoed. U kunt het tegoed van EA monetaire toezeg ging echter niet gebruiken om te betalen voor de kosten van producten en services van derden, waaronder die van de Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

Wanneer u resources voor Azure Synapse Analytics maakt, ziet u geschatte kosten. Een werk ruimte heeft een serverloze SQL-groep die is gemaakt met de werk ruimte. SQL-groep zonder server maakt geen kosten in rekening totdat u query's uitvoert. Andere resources, zoals exclusieve SQL-groepen en serverloze Apache Spark Pools, moeten worden gemaakt in de werk ruimte.

Om een te maken <ResourceName> en de geschatte prijs weer te geven:

1. Ga naar de service in de Azure Portal.
2. Maak de resource.
3. Bekijk de geschatte prijs die in de samen vatting wordt weer gegeven.
4. De resource is gemaakt.

![Voor beeld met geschatte kosten tijdens het maken van een resource](./media/plan-manage-costs/create-workspace-cost.png)


Als uw Azure-abonnement een bestedings limiet heeft, voor komt u dat u kunt bestedingen over uw tegoed. Wanneer u Azure-resources maakt en gebruikt, worden uw tegoeden gebruikt. Wanneer u de krediet limiet bereikt, worden de resources die u hebt geïmplementeerd, uitgeschakeld voor de rest van die facturerings periode. U kunt uw krediet limiet niet wijzigen, maar wel verwijderen. Zie [Azure bestedings limiet](../cost-management-billing/manage/spending-limit.md)voor meer informatie over bestedings limieten.

## <a name="monitor-costs"></a>Kosten bewaken

Wanneer u Azure Synapse-resources gebruikt, zijn er kosten in rekening gebracht. De kosten voor de Azure resource usage-eenheid variëren per tijds interval (seconden, minuten, uren en dagen) of per eenheids gebruik (bytes, mega bytes, enzovoort). Zodra u begint met het gebruik van resources in azure Synapse, worden de kosten in rekening gebracht en kunt u de kosten voor de kosten [analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bekijken.

Wanneer u kosten analyse gebruikt, bekijkt u de kosten voor Azure Synapse Analytics in grafieken en tabellen voor verschillende tijds intervallen. Enkele voor beelden zijn de dag, de huidige en de vorige maand en het jaar. Daarnaast bekijkt u de kosten voor budgetten en geraamde kosten. U kunt in de loop van de tijd meer weer gaven gebruiken om de uitgaven trends te identificeren. En u kunt zien waar de overuitgave van het probleem is opgetreden. Als u budgetten hebt gemaakt, kunt u ook gemakkelijk zien waar ze worden overschreden.

Kosten voor Azure Synapse weer geven in kosten analyse:

1. Meld u aan bij Azure Portal.
2. Open de scope, het abonnement of de resource groep in de Azure Portal en selecteer **kosten analyse** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens  **kosten analyse** in het menu. Selecteer **bereik** om over te scha kelen naar een ander bereik in cost analysis.
3. Kosten voor services worden standaard weer gegeven in de eerste cirkel diagram. Selecteer het gebied in het diagram met het label Azure Synapse.

De werkelijke maandelijkse kosten worden weer gegeven wanneer u de kosten analyse voor het eerst opent. Hier volgt een voor beeld waarin alle maandelijkse gebruiks kosten worden weer gegeven.

![Voor beeld van het weer geven van geaccumuleerde kosten voor een abonnement](./media/plan-manage-costs/actual-monthly-costs.png)

- Als u de kosten voor één service, zoals Azure Synapse, wilt beperken, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**. Selecteer vervolgens **Azure Synapse Analytics**.

Hier volgt een voor beeld van de kosten voor de Azure-Synapse.

![Voor beeld van het weer geven van geaccumuleerde kosten voor servicenaam](./media/plan-manage-costs/filtered-monthly-costs.png)

In het vorige voor beeld ziet u de huidige kosten voor de service. Kosten per Azure-regio's (locaties) en Azure Synapse-kosten per resource groep worden ook weer gegeven. Hier kunt u de kosten zelf bekijken.

## <a name="create-budgets"></a>Budgetten maken

U kunt [budgetten](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in azure als u meer granulariteit in uw bewaking wilt. Met filters kunt u ervoor zorgen dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie voor meer informatie over de filter opties voor het maken van een budget [groeps-en filter opties](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u of anderen extra gegevens analyse voor kosten moeten uitvoeren. Een financiële teams kunnen de gegevens bijvoorbeeld analyseren met Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Andere manieren om de kosten voor Azure Synapse te beheren en te verlagen 

### <a name="serverless-sql-pool"></a>Serverloze SQL-pool

Zie [kosten beheer voor serverloze SQL-groepen in azure Synapse Analytics](./sql/data-processed.md) voor meer informatie over kosten voor SERVERloze SQL-groepen.

### <a name="dedicated-sql-pool"></a>Toegewezen SQL-pool

U kunt de kosten voor een toegewezen SQL-groep beheren door de resource te onderbreken wanneer deze niet wordt gebruikt. Als u de database bijvoorbeeld ‘s nachts en in het weekend niet gebruikt, kunt u deze dan onderbreken en gedurende de dag hervatten. Zie voor meer informatie [de reken kracht onderbreken en hervatten in de toegewezen SQL-groep via de Azure Portal](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>Gegevens integratie-pijp lijnen en gegevens stromen 

Zie [kosten plannen en beheren voor Azure Data Factory](../data-factory/plan-manage-costs.md) voor meer informatie over de kosten voor gegevens integratie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kosten analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over hoe u [onverwachte kosten kunt voor komen](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.
- Meer informatie over het plannen en beheren van de kosten voor [Azure machine learning](../machine-learning/concept-plan-manage-cost.md)