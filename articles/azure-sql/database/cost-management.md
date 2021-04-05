---
title: Kosten plannen en beheren voor Azure SQL Database
description: Meer informatie over het plannen en beheren van de kosten voor Azure SQL Database met behulp van kosten analyse in de Azure Portal.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734627"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Kosten plannen en beheren voor Azure SQL Database

In dit artikel wordt beschreven hoe u de kosten voor Azure SQL Database plant en beheert. Eerst gebruikt u de prijs calculator van Azure om Azure-resources toe te voegen en de geschatte kosten te controleren. Nadat u Azure SQL Database resources hebt gebruikt, gebruikt u Cost Management-functies om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends identificeren om gebieden te identificeren waar u mogelijk wilt handelen. Kosten voor Azure SQL Database zijn slechts een deel van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Azure SQL Database plant en beheert, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief alle services van derden.


## <a name="prerequisites"></a>Vereisten

Kosten analyse ondersteunt de meeste typen Azure-accounts, maar niet alle. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Voor het weer geven van kosten gegevens hebt u ten minste lees toegang voor een Azure-account nodig. 

Zie [Toegang tot gegevens toewijzen](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.


## <a name="sql-database-initial-cost-considerations"></a>Overwegingen voor de eerste kosten SQL Database

Wanneer u aan de slag gaat met Azure SQL Database, kunt u rekening houden met verschillende functies voor kosten besparing:


### <a name="vcore-or-dtu-purchasing-models"></a>vCore of DTU-aankoop modellen 

Azure SQL Database ondersteunt twee inkoop modellen: vCore en DTU. De manier waarop u kosten in rekening brengt, is afhankelijk van de aankoop modellen. het is belang rijk dat u begrijpt welk model het meest geschikt is voor uw werk belasting bij het plannen en overwegen. Voor informatie over vCore en DTU-aankoop modellen, Zie [kiezen tussen de vCore en DTU-aankoop modellen](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Ingericht of serverloos

In het vCore-aankoop model ondersteunt Azure SQL Database ook twee typen reken lagen: ingerichte door Voer en serverloos. De manier waarop u voor elke Compute-laag betaalt, is het belang rijk om te begrijpen wat het beste werkt voor uw werk belasting bij het plannen en overwegen van kosten. Zie [overzicht van vCore-modellen-reken lagen](service-tiers-vcore.md#compute-tiers)voor meer informatie.

In de ingerichte Compute-laag van het op vCore gebaseerde aankoop model kunt u uw bestaande licenties uitwisselen voor kortings tarieven. Zie [Azure Hybrid Benefit (AHB) (Engelstalig)](../azure-hybrid-benefit.md)voor meer informatie.

### <a name="elastic-pools"></a>Pools voor Elastic Database

Voor omgevingen met meerdere data bases met verschillende en onvoorspelbare gebruiks vereisten kunnen elastische Pools kosten besparingen bieden ten opzichte van het inrichten van dezelfde hoeveelheid data bases. Zie [elastische Pools](elastic-pool-overview.md)voor meer informatie.

## <a name="estimate-azure-sql-database-costs"></a>Azure SQL Database kosten schatten

Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om de kosten voor verschillende Azure SQL database configuraties te schatten. De informatie en prijzen in de volgende afbeelding zijn bijvoorbeeld alleen bedoeld:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Voor beeld van prijs calculator Azure SQL Database":::

U kunt ook schatten hoe verschillende opties voor het Bewaar beleid van invloed zijn op de kosten. De informatie en prijzen in de volgende afbeelding zijn bijvoorbeeld alleen bedoeld:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Azure SQL Database prijs calculator voor beeld voor opslag":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Het volledige facturerings model voor Azure SQL Database begrijpen

Azure SQL Database wordt uitgevoerd op een Azure-infra structuur, waarmee de kosten samen met Azure SQL Database worden samengevoegd wanneer u de nieuwe resource implementeert. Het is belang rijk om te begrijpen dat extra infra structuur kosten kan toenemen. U moet deze kosten beheren wanneer u wijzigingen aanbrengt in geïmplementeerde resources. 


Azure SQL Database (met uitzonde ring van serverloze) wordt gefactureerd op basis van een voorspelbaar uurtarief. Als de SQL database minder dan een uur actief is, worden er kosten in rekening gebracht voor elk uur dat de data base bestaat met behulp van de meest geselecteerde service tier, ingerichte opslag en IO die tijdens dat uur is toegepast, ongeacht het gebruik of de data base is minder dan een uur actief.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Monetair tegoed gebruiken met Azure SQL Database

U kunt betalen voor Azure SQL Database kosten met uw Azure-voor uitbetaling (voorheen monetaire toezeg ging genoemd)-tegoed. U kunt het tegoed van Azure-betaling echter niet gebruiken om te betalen voor de kosten van producten en services van derden, waaronder die van de Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

Tijdens het proces van het maken van een Azure SQL Database, kunt u de geschatte kosten bekijken tijdens de configuratie van de compute-laag. 

Als u dit scherm wilt openen, selecteert u **Data Base configureren** op het tabblad **basis beginselen** van de pagina **SQL database maken** . De informatie en prijzen in de volgende afbeelding zijn bijvoorbeeld alleen bedoeld:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Voor beeld van een schatting van de kosten in de Azure Portal":::



Als uw Azure-abonnement een bestedings limiet heeft, voor komt u dat u kunt bestedingen over uw tegoed. Wanneer u Azure-resources maakt en gebruikt, worden uw tegoeden gebruikt. Wanneer u de krediet limiet bereikt, worden de resources die u hebt geïmplementeerd, uitgeschakeld voor de rest van die facturerings periode. U kunt uw krediet limiet niet wijzigen, maar wel verwijderen. Zie [Azure bestedings limiet](../../cost-management-billing/manage/spending-limit.md)voor meer informatie over bestedings limieten.

## <a name="monitor-costs"></a>Kosten bewaken

Wanneer u Azure SQL Database gebruikt, ziet u de geschatte kosten in de portal. Gebruik de volgende stappen om de kosten raming te controleren:

1. Meld u aan bij de Azure Portal en navigeer naar de resource groep van uw Azure-SQL database. U kunt de resource groep vinden door te navigeren naar uw data base en **resource groep** te selecteren in het gedeelte **overzicht** .
1. Selecteer in het menu **kosten analyse**.
1. Bekijk **geaccumuleerde kosten** en stel de grafiek onder aan **service naam** in. Dit diagram toont een schatting van uw huidige SQL Database kosten. Als u de kosten voor de hele pagina wilt beperken tot Azure SQL Database, selecteert u **filter toevoegen** en selecteert u vervolgens **Azure SQL database**. De informatie en prijzen in de volgende afbeelding zijn bijvoorbeeld alleen bedoeld:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Voor beeld van het weer geven van geaccumuleerde kosten in de Azure Portal":::

Hier kunt u de kosten zelf bekijken. Zie voor meer informatie over de verschillende instellingen voor kosten analyse beginnen met het analyseren van de [kosten](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Budgetten maken

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

U kunt [budgetten](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in azure als u meer granulariteit in uw bewaking wilt. Met filters kunt u ervoor zorgen dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie [groeps-en filter opties](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie over de filter opties wanneer u een budget maakt.

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u of anderen extra gegevens analyse voor kosten moeten uitvoeren. Een financiële teams kunnen de gegevens bijvoorbeeld analyseren met Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Andere manieren om de kosten voor Azure SQL Database te beheren en te verlagen

Met Azure SQL Database kunt u ook resources omhoog of omlaag schalen om de kosten te beheren op basis van de behoeften van uw toepassing. Zie [database bronnen dynamisch schalen](scale-resources.md)voor meer informatie.

Bespaar geld door door te voeren naar een reserve ring voor reken resources van een tot drie jaar. Zie [kosten besparen voor resources met gereserveerde capaciteit](reserved-capacity-overview.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kosten analyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over hoe u [onverwachte kosten kunt voor komen](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.