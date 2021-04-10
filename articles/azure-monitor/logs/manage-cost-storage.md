---
title: Het gebruik en de kosten voor Azure Monitor logboeken beheren
description: Meer informatie over het wijzigen van het prijs plan en het beheren van het gegevens volume en het Bewaar beleid voor uw Log Analytics-werk ruimte in Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bwren
ms.openlocfilehash: 5048364aed1eea8d0c32d9134a4ba5a22d28b989
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560451"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gebruik en kosten beheren met Azure Monitor-logboeken    

> [!NOTE]
> In dit artikel wordt beschreven hoe u uw kosten voor Azure Monitor-logboeken begrijpt en beheert. Een verwant artikel, het [bewaken van het gebruik en de geschatte kosten](../usage-estimated-costs.md) , beschrijft het weer geven van het gebruik en de geschatte kosten in meerdere Azure-bewakings functies voor verschillende prijs modellen. Alle prijzen en kosten die in dit artikel worden weer gegeven, zijn alleen bedoeld als voor beeld. 

Azure Monitor-Logboeken is ontworpen voor het schalen en ondersteunen van het verzamelen, indexeren en opslaan van enorme hoeveel heden gegevens per dag vanuit elke bron in uw onderneming of die in Azure is geïmplementeerd.  Hoewel dit een primair stuur programma voor uw organisatie is, is de kosten efficiëntie uiteindelijk het onderliggende stuur programma. Daarom is het belang rijk te weten dat de kosten van een Log Analytics werk ruimte alleen gebaseerd zijn op het volume van verzamelde gegevens, dat ook afhankelijk is van het geselecteerde plan en hoe lang u ervoor hebt gekozen om gegevens op te slaan die zijn gegenereerd op basis van uw verbonden bronnen.  

In dit artikel wordt uitgelegd hoe u opgenomen gegevens volume en opslag groei proactief kunt controleren en hoe u limieten definieert om deze kosten te beheren. 

## <a name="pricing-model"></a>Prijsmodel

De standaard prijs voor Log Analytics is een model voor **betalen naar gebruik** op basis van het gegevens volume dat is opgenomen en optioneel voor langere gegevens retentie. Het gegevens volume wordt gemeten als de grootte van de gegevens die worden opgeslagen in GB (10 ^ 9 bytes). Elke Log Analytics-werk ruimte wordt in rekening gebracht als een afzonderlijke service en draagt bij aan de factuur voor uw Azure-abonnement. De hoeveelheid gegevens opname kan aanzienlijk zijn, afhankelijk van de volgende factoren: 

  - Aantal ingeschakelde beheer oplossingen en hun configuratie
  - Aantal bewaakte Vm's
  - Type gegevens die worden verzameld van elke bewaakte VM 
  
Naast het betalen naar gebruik-model is Log Analytics **capaciteits reserverings** lagen waarmee u Maxi maal 25% kunt besparen op basis van de betalen naar gebruik-prijs. Met de prijzen voor capaciteits reservering kunt u een reserve ring kopen vanaf 100 GB per dag. Elk gebruik boven het reserverings niveau wordt gefactureerd op basis van het betalen naar gebruik-tarief. De lagen voor capaciteits reservering hebben een toezeggings periode van 31 dagen. Tijdens de toezeggings periode kunt u overschakelen naar een reserverings tier op een hoger niveau (waardoor de dag van 31 dagen opnieuw wordt opgestart), maar u kunt niet teruggaan naar betalen naar gebruik of naar een reserverings laag met een lagere capaciteit totdat de toezeggings periode is voltooid. Facturering voor de reserverings lagen voor capaciteit wordt dagelijks uitgevoerd. Meer [informatie](https://azure.microsoft.com/pricing/details/monitor/) over log Analytics prijzen voor betalen per gebruik en capaciteits reservering. 

In alle prijs categorieën wordt de gegevens grootte van een gebeurtenis berekend op basis van een teken reeks representatie van de eigenschappen die in Log Analytics voor deze gebeurtenis zijn opgeslagen, ongeacht of de gegevens worden verzonden vanuit een agent of worden toegevoegd tijdens het opname proces. Dit omvat alle [aangepaste velden](custom-fields.md) die worden toegevoegd wanneer gegevens worden verzameld en vervolgens worden opgeslagen in log Analytics. Diverse eigenschappen die worden gebruikt voor alle gegevens typen, inclusief enkele [log Analytics standaard eigenschappen](./log-standard-columns.md), worden uitgesloten in de berekening van de gebeurtenis grootte. Dit omvat `_ResourceId` , `_SubscriptionId` ,, en `_ItemId` `_IsBillable` `_BilledSize` `Type` . Alle andere eigenschappen die zijn opgeslagen in Log Analytics, worden opgenomen in de berekening van de gebeurtenis grootte. Sommige gegevens typen zijn gratis van gegevens opname kosten, bijvoorbeeld de AzureActivity-, heartbeat-en gebruiks typen. Als u wilt bepalen of een gebeurtenis is uitgesloten van facturering voor gegevens opname, kunt u de `_IsBillable` eigenschap gebruiken zoals [hieronder](#data-volume-for-specific-events)wordt weer gegeven. Gebruik wordt gerapporteerd in GB (1.0 E9 bytes). 

Houd er ook rekening mee dat sommige oplossingen, zoals [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) en [Configuration Management](https://azure.microsoft.com/pricing/details/automation/) hun eigen prijs modellen hebben. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics toegewezen clusters

Log Analytics toegewezen clusters zijn verzamelingen van werk ruimten in één beheerd Azure Data Explorer-cluster ter ondersteuning van geavanceerde scenario's zoals door de [klant beheerde sleutels](customer-managed-keys.md).  Log Analytics toegewezen clusters maken gebruik van een prijs model voor capaciteits reservering, dat moet worden geconfigureerd op ten minste 1000 GB per dag. Voor dit capaciteits niveau geldt een korting van 25% ten opzichte van de prijzen voor betalen per gebruik. Elk gebruik boven het reserverings niveau wordt gefactureerd op basis van het betalen naar gebruik-tarief. De reserve ring van de cluster capaciteit heeft een toezeggings periode van 31 dagen nadat het reserverings niveau is verhoogd. Tijdens de toezeggings periode kan het capaciteits reserverings niveau niet worden verminderd, maar het kan op elk gewenst moment worden verhoogd. Wanneer werk ruimten zijn gekoppeld aan een cluster, wordt de facturering van gegevens opname voor deze werk ruimten uitgevoerd op het cluster niveau met het geconfigureerde capaciteits reserverings niveau. Meer informatie over het [maken van een log Analytics clusters](customer-managed-keys.md#create-cluster) en [het koppelen van werk ruimten aan het](customer-managed-keys.md#link-workspace-to-cluster)cluster. De prijs informatie voor capaciteits reservering is beschikbaar op de [pagina met Azure monitor prijzen]( https://azure.microsoft.com/pricing/details/monitor/).  

Het reserverings niveau voor cluster capaciteit wordt via een programma geconfigureerd met Azure Resource Manager met behulp van de `Capacity` para meter onder `Sku` . De `Capacity` is opgegeven in eenheden van GB en kan waarden hebben van 1000 GB/dag of meer in stappen van 100 GB/dag. Dit is een gedetailleerde beschrijving van Azure Monitor door de [klant beheerde sleutel](customer-managed-keys.md#create-cluster). Als voor uw cluster een reserve ring nodig is die hoger is dan 2000 GB/dag, neemt u contact met ons op [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Er zijn twee facturerings methoden voor gebruik in een cluster. Deze kunnen worden opgegeven met de `billingType` para meter bij het [configureren van uw cluster](customer-managed-keys.md#customer-managed-key-operations). De twee modi zijn: 

1. **Cluster**: in dit geval (dit is de standaard instelling), wordt de facturering voor opgenomen gegevens uitgevoerd op het cluster niveau. De opgenomen gegevens aantallen uit elke werk ruimte die aan een cluster is gekoppeld, worden geaggregeerd voor het berekenen van de dagelijkse factuur voor het cluster. Houd er rekening mee dat toewijzingen per knoop punt van [Azure Security Center](../../security-center/index.yml) worden toegepast op het niveau van de werk ruimte vóór deze aggregatie van geaggregeerde gegevens in alle werk ruimten in het cluster. 

2. **Werk ruimten**: de kosten voor de capaciteits reservering voor uw cluster worden proportioneel toegeschreven aan de werk ruimten in het cluster (na de accounting van toewijzingen per knoop punt van [Azure Security Center](../../security-center/index.yml) voor elke werk ruimte.) Als het totale gegevens volume dat is opgenomen in een werk ruimte voor een dag kleiner is dan de capaciteits reservering, wordt elke werk ruimte gefactureerd voor de gegevens die zijn opgenomen in de capaciteits reservering per GB, waarbij ze een fractie van de capaciteits reservering factureren en het niet-gebruikte deel van de capaciteits reservering wordt gefactureerd aan de cluster bron. Als het totale gegevens volume dat is opgenomen in een werk ruimte voor een dag meer is dan de capaciteits reservering, wordt elke werk ruimte gefactureerd voor een fractie van de capaciteits reservering op basis van de Fractie van de opgenomen gegevens die dag en elke werk ruimte voor een fractie van de opgenomen gegevens boven de capaciteits reservering. Er wordt niets in rekening gebracht voor de cluster resource als het totale gegevens volume dat in een werk ruimte is opgenomen voor een dag de capaciteits reservering overschrijdt.

In de facturerings opties van het cluster wordt de Bewaar periode voor gegevens per werk ruimte gefactureerd. Houd er rekening mee dat het factureren van het cluster begint wanneer het cluster wordt gemaakt, ongeacht of er werk ruimten aan het cluster zijn gekoppeld. Houd er ook rekening mee dat werk ruimten die zijn gekoppeld aan een cluster geen prijs categorie meer hebben.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Schatting van de kosten voor het beheren van uw omgeving 

Als u Azure Monitor-logboeken nog niet gebruikt, kunt u de [Azure monitor prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=monitor) gebruiken om de kosten van het gebruik van log Analytics te schatten. Begin met het invoeren van ' Azure Monitor ' in het zoekvak en klik op de resulterende Azure Monitor tegel. Schuif omlaag in de pagina naar Azure Monitor en selecteer Log Analytics in de vervolg keuzelijst Type.  Hier kunt u het aantal Vm's en de GB aan gegevens opgeven die u naar verwachting van elke VM wilt verzamelen. Doorgaans is 1 tot 3 GB gegevens maand opgenomen van een typische Azure-VM. Als u Azure Monitor logboeken al evalueert, kunt u uw gegevens statistieken uit uw eigen omgeving gebruiken. Hieronder vindt u informatie over het bepalen [van het aantal bewaakte vm's](#understanding-nodes-sending-data) en het [volume van de gegevens die in uw werk ruimte worden](#understanding-ingested-data-volume)opgenomen. 

## <a name="understand-your-usage-and-estimate-costs"></a>Inzicht in uw gebruik en geschatte kosten

Als u Azure Monitor-logboeken nu gebruikt, is het eenvoudig om te begrijpen wat de kosten waarschijnlijk zijn op basis van recente gebruiks patronen. Gebruik hiervoor  **log Analytics gebruik en geschatte kosten** om het gegevens gebruik te controleren en analyseren. Hier ziet u hoeveel gegevens worden verzameld door elke oplossing, hoeveel gegevens er worden bewaard en een schatting van uw kosten op basis van de hoeveelheid gegevens die wordt opgenomen en een extra Bewaar periode van meer dan het inbegrepen bedrag.

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="Gebruik en geraamde kosten":::

Als u uw gegevens gedetailleerder wilt bekijken, klikt u op het pictogram aan de rechter bovenhoek van een van de grafieken op de pagina **gebruik en geschatte kosten** . Nu kunt u met deze query werken om meer details van uw gebruik te bekijken.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="Logboeken weer geven":::

Op de pagina **gebruik en geschatte kosten** kunt u uw gegevens volume voor de maand controleren. Dit omvat alle factureer bare gegevens die in uw Log Analytics-werk ruimte worden ontvangen en bewaard.  
 
Log Analytics kosten worden toegevoegd aan uw Azure-factuur. U kunt de details van uw Azure-factuur bekijken onder het gedeelte Facturering van de Azure Portal of in de [Azure billing-Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Log Analytics gebruik op uw Azure-factuur weer geven 

Azure biedt een groot aantal handige functies in de hub [Azure Cost Management en facturering](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json) . Zo kunt u met de functionaliteit ' cost analysis ' uw uitgaven voor Azure-resources weer geven. Voeg eerst een filter toe op resource type (aan micro soft. operationalinsights/Workspace voor Log Analytics en micro soft. operationalinsights/cluster for Log Analytics-clusters) zodat u uw Log Analytics uitgaven kunt volgen. Selecteer vervolgens ' groeperen op ' om ' meter categorie ' of ' meter ' te selecteren.  Houd er rekening mee dat andere services, zoals Azure Security Center en Azure-Sentinel, hun gebruik ook voor Log Analytics werkruimte resources kunnen factureren. Als u de toewijzing aan service naam wilt zien, kunt u de tabel weergave selecteren in plaats van een grafiek. 

Meer informatie over uw gebruik kan worden verkregen door [uw gebruik te downloaden uit de Azure-portal](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). In de gedownloade spreadsheet kunt u het gebruik per Azure-resource (bijvoorbeeld Log Analytics-werkruimte) per dag zien. In dit Excel-werk blad kunt u het gebruik van uw Log Analytics-werk ruimten vinden door eerst te filteren op de kolom meter categorie om ' Log Analytics ' weer te geven. Insight and Analytics ' (gebruikt door enkele van de verouderde prijs categorieën) en ' Azure Monitor ' (gebruikt door prijs categorieën voor capaciteits reservering) en voegt vervolgens een filter toe aan de kolom ' instance ID ' die is ingesteld op ' bevat werk ruimte ' of ' contains cluster ' (de laatste voor het toevoegen van Log Analytics cluster gebruik). Het gebruik wordt weer gegeven in de kolom verbruikte hoeveelheid en de eenheid voor elk item wordt weer gegeven in de kolom eenheid.  Er is meer informatie beschikbaar om u te helpen [inzicht te krijgen in uw Microsoft Azure-factuur](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="changing-pricing-tier"></a>Prijs categorie wijzigen

Als u de Log Analytics prijs categorie van uw werk ruimte wilt wijzigen, 

1. In de Azure Portal opent u het **gebruik en de geschatte kosten** van uw werk ruimte waar u een lijst ziet van elk van de prijs categorieën die beschikbaar zijn voor deze werk ruimte.

2. Bekijk de geschatte kosten voor elk van de prijs categorieën. Deze schatting is gebaseerd op het gebruik van de afgelopen 31 dagen, dus deze kosten raming is afhankelijk van de laatste 31 dagen die representatief is voor uw typische gebruik. In het onderstaande voor beeld ziet u hoe, op basis van de gegevens patronen van de afgelopen 31 dagen, deze werk ruimte minder kost in de betalen naar gebruik-laag (#1) vergeleken met de 100 GB/dagen capaciteit reserverings tier (#2).  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="Prijscategorieën":::
    
3. Als u de prijs categorie hebt gecontroleerd op basis van de laatste 31 dagen van gebruik, klikt u op **selecteren**.  

U kunt [de prijs categorie ook instellen via Azure Resource Manager](./resource-manager-workspace.md) met behulp `sku` van de para meter ( `pricingTier` in de Azure Resource Manager sjabloon). 

## <a name="legacy-pricing-tiers"></a>Verouderde prijscategorieën

Abonnementen die een Log Analytics werk ruimte hebben of Application Insights resource vóór 2 april 2018 zijn gekoppeld aan een Enterprise Overeenkomst die zijn gestart vóór 1 februari 2019, blijven toegang hebben tot de verouderde prijs Categorieën: **gratis**, **zelfstandig (per GB)** en **per knoop punt (OMS)**.  Voor werk ruimten in de gratis prijs categorie geldt een dagelijkse gegevens opname van 500 MB (met uitzonde ring van beveiligings gegevens typen die worden verzameld door [Azure Security Center](../../security-center/index.yml)) en de Bewaar periode van gegevens is beperkt tot 7 dagen. De gratis prijs categorie is alleen bedoeld voor evaluatie doeleinden. Werk ruimten in de zelfstandige of per knooppunt prijs categorie hebben een door de gebruiker Configureer bare Bewaar periode van 30 tot 730 dagen.

Het gebruik van de zelfstandige prijs categorie wordt gefactureerd op basis van het opgenomen gegevens volume. Het wordt gerapporteerd in de **log Analytics** -service en de meter heet ' geanalyseerde gegevens '. 

De prijs categorie per knoop punt kosten per bewaakte VM (knoop punt) op een granulatie van het uur. Voor elk bewaakt knoop punt wordt voor de werk ruimte 500 MB aan gegevens toegewezen per dag die niet wordt gefactureerd. Deze toewijzing wordt berekend op basis van het uurloon en wordt elke dag geaggregeerd op het niveau van de werk ruimte. Gegevens die boven de cumulatieve dagelijkse gegevens toewijzing zijn opgenomen, worden per GB in rekening gebracht als gegevens overschrijding. Op uw factuur wordt de service **Insight and Analytics** voor log Analytics gebruik als de werk ruimte zich in de prijs categorie per knoop punt bevindt. Het gebruik wordt gerapporteerd op drie meters:

1. Knoop punt: dit is het gebruik van het aantal bewaakte knoop punten (Vm's) in eenheden van knoop punt * maanden.
2. Gegevens overschrijding per knoop punt: dit is het aantal GB aan gegevens dat de geaggregeerde gegevens toewijzing overschrijdt.
3. Gegevens per knoop punt: dit is het aantal opgenomen gegevens dat is gedekt door de geaggregeerde gegevens toewijzing. Deze meter wordt ook gebruikt wanneer de werk ruimte zich in alle prijs categorieën bevindt om de hoeveelheid gegevens weer te geven die onder de Azure Security Center vallen.

> [!TIP]
> Als uw werk ruimte toegang heeft tot de prijs categorie **per knoop punt** , maar u vraagt zich af of deze kosten lager zijn in een betalen per gebruik-laag, kunt u [de onderstaande query gebruiken](#evaluating-the-legacy-per-node-pricing-tier) om eenvoudig een aanbeveling te krijgen. 

Werk ruimten die zijn gemaakt vóór 2016 april hebben ook toegang tot de oorspronkelijke **Standard** -en **Premium** -prijs categorieën die respectievelijk 30 en 365 dagen zijn bewaard. Nieuwe werk ruimten kunnen niet worden gemaakt in de prijs categorie **Standard** of **Premium** , en als een werk ruimte uit deze lagen wordt verplaatst, kan deze niet meer worden teruggezet. Gegevens opname meters voor deze verouderde lagen worden ' geanalyseerde gegevens ' genoemd.

Er zijn ook enkele gedragingen tussen het gebruik van verouderde Log Analytics lagen en hoe het gebruik wordt gefactureerd voor [Azure Security Center](../../security-center/index.yml). 

1. Als de werk ruimte zich in de verouderde laag Standard of Premium bevindt, wordt Azure Security Center alleen gefactureerd voor Log Analytics gegevens opname, niet per knoop punt.
2. Als de werk ruimte zich in de laag verouderd per knoop punt bevindt, wordt Azure Security Center gefactureerd op basis van het huidige [Azure Security Center op knoop punt gebaseerde prijs model](https://azure.microsoft.com/pricing/details/security-center/). 
3. In andere prijs categorieën (inclusief capaciteits reserveringen), als Azure Security Center is ingeschakeld vóór 19 juni 2017, wordt Azure Security Center alleen gefactureerd voor Log Analytics gegevens opname. Anders wordt Azure Security Center gefactureerd met het huidige prijs model voor Azure Security Center op basis van knoop punten.

Meer informatie over de beperkingen van de prijs categorie is beschikbaar op [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).

Geen van de verouderde prijs categorieën heeft prijzen op basis van regionaal.  

> [!NOTE]
> Als u de rechten wilt gebruiken die afkomstig zijn van het aanschaffen van OMS E1 Suite, OMS E2 Suite of OMS Add-On voor System Center, kiest u de prijs categorie Log Analytics *per knoop punt* .

## <a name="log-analytics-and-security-center"></a>Log Analytics en Security Center

[Azure Security Center](../../security-center/index.yml) facturering is nauw verbonden met log Analytics facturering. Security Center biedt een toewijzing van 500 MB/knoop punten voor de volgende subset van [beveiligings gegevens typen](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, Security Baseline baseline, Summary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) en de gegevens typen update en update Summary wanneer de updatebeheer oplossing niet wordt uitgevoerd op de werk ruimte of de doel groep van de oplossing is ingeschakeld. Als de werk ruimte in de prijs categorie verouderd per knoop punt staat, worden de Security Center-en Log Analytics toewijzingen gecombineerd en gezamenlijk toegepast op alle factureer bare opgenomen gegevens.  

## <a name="change-the-data-retention-period"></a>De gegevensretentieperiode wijzigen

In de volgende stappen wordt beschreven hoe u kunt configureren hoe lang logboek gegevens worden bewaard in uw werk ruimte. Bewaren van gegevens op het niveau van de werk ruimte kan worden geconfigureerd van 30 tot 730 dagen (2 jaar) voor alle werk ruimten, tenzij ze gebruikmaken van de verouderde gratis prijs categorie. Het bewaren van afzonderlijke gegevens typen kan worden ingesteld op Maxi maal vier dagen. Meer [informatie](https://azure.microsoft.com/pricing/details/monitor/) over prijzen voor langere retentie van gegevens.  Als u gegevens wilt bewaren die langer zijn dan 730 dagen, kunt u het gebruik van [log Analytics werkruimte gegevens exporteren](logs-data-export.md).

### <a name="workspace-level-default-retention"></a>Standaard retentie op werkruimte niveau

Als u de standaard retentie voor uw werk ruimte wilt instellen, 
 
1. Selecteer in de Azure Portal in uw werk ruimte de optie **gebruik en geschatte kosten** in het linkerdeel venster.
2. Klik op boven aan de pagina **Gebruik en geschatte kosten** op **Gegevensretentie**.
3. Gebruik de schuifregelaar in het deelvenster om het aantal dagen te vergroten of te verkleinen. Klik vervolgens op **OK**.  Als u zich in de laag *gratis* bevindt, kunt u de Bewaar periode voor gegevens niet wijzigen en moet u upgraden naar de laag betaald om deze instelling te kunnen beheren.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="Instelling voor het bewaren van gegevens van de werk ruimte wijzigen":::

Als de retentie is verlaagd, is er een respijt periode van een enkele dag voordat de gegevens die ouder zijn dan de nieuwe Bewaar instelling worden verwijderd. 

Op de pagina **gegevens behoud** kunnen Bewaar instellingen van 30, 31, 60, 90, 120, 180, 270, 365, 550 en 730 dagen worden bewaard. Als een andere instelling vereist is, kan deze worden geconfigureerd met behulp van [Azure Resource Manager](./resource-manager-workspace.md) met de `retentionInDays` para meter. Wanneer u de Bewaar periode van de gegevens instelt op 30 dagen, kunt u een onmiddellijke verwijdering van oudere gegevens activeren met behulp van de `immediatePurgeDataOn30Days` para meter (waardoor de enige dag wordt uitgesteld). Dit kan handig zijn voor nalevings scenario's waarbij onmiddellijke gegevens verwijdering nood zakelijk is. Deze functie direct opschonen wordt alleen weer gegeven via Azure Resource Manager. 

Werk ruimten met een Bewaar periode van 30 dagen kunnen de gegevens gedurende 31 dagen werkelijk bewaren. Als het van cruciaal belang is dat gegevens slechts gedurende 30 dagen worden bewaard, gebruikt u de Azure Resource Manager om de Bewaar periode in te stellen op 30 dagen en met de `immediatePurgeDataOn30Days` para meter.  

Twee gegevens typen- `Usage` en `AzureActivity` --worden standaard gedurende een minimum van 90 dagen bewaard en er worden geen kosten in rekening gebracht voor deze Bewaar periode van 90 dagen. Als de retentie van de werk ruimte groter is dan 90 dagen, wordt de Bewaar periode van deze gegevens typen ook verhoogd.  Deze gegevens typen zijn ook gratis van de kosten voor gegevens opname. 

Gegevens typen van Application Insights resources (,,,,,,,, en) op basis van een werk ruimte `AppAvailabilityResults` `AppBrowserTimings` `AppDependencies` `AppExceptions` `AppEvents` `AppMetrics` `AppPageViews` `AppPerformanceCounters` `AppRequests` `AppSystemEvents` `AppTraces` worden ook standaard 90 dagen bewaard en er worden geen kosten in rekening gebracht voor de Bewaar periode van 90 dagen. De retentie kan worden aangepast met de functionaliteit voor het bewaren van gegevens typen. 

Opmerking: De [API voor opschonen](/rest/api/loganalytics/workspacepurge/purge) van Log Analytics heeft geen invloed op de facturering van retentie en is bedoeld om voor een zeer beperkt aantal gevallen te worden gebruikt. Om uw retentie factuur te verminderen, moet de Bewaar periode voor de werk ruimte of voor specifieke gegevens typen worden verminderd. 

### <a name="retention-by-data-type"></a>Bewaren op gegevens type

Het is ook mogelijk om verschillende Bewaar instellingen op te geven voor afzonderlijke gegevens typen van 4 tot 730 dagen (met uitzonde ring van werk ruimten in de prijs categorie verouderde gratis) die de standaard retentie van het werkruimte niveau onderdrukken. Elk gegevens type is een subresource van de werk ruimte. De SecurityEvent-tabel kan bijvoorbeeld als volgt worden behandeld in [Azure Resource Manager](../../azure-resource-manager/management/overview.md) :

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Houd er rekening mee dat het gegevens type (tabel) hoofdletter gevoelig is.  Als u de huidige Bewaar instellingen per gegevens type van een bepaald gegevens type (in dit voor beeld SecurityEvent) wilt ophalen, gebruikt u:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> Bewaren wordt alleen geretourneerd voor een gegevens type als de retentie expliciet is ingesteld.  Gegevens typen waarvoor geen retentie is ingesteld (en waardoor de Bewaar periode voor de werk ruimte wordt overgenomen), retour neren niets van deze aanroep. 

Als u de huidige Bewaar instellingen per gegevens type wilt ophalen voor alle gegevens typen in uw werk ruimte waarvoor de Bewaar periode per gegevens type is ingesteld, laat u gewoon het specifieke gegevens type weg, bijvoorbeeld:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Ga als volgt te werk om de Bewaar periode van een bepaald gegevens type (in dit voor beeld SecurityEvent) in te stellen op 730 dagen.

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Geldige waarden voor `retentionInDays` zijn 30 tot en met 730.

De `Usage` `AzureActivity` gegevens typen en kunnen niet worden ingesteld met aangepaste retentie. Ze nemen het maximum van de standaard retentie van de werk ruimte of 90 dagen in beslag. 

Een uitstekend hulp programma om rechtstreeks verbinding te maken met Azure Resource Manager om Bewaar periode in te stellen op gegevens type is het OSS-hulp programma [ARMclient](https://github.com/projectkudu/ARMClient).  Meer informatie over ARMclient van artikelen op [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en de [Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Hier volgt een voor beeld van het gebruik van ARMClient, waarbij SecurityEvent-gegevens worden ingesteld op een retentie van 730 dagen:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Het instellen van de Bewaar periode voor afzonderlijke gegevens typen kan worden gebruikt om de kosten voor het bewaren van gegevens te verlagen.  Voor gegevens die vanaf oktober 2019 worden verzameld (als deze functie is uitgebracht) vermindert u de retentie voor sommige gegevens typen in de loop van de tijd om uw Bewaar kosten te verlagen.  Voor de gegevens die u eerder hebt verzameld, is het instellen van een lagere Bewaar periode voor een afzonderlijk type geen invloed op de kosten voor de Bewaar periode.  

## <a name="manage-your-maximum-daily-data-volume"></a>Uw maximale dagelijkse gegevens volume beheren

U kunt een dagelijks CAP configureren en de dagelijkse opname voor uw werk ruimte beperken, maar zorg ervoor dat uw doel niet kan worden bereikt door de dagelijkse limiet te gebruiken.  Anders verliest u gegevens voor de rest van de dag, wat van invloed kan zijn op andere Azure-Services en-oplossingen waarvan de functionaliteit afhankelijk is van actuele gegevens die beschikbaar zijn in de werk ruimte.  Dit is nadelig voor uw mogelijkheid om waarnemingen te doen en waarschuwingen te krijgen over de status van resources die uw IT-services ondersteunen.  Het dagelijks kapje is bedoeld om te worden gebruikt als een manier om een **onverwachte toename** van het gegevens volume van uw beheerde resources te beheren en binnen uw limiet te blijven, of wanneer u niet-geplande kosten voor uw werk ruimte wilt beperken. Het is niet de bedoeling dat u een daglimiet in een werkruimte instelt die elke dag wordt bereikt.

Voor elke werk ruimte is de dagelijkse Cap toegepast op een ander uur van de dag. De reset uur wordt weer gegeven op de pagina met het **dagelijks kapje** (zie hieronder). Deze reset uur kan niet worden geconfigureerd. 

Zodra de dagelijkse limiet is bereikt, stopt het verzamelen van factureer bare gegevens typen voor de rest van de dag. De latentie die inherent is aan het Toep assen van het dagelijkse kapje betekent dat het kapje niet precies het opgegeven niveau voor de dagelijkse Cap wordt toegepast. Er wordt een waarschuwings banner boven aan de pagina weer gegeven voor de geselecteerde Log Analytics-werk ruimte en er wordt een bewerkings gebeurtenis verzonden naar de *bewerkings* tabel onder **LogManagement** categorie. Het verzamelen van gegevens wordt hervat nadat het tijdstip waarop de tijd opnieuw is ingesteld onder *dagelijkse limiet is ingesteld op*. We raden u aan een waarschuwings regel te definiëren op basis van deze bewerkings gebeurtenis, die is geconfigureerd om te worden gewaarschuwd als de dagelijkse gegevens limiet is bereikt (Zie [hieronder](#alert-when-daily-cap-reached)). 

> [!NOTE]
> Het dagelijks kapje kan het verzamelen van gegevens niet met precies het opgegeven Cap-niveau stoppen en er worden een aantal overtollige gegevens verwacht, met name als de werk ruimte grote hoeveel heden gegevens ontvangt. [Hieronder](#view-the-effect-of-the-daily-cap) vindt u een overzicht van de informatie die nuttig is voor het bestudeert van het dagelijkse Cap-gedrag. 

> [!WARNING]
> Het dagelijks kapje stopt niet het verzamelen van gegevens typen WindowsEvent, SecurityAlert, Security Baseline baseline, Summary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus, update en update Summary, met uitzonde ring van werk ruimten waarin Azure Security Center is geïnstalleerd vóór 19 juni 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificeren welke dagelijkse gegevens limiet moet worden gedefinieerd

Bekijk [log Analytics gebruik en de geschatte kosten](../usage-estimated-costs.md) om inzicht te krijgen in de trend van de gegevens opname en wat het dagelijkse volume Cap is dat moet worden gedefinieerd. U moet er rekening mee houden, omdat u hebt gewonnen? d u kunt uw resources controleren nadat de limiet is bereikt. 

### <a name="set-the-daily-cap"></a>Het dagelijks kapje instellen

In de volgende stappen wordt beschreven hoe u een limiet kunt configureren voor het beheren van de hoeveelheid gegevens die Log Analytics werk ruimte wordt opgenomen per dag.  

1. Selecteer in de werkruimte in het linkerdeelvenster **Gebruik en geschatte kosten**.
2. Klik op de pagina **gebruik en geschatte kosten** voor de geselecteerde werk ruimte op **gegevens limiet** boven aan de pagina. 
3. Dagelijks Cap is standaard **uitgeschakeld** ? Klik op aan om deze **functie in te** scha kelen en stel de gegevens volume limiet in GB/dag in.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Gegevens limiet Log Analytics configureren":::
    
De daglimiet kan via ARM worden geconfigureerd door de `dailyQuotaGb` para meter in te stellen onder `WorkspaceCapping` zoals beschreven in [werk ruimten-maken of bijwerken](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping). 

### <a name="view-the-effect-of-the-daily-cap"></a>Het effect van het dagelijkse kapje weer geven

Als u het effect van het dagelijks kapje wilt bekijken, is het belang rijk dat u rekening moet doen met de typen beveiligings gegevens die niet zijn opgenomen in het dagelijks kapje en het opnieuw instellen van het uur voor uw werk ruimte. Het dagelijks reset-uur wordt weer gegeven op de pagina met het **dagelijks kapje** .  De volgende query kan worden gebruikt om de gegevens volumes bij te houden, afhankelijk van de dagelijkse limiet tussen de dagelijkse limieten. In dit voor beeld is de tijd van de werk ruimte opnieuw ingesteld op 14:00.  U moet dit bijwerken voor uw werk ruimte.

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

(In het gegevens type gebruik worden de eenheden `Quantity` in MB gebruikt.)

### <a name="alert-when-daily-cap-reached"></a>Waarschuwen wanneer de dagelijkse limiet is bereikt

Hoewel we een visuele hint presen teren in de Azure Portal als aan de drempel waarde voor gegevens limiet is voldaan, wordt dit gedrag niet noodzakelijkerwijs uitgelijnd op de manier waarop u operationele problemen beheert die onmiddellijke aandacht vereisen.  Als u een waarschuwings melding wilt ontvangen, kunt u in Azure Monitor een nieuwe waarschuwings regel maken.  Zie [waarschuwingen maken, weer geven en beheren](../alerts/alerts-metric.md)voor meer informatie.

Om aan de slag te gaan, zijn hier de aanbevolen instellingen voor de waarschuwing voor het uitvoeren van query's `Operation` in de tabel met behulp van de `_LogOperation` functie. 

- Doel: Selecteer uw Log Analytics resource
- Gezocht 
   - Signaal naam: aangepaste zoek opdracht in Logboeken
   - Zoek query: `_LogOperation | where Operation == "Data Collection Status" | where Detail contains "OverQuota"`
   - Gebaseerd op: aantal resultaten
   - Voor waarde: groter dan
   - Drempel waarde: 0
   - Periode: 5 (minuten)
   - Frequentie: 5 (minuten)
- Naam van waarschuwings regel: dagelijkse gegevens limiet bereikt
- Ernst: waarschuwing (Ernst 1)

Zodra een waarschuwing is gedefinieerd en de limiet is bereikt, wordt er een waarschuwing geactiveerd en wordt de reactie uitgevoerd die is gedefinieerd in de actie groep. Dit kan uw team op de hoogte stellen via e-mail en SMS-berichten, of acties automatiseren met webhooks, Automation-runbooks of [integreren met een externe ITSM-oplossing](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Het oplossen van problemen met een hoger gebruik dan verwacht

Hoger gebruik wordt veroorzaakt door een of beide volgende oorzaken:
- Meer knoop punten dan de verwachte verzen ding van gegevens naar Log Analytics werk ruimte
- Er worden meer gegevens dan verwacht verzonden naar Log Analytics-werk ruimte (mogelijk als gevolg van het gebruik van een nieuwe oplossing of een configuratie wijziging naar een bestaande oplossing)

## <a name="understanding-nodes-sending-data"></a>Informatie over knoop punten die gegevens verzenden

Gebruik voor meer informatie over het aantal knoop punten dat elke dag in de afgelopen maand de heartbeats rapporteert van de agent.

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Het aantal knoop punten dat gegevens in de afgelopen 24 uur verzendt, gebruiken de query: 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

De follow-query kan worden gebruikt om een lijst met knoop punten te verkrijgen die gegevens verzenden (en de hoeveelheid gegevens die door elk van beide worden verzonden):

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Knoop punten die worden gefactureerd op basis van de prijs categorie verouderd per knoop punt

De [prijs categorie verouderd per knoop punt](#legacy-pricing-tiers) voor knoop punten met granulatie op basis van een uurloon en telt ook geen knoop punten die een set typen beveiligings gegevens verzenden. Het dagelijkse aantal knoop punten is bijna de volgende query:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

Het aantal eenheden op uw factuur is in eenheden van het knoop punt * maanden dat wordt weer gegeven `billableNodeMonthsPerDay` in de query. Als de Updatebeheer oplossing is geïnstalleerd op de werk ruimte, voegt u de gegevens typen update en update Summary toe aan de lijst in de WHERE-component in de bovenstaande query. Ten slotte is er een extra complexiteit in het feitelijke facturerings algoritme wanneer het doel van de oplossing wordt gebruikt die niet wordt weer gegeven in de bovenstaande query. 


> [!TIP]
> Gebruik deze `find` query's spaarzaam als scans over gegevens typen [veel resources](./query-optimization.md#query-performance-pane) zijn om uit te voeren. Als u geen resultaten **per computer** nodig hebt, voert u een query uit op het type gebruiks gegevens (zie hieronder).

## <a name="understanding-ingested-data-volume"></a>Meer informatie over opgenomen gegevens volume

Op de pagina **gebruik en geschatte kosten** toont het diagram *gegevens opname per oplossing* het totale volume van de verzonden gegevens en hoeveel er door elke oplossing wordt verzonden. Zo kunt u trends bepalen, bijvoorbeeld of het algehele gegevens gebruik (of het gebruik door een bepaalde oplossing) groeit, stabieler of aflopend blijft. 

### <a name="data-volume-for-specific-events"></a>Gegevens volume voor specifieke gebeurtenissen

Als u de grootte van opgenomen gegevens voor een bepaalde reeks gebeurtenissen wilt bekijken, kunt u een query uitvoeren op de specifieke tabel (in dit voor beeld `Event` ) en de query vervolgens beperken tot de gebeurtenissen die van belang zijn (in dit voor beeld gebeurtenis-ID 5145 of 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Houd er rekening mee dat met de component `where _IsBillable = true` gegevens typen worden gefilterd van bepaalde oplossingen waarvoor geen opname kosten worden berekend. Meer [informatie](./log-standard-columns.md#_isbillable) over `_IsBillable` .

### <a name="data-volume-by-solution"></a>Gegevensvolume per oplossing

De query die wordt gebruikt om het factureer bare gegevens volume per oplossing in de afgelopen maand te bekijken (met uitzonde ring van de laatste gedeeltelijke dag) is:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

De component with `TimeGenerated` is alleen om ervoor te zorgen dat de query-ervaring in het Azure Portal na de standaard 24 uur terugkeert. Wanneer u het gegevens type gebruik gebruikt `StartTime` en `EndTime` de tijds verzamelingen weergeeft waarvoor de resultaten worden weer gegeven. 

### <a name="data-volume-by-type"></a>Gegevens volume per type

U kunt verder inzoomen om gegevens trends weer te geven voor op gegevens type:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

Of om een tabel weer te geven op oplossing en type voor de afgelopen maand,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Gegevens volume per computer

Het `Usage` gegevens type bevat geen informatie op computer niveau. Als u de **grootte** van opgenomen gegevens per computer wilt zien, gebruikt u de `_BilledSize` [eigenschap](./log-standard-columns.md#_billedsize), die de grootte in bytes levert:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

De `_IsBillable` [eigenschap](./log-standard-columns.md#_isbillable) geeft aan of de opgenomen gegevens kosten in rekening worden gebracht. 

Voor een overzicht van het **aantal** factureer bare gebeurtenissen dat per computer is opgenomen, gebruikt u 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> Gebruik deze `find` query's spaarzaam als scans over gegevens typen [veel resources](./query-optimization.md#query-performance-pane) zijn om uit te voeren. Als u geen resultaten **per computer** nodig hebt, voert u een query uit op het gegevens type gebruik.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Gegevens volume per Azure-resource, resource groep of abonnement

Voor gegevens van knoop punten die worden gehost in azure, kunt u de **grootte** van opgenomen gegevens __per computer__ verkrijgen, met behulp van de [eigenschap](./log-standard-columns.md#_resourceid)_ResourceId, die het volledige pad naar de bron bevat:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Voor gegevens van knoop punten die worden gehost in azure, kunt u de **grootte** van opgenomen gegevens __per Azure-abonnement__ verkrijgen. Haal de `_SubscriptionId` eigenschap op als:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

Als u gegevens volume per resource groep wilt ophalen, kunt u het volgende parseren `_ResourceId` :

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

U kunt ook de `_ResourceId` volledigere, indien nodig, parseren en gebruiken

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Gebruik deze `find` query's spaarzaam als scans over gegevens typen [veel resources](./query-optimization.md#query-performance-pane) zijn om uit te voeren. Als u geen resultaten per abonnement, resource groep of bron naam nodig hebt, voert u vervolgens een query uit op het gegevens type gebruik.

> [!WARNING]
> Sommige velden van het gegevens type gebruik, terwijl ze nog steeds in het schema zijn, zijn afgeschaft en hun waarden worden niet meer ingevuld. Dit zijn zowel **computers** als velden met betrekking tot opname (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** en **AverageProcessingTimeMs**.


### <a name="querying-for-common-data-types"></a>Query's uitvoeren voor algemene gegevens typen

Als u meer wilt weten over de gegevens bron voor een bepaald gegevens type, vindt u hier enkele nuttige voorbeeld query's:

+ **Application Insights resources op basis van een werk ruimte**
  - meer informatie over het [beheren van het gebruik en de kosten voor Application Insights](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ **Beveiligingsoplossing**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Logboekbeheeroplossing**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf-gegevenstype**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Gebeurtenisgegevenstype**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog-gegevenstype**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics**-gegevenstype
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>Tips voor het verminderen van het gegevensvolume

Hieronder vindt u enkele suggesties voor het verkleinen van het aantal logboeken dat wordt verzameld:

| Bron van hoog gegevensvolume | Het gegevensvolume verminderen |
| -------------------------- | ------------------------- |
| Container Insights         | [Configureer container Insights](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost) om alleen de gegevens te verzamelen die u nodig hebt. |
| Beveiligingsgebeurtenissen            | Selecteer [normale of minimale beveiligingsgebeurtenissen](../../security-center/security-center-enable-data-collection.md#data-collection-tier) <br> Wijzig het beleid voor beveiligingscontrole zodat alleen de gewenste gebeurtenissen worden verzameld. Controleer vooral de noodzaak voor het verzamelen van gebeurtenissen voor <br> - [filterplatform controleren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [register controleren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [bestandssysteem controleren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernel-object controleren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [greepbewerking controleren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> -Verwissel bare opslag controleren |
| Prestatiemeteritems       | Wijzig de [Prestatiemeteritemconfiguratie](../agents/data-sources-performance-counters.md) in: <br> - Frequentie van het verzamelen van gegevens beperken <br> - Aantal prestatiemeteritems beperken |
| Gebeurtenislogboeken                 | Wijzig [Configuratie van gebeurtenislogboek](../agents/data-sources-windows-events.md) in: <br> - Aantal verzamelde gebeurtenislogboeken beperken <br> - Alleen vereiste gebeurtenisniveaus verzamelen. Bijvoorbeeld, gebeurtenissen op *informatie* niveau niet verzamelen |
| Syslog                     | Wijzig de [syslog-configuratie](../agents/data-sources-syslog.md) in: <br> - Aantal verzamelde installaties beperken <br> - Alleen vereiste gebeurtenisniveaus verzamelen. Bijvoorbeeld, gebeurtenissen op *informatie*- en *foutopsporings* niveau niet verzamelen |
| AzureDiagnostics           | Wijzig de [resource logboek verzameling](../essentials/diagnostic-settings.md#create-in-azure-portal) in: <br> - Het aantal resources dat logboeken naar Log Analytics verzendt te verkleinen <br> - Alleen vereiste logboeken te verzamelen |
| Oplossingsgegevens van computers die de oplossing niet nodig hebben | Gebruik een [oplossing als doel](../insights/solution-targeting.md) voor het verzamelen van gegevens van alleen vereiste groepen computers. |
| Application Insights | Beoordelings opties voor [https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume](managing Application Insights data volume) |
| [SQL-analyse](../insights/azure-sql.md) | Gebruik [set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) om de controle-instellingen af te stemmen. |
| Azure Sentinel | Bekijk alle [Sentinel-gegevens bronnen](../../sentinel/connect-data-sources.md) die u onlangs hebt ingeschakeld als bronnen van extra gegevens volume. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Knoop punten ophalen als gefactureerd in de prijs categorie per knoop punt

Als u een lijst wilt ophalen met computers die worden gefactureerd als knoop punten als de werk ruimte zich in de prijs categorie verouderd per knoop punt bevindt, zoekt u naar knoop punten waarnaar **gefactureerde gegevens typen** worden verzonden (sommige gegevens typen zijn gratis). Als u dit wilt doen, gebruikt u de `_IsBillable` [eigenschap](./log-standard-columns.md#_isbillable) en gebruikt u het meest linkse veld van de Fully Qualified Domain name. Hiermee wordt het aantal computers met gefactureerde gegevens per uur geretourneerd (de granulatie van de knoop punten die worden geteld en gefactureerd):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Aantal knoop punten voor beveiliging en automatisering ophalen

U kunt de query gebruiken om het aantal afzonderlijke beveiligings knooppunten te bekijken:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Als u het aantal afzonderlijke automatiserings knooppunten wilt zien, gebruikt u de query:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>De prijs categorie verouderd per knoop punt evalueren

Het besluit of werk ruimten met toegang tot de verouderde prijs categorie **per knoop punt** beter zijn uitgeschakeld in die laag of in een huidige **betalen naar gebruik** -of **capaciteits reserverings** niveau, is het vaak moeilijk voor klanten te beoordelen.  Dit houdt in dat er rekening wordt gehouden met de afweging tussen de vaste kosten per bewaakt knoop punt in de prijs categorie per knoop punt en de opgenomen gegevens toewijzing van 500 MB/knoop punt/dag en de kosten voor het betalen van geconsumeerde gegevens in de laag betalen naar gebruik (per GB). 

Om deze evaluatie te vergemakkelijken, kan de volgende query worden gebruikt om een aanbeveling voor de optimale prijs categorie te maken op basis van de gebruiks patronen van een werk ruimte.  Met deze query wordt gekeken naar de bewaakte knoop punten en gegevens die in de afgelopen 7 dagen zijn opgenomen in een werk ruimte, en voor elke dag wordt geëvalueerd welke prijs categorie optimaal zou zijn. Als u de query wilt gebruiken, moet u opgeven

1. Hiermee wordt aangegeven of de werk ruimte Azure Security Center gebruikt door `workspaceHasSecurityCenter` in te stellen op `true` of `false` , 
2. werk de prijzen bij als u specifieke kortingen hebt en
3. Geef het aantal dagen op dat u wilt terugkijken en analyseren door in te stellen `daysToEvaluate` . Dit is handig als de query te lang duurt om 7 dagen aan gegevens te bekijken. 

Dit is de query voor de aanbeveling prijs categorie:

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/en-us/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CarRes100Price = 196.; // Enter your price for the 100 GB/day Capacity Reservation
let CarRes200Price = 368.; // Enter your price for the 200 GB/day Capacity Reservation
let CarRes300Price = 540.; // Enter your price for the 300 GB/day Capacity Reservation
let CarRes400Price = 704.; // Enter your price for the 400 GB/day Capacity Reservation
let CarRes500Price = 865.; // Enter your price for the 500 GB/day Capacity Reservation
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CapRes100DailyCost = CarRes100Price + max_of(billableGB - 100, 0.)* PerGBPrice
| extend CapRes200DailyCost = CarRes200Price + max_of(billableGB - 200, 0.)* PerGBPrice
| extend CapRes300DailyCost = CarRes300Price + max_of(billableGB - 300, 0.)* PerGBPrice
| extend CapRes400DailyCost = CarRes400Price + max_of(billableGB - 400, 0.)* PerGBPrice
| extend CapResLevel500AndAbove = max_of(floor(billableGB, 100),500)
| extend CapRes500AndAboveDailyCost = CarRes500Price*CapResLevel500AndAbove/500 + max_of(billableGB - CapResLevel500AndAbove, 0.)* PerGBPrice
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CapRes100DailyCost,CapRes200DailyCost,
    CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CapRes100DailyCost, "Capacity Reservation (100 GB/day)",
    MinCost == CapRes200DailyCost, "Capacity Reservation (200 GB/day)",
    MinCost == CapRes300DailyCost, "Capacity Reservation (300 GB/day)",
    MinCost == CapRes400DailyCost, "Capacity Reservation (400 GB/day)",
    MinCost == CapRes500AndAboveDailyCost, strcat("Capacity Reservation (",CapResLevel500AndAbove," GB/day)"),
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CapRes100DailyCost, CapRes200DailyCost, CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Deze query is geen exacte replicatie van de manier waarop het gebruik wordt berekend, maar werkt in de meeste gevallen aan aanbevelingen voor de prijs categorie.  

> [!NOTE]
> Als u de rechten wilt gebruiken die afkomstig zijn van het aanschaffen van OMS E1 Suite, OMS E2 Suite of OMS Add-On voor System Center, kiest u de prijs categorie Log Analytics *per knoop punt* .

## <a name="create-an-alert-when-data-collection-is-high"></a>Een waarschuwing maken wanneer het verzamelen van gegevens hoog is

In deze sectie wordt beschreven hoe u een waarschuwing maakt het gegevens volume in de afgelopen 24 uur een opgegeven hoeveelheid overschrijdt, met behulp van Azure Monitor [logboek waarschuwingen](../alerts/alerts-unified-log.md). 

Voer de volgende stappen uit om te waarschuwen als het gefactureerde gegevens volume dat in de afgelopen 24 uur is opgenomen, groter is dan 50 GB: 

- **Waarschuwingsvoorwaarde definiëren** - geef uw Log Analytics-werkruimte op als het resourcedoel.
- **Waarschuwingscriteria** - geef het volgende op:
   - **Signaalnaam** - selecteer **Aangepast zoeken in logboeken**
   - **Zoek query** naar `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` . Als u een andere 
   - **Waarschuwingslogica** is **Gebaseerd op het** *aantal resultaten*, en **Voorwaarde** is *Groter dan* een **Drempelwaarde** van *0*
   - **Tijd periode** van *1440* minuten en **waarschuwings frequentie** tot elke *1440* minuten om één keer per dag uit te voeren.
- **Waarschuwingsdetails definiëren** - geef het volgende op:
   - **Naam** aan *factureer bare gegevens volume groter dan 50 GB in 24 uur*
   - **Ernst** op *Waarschuwing*

Maak een nieuwe [Actiegroep](../alerts/action-groups.md) of geef een bestaande op, zodat u een melding ontvangt wanneer aan de criteria voor een logboekwaarschuwing wordt voldaan.

Wanneer u een waarschuwing ontvangt, gebruikt u de stappen in de bovenstaande secties over het oplossen van het gebruik van een probleem dat groter is dan verwacht.

## <a name="data-transfer-charges-using-log-analytics"></a>Kosten voor gegevens overdracht met behulp van Log Analytics

Het verzenden van gegevens naar Log Analytics kan gegevens bandbreedte kosten, maar is beperkt tot Virtual Machines waarbij een Log Analytics agent is geïnstalleerd en niet van toepassing is bij het gebruik van diagnostische instellingen of andere connectors die zijn ingebouwd in azure Sentinel. Zoals beschreven op de [pagina met prijzen voor Azure-band breedte](https://azure.microsoft.com/pricing/details/bandwidth/), wordt gegevens overdracht tussen Azure-Services in twee regio's in rekening gebracht als uitgaande gegevens overdracht tegen het normale tarief. Inkomende gegevens overdracht is gratis. Deze kosten zijn echter zeer klein (aantal%) vergeleken met de kosten voor Log Analytics gegevens opname. Het beheer van de kosten voor Log Analytics moet daarom zich richten op uw opgenomen [gegevens volume](#understanding-ingested-data-volume). 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Problemen oplossen waarom Log Analytics geen gegevens meer verzamelt

Als u zich in de prijs categorie verouderde gratis bevindt en meer dan 500 MB aan gegevens op een dag hebt verzonden, stopt het verzamelen van gegevens voor de rest van de dag. Het bereiken van de dagelijkse limiet is een gemeen schappelijke reden dat Log Analytics het verzamelen van gegevens niet meer of gegevens lijkt te missen.  Log Analytics maakt een gebeurtenis van het type bewerking wanneer het verzamelen van gegevens wordt gestart en gestopt. Voer de volgende query uit in de zoek opdracht om te controleren of u de dagelijkse limiet bereikt en ontbrekende gegevens: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Wanneer het verzamelen van gegevens stopt, is de OperationStatus **Waarschuwing**. Wanneer het verzamelen van gegevens start, is de OperationStatus **Geslaagd**. De volgende tabel beschrijft de redenen waarom het verzamelen van gegevens wordt gestopt en een aanbevolen actie om het verzamelen van gegevens te hervatten:  

|Reden voor verzamelen stopt| Oplossing| 
|-----------------------|---------|
|Het dagelijkse kapje van uw werk ruimte is bereikt|Wacht tot de verzameling automatisch opnieuw wordt opgestart of verhoog de dagelijkse gegevens volume limiet die wordt beschreven in het maximale dagelijkse gegevens volume beheren. De tijd voor het opnieuw instellen van dagelijkse limieten wordt weer gegeven op de pagina **dagelijks Cap** . |
| Uw werk ruimte heeft de [frequentie van het gegevens opname volume](../service-limits.md#log-analytics-workspaces) bereikt | De standaardlimiet voor opnamevolumes voor gegevens die worden verzonden vanuit Azure-resources met Diagnostische instellingen is ongeveer 6 GB/minuut per werkruimte. Dit is een geschatte waarde, omdat de werkelijke grootte kan variëren tussen gegevenstypen afhankelijk van de lengte van het logboek en de compressieverhouding. Deze limiet geldt niet voor gegevens die worden verzonden via agents of de Data Collector-API. Als u gegevens met een hogere snelheid naar één werkruimte verzendt, worden sommige gegevens verwijderd en wordt er om de zes uur een gebeurtenis verzonden naar de bewerkingstabel in uw werkruimte, terwijl de drempel nog steeds wordt overschreden. Als uw opnamevolume de limiet blijft overschrijden of als u verwacht deze binnenkort te bereiken, kunt u een verhoging voor uw werkruimte aanvragen door een e-mail te verzenden naar LAIngestionRate@microsoft.com of een ondersteuningsaanvraag te openen. De gebeurtenis waarnaar u moet zoeken die aangeeft dat er een limiet voor de gegevensopname is, kan worden gevonden met de query `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`. |
|De dagelijkse limiet van de verouderde gratis prijs categorie is bereikt |Wacht tot de volgende dag voor het automatisch opnieuw opstarten van de verzameling of de prijs categorie is gewijzigd.|
|Het Azure-abonnement bevindt zich in een onderbroken staat vanwege:<br> De gratis proef versie is beëindigd<br> Azure Pass is verlopen<br> De maandelijkse bestedings limiet is bereikt (bijvoorbeeld op een MSDN-of Visual Studio-abonnement)|Overstappen op een betaald abonnement<br> Limiet verwijderen of wachten tot de limiet is ingesteld|

Als u een melding wilt ontvangen wanneer het verzamelen van gegevens wordt gestopt, gebruikt u de stappen in de waarschuwing *dagelijkse gegevens Cap maken* om een melding te ontvangen wanneer het verzamelen van gegevens wordt gestopt. Gebruik de stappen die worden beschreven in [een actie groep maken](../alerts/action-groups.md) om een e-mail, webhook of runbook-actie voor de waarschuwings regel te configureren. 

## <a name="limits-summary"></a>Limieten overzicht

Er zijn enkele aanvullende Log Analytics limieten, waarvan sommige afhankelijk zijn van de Log Analytics prijs categorie. Deze worden beschreven in [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).


## <a name="next-steps"></a>Volgende stappen

- Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../logs/log-query-overview.md) voor meer informatie over het gebruik van de Zoek taal. U kunt zoekquery’s gebruiken om aanvullende analyses uit te voeren op de gebruiksgegevens.
- Gebruik de stappen in [Een nieuwe logboekwaarschuwing maken](../alerts/alerts-metric.md) om een melding te krijgen wanneer aan een zoekcriterium wordt voldaan.
- Gebruik een [oplossing als doel](../insights/solution-targeting.md) voor het verzamelen van gegevens van alleen vereiste groepen computers.
- Als u een effectief gebeurtenis verzamelings beleid wilt configureren, raadpleegt u [Azure Security Center filter beleid](../../security-center/security-center-enable-data-collection.md).
- Wijzig de [prestatiemeteritemconfiguratie](../agents/data-sources-performance-counters.md).
- Controleer de [configuratie van gebeurtenis logboeken](../agents/data-sources-windows-events.md)om de instellingen van uw gebeurtenis verzameling te wijzigen.
- Bekijk [syslog-configuratie](../agents/data-sources-syslog.md)om de instellingen van uw syslog-verzameling te wijzigen.
- Bekijk [syslog-configuratie](../agents/data-sources-syslog.md)om de instellingen van uw syslog-verzameling te wijzigen.
