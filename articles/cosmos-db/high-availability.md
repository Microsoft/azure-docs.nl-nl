---
title: Hoge beschikbaarheid in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe Azure Cosmos DB hoge Beschik baarheid biedt
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fd704d45aa7dc10835a205f12ce26fc01a7ea44f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584496"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Hoe biedt Azure Cosmos DB hoge Beschik baarheid?
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB biedt hoge Beschik baarheid op twee primaire manieren. Ten eerste Azure Cosmos DB repliceert gegevens over regio's die zijn geconfigureerd binnen een Cosmos-account. Ten tweede houdt Azure Cosmos DB 4 replica's van gegevens in een regio bij.

Azure Cosmos DB is een wereld wijd gedistribueerde database service en is een basis service die beschikbaar is in [alle regio's waar Azure beschikbaar is](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). U kunt een wille keurig aantal Azure-regio's koppelen aan uw Azure Cosmos-account en uw gegevens worden automatisch en transparant gerepliceerd. U kunt op elk gewenst moment een regio toevoegen aan of verwijderen uit uw Azure Cosmos-account. Cosmos DB is beschikbaar in alle vijf verschillende Azure-Cloud omgevingen die beschikbaar zijn voor klanten:

* **Open bare Azure** -Cloud, die wereld wijd beschikbaar is.

* **Azure China 21vianet** is beschikbaar via een unieke samen werking tussen micro soft en 21vianet, een van de grootste Internet providers van het land in China.

* **Azure Duitsland** biedt services onder een model van de gegevens beheerder, wat ervoor zorgt dat klant gegevens in Duitsland blijven onder controle van T-Systems International GmbH, een dochter onderneming van Deutsche Telekom, die fungeert als de Duitse gegevens beheerder.

* **Azure Government** is beschikbaar in vier regio's van de Verenigde Staten naar overheids instanties van de Verenigde Staten en hun partners.

* **Azure Government voor ministerie van defensie (DoD)** is beschikbaar in twee regio's in de Verenigde Staten van het Amerikaanse ministerie van defensie.

Binnen een regio beheert Azure Cosmos DB vier kopieën van uw gegevens als replica's binnen fysieke partities, zoals wordt weer gegeven in de volgende afbeelding:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fysieke partitionering" border="false":::

* De gegevens in azure Cosmos-containers zijn [horizon taal gepartitioneerd](partitioning-overview.md).

* Een partitieset is een verzameling van meerdere replica sets. Binnen elke regio wordt elke partitie beveiligd door een replicaset waarbij alle schrijf bewerkingen worden gerepliceerd en blijvend vastgelegd door een meerderheid van replica's. Replica's worden verdeeld over Maxi maal 10-20 fout domeinen.

* Elke partitie in alle regio's wordt gerepliceerd. Elke regio bevat alle gegevens partities van een Azure Cosmos-container en kan lees-en schrijf bewerkingen uitvoeren wanneer meerdere regio's kunnen worden geschreven.  

Als uw Azure Cosmos-account wordt gedistribueerd over *n* Azure-regio's, zijn er ten minste *n* x 4 kopieën van al uw gegevens. Als u een Azure Cosmos-account in meer dan twee regio's hebt, verbetert de beschik baarheid van uw toepassing en beschikt u over een lage latentie in de bijbehorende regio's.

## <a name="slas-for-availability"></a>Sla's voor Beschik baarheid

Azure Cosmos DB biedt uitgebreide service overeenkomsten die de door Voer, latentie bij het 99e percentiel, consistentie en hoge Beschik baarheid omvatten. In de volgende tabel ziet u de garanties voor maximale Beschik baarheid van Azure Cosmos DB voor accounts met meerdere regio's. Voor een hogere schrijf Beschik baarheid configureert u uw Azure Cosmos-account zodanig dat er meerdere schrijf regio's zijn.

|Het type bewerking  | Eén regio |Meerdere regio's (schrijf bewerkingen met één regio)|Meerdere regio's (schrijf bewerkingen met meerdere regio's) |
|---------|---------|---------|-------|
|Schrijfbewerkingen    | 99,99    |99,99   |99,999|
|Leesbewerkingen     | 99,99    |99,999  |99,999|

> [!NOTE]
> In de praktijk is de daad werkelijke schrijf Beschik baarheid voor gebonden veroudering, sessie, consistent voor voegsel en uiteindelijke consistentie modellen aanzienlijk hoger dan de gepubliceerde Sla's. De werkelijke Lees Beschik baarheid voor alle consistentie niveaus is aanzienlijk hoger dan de gepubliceerde Sla's.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Hoge Beschik baarheid met Azure Cosmos DB in het geval van regionale storingen

Voor zeldzame gevallen van regionale uitval zorgt Azure Cosmos DB ervoor dat uw data base altijd Maxi maal beschikbaar is. De volgende details vastleggen Azure Cosmos DB gedrag tijdens een storing, afhankelijk van de configuratie van uw Azure Cosmos-account:

* Met Azure Cosmos DB voordat een schrijf bewerking wordt bevestigd aan de client, worden de gegevens blijvend vastgelegd door een quorum van replica's binnen de regio die de schrijf bewerkingen accepteert. Zie voor meer informatie [consistentie niveaus en door Voer](./consistency-levels.md#consistency-levels-and-throughput)

* Multi-regio-accounts die zijn geconfigureerd met meerdere-schrijf regio's, zijn Maxi maal beschikbaar voor schrijf bewerkingen en lees bewerkingen. Regionale failovers worden gedetecteerd en verwerkt in de Azure Cosmos DB-client. Ze zijn ook onmiddellijk en vereisen geen wijzigingen van de toepassing.

* Accounts met een enkele regio kunnen Beschik baarheid verliezen na een regionale storing. Het is altijd aanbevolen om ten **minste twee regio's** (bij voor keur, ten minste twee schrijf regio's) in te stellen met uw Azure Cosmos-account om te allen tijde hoge Beschik baarheid te garanderen.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Accounts met meerdere regio's met een regio voor één schrijf bewerking (schrijf regio-uitval)

* Tijdens een onderbreking van de schrijf regio wordt een secundaire regio in het Azure Cosmos-account automatisch bevorderd als de nieuwe primaire schrijf regio als **automatische failover inschakelen** is geconfigureerd op het Azure Cosmos-account. Wanneer deze functie is ingeschakeld, wordt de failover uitgevoerd naar een andere regio in de volg orde van de regio prioriteit die u hebt opgegeven.

* Houd er rekening mee dat hand matige failover niet moet worden geactiveerd en dat de aanwezigheid van de bron-of doel regio niet kan worden uitgevoerd. Dit komt doordat een consistentie controle vereist is voor de failover-procedure die de verbinding tussen de regio's vereist.

* Wanneer de eerder beïnvloede regio weer online is, worden alle Schrijf gegevens die niet zijn gerepliceerd toen de regio is mislukt, beschikbaar gesteld via de [feed conflicten](how-to-manage-conflicts.md#read-from-conflict-feed). Toepassingen kunnen de feed voor conflicten lezen, de conflicten oplossen op basis van de toepassingsspecifieke logica en de bijgewerkte gegevens naar de Azure Cosmos-container schrijven, indien van toepassing.

* Zodra de eerder beïnvloede schrijf regio herstelt, wordt deze automatisch beschikbaar als een lees regio. U kunt teruggaan naar de herstelde regio als de schrijf regio. U kunt de regio's wijzigen met behulp van [Power shell, Azure CLI of Azure Portal](how-to-manage-database-account.md#manual-failover). Er zijn **geen gegevens of beschik baarheids verlies** vóór, tijdens of nadat u de schrijf regio hebt overgeschakeld en uw toepassing Maxi maal beschikbaar is.

> [!IMPORTANT]
> Het wordt ten zeerste aangeraden om de Azure Cosmos-accounts te configureren die worden gebruikt voor productie werkbelastingen om **automatische failover mogelijk te maken**. Hierdoor kunnen Cosmos DB automatisch een failover van de account databases naar beschikbaar regio's. Als deze configuratie niet is geconfigureerd, wordt de schrijf Beschik baarheid voor de duur van de onderbreking van de schrijf regio niet meer in rekening gebracht, omdat de hand matige failover niet kan worden uitgevoerd als gevolg van een gebrek aan regionale connectiviteit.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Accounts met meerdere regio's met een regio voor één schrijf bewerking (Lees regio lezen)

* Tijdens een onderbreking van de Lees regio worden Azure Cosmos-accounts die gebruikmaken van een consistentie niveau of sterke consistentie met drie of meer Lees regio's, Maxi maal beschikbaar voor lees-en schrijf bewerkingen.

* Azure Cosmos-accounts die gebruikmaken van sterke consistentie met drie regio's (één schrijven, twee gelezen), behouden de beschik baarheid van schrijven tijdens een onderbreking van de Lees regio. Voor accounts waarvoor twee regio's en automatische failover zijn ingeschakeld, accepteert het account geen schrijf bewerkingen meer totdat de regio is gemarkeerd als mislukt en er een automatische failover wordt uitgevoerd.

* De verbinding met het betrokken gebied wordt automatisch verbroken en wordt offline gemarkeerd. De [Azure Cosmos DB sdk's](sql-api-sdk-dotnet.md) omleiden Lees aanroepen naar de volgende beschik bare regio in de lijst voorkeurs regio.

* Als geen van de regio's in de lijst met voorkeursregio's beschikbaar is, vallen aanroepen automatisch terug naar de huidige schrijfregio.

* Er zijn geen wijzigingen vereist in de toepassings code voor het verwerken van de onderbreking van de Lees regio. Wanneer de betrokken Lees regio weer online is, wordt deze automatisch gesynchroniseerd met de huidige schrijf regio en is deze weer beschikbaar om Lees aanvragen te kunnen behandelen.

* Latere leesbewerkingen worden omgeleid naar de herstelde regio zonder dat er wijzigingen in uw toepassingscode nodig zijn. Tijdens zowel de failover als het opnieuw deel nemen aan een eerder mislukte regio, worden Lees consistentie garanties door Azure Cosmos DB door lopen.

* Zelfs in een zeldzame en vervelend-gebeurtenis wanneer de Azure-regio permanent is onherstelbare, is er geen gegevens verlies als uw Azure Cosmos-account met meerdere regio's is geconfigureerd met *sterke* consistentie. In het geval van een permanente onherstelbare-schrijf regio, een Azure Cosmos-account met meerdere regio's dat is geconfigureerd met consistentie van afhankelijkheid, wordt het gegevens verlies venster beperkt tot het verouderde venster (*k* of *t*) waarbij K = 100000 updates of t = 5 minuten, die ooit het eerst gebeurt. Voor sessie, consistent voor voegsel en uiteindelijke consistentie niveaus geldt een maximum van 15 minuten voor het venster van het potentiële gegevens verlies. Zie voor meer informatie over RTO-en RPO-doelen voor Azure Cosmos DB [consistentie niveaus en gegevens duurzaamheid](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Ondersteuning voor beschikbaarheids zone

Naast de tolerantie voor meerdere regio's ondersteunt Azure Cosmos DB ook **zone redundantie** in ondersteunde regio's wanneer u een regio selecteert om te koppelen aan uw Azure Cosmos-account.

Met de ondersteuning voor de beschikbaarheids zone (AZ) zorgt Azure Cosmos DB ervoor dat replica's in meerdere zones binnen een bepaalde regio worden geplaatst om hoge Beschik baarheid en tolerantie te bieden voor zonegebonden storingen. Beschikbaarheidszones bieden een SLA van 99,995% Beschik baarheid zonder wijzigingen in de latentie. In het geval van een storing in één zone biedt zone redundantie volledige gegevens duurzaamheid met RPO = 0 en beschik baarheid met RTO = 0. Zone redundantie is een aanvullende mogelijkheid voor regionale replicatie. Alleen zone redundantie kan worden verzorgd om regionale tolerantie te garanderen.

Zone redundantie kan alleen worden geconfigureerd bij het toevoegen van een nieuwe regio aan een Azure Cosmos-account. Voor bestaande regio's kan zone redundantie worden ingeschakeld door de regio te verwijderen en vervolgens weer toe te voegen met de zone redundantie ingeschakeld. Voor een account van één regio moet er één extra regio worden toegevoegd aan een tijdelijke failover naar, de gewenste regio verwijderen en toevoegen met zone redundantie ingeschakeld.

Bij het configureren van schrijf bewerkingen met meerdere regio's voor uw Azure Cosmos-account, kunt u zonder extra kosten voor zone redundantie kiezen. Raadpleeg anders de onderstaande tabel met betrekking tot de prijzen voor ondersteuning voor zone redundantie. Zie de [beschikbaarheids zones](../availability-zones/az-region.md)voor een lijst met regio's waarvoor beschikbaarheids zones beschikbaar zijn.

De volgende tabel bevat een overzicht van de mogelijkheden voor hoge Beschik baarheid van verschillende account configuraties:

|KPI|Eén regio zonder AZs|Eén regio met AZs|Meerdere regio's, schrijf bewerkingen met één regio met AZs|Meerdere regio's, meerdere regio's schrijven met AZs|
|---------|---------|---------|---------|---------|
|SLA voor Beschik baarheid schrijven | 99,99% | 99,995% | 99,995% | 99,999% |
|SLA voor Beschik baarheid lezen  | 99,99% | 99,995% | 99,995% | 99,999% |
|Zone fouten – gegevens verlies | Gegevensverlies | Geen gegevens verlies | Geen gegevens verlies | Geen gegevens verlies |
|Zone fouten-Beschik baarheid | Beschikbaarheids verlies | Geen beschikbaar verlies | Geen beschikbaar verlies | Geen beschikbaar verlies |
|Regionale storing – gegevens verlies | Gegevensverlies |  Gegevensverlies | Afhankelijk van consistentie niveau. Bekijk [consistentie, Beschik baarheid en prestaties](./consistency-levels.md) voor meer informatie. | Afhankelijk van consistentie niveau. Bekijk [consistentie, Beschik baarheid en prestaties](./consistency-levels.md) voor meer informatie.
|Regionale storingen-Beschik baarheid | Beschikbaarheids verlies | Beschikbaarheids verlies | Geen Beschik baarheid in het geval van een storing in de Lees regio, tijdelijke fout bij het schrijven van regio's | Geen beschikbaar verlies |
|Prijs (***1** _) | N.v.t. | Ingericht aantal RU/s x 1,25 | Ingericht aantal RU/s x 1,25 (_ *_2_* *) | Schrijf frequentie voor meerdere regio's |

***1*** voor serverloze accounts aanvraag eenheden (ru) worden vermenigvuldigd met een factor van 1,25.

***2*** 1,25 rente alleen toegepast op de REGIO'S waarin AZ is ingeschakeld.

Beschikbaarheidszones kan worden ingeschakeld via:

* [Azure-portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure-CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager-sjablonen](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Maxi maal beschik bare toepassingen bouwen

* Bekijk het verwachte [gedrag van de Azure Cosmos-sdk's](troubleshoot-sdk-availability.md) tijdens deze gebeurtenissen. Dit zijn de configuraties die van invloed zijn op de app.

* Configureer uw Azure Cosmos-account om te zorgen voor hoge schrijf-en lees beschikbaarheid om ten minste twee regio's te omvatten met meerdere-schrijf regio's. Deze configuratie biedt de hoogste Beschik baarheid, de laagste latentie en de beste schaal baarheid voor zowel lees-als schrijf bewerkingen die worden ondersteund door service overeenkomsten. Zie How to [Configure Your Azure Cosmos account with meerdere write-Regions](tutorial-global-distribution-sql-api.md)(Engelstalig) voor meer informatie.

* Voor Azure Cosmos-accounts met meerdere regio's die zijn geconfigureerd met een enkele schrijf regio, [schakelt u automatische failover in met behulp van Azure CLI of Azure Portal](how-to-manage-database-account.md#automatic-failover). Nadat u automatische failover hebt ingeschakeld, Cosmos DB automatisch een failover van uw account uit, wanneer er een regionale nood geval is.  

* Zelfs als uw Azure Cosmos-account Maxi maal beschikbaar is, is uw toepassing mogelijk niet juist ontworpen om Maxi maal beschikbaar te blijven. Als u de end-to-end hoge Beschik baarheid van uw toepassing wilt testen, kunt u als onderdeel van uw toepassing tests of herstel na nood gevallen, de automatische failover voor het account tijdelijk uitschakelen, de [hand matige failover aanroepen met behulp van Power shell, Azure CLI of Azure Portal](how-to-manage-database-account.md#manual-failover)en vervolgens de failover van uw toepassing controleren. Zodra het proces is voltooid, kunt u een failover uitvoeren naar de primaire regio en de automatische failover voor het account herstellen.

> [!IMPORTANT]
> Roep geen hand matige failover aan tijdens een Cosmos DB-onderbreking op de bron-of doel regio's, omdat er regio's moeten worden verbonden om de consistentie van de gegevens te waarborgen.

* Binnen een wereld wijd gedistribueerde database omgeving is er een rechtstreekse relatie tussen het consistentie niveau en de duurzaamheid van de gegevens in de aanwezigheid van een regionale storing. Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld na een storende gebeurtenis. De tijd die nodig is om een toepassing volledig te herstellen, wordt de beoogde herstel tijd (RTO) genoemd. U moet ook inzicht krijgen in de maximale periode van recente gegevens updates die de toepassing kan afnemen bij het herstellen na een storende gebeurtenis. Deze periode wordt het beoogde herstelpunt (RPO) genoemd. Zie [consistentie niveaus en gegevens duurzaamheid](./consistency-levels.md#rto) voor een overzicht van de RPO en RTO voor Azure Cosmos db.

## <a name="what-to-expect-during-a-region-outage"></a>Wat u kunt verwachten tijdens de onderbreking van een regio

Voor accounts met één regio kunnen clients de lees-en schrijf Beschik baarheid verliezen.

Accounts met meerdere regio's zijn afhankelijk van de volgende tabel.

| Regio's schrijven | Automatische failover | Wat u kunt verwachten | Wat u moet doen |
| -- | -- | -- | -- |
| Enkele schrijf regio | Niet ingeschakeld | In het geval van een storing in een lees regio worden alle clients omgeleid naar andere regio's. Geen lees-of schrijf Beschik baarheid. Geen gegevens verlies. <p/> In het geval van een storing in de schrijf regio, kunnen clients schrijf Beschik baarheid verliezen. Gegevens verlies is afhankelijk van het geselecteerde constistency-niveau. <p/> Cosmos DB wordt de schrijf beschikbaarheid automatisch hersteld wanneer de storing wordt beëindigd. | Zorg er tijdens de onderbreking voor dat er voldoende capaciteit is ingericht in de overige regio's ter ondersteuning van het Lees verkeer. <p/> Activeer geen hand matige failover tijdens de onderbreking, omdat *deze niet kan* worden uitgevoerd. <p/> Als de storing is overschreden, past u de ingerichte capaciteit zo nodig aan. |
| Enkele schrijf regio | Ingeschakeld | In het geval van een storing in een lees regio worden alle clients omgeleid naar andere regio's. Geen lees-of schrijf Beschik baarheid. Geen gegevens verlies. <p/> In het geval van een storing in de schrijf regio, ondervindt clients het verlies van Beschik baarheid voordat Cosmos DB automatisch een nieuwe regio kiest als de nieuwe schrijf regio volgens uw voor keuren. Gegevens verlies is afhankelijk van het geselecteerde constistency-niveau. | Zorg er tijdens de onderbreking voor dat er voldoende capaciteit is ingericht in de overige regio's ter ondersteuning van het Lees verkeer. <p/> Activeer geen hand matige failover tijdens de onderbreking, omdat *deze niet kan* worden uitgevoerd. <p/> Wanneer de storing is opgetreden, kunt u de niet-gerepliceerde gegevens in de mislukte regio herstellen vanuit de [feed met conflicten](how-to-manage-conflicts.md#read-from-conflict-feed), de schrijf regio opnieuw naar de oorspronkelijke regio verplaatsen en de ingerichte capaciteit aanpassen naar wens. |
| Meerdere schrijf regio's | Niet van toepassing | Geen lees-of schrijf Beschik baarheid. <p/> Gegevens verlies per consistentie niveau geselecteerd. | Zorg er tijdens de onderbreking voor dat er voldoende capaciteit is ingericht in de overige regio's ter ondersteuning van extra verkeer. <p/> Wanneer de storing is opgetreden, kunt u de niet-gerepliceerde gegevens in de regio mislukt herstellen vanuit de [feed voor conflicten](how-to-manage-conflicts.md#read-from-conflict-feed) en de ingerichte capaciteit indien nodig opnieuw aanpassen. |

## <a name="next-steps"></a>Volgende stappen

Daarna kunt u de volgende artikelen lezen:

* [Beschik baarheid en prestaties voor diverse consistentie niveaus](./consistency-levels.md)

* [Wereldwijd schalen van ingerichte doorvoer](./request-units.md)

* [Wereldwijde distributie - achter de schermen](global-dist-under-the-hood.md)

* [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)

* [Uw Cosmos-account configureren met meerdere schrijf regio's](how-to-multi-master.md)

* [SDK-gedrag voor omgevingen met meerdere regio's](troubleshoot-sdk-availability.md)