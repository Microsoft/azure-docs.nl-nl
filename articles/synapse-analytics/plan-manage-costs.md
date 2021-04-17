---
title: Plannen voor het beheren van kosten voor Azure Synapse Analytics
description: Meer informatie over het plannen en beheren van kosten voor Azure Synapse Analytics met behulp van kostenanalyse in de Azure Portal.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 15ac6ce6a1a49bbbd15849adec373dd0fcd42c10
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568519"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Kosten voor Azure Synapse Analytics

In dit artikel wordt beschreven hoe u kosten voor uw Azure Synapse Analytics. Eerst gebruikt u de Azure-prijscalculator om de kosten Azure Synapse plannen voordat u resources voor de service toevoegt om de kosten te schatten. Controleer vervolgens de geschatte kosten wanneer Azure Synapse resources toevoegt.

Nadat u uw resources hebt Azure Synapse, gebruikt u Cost Management om budgetten in te stellen en kosten te bewaken. U kunt ook geraamde kosten bekijken en bestedingstrends identificeren om gebieden te identificeren waar u mogelijk actie wilt ondernemen. Kosten voor Azure Synapse zijn slechts een deel van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Azure Synapse kunt plannen en beheren, wordt u gefactureerd voor alle Azure-services en -resources die worden gebruikt in uw Azure-abonnement, met inbegrip van de services van derden.

## <a name="prerequisites"></a>Vereisten

Kostenanalyse in Cost Management ondersteunt de meeste Typen Azure-account, maar niet allemaal. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Als u kostengegevens wilt weergeven, hebt u ten minste leestoegang nodig voor een Azure-account. Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Kosten schatten voordat u Azure Synapse Analytics

Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voordat u Azure Synapse Analytics.

Azure Synapse beschikt over verschillende resources met verschillende kosten, zoals te zien is in de onderstaande kostenraming. 

![Voorbeeld van geschatte kosten in de Azure-prijscalculator](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Inzicht in het volledige factureringsmodel voor Azure Synapse Analytics

Azure Synapse wordt uitgevoerd op een Azure-infrastructuur die kosten met zich Azure Synapse wanneer u de nieuwe resource implementeert. Het is belangrijk om te begrijpen dat extra infrastructuur kosten kan genereren. U moet die kosten beheren wanneer u wijzigingen aan de geïmplementeerde resources aan aangebracht. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Kosten die doorgaans toenemen met Azure Synapse Analytics

Wanneer u resources voor Azure Synapse, worden er ook resources voor andere Azure-services gemaakt. Deze omvatten:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>De kosten kunnen toenemen na het verwijderen van een resource

Nadat u de Azure Synapse hebt verwijderd, kunnen de volgende resources blijven bestaan. Ze blijven kosten maken totdat u ze verwijdert.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Azure-vooruitbetalingstegoed gebruiken met Azure Synapse 

U kunt betalen voor Azure Synapse met uw Azure-vooruitbetalingstegoed (voorheen financiële toezegging genoemd). U kunt echter geen Azure-vooruitbetalingstegoed gebruiken om te betalen voor kosten voor producten en services van derden, inclusief die van de Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

Wanneer u resources voor Azure Synapse Analytics, ziet u de geschatte kosten. Een werkruimte heeft een serverloze SQL-pool die is gemaakt met de werkruimte. Er worden geen kosten in rekening gebracht voor een serverloze SQL-pool totdat u query's hebt uitgevoerd. Andere resources, zoals toegewezen SQL-pools en serverloze Apache Spark pools, moeten in de werkruimte worden gemaakt.

Een maken en <ResourceName> de geschatte prijs weergeven:

1. Navigeer naar de service in Azure Portal.
2. Maak de resource.
3. Bekijk de geschatte prijs die in de samenvatting wordt weergegeven.
4. Klaar met het maken van de resource.

![Voorbeeld van geschatte kosten bij het maken van een resource](./media/plan-manage-costs/create-workspace-cost.png)


Als uw Azure-abonnement een bestedingslimiet heeft, voorkomt Azure dat u uw tegoed kunt overschrijden. Wanneer u Azure-resources maakt en gebruikt, wordt uw tegoed gebruikt. Wanneer u de tegoedlimiet bereikt, worden de resources die u hebt geïmplementeerd, uitgeschakeld voor de rest van die factureringsperiode. U kunt uw tegoedlimiet niet wijzigen, maar u kunt deze wel verwijderen. Zie Azure-bestedingslimiet voor meer [informatie over bestedingslimieten.](../cost-management-billing/manage/spending-limit.md)

## <a name="monitor-costs"></a>Kosten bewaken

Wanneer u Azure Synapse resources gebruikt, worden er kosten in rekening brengen. Kosten voor azure-resourcegebruikseenheden variëren per tijdsinterval (seconden, minuten, uren en dagen) of per eenheidsgebruik (bytes, megabytes, e.d.) Zodra u begint met het gebruik van resources in Azure Synapse, worden er kosten gemaakt en ziet u de kosten in [kostenanalyse.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Wanneer u kostenanalyse gebruikt, bekijkt u de kosten voor Azure Synapse Analytics grafieken en tabellen voor verschillende tijdsintervallen. Enkele voorbeelden zijn per dag, huidige en vorige maand en jaar. U bekijkt ook de kosten op budgetten en geraamde kosten. Als u overschakelt naar langere weergaven gedurende een periode, kunt u bestedingstrends identificeren. En u ziet waar er mogelijk te veel is uitgevallen. Als u budgetten hebt gemaakt, kunt u ook eenvoudig zien waar ze worden overschreden.

U kunt Azure Synapse in kostenanalyse weergeven:

1. Meld u aan bij Azure Portal.
2. Open het bereik, het abonnement of de resourcegroep, in de Azure Portal en selecteer **Kostenanalyse** in het menu. Ga bijvoorbeeld naar Abonnementen, **selecteer** een abonnement in de lijst en selecteer vervolgens  **Kostenanalyse** in het menu. Selecteer **Bereik om** over te schakelen naar een ander bereik in kostenanalyse.
3. Standaard worden de kosten voor services weergegeven in de eerste donut-grafiek. Selecteer het gebied in de grafiek met het Azure Synapse.

De werkelijke maandelijkse kosten worden weergegeven wanneer u de kostenanalyse voor het eerst opent. Hier is een voorbeeld van alle maandelijkse gebruikskosten.

![Voorbeeld van opgebouwde kosten voor een abonnement](./media/plan-manage-costs/actual-monthly-costs.png)

- Als u de kosten voor één service wilt beperken, zoals Azure Synapse, selecteert u **Filter toevoegen** en selecteert u **vervolgens Servicenaam.** Selecteer vervolgens **Azure Synapse Analytics**.

Hier is een voorbeeld van de kosten voor alleen Azure Synapse.

![Voorbeeld van geaccumuleerde kosten voor ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

In het voorgaande voorbeeld ziet u de huidige kosten voor de service. Kosten per Azure-regio (locaties) en Azure Synapse per resourcegroep worden ook weergegeven. Hier kunt u zelf de kosten verkennen.

## <a name="create-budgets"></a>Budgetten maken

U kunt [budgetten](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en -resourcegroepen, zodat ze nuttig zijn als onderdeel van een algehele strategie voor kostenbewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in Azure als u meer granulariteit in uw bewaking wilt hebben. Filters zorgen ervoor dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie Groeps- en filteropties voor meer informatie over de filteropties wanneer u [een budget maakt.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kostengegevens exporteren](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslagaccount. Dit is handig wanneer u of anderen extra gegevensanalyses moeten uitvoeren voor de kosten. Een financiële team kan de gegevens bijvoorbeeld analyseren met Behulp van Excel of Power BI. U kunt uw kosten exporteren volgens een dagelijks, wekelijks of maandelijks schema en een aangepast datumbereik instellen. Het exporteren van kostengegevens is de aanbevolen manier om kostengegevenssets op te halen.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Andere manieren om de kosten voor uw Azure Synapse 

### <a name="serverless-sql-pool"></a>Serverloze SQL-pool

Zie Kostenbeheer voor serverloze [SQL-pool in](./sql/data-processed.md) Azure Synapse Analytics

### <a name="dedicated-sql-pool"></a>Toegewezen SQL-pool

U kunt de kosten voor een toegewezen SQL-pool beheersen door de resource te onderbreken wanneer deze niet wordt gebruikt. Als u de database bijvoorbeeld ‘s nachts en in het weekend niet gebruikt, kunt u deze dan onderbreken en gedurende de dag hervatten. Zie Berekening onderbreken en hervatten [in toegewezen SQL-pool via de](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Azure Portal

### <a name="serverless-apache-spark-pool"></a>Serverloze Apache Spark-pool

Als u de kosten voor uw serverloze Apache Spark wilt beheren, moet u de functie voor automatisch onderbreken Apache Spark serverloze functie inschakelen en de time-outwaarde dienovereenkomstig instellen.  Wanneer u Synapse Studio gebruikt voor uw ontwikkeling, verzendt de studio een keep alive-bericht om de sessie actief te houden. Deze kan ook worden geconfigureerd. Stel daarom een korte time-outwaarde in voor automatisch onderbreken.  Wanneer u klaar bent, sluit u de sessie. De Apache Spark pool wordt automatisch onderbroken zodra de time-outwaarde is bereikt.
 
Maak tijdens de ontwikkeling meerdere Apache Spark pooldefinities van verschillende grootten.  Het maken Apache Spark pooldefinities is gratis en er worden alleen kosten in rekening gebracht voor gebruik.  Apache Spark gebruik in Azure Synapse wordt per vCore-uur in rekening gebracht en naar pro 5 per minuut.  Gebruik bijvoorbeeld kleine poolgrootten voor codeontwikkeling en -validatie terwijl u grotere poolgrootten gebruikt voor prestatietests.


### <a name="data-integration---pipelines-and-data-flows"></a>Gegevensintegratie - pijplijnen en gegevensstromen 

Zie Kosten voor gegevensintegratie plannen en beheren voor meer [informatie](../data-factory/plan-manage-costs.md) Azure Data Factory

## <a name="next-steps"></a>Volgende stappen

- Meer [informatie over het optimaliseren van uw cloudinvestering met Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Meer informatie over kostenbeheer met [kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het voorkomen [van onverwachte kosten.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de begeleide cursus.
- Meer informatie over het plannen en beheren van [kosten voor Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)