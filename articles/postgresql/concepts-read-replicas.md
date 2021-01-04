---
title: Replica's lezen-Azure Database for PostgreSQL-één server
description: In dit artikel wordt de functie voor het lezen van replica's in Azure Database for PostgreSQL-één server beschreven.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: dc19b95e891235ac35c703adef50a23a9f70fbdb
ms.sourcegitcommit: 0830e02635d2f240aae2667b947487db01f5fdef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/21/2020
ms.locfileid: "97706793"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Replica's lezen in Azure Database for PostgreSQL-één server

Met de functie replica lezen kunt u gegevens van een Azure Database for PostgreSQL server repliceren naar een alleen-lezen server. Replica's worden **asynchroon** bijgewerkt met de systeem eigen fysieke replicatie technologie van de postgresql-engine. U kunt van de primaire server naar Maxi maal vijf replica's repliceren.

Replica's zijn nieuwe servers die u op een soortgelijke manier beheert als gewone Azure Database for PostgreSQL-servers. Voor elke Lees replica wordt u gefactureerd voor de ingerichte Compute in vCores en Storage in GB/maand.

Meer informatie over het [maken en beheren van replica's](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Wanneer moet u een lees replica gebruiken?
De functie voor het lezen van replica's helpt bij het verbeteren van de prestaties en schaal baarheid van Lees bare werk belastingen. Lees werkbelastingen kunnen worden geïsoleerd voor de replica's, terwijl schrijf werkbelastingen kunnen worden omgeleid naar de primaire. Het lezen van replica's kan ook worden geïmplementeerd in een andere regio en kan worden gepromoveerd tot een lezen/schrijven-server in het geval van een nood herstel.

Een veelvoorkomend scenario is om BI-en analytische werk belastingen de Lees replica te laten gebruiken als gegevens bron voor rapportage.

Omdat replica's alleen-lezen zijn, beperken ze niet rechtstreeks de overhead van de schrijf capaciteit op de primaire.

### <a name="considerations"></a>Overwegingen
De functie is bedoeld voor scenario's waarbij de vertraging acceptabel is en bedoeld is voor het offloaden van query's. Het is niet bedoeld voor synchrone replicatie scenario's waarbij de replica gegevens naar verwachting actueel zijn. Er is een meet bare vertraging tussen de primaire en de replica. Dit kan binnen enkele minuten of zelfs uren worden uitgevoerd, afhankelijk van de werk belasting en de latentie tussen de primaire en de replica. De gegevens op de replica worden uiteindelijk consistent met de gegevens op de primaire. Gebruik deze functie voor werk belastingen die deze vertraging kunnen bevatten. 

> [!NOTE]
> Voor de meeste werk belastingen kunnen replica's worden gelezen die bijna in realtime worden bijgewerkt. De replicatie vertraging kan echter nog steeds worden uitgebreid met duurzame, zware write-intensieve primaire workloads en kan niet worden opgeteld bij de primaire. Hierdoor kan het gebruik van de opslag op het primaire bestand ook toenemen als de WAL-bestanden niet worden verwijderd totdat ze zijn ontvangen op de replica. Als dit probleem zich blijft voordoen, het verwijderen en opnieuw maken van de Lees replica nadat de Write-intensieve workloads zijn voltooid, is de optie om de replica terug te zetten naar een goede status met betrekking tot de vertraging.
> Asynchrone Lees replica's zijn niet geschikt voor dergelijke zware schrijf werkbelastingen. Bij het evalueren van replica's voor uw toepassing controleert u de vertraging van de replica voor een volledige werk belasting van de app en de piek tijden en de verwachte RTO/RPO op verschillende punten van de werkbelasting cyclus.
> 
## <a name="cross-region-replication"></a>Replicatie in meerdere regio's
U kunt een lees replica maken in een andere regio dan de primaire server. Replicatie tussen regio's kan handig zijn voor scenario's zoals het plannen van herstel na nood gevallen of gegevens dichter bij uw gebruikers te brengen.

>[!NOTE]
> Basic-laag servers ondersteunen alleen replicatie met dezelfde regio.

U kunt een primaire server in een [Azure database for PostgreSQL regio](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)hebben. Een primaire server kan een replica hebben in het gekoppelde gebied of in de universele replica regio's. In de onderstaande afbeelding ziet u welke replica regio's er beschikbaar zijn, afhankelijk van de primaire regio.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Replica regio's lezen":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universele replica regio's
U kunt altijd een lees replica maken in een van de volgende regio's, ongeacht waar de primaire server zich bevindt. Dit zijn de universele replica regio's:

Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, VS, Azië-oost, VS-Oost, VS-Oost 2, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Noord-Centraal VS, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, UK-west, Europa-west, VS-West, VS-West-Centraal vs.

### <a name="paired-regions"></a>Gekoppelde regio's
Naast de universele replica regio's, kunt u een lees replica maken in het gekoppelde Azure-gebied van de primaire server. Als u het paar van uw regio niet weet, kunt u meer informatie vinden in het [artikel gekoppelde regio's in azure](../best-practices-availability-paired-regions.md).

Als u verschillende regio's replica's gebruikt voor het plannen van herstel na nood gevallen, raden we u aan om de replica in het gekoppelde gebied te maken in plaats van een van de andere regio's. Gekoppelde regio's vermijden gelijktijdige updates en geven geen prioriteiten voor fysieke isolatie en gegevens locatie.  

U moet rekening houden met de volgende beperkingen: 

* Regionale Beschik baarheid: Azure Database for PostgreSQL is beschikbaar in Frankrijk-centraal, UAE-noord en Duitsland-centraal. De gekoppelde regio's zijn echter niet beschikbaar.
    
* Uni-directionele paren: sommige Azure-regio's zijn in slechts één richting gekoppeld. Deze regio's omvatten West-India, Brazilië-zuid. 
   Dit betekent dat een primaire server in West-India een replica kan maken in India-zuid. Een primaire server in India-zuid kan echter geen replica maken in West-India. Dit komt doordat de secundaire regio van West-India India-zuid is, India-zuid maar de secundaire regio van het westen is niet West-India.


## <a name="create-a-replica"></a>Replica's maken
Wanneer u de werk stroom voor het maken van de replica start, wordt er een lege Azure Database for PostgreSQL-server gemaakt. De nieuwe server wordt gevuld met de gegevens die zich op de primaire server bevonden. De aanmaak tijd is afhankelijk van de hoeveelheid gegevens op de primaire en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van een paar minuten tot enkele uren.

Elke replica is ingeschakeld voor [automatische groei](concepts-pricing-tiers.md#storage-auto-grow)van opslag. Met de functie voor automatisch uitbreiden kan de replica de gegevens repliceren, en wordt voor komen dat de replicatie wordt onderbroken vanwege onvoldoende opslag fouten.

De functie voor het lezen van replica's maakt gebruik van fysieke PostgreSQL-replicatie, geen logische replicatie. Het streamen van replicatie via replicatie sleuven is de standaard bewerkings modus. Als dat nodig is, wordt de back-upfunctie voor logboek registratie gebruikt voor het bijwerken.

Meer informatie over [het maken van een lees replica in de Azure Portal](howto-read-replicas-portal.md).

Als uw bron PostgreSQL-server is versleuteld met door de klant beheerde sleutels, raadpleegt u de [documentatie](concepts-data-encryption-postgresql.md) voor aanvullende overwegingen.

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica
Wanneer u een replica maakt, neemt deze de firewall regels of het VNet-service-eind punt van de primaire server niet over. Deze regels moeten onafhankelijk worden ingesteld voor de replica.

De replica neemt het beheerders account over van de primaire server. Alle gebruikers accounts op de primaire server worden gerepliceerd naar de replica's die worden gelezen. U kunt alleen verbinding maken met een lees replica met behulp van de gebruikers accounts die beschikbaar zijn op de primaire server.

U kunt verbinding maken met de replica door de hostnaam en een geldig gebruikers account te gebruiken, net zoals bij een gewone Azure Database for PostgreSQL-server. Voor een server met de naam **mijn replica** met de gebruikers naam **myadmin**, kunt u verbinding maken met de replica met behulp van psql:

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Voer bij de prompt het wacht woord voor het gebruikers account in.

## <a name="monitor-replication"></a>Replicatie controleren
Azure Database for PostgreSQL biedt twee metrische gegevens voor het controleren van replicatie. De twee meet waarden zijn de **maximale vertraging voor replica's** en **replica vertraging**. Zie voor meer informatie over het weer geven van deze metrische gegevens het gedeelte **een replica bewaken** in het [artikel Lees-en replica-instructies](howto-read-replicas-portal.md).

De **maximale vertraging** voor de metrische gegevens van replica's toont de vertraging in bytes tussen de primaire en de meest bewaarde replica. Deze metriek is alleen van toepassing op de primaire server en is alleen beschikbaar als ten minste één van de Lees replica's is verbonden met de primaire en de primaire-replicatie modus. De informatie over de vertraging geeft geen details weer wanneer de replica wordt uitgevoerd met de gearchiveerde logboeken van de primaire gegevens in een replicatie modus voor bestands verzendingen.

De metriek van de **replica vertraging** toont de tijd sinds de laatste geplayte trans actie. Als er geen trans acties plaatsvinden op de primaire server, weerspiegelt de metriek deze tijds periode. Deze metrische gegevens zijn alleen van toepassing en zijn alleen beschikbaar voor replica servers. Replica vertraging wordt berekend op basis van de `pg_stat_wal_receiver` weer gave:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Stel een waarschuwing in om u te informeren wanneer de replica vertraging een waarde bereikt die niet geschikt is voor uw werk belasting. 

Voor meer inzicht moet u rechtstreeks een query uitvoeren op de primaire server om de replicatie vertraging in bytes op alle replica's te verkrijgen.

> [!NOTE]
> Als een primaire server of een lees bewerking van de replica opnieuw wordt gestart, wordt de tijd die nodig is om opnieuw op te starten en te worden weer gegeven in de metrische gegevens van de replica vertraging.

## <a name="stop-replication--promote-replica"></a>Replicatie stoppen/replica verhogen
U kunt de replicatie tussen een primaire en een replica op elk gewenst moment stoppen. Met de actie stoppen wordt de replica opnieuw opgestart en wordt de replica gepromoot als een onafhankelijke, zelfstandige Lees bare server. De gegevens op de zelfstandige server zijn de gegevens die beschikbaar waren op de replica server op het moment dat de replicatie wordt gestopt. Volgende updates bij de primaire worden niet door gegeven aan de replica. Op de replica server kunnen echter logboeken zijn verzameld die nog niet zijn toegepast. Als onderdeel van het proces om het opnieuw te starten, gelden de replica alle logboeken in behandeling voordat client verbindingen worden geaccepteerd.  

### <a name="considerations"></a>Overwegingen
- Voordat u de replicatie op een lees replica stopt, controleert u of de replicatie vertraging heeft om te controleren of de replica alle gegevens bevat die u nodig hebt. 
- Als de Lees replica alle logboeken in behandeling moet Toep assen voordat deze kan worden gemaakt op een zelfstandige server, kan RTO hoger zijn voor het schrijven van zware werk belastingen wanneer de replicatie stoppen als er aanzienlijke vertraging optreedt op de replica. Let op dit wanneer u van plan bent een replica te promo veren.
- De gepromoveerde replica server kan niet opnieuw worden gemaakt in een replica.
- Als u een replica promo veren als primaire server, kunt u geen replicatie terugzetten naar de oude primaire server. Als u wilt terugkeren naar de oude primaire regio, kunt u een nieuwe replica-server met een nieuwe naam maken (of) de oude primaire verwijderen en een replica maken met de oude primaire naam.
- Als u meerdere replica's hebt gelezen en u het niveau van een van hen naar uw primaire server wilt promo veren, worden er nog andere replica servers verbonden met de oude primaire. U moet mogelijk replica's opnieuw maken op basis van de nieuwe, gepromoveerde server.

Wanneer u de replicatie stopt, verliest de replica alle koppelingen met de vorige primaire en andere replica's.

Meer informatie over het [stoppen van replicatie naar een replica](howto-read-replicas-portal.md).

## <a name="failover-to-replica"></a>Failover naar replica

In het geval van een primaire server fout, wordt er **niet** automatisch een failover naar de Lees replica uitgevoerd. 

Omdat replicatie asynchroon is, kan er een aanzienlijke vertraging optreden tussen de primaire en de replica. De hoeveelheid vertraging wordt beïnvloed door een aantal factoren, zoals het type werk belasting dat wordt uitgevoerd op de primaire server en de latentie tussen de primaire en de replica server. In typische gevallen met nominaal schrijf werk belasting wordt een replica vertraging verwacht tussen enkele seconden en paar minuten. In gevallen waarin de primaire uitvoering zeer zware werk belasting voor schrijven en de replica niet snel genoeg is, kan de vertraging echter veel hoger zijn. U kunt de replicatie vertraging voor elke replica bijhouden met behulp van de metrische *replica vertraging*. Met deze metriek wordt de tijd weer gegeven sinds de laatste herspeelde trans actie op de replica. U wordt aangeraden de gemiddelde vertraging te identificeren door de replica vertraging gedurende een bepaalde periode te bestuderen. U kunt een waarschuwing instellen voor replica vertraging, zodat u wordt gewaarschuwd om actie te ondernemen als deze buiten het verwachte bereik komt.

> [!Tip]
> Als u een failover naar de replica doorzoekt, geeft de vertraging op het moment dat u de replica loskoppelt, aan hoeveel gegevens er verloren zijn gegaan.

Zodra u hebt vastgesteld dat u een failover naar een replica wilt uitvoeren, 

1. Replicatie naar de replica stoppen<br/>
   Deze stap is nodig om de replica server een zelfstandige server te maken en schrijf bewerkingen te kunnen accepteren. Als onderdeel van dit proces wordt de replica server opnieuw opgestart en ontkoppeld van de primaire. Zodra u stopt met het uitvoeren van replicatie, duurt het back-end doorgaans enkele minuten om eventuele overbodige Logboeken toe te passen die nog niet zijn toegepast en om de data base te openen als een lees-schrijfbaar server. Zie de sectie [Replicatie stoppen](#stop-replication--promote-replica) in dit artikel voor meer informatie over de implicaties van deze actie.
    
2. Uw toepassing naar de (voormalige) replica laten wijzen<br/>
   Elke server heeft een unieke connection string. Werk uw toepassing bij connection string zodat deze verwijst naar de (voormalige) replica in plaats van de primaire.
    
Zodra uw toepassing Lees-en schrijf bewerkingen heeft verwerkt, hebt u de failover voltooid. De uitval tijd van uw toepassings ervaring is afhankelijk van wanneer u een probleem detecteert en de stappen 1 en 2 hierboven uitvoert.

### <a name="disaster-recovery"></a>Herstel na noodgeval

Wanneer er sprake is van een belang rijke nood geval, zoals een zone-niveau of regionale storingen op beschikbaarheids gebied, kunt u herstel na nood gevallen uitvoeren door uw Lees replica te promo veren. U kunt vanuit de gebruikers interface-Portal naar de server voor het lezen van replica's navigeren. Klik vervolgens op het tabblad Replicatie en u kunt de replica stoppen om deze te promo veren tot een onafhankelijke server. U kunt ook de [Azure cli](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) gebruiken om de replica-server te stoppen en te promo veren.

## <a name="considerations"></a>Overwegingen

In deze sectie vindt u een overzicht van de overwegingen voor de functie replica lezen.

### <a name="prerequisites"></a>Vereisten
Het lezen van replica's en [logische decodering](concepts-logical.md) is beide afhankelijk van het post gres-logboek (write-Ahead) (Wal) voor informatie. Deze twee functies hebben verschillende niveaus van logboek registratie nodig van post gres. Voor logische decodering is een hoger niveau van logboek registratie vereist dan bij het lezen van replica's.

Als u het juiste niveau van logboek registratie wilt configureren, gebruikt u de Azure Replication support-para meter. Ondersteuning voor Azure-replicatie heeft drie instellings opties:

* **Uit** : Hiermee wordt de minste informatie in de wal geplaatst. Deze instelling is niet beschikbaar op de meeste Azure Database for PostgreSQL-servers.  
* **Replica** : uitgebreidere dan **uit**. Dit is het minimale registratie niveau dat nodig is voor het werken met [replica's](concepts-read-replicas.md) . Deze instelling is de standaard waarde op de meeste servers.
* **Logisch** -uitgebreidere dan de **replica**. Dit is het minimale registratie niveau voor het werken met logische code ring. Het lezen van replica's werkt ook bij deze instelling.


### <a name="new-replicas"></a>Nieuwe replica's
Er wordt een lees replica gemaakt als een nieuwe Azure Database for PostgreSQL-server. Een bestaande server kan niet worden gemaakt in een replica. Het is niet mogelijk om een replica van een andere Lees replica te maken.

### <a name="replica-configuration"></a>Replica configuratie
Een replica wordt gemaakt met behulp van dezelfde reken-en opslag instellingen als de primaire. Nadat een replica is gemaakt, kunnen verschillende instellingen worden gewijzigd, waaronder opslag en back-up van de Bewaar periode.

Firewall regels, regels voor virtuele netwerken en parameter instellingen worden niet overgenomen van de primaire server naar de replica wanneer de replica wordt gemaakt of daarna.

### <a name="scaling"></a>Schalen
VCores schalen of tussen Algemeen en geoptimaliseerd voor geheugen:
* PostgreSQL vereist `max_connections` dat de instelling op een secundaire server [groter is dan of gelijk is aan de instelling op de primaire](https://www.postgresql.org/docs/current/hot-standby.html), anders kan het secundaire niet worden gestart.
* In Azure Database for PostgreSQL wordt het Maxi maal toegestane aantal verbindingen voor elke server vastgesteld op de reken-SKU sinds de verbindingen van het geheugen bezet zijn. U vindt meer informatie over de [toewijzing tussen max_connections en Compute-sku's](concepts-limits.md).
* **Omhoog schalen**: u kunt de berekening van een replica eerst opschalen en vervolgens omhoog schalen. Deze volg orde zorgt ervoor dat fouten de vereiste niet schenden `max_connections` .
* **Omlaag schalen**: schaal eerst de primaire Compute en schaal vervolgens omlaag in de replica. Als u de replica lager wilt schalen dan de primaire, treedt er een fout op omdat dit de vereiste schendt `max_connections` .

Opslag ruimte schalen:
* Voor alle replica's is opslag automatisch verg Roten ingeschakeld om replicatie problemen vanuit een opslag-volledige replica te voor komen. Deze instelling kan niet worden uitgeschakeld.
* U kunt de opslag ook hand matig schalen, op dezelfde manier als op een andere server


### <a name="basic-tier"></a>Basislaag
Basic-laag servers ondersteunen alleen replicatie met dezelfde regio.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[Postgresql vereist](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) dat de waarde van de `max_prepared_transactions` para meter op de Lees replica groter dan of gelijk aan de primaire waarde is. anders wordt de replica niet gestart. Als u wilt wijzigen `max_prepared_transactions` op de primaire, wijzigt u deze eerst op de replica's.

### <a name="stopped-replicas"></a>Gestopte replica's
Als u de replicatie tussen een primaire server en een lees replica stopt, wordt de replica opnieuw gestart om de wijziging toe te passen. De gestopte replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen accepteert. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

### <a name="deleted-primary-and-standalone-servers"></a>De primaire en zelfstandige servers zijn verwijderd
Wanneer een primaire server wordt verwijderd, worden alle bijbehorende Lees replica's zelfstandige servers. De replica's worden opnieuw gestart om deze wijziging weer te geven.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het maken en beheren van Lees replica's in de Azure Portal](howto-read-replicas-portal.md).
* Meer informatie over het [maken en beheren van Lees replica's in azure CLI en rest API](howto-read-replicas-cli.md).