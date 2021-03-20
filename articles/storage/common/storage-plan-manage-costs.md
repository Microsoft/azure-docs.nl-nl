---
title: Kosten plannen en beheren voor Azure Blob-opslag
description: Meer informatie over het plannen en beheren van de kosten voor Azure Blob-opslag met behulp van kosten analyse in Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 0bad4637f13bbcf02047416499e4f82fdc53eb4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98601304"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Kosten plannen en beheren voor Azure Blob-opslag

Dit artikel helpt u bij het plannen en beheren van de kosten voor Azure Blob-opslag. U kunt eerst kosten ramen met behulp van de Azure-prijs calculator. Nadat u uw opslag account hebt gemaakt, optimaliseert u het account zodat u alleen betaalt voor wat u nodig hebt. Gebruik functies voor kosten beheer om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en de uitgaven trends controleren om gebieden te identificeren waar u mogelijk wilt handelen.

Houd er rekening mee dat de kosten voor Blob-opslag slechts een deel van de maandelijkse kosten in uw Azure-factuur zijn. Hoewel in dit artikel wordt uitgelegd hoe u kosten voor Blob Storage kunt schatten en beheren, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt voor uw Azure-abonnement, inclusief de services van derden. Nadat u vertrouwd bent met het beheren van de kosten voor Blob-opslag, kunt u soort gelijke methoden Toep assen om de kosten te beheren voor alle Azure-Services die in uw abonnement worden gebruikt.

## <a name="estimate-costs"></a>Kosten schatten

Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voordat u gegevens gaat maken en overdragen naar een Azure Storage-account.

1. Kies op de pagina [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de tegel **opslag accounts** .

2. Schuif omlaag in de pagina en zoek het gedeelte **opslag accounts** in uw schatting.

3. Kies opties in de vervolg keuzelijst. 

   Wanneer u de waarde van deze vervolg keuzelijsten wijzigt, verandert de kosten raming. Deze schatting wordt weer gegeven in de bovenhoek en onder aan de schatting.

   ![Scherm opname met uw schatting](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Wanneer u de waarde van de vervolg keuzelijst **type** wijzigt, veranderen ook andere opties die in dit werk blad worden weer gegeven. Gebruik de koppelingen in het gedeelte **meer info** voor meer informatie over wat elke optie betekent en hoe deze opties van invloed zijn op de prijs van bewerkingen die betrekking hebben op opslag. 

4. Wijzig de overige opties om de invloed op uw schatting te zien.

   > [!NOTE]
   > U kunt betalen voor Azure Blob Storage-kosten met uw Azure-voor uitbetaling (voorheen monetaire toezeg ging)-tegoed. U kunt het tegoed van Azure-betaling echter niet gebruiken om te betalen voor de kosten van producten en services van derden, waaronder die van de Azure Marketplace.

## <a name="optimize-costs"></a>Kosten optimaliseren

U kunt deze opties gebruiken om de kosten te verlagen. 

- Opslag capaciteit reserveren

- Gegevens indelen in toegangs lagen

- Gegevens automatisch tussen toegangs lagen verplaatsen

In deze sectie vindt u meer informatie over elke optie. 

#### <a name="reserve-storage-capacity"></a>Opslag capaciteit reserveren

U kunt geld besparen op opslag kosten voor BLOB-gegevens met Azure Storage gereserveerde capaciteit. Azure Storage gereserveerde capaciteit biedt u een korting op capaciteit voor blok-blobs en voor Azure Data Lake Storage Gen2 gegevens in standaard opslag accounts wanneer u een reserve ring voor één of drie jaar doorvoert. Een reserve ring biedt een vaste hoeveelheid opslag capaciteit voor de periode van de reserve ring. Azure Storage gereserveerde capaciteit kan uw capaciteits kosten voor blok-blobs en Azure Data Lake Storage Gen2 gegevens aanzienlijk verminderen. 

Zie [kosten optimaliseren voor Blob Storage met gereserveerde capaciteit](../blobs/storage-blob-reserved-capacity.md)voor meer informatie.

#### <a name="organize-data-into-access-tiers"></a>Gegevens indelen in toegangs lagen

U kunt de kosten verlagen door BLOB-gegevens te plaatsen in de meest rendabele toegangs lagen. U kunt kiezen uit drie lagen die zijn ontworpen om uw kosten te optimaliseren om gegevens te gebruiken. De *warme* laag heeft bijvoorbeeld hogere opslag kosten, maar lagere toegangs kosten. Als u van plan bent om gegevens regel matig te benaderen, is de warme laag mogelijk de meest rendabele keuze. Als u van plan bent om gegevens minder vaak te gebruiken, kan het *koud* of het *Archief* het meest zinvol zijn omdat de kosten van het openen van gegevens worden verhoogd en de kosten voor het opslaan van gegevens worden verminderd.    

Zie [Azure Blob Storage: Hot, cool en Archive Access lagen](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal)voor meer informatie.

#### <a name="automatically-move-data-between-access-tiers"></a>Gegevens automatisch tussen toegangs lagen verplaatsen

Gebruik levenscyclus beheer beleid om gegevens periodiek te verplaatsen tussen lagen om het geld op te slaan. Met deze beleids regels kunt u gegevens verplaatsen naar met behulp van regels die u opgeeft. U kunt bijvoorbeeld een regel maken waarmee blobs naar de opslaglaag worden verplaatst als deze Blob in 90 dagen niet is gewijzigd. Door beleids regels te maken waarmee de toegangs tier van uw gegevens wordt aangepast, kunt u de minst dure opslag opties voor uw behoeften ontwerpen.

Zie [de Azure Blob-opslag levenscyclus beheren](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) voor meer informatie.

## <a name="create-budgets"></a>Budgetten maken

U kunt [budgetten](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en waarschuwingen te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. Ze kunnen echter beperkte functionaliteit hebben voor het beheren van afzonderlijke Azure-service kosten, zoals de kosten van Azure Storage, omdat ze zijn ontworpen om de kosten op een hoger niveau bij te houden.

## <a name="monitor-costs"></a>Kosten bewaken

Wanneer u Azure-resources met Azure Storage gebruikt, worden er kosten in rekening gebracht. De kosten voor de resource gebruiks eenheid variëren per tijds interval (seconden, minuten, uren en dagen) of per eenheids gebruik (bytes, mega bytes, enzovoort). Er worden kosten in rekening gebracht zodra het gebruik van Azure Storage wordt gestart. U kunt de kosten bekijken in het deel venster [kosten analyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) in de Azure Portal.

Wanneer u kosten analyse gebruikt, kunt u de kosten van Azure Storage in grafieken en tabellen weer geven voor verschillende tijds intervallen. Enkele voor beelden zijn de dag, de huidige en de vorige maand en het jaar. U kunt ook kosten weer geven op basis van budgetten en geraamde kosten. Door over te scha kelen naar langere weer gaven kunt u uitgaven trends helpen identificeren en zien waar overuitgave mogelijk is gebeurd. Als u budgetten hebt gemaakt, kunt u ook eenvoudig zien waar ze zijn overschreden.

>[!NOTE]
> Kostenanalyse biedt ondersteuning voor verschillende typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig. Zie [Toegang tot gegevens toewijzen](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

Azure Storage kosten voor de kosten analyse weer geven:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Open het venster **Cost Management en facturering** , selecteer **kosten beheer** in het menu en selecteer vervolgens **kosten analyse**. U kunt vervolgens het bereik voor een specifiek abonnement wijzigen in de vervolg keuzelijst **bereik** .

   ![Scherm opname met bereik](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Als u alleen de kosten voor Azure Storage wilt weer geven, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**. Kies vervolgens **opslag** in de lijst. 

   Hier volgt een voor beeld van de kosten voor alleen Azure Storage:

   ![Scherm opname van filteren op opslag](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

In het vorige voor beeld ziet u de huidige kosten voor de service. De kosten per Azure-regio (locaties) en per resource groep worden ook weer gegeven. U kunt ook andere filters toevoegen (bijvoorbeeld: een filter om de kosten voor specifieke opslag accounts te bekijken).

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u of anderen extra gegevens analyse voor kosten moeten uitvoeren. Een financiële teams kunnen de gegevens bijvoorbeeld analyseren met Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de werking van prijzen met Azure Storage. Zie [Azure Storage prijzen overzicht](https://azure.microsoft.com/pricing/details/storage/).
- [Optimaliseer de kosten voor Blob Storage met gereserveerde capaciteit](../blobs/storage-blob-reserved-capacity.md).
- Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kosten analyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over hoe u [onverwachte kosten kunt voor komen](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.