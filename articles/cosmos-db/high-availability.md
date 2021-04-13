---
title: Hoge beschikbaarheid in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe Azure Cosmos DB hoge beschikbaarheid biedt
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ac1e77d99707cdaa34ef42eb9b327a62f4e864c0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365362"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Hoe biedt Azure Cosmos DB hoge beschikbaarheid?
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB biedt hoge beschikbaarheid op twee primaire manieren. Eerst worden Azure Cosmos DB gerepliceerd in regio's die zijn geconfigureerd binnen een Cosmos-account. Ten tweede Azure Cosmos DB 4 replica's van gegevens in een regio onderhouden.

Azure Cosmos DB is een wereldwijd gedistribueerde databaseservice en is een basisservice die beschikbaar is in alle regio's [waar Azure beschikbaar is.](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all) U kunt een groot aantal Azure-regio's koppelen aan uw Azure Cosmos-account en uw gegevens worden automatisch en transparant gerepliceerd. U kunt een regio op elk moment toevoegen aan of verwijderen uit uw Azure Cosmos-account. Cosmos DB is beschikbaar in alle vijf de verschillende Azure-cloudomgevingen die beschikbaar zijn voor klanten:

* **Openbare** Azure-cloud, die wereldwijd beschikbaar is.

* **Azure China 21Vianet** is beschikbaar via een unieke samenwerking tussen Microsoft en 21Vianet, een van de grootste internetproviders in China.

* **Azure Duitsland** biedt services onder een model voor gegevensbeheerders, dat ervoor zorgt dat klantgegevens in Duitsland blijven onder het beheer van T-Systems International GmbH, een dochteronderneming van Telekom, die als de Duitse gegevensbeheerder optreedt.

* **Azure Government** is beschikbaar in vier regio's in de Verenigde Staten voor amerikaanse overheidsinstanties en hun partners.

* **Azure Government for Department of Defense (DoD)** is beschikbaar in twee regio's in de Verenigde Staten voor het Amerikaanse ministerie van defensie.

Binnen een regio onderhoudt Azure Cosmos DB vier kopieën van uw gegevens als replica's binnen fysieke partities, zoals wordt weergegeven in de volgende afbeelding:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fysieke partitionering" border="false":::

* De gegevens in Azure Cosmos-containers [worden horizontaal gepartitiefd.](partitioning-overview.md)

* Een partitieset is een verzameling van meerdere replicasets. Binnen elke regio wordt elke partitie beveiligd door een replicaset, met alle schrijfingen gerepliceerd en blijvend vastgelegd door een meerderheid van de replica's. Replica's worden verdeeld over maar liefst 10-20 foutdomeinen.

* Elke partitie in alle regio's wordt gerepliceerd. Elke regio bevat alle gegevenspartities van een Azure Cosmos-container en kan zowel lees- als schrijf- en schrijfgegevens verwerken wanneer schrijf-in-/uit-meerdere regio's zijn ingeschakeld.  

Als uw Azure Cosmos-account is gedistribueerd over *N* Azure-regio's, zijn er ten minste *N* x 4 kopieën van al uw gegevens. Een Azure Cosmos-account in meer dan 2 regio's verbetert de beschikbaarheid van uw toepassing en biedt lage latentie in de gekoppelde regio's.

## <a name="slas-for-availability"></a>SLA's voor beschikbaarheid

Azure Cosmos DB biedt uitgebreide SLA's die doorvoer, latentie in het 99e percentiel, consistentie en hoge beschikbaarheid omvatten. In de onderstaande tabel ziet u de garanties voor hoge beschikbaarheid van Azure Cosmos DB voor accounts met één en meerdere regio's. Voor hogere schrijfbeschikbaarheid configureert u uw Azure Cosmos-account zo dat deze meerdere schrijfregio's heeft.

|Het type bewerking  | Eén regio |Meerdere regio's (schrijf schrijft in één regio)|Meerdere regio's (schrijf schrijft in meerdere regio's) |
|---------|---------|---------|-------|
|Schrijfbewerkingen    | 99,99    |99,99   |99,999|
|Leesbewerkingen     | 99,99    |99,999  |99,999|

> [!NOTE]
> In de praktijk is de werkelijke schrijfbeschikbaarheid voor modellen voor gebonden veroudering, sessie, consistent voorvoegsel en uiteindelijke consistentie aanzienlijk hoger dan de gepubliceerde SLA's. De werkelijke leesbeschikbaarheid voor alle consistentieniveaus is aanzienlijk hoger dan de gepubliceerde SLA's.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Hoge beschikbaarheid met Azure Cosmos DB in het geval van regionale uitval

In zeldzame gevallen van regionale uitval zorgt Azure Cosmos DB ervoor dat uw database altijd zeer beschikbaar is. De volgende details leggen Azure Cosmos DB gedrag vast tijdens een storing, afhankelijk van de configuratie van uw Azure Cosmos-account:

* Met Azure Cosmos DB, voordat een schrijfbewerking aan de client wordt bevestigd, worden de gegevens blijvend vastgelegd door een quorum van replica's binnen de regio die de schrijfbewerkingen accepteert. Zie Consistentieniveaus en doorvoer voor [meer informatie](./consistency-levels.md#consistency-levels-and-throughput)

* Accounts voor meerdere regio's die zijn geconfigureerd met meerdere schrijfregio's, zijn zeer beschikbaar voor zowel schrijf- als lees- en schrijfree. Regionale failovers worden gedetecteerd en verwerkt in de Azure Cosmos DB client. Ze zijn ook onmiddellijk en vereisen geen wijzigingen van de toepassing.

* Accounts voor één regio verliezen mogelijk de beschikbaarheid na een regionale storing. Het is altijd raadzaam om ten minste twee regio's **(bij** voorkeur ten minste twee schrijfregio's) in te stellen met uw Azure Cosmos-account om te allen tijde hoge beschikbaarheid te garanderen.

> [!IMPORTANT]
> Wanneer u SQL-API's gebruikt, moet u de Cosmos DB SDK configureren om alle opgegeven leesregio's te gebruiken om te profiteren van de verbeterde beschikbaarheid. Raadpleeg dit [artikel voor](troubleshoot-sdk-availability.md) meer informatie.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Accounts voor meerdere regio's met één schrijfregio (schrijfregio-storing)

* Tijdens een storing in een schrijfregio promovert het Azure Cosmos-account automatisch een secundaire regio naar de nieuwe primaire schrijfregio wanneer automatische **failover** inschakelen is geconfigureerd in het Azure Cosmos-account. Wanneer deze functie is ingeschakeld, vindt de failover plaats in een andere regio in volgorde van de regioprioriteit die u hebt opgegeven.

* Handmatige failover mag niet worden geactiveerd en slaagt niet in aanwezigheid van een storing in de bron- of doelregio. Dit komt door een consistentiecontrole die is vereist voor de failoverprocedure die connectiviteit tussen de regio's vereist.

* Wanneer de eerder beïnvloede regio weer online is, worden schrijfgegevens die niet zijn gerepliceerd toen de regio uitgevallen, beschikbaar gesteld via de [conflictfeed](how-to-manage-conflicts.md#read-from-conflict-feed). Toepassingen kunnen de conflictenfeed lezen, conflicten oplossen op basis van de toepassingsspecifieke logica en de bijgewerkte gegevens waar nodig terugschrijven naar de Azure Cosmos-container.

* Zodra de eerder beïnvloede schrijfregio is hersteld, wordt deze automatisch beschikbaar als een leesregio. U kunt weer overschakelen naar de herstelde regio als de schrijfregio. U kunt tussen regio's schakelen met [behulp van PowerShell, Azure CLI of Azure Portal.](how-to-manage-database-account.md#manual-failover) Er zijn **geen gegevens of beschikbaarheidsverlies** vóór, tijdens of na het overschakelen van de schrijfregio en uw toepassing blijft zeer beschikbaar.

> [!IMPORTANT]
> Het wordt ten zeerste aangeraden de Azure Cosmos-accounts te configureren die worden gebruikt voor productieworkloads om **automatische failover in te stellen.** Hierdoor kunnen Cosmos DB accountdatabases automatisch beschikbaar maken voor regio's. Als deze configuratie niet beschikbaar is, is er voor het account gedurende de hele periode van de schrijfregio-storing geen beschikbaarheid meer beschikbaar, omdat handmatige failover niet lukt vanwege een gebrek aan regioconnectiviteit.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Accounts voor meerdere regio's met één schrijfregio (storing in leesregio)

* Tijdens een storing in een leesregio blijven Azure Cosmos-accounts die consistentieniveau of sterke consistentie met drie of meer leesregio's gebruiken, uiterst beschikbaar voor lees- en schrijfingen.

* Azure Cosmos-accounts die gebruikmaken van een sterke consistentie met drie regio's (één schrijf-, twee lees- en schrijfbeschikbaarheid) blijven beschikbaar tijdens een storing in de leesregio. Voor accounts met twee regio's en automatische failover ingeschakeld, accepteert het account geen schrijfgegevens meer totdat de regio is gemarkeerd als mislukt en automatische failover plaatsvindt.

* De verbinding met de beïnvloede regio wordt automatisch verbroken en wordt als offline gemarkeerd. De [Azure Cosmos DB-SDK's](sql-api-sdk-dotnet.md) leiden leesoproepen om naar de volgende beschikbare regio in de lijst met voorkeursregio's.

* Als geen van de regio's in de lijst met voorkeursregio's beschikbaar is, vallen aanroepen automatisch terug naar de huidige schrijfregio.

* Er zijn geen wijzigingen vereist in uw toepassingscode om leesregio-uitval af te handelen. Wanneer de beïnvloede leesregio weer online is, wordt deze automatisch gesynchroniseerd met de huidige schrijfregio en is deze weer beschikbaar voor het verwerken van leesaanvragen.

* Latere leesbewerkingen worden omgeleid naar de herstelde regio zonder dat er wijzigingen in uw toepassingscode nodig zijn. Tijdens zowel failover als het opnieuw aaneengelen van een eerder mislukte regio, blijven de leesconsistentiegaranties door de Azure Cosmos DB.

* Zelfs in zeldzame en helaas gevallen waarin de Azure-regio permanent onherstelbaar is, is er geen gegevensverlies  als uw Azure Cosmos-account voor meerdere regio's is geconfigureerd met de consistentie Sterk. In het geval van een permanent onherstelbare schrijfregio, een Azure Cosmos-account voor meerdere regio's dat is geconfigureerd met de consistentie Gebonden veroudering, wordt het venster voor mogelijk gegevensverlies beperkt tot het verouderingsvenster *(K* of *T)* waarin K=100.000 updates of T=5 minuten, wat ooit het eerst gebeurt. Voor sessie-, consistent-voorvoegsel- en uiteindelijke consistentieniveaus is het venster voor mogelijk gegevensverlies beperkt tot maximaal 15 minuten. Zie Consistentieniveaus en duurzaamheid van gegevens voor meer informatie over RTO- en RPO-doelen voor [Azure Cosmos DB](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Ondersteuning voor beschikbaarheidszone

Naast regio-overschrijdende tolerantie ondersteunt Azure Cosmos DB **zone-redundantie** in ondersteunde regio's bij het selecteren van een regio die u wilt koppelen aan uw Azure Cosmos-account.

Met ondersteuning voor beschikbaarheidszone (AZ) zorgt Azure Cosmos DB ervoor dat replica's worden geplaatst in meerdere zones binnen een bepaalde regio om hoge beschikbaarheid en tolerantie voor zonefouten te bieden. Beschikbaarheidszones bieden een SLA met een beschikbaarheid van 99,995% zonder wijzigingen in de latentie. In het geval van een storing in één zone biedt zone-redundantie een volledige duurzaamheid van gegevens met RPO =0 en beschikbaarheid met RTO=0. Zone-redundantie is een aanvullende mogelijkheid voor regionale replicatie. Zoneredundantie alleen kan niet worden vertrouwd om regionale tolerantie te bereiken.

Zone-redundantie kan alleen worden geconfigureerd wanneer u een nieuwe regio toevoegt aan een Azure Cosmos-account. Voor bestaande regio's kan zone-redundantie worden ingeschakeld door de regio te verwijderen en deze vervolgens weer toe te voegen met de zone-redundantie ingeschakeld. Voor een account voor één regio moet u één extra regio toevoegen om tijdelijk een failover naar uit te kunnen brengen en vervolgens de gewenste regio te verwijderen en toe te voegen met zone-redundantie ingeschakeld.

Wanneer u schrijfgegevens voor meerdere regio's configureert voor uw Azure Cosmos-account, kunt u zonder extra kosten kiezen voor zone-redundantie. Anders raadpleegt u de onderstaande tabel met betrekking tot de prijzen voor ondersteuning voor zone-redundantie. Zie Beschikbaarheidszones voor een lijst met regio's waar [beschikbaarheidszones beschikbaar zijn.](../availability-zones/az-region.md)

De volgende tabel bevat een overzicht van de mogelijkheden voor hoge beschikbaarheid van verschillende accountconfiguraties:

|KPI|Eén regio zonder AZ's|Eén regio met AZ's|Schrijf schrijft in meerdere regio's en één regio met AZ's|Schrijf schrijft in meerdere regio's met AZ's|
|---------|---------|---------|---------|---------|
|SLA voor schrijfbeschikbaarheid | 99,99% | 99.995% | 99.995% | 99,999% |
|SLA voor leesbeschikbaarheid  | 99,99% | 99.995% | 99.995% | 99,999% |
|Zonefouten – gegevensverlies | Gegevensverlies | Geen gegevensverlies | Geen gegevensverlies | Geen gegevensverlies |
|Zonefouten – beschikbaarheid | Beschikbaarheidsverlies | Geen beschikbaarheidsverlies | Geen beschikbaarheidsverlies | Geen beschikbaarheidsverlies |
|Regionale storing – gegevensverlies | Gegevensverlies |  Gegevensverlies | Afhankelijk van het consistentieniveau. Zie [Consistentie, beschikbaarheid en prestatie-afwegingen](./consistency-levels.md) voor meer informatie. | Afhankelijk van het consistentieniveau. Zie [Consistentie, beschikbaarheid en prestatie-afwegingen](./consistency-levels.md) voor meer informatie.
|Regionale storing – beschikbaarheid | Beschikbaarheidsverlies | Beschikbaarheidsverlies | Geen beschikbaarheidsverlies voor leesregiofout, tijdelijk voor schrijfregiofout | Geen beschikbaarheidsverlies |
|Prijs (***1** _) | N.v.t. | Inrichten van RU/s x 1,25-snelheid | Inrichten van RU/s x 1,25-snelheid (_*_2_**) | Schrijfsnelheid voor meerdere regio's |

***1*** Voor serverloze accounts worden aanvraageenheden (RU) vermenigvuldigd met een factor 1,25.

***2*** Het tarief van 1,25 wordt alleen toegepast op de regio's waarin AZ is ingeschakeld.

Beschikbaarheidszones kunnen worden ingeschakeld via:

* [Azure-portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure-CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager-sjablonen](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Toepassingen met hoge beschikbaar maken

* Bekijk het verwachte [gedrag van de Azure Cosmos SDK's](troubleshoot-sdk-availability.md) tijdens deze gebeurtenissen en welke configuraties van invloed zijn.

* Configureer uw Azure Cosmos-account voor ten minste twee regio's met meerdere schrijfregio's om een hoge beschikbaarheid van schrijven en lezen te garanderen. Deze configuratie biedt de hoogste beschikbaarheid, laagste latentie en beste schaalbaarheid voor zowel lees- als schrijf schrijfmogelijkheden die worden gebruikt door SLA's. Zie Uw [Azure Cosmos-account configureren](tutorial-global-distribution-sql-api.md)met meerdere schrijfregio's voor meer informatie.

* Voor Azure Cosmos-accounts voor meerdere regio's die zijn geconfigureerd met een regio voor één schrijfaccount, moet u automatische [failover](how-to-manage-database-account.md#automatic-failover)inschakelen met behulp van Azure CLI of Azure Portal . Nadat u automatische failover hebt ingeschakeld, wordt er, wanneer er sprake is van een regionaal noodlot, Cosmos DB automatisch een failover van uw account.  

* Zelfs als uw Azure Cosmos-account zeer beschikbaar is, is uw toepassing mogelijk niet correct ontworpen om zeer beschikbaar te blijven. Als u de end-to-end hoge beschikbaarheid van uw toepassing wilt testen als onderdeel van uw toepassingstests of dr-hersteloefeningen, schakelt u de automatische failover voor het account tijdelijk uit, roept u de handmatige failover aan met Behulp van [PowerShell, Azure CLI](how-to-manage-database-account.md#manual-failover)of Azure Portal en bewaakt u vervolgens de failover van uw toepassing. Als u klaar bent, kunt u een failback naar de primaire regio en automatische failover voor het account herstellen.

> [!IMPORTANT]
> Roep geen handmatige failover aan tijdens een Cosmos DB storing in de bron- of doelregio's, omdat hiervoor connectiviteit tussen regio's is vereist om gegevensconsistentie te behouden en het niet lukt.

* Binnen een wereldwijd gedistribueerde databaseomgeving is er een directe relatie tussen het consistentieniveau en de duurzaamheid van gegevens in de aanwezigheid van een storing in de hele regio. Bij het ontwikkelen van uw bedrijfscontinuïteitsplan moet u weten hoe lang het maximaal acceptabel is voordat de toepassing volledig wordt hersteld na een verstorende gebeurtenis. De tijd die een toepassing nodig heeft om volledig te herstellen, wordt RTO (Recovery Time Objective) genoemd. U moet ook inzicht hebben in de maximale periode van recente gegevensupdates die de toepassing kan verliezen bij het herstellen na een verstorende gebeurtenis. Deze periode wordt het beoogde herstelpunt (RPO) genoemd. Zie Consistentieniveaus en duurzaamheid van gegevens voor informatie Azure Cosmos DB RPO en RTO [voor meer informatie](./consistency-levels.md#rto)

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>Wat u kunt verwachten tijdens een Cosmos DB regio-storing

Voor accounts met één regio zullen clients verlies van lees- en schrijfbeschikbaarheid ervaren.

Accounts voor meerdere regio's vertonen verschillende gedragingen, afhankelijk van de volgende tabel.

| Schrijfregio's | Automatische failover | Wat u kunt verwachten | Wat u moet doen |
| -- | -- | -- | -- |
| Eén schrijfregio | Niet ingeschakeld | In geval van een storing in een leesregio worden alle clients omgeleid naar andere regio's. Geen beschikbaarheidsverlies voor lezen of schrijven. Geen gegevensverlies. <p/> In het geval van een storing in de schrijfregio zullen clients schrijfbeschikbaarheidsverlies ervaren. Als er geen sterk consistentieniveau is geselecteerd, zijn sommige gegevens mogelijk niet gerepliceerd naar de resterende actieve regio's. Dit is afhankelijk van het geselecteerde consistenvy-niveau, zoals beschreven in [deze sectie](consistency-levels.md#rto). Als de getroffen regio permanent gegevensverlies heeft, kunnen niet-geplicateerde gegevens verloren gaan. <p/> Cosmos DB worden schrijfbeschikbaarheid automatisch hersteld wanneer de storing is beëindigd. | Zorg er tijdens de storing voor dat er in de resterende regio's voldoende inrichtende RUS's zijn om leesverkeer te ondersteunen. <p/> *Activeer* geen handmatige failover tijdens de storing, omdat deze niet slaagt. <p/> Wanneer de storing is voorbij, past u de inrichtende RUs waar nodig opnieuw aan. |
| Eén schrijfregio | Ingeschakeld | In geval van een storing in een leesregio worden alle clients omgeleid naar andere regio's. Geen beschikbaarheidsverlies voor lezen of schrijven. Geen gegevensverlies. <p/> In het geval van een storing in de schrijfregio zullen clients schrijfbeschikbaarheidsverlies ervaren totdat Cosmos DB automatisch een nieuwe regio als de nieuwe schrijfregio kiest op basis van uw voorkeuren. Als er geen sterk consistentieniveau is geselecteerd, zijn sommige gegevens mogelijk niet gerepliceerd naar de resterende actieve regio's. Dit is afhankelijk van het geselecteerde consistenvy-niveau, zoals beschreven in [deze sectie](consistency-levels.md#rto). Als de getroffen regio permanent gegevensverlies heeft, kunnen niet-geplicateerde gegevens verloren gaan. | Zorg er tijdens de storing voor dat er in de resterende regio's voldoende inrichtende RUS's zijn om leesverkeer te ondersteunen. <p/> *Activeer* geen handmatige failover tijdens de storing, omdat deze niet slaagt. <p/> Wanneer de storing is voorbij, kunt u de schrijfregio weer naar de oorspronkelijke regio verplaatsen en de inrichtende RUs waar nodig opnieuw aanpassen. Accounts met SQL-API's kunnen ook de niet-gerepliceerde gegevens in de mislukte regio herstellen vanuit de [conflictenfeed.](how-to-manage-conflicts.md#read-from-conflict-feed) |
| Meerdere schrijfregio's | Niet van toepassing | Geen beschikbaarheidsverlies voor lezen of schrijven. <p/> Onlangs bijgewerkte gegevens in de mislukte regio kunnen mogelijk niet worden gebruikt in de resterende actieve regio's. Uiteindelijke, consistente voorvoegsel- en sessieconsistentieniveaus garanderen een veroudering van <15 minuten. Gebonden veroudering garandeert minder dan K updates of T seconden, afhankelijk van de configuratie. Als de getroffen regio permanent gegevensverlies heeft, kunnen niet-geplicateerde gegevens verloren gaan. | Zorg er tijdens de storing voor dat er in de resterende regio's voldoende inrichtende RUS's zijn om extra verkeer te ondersteunen. <p/> Wanneer de storing is voorbij, kunt u de inrichtende RUs waar nodig opnieuw aanpassen. Indien mogelijk worden Cosmos DB niet-gerepliceerde gegevens in de mislukte regio automatisch hersteld met behulp van de geconfigureerde conflictoplossingsmethode voor SQL API-accounts en Last Write Wins voor accounts die andere API's gebruiken. |

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u de volgende artikelen lezen:

* [Beschikbaarheid en prestatie-afwegingen voor verschillende consistentieniveaus](./consistency-levels.md)

* [Wereldwijd schalen van ingerichte doorvoer](./request-units.md)

* [Wereldwijde distributie - achter de schermen](global-dist-under-the-hood.md)

* [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)

* [Uw Cosmos-account configureren met meerdere schrijfregio's](how-to-multi-master.md)

* [SDK-gedrag in omgevingen met meerdere regio's](troubleshoot-sdk-availability.md)