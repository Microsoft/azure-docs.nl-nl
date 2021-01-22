---
title: Replica's lezen-Azure Database for MariaDB
description: "Meer informatie over het lezen van replica's in Azure Database for MariaDB: het kiezen van regio's, het maken van replica's, het verbinden van replica's, het bewaken van replicatie en het stoppen van de replicatie."
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 375db7e5c113c2a48642365624207ce3acbde8a2
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664211"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Leesreplica's in Azure Database for MariaDB

Met de functie leesreplica kunt u gegevens van een Azure Database for MariaDB-server repliceren naar een alleen-lezen server. U kunt maximaal vijf replica's van de bronserver repliceren. Replica's worden asynchroon bijgewerkt met behulp van de MariaDB-engine van het binaire logboek bestand (binlog) met de globale trans actie-ID (GTID). Zie het [overzicht van binlog-replicatie](https://mariadb.com/kb/en/library/replication-overview/)voor meer informatie over binlog-replicatie.

Replica's zijn nieuwe servers die u op dezelfde manier beheert als gewone Azure Database for MariaDB servers. Voor elke Lees replica wordt u gefactureerd voor de ingerichte Compute in vCores en Storage in GB/maand.

Zie de [documentatie voor MariaDB-replicatie](https://mariadb.com/kb/en/library/gtid/)voor meer informatie over GTID-replicatie.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

## <a name="when-to-use-a-read-replica"></a>Wanneer moet u een lees replica gebruiken?

De functie voor het lezen van replica's helpt bij het verbeteren van de prestaties en schaal baarheid van Lees bare werk belastingen. Lees werkbelastingen kunnen worden geïsoleerd voor de replica's, terwijl schrijf werkbelastingen kunnen worden omgeleid naar de primaire.

Een veelvoorkomend scenario is om BI-en analytische werk belastingen de Lees replica te laten gebruiken als gegevens bron voor rapportage.

Omdat replica's alleen-lezen zijn, beperken ze niet rechtstreeks de overhead van de schrijf capaciteit op de primaire. Deze functie is niet gericht op schrijfintensieve werkbelastingen.

De functie replica lezen maakt gebruik van asynchrone replicatie. De functie is niet bedoeld voor synchrone replicatie scenario's. Er is een meet bare vertraging tussen de bron en de replica. De gegevens op de replica worden uiteindelijk consistent met de gegevens op de primaire. Gebruik deze functie voor werk belastingen die deze vertraging kunnen bevatten.

## <a name="cross-region-replication"></a>Replicatie in meerdere regio's

U kunt een lees replica maken in een andere regio dan de bron server. Replicatie tussen regio's kan handig zijn voor scenario's zoals het plannen van herstel na nood gevallen of gegevens dichter bij uw gebruikers te brengen.

U kunt een bron server in een [Azure database for MariaDB regio](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb)hebben.  Een bron server kan een replica hebben in het gekoppelde gebied of in de universele replica regio's. In de onderstaande afbeelding ziet u welke replica regio's er beschikbaar zijn, afhankelijk van de bron regio.

[![Replica regio's lezen](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universele replica regio's

U kunt in een van de volgende regio's een lees replica maken, ongeacht waar de bron server zich bevindt. De ondersteunde regio's voor universele replica's zijn:

Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, VS, Azië-oost, VS-Oost, VS-Oost 2, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Noord-Centraal VS, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, UK-west, Europa-west, VS-West, VS-West-Centraal vs.

### <a name="paired-regions"></a>Gekoppelde regio's

Naast de universele replica regio's, kunt u een lees replica maken in het gekoppelde Azure-gebied van de bron server. Als u het paar van uw regio niet weet, kunt u meer informatie vinden in het [artikel gekoppelde regio's in azure](../best-practices-availability-paired-regions.md).

Als u verschillende regio's replica's gebruikt voor het plannen van herstel na nood gevallen, raden we u aan om de replica in het gekoppelde gebied te maken in plaats van een van de andere regio's. Gekoppelde regio's vermijden gelijktijdige updates en geven geen prioriteiten voor fysieke isolatie en gegevens locatie.  

Er zijn echter beperkingen om rekening mee te houden: 

* Regionale Beschik baarheid: Azure Database for MariaDB is beschikbaar in Frankrijk-centraal, UAE-noord en Duitsland-centraal. De gekoppelde regio's zijn echter niet beschikbaar.

* Uni-directionele paren: sommige Azure-regio's zijn in slechts één richting gekoppeld. Deze regio's omvatten West-India, Brazilië-zuid en US Gov-Virginia. 
   Dit betekent dat een bron server in West-India een replica kan maken in India-zuid. Een bron server in India-zuid kan echter geen replica maken in West-India. Dit komt doordat de secundaire regio van West-India India-zuid is, India-zuid maar de secundaire regio van het westen is niet West-India.

## <a name="create-a-replica"></a>Replica's maken

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MariaDB-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de bron server zich in een van deze prijs categorieën bevindt.

Als een bron server geen bestaande replica servers heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie.

Wanneer u de werk stroom voor het maken van de replica start, wordt er een lege Azure Database for MariaDB-server gemaakt. De nieuwe server wordt gevuld met de gegevens die zich op de bron server bevonden. De aanmaak tijd is afhankelijk van de hoeveelheid gegevens op de bron en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van een paar minuten tot enkele uren.

> [!NOTE]
> Als u geen opslag waarschuwing hebt ingesteld op uw servers, kunt u dit het beste doen. De waarschuwing meldt u wanneer een server de opslag limiet nadert, die van invloed is op de replicatie.

Meer informatie over [het maken van een lees replica in de Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica

Bij het maken neemt een replica de firewall regels van de bron server over. Daarna zijn deze regels onafhankelijk van de bron server.

De replica neemt het beheerders account over van de bron server. Alle gebruikers accounts op de bron server worden gerepliceerd naar de replica's die worden gelezen. U kunt alleen verbinding maken met een lees replica met behulp van de beschik bare gebruikers accounts op de bron server.

U kunt verbinding maken met de replica door de hostnaam en een geldig gebruikers account te gebruiken, net zoals bij een gewone Azure Database for MariaDB-server. Voor een server met de naam **myreplica** met de gebruikers naam **myadmin** van de beheerder kunt u verbinding maken met de replica met behulp van de MySQL cli:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Voer bij de prompt het wacht woord voor het gebruikers account in.

## <a name="monitor-replication"></a>Replicatie controleren

Azure Database for MariaDB levert de **replicatie vertraging in seconden** metric in azure monitor. Deze metriek is alleen beschikbaar voor replica's.

Deze metrische gegevens worden berekend met behulp van de `seconds_behind_master` beschik bare metrische gegevens in de opdracht van MariaDB `SHOW SLAVE STATUS` .

Stel een waarschuwing in om u te informeren wanneer de replicatie vertraging een waarde bereikt die niet geschikt is voor uw werk belasting.

## <a name="stop-replication"></a>Replicatie stoppen

U kunt de replicatie tussen een bron en een replica stoppen. Nadat de replicatie tussen een bron server en een lees replica is gestopt, wordt de replica een zelfstandige server. De gegevens op de zelfstandige server zijn de gegevens die beschikbaar zijn op de replica op het moment dat de opdracht stop-replicatie werd gestart. De zelfstandige server is niet actief op de bron server.

Wanneer u ervoor kiest om de replicatie naar een replica te stoppen, gaan alle koppelingen naar de vorige bron en andere replica's verloren. Er is geen automatische failover tussen een bron en de replica.

> [!IMPORTANT]
> De zelfstandige server kan niet opnieuw in een replica worden gemaakt.
> Voordat u de replicatie op een lees replica stopt, moet u ervoor zorgen dat de replica over alle gegevens beschikt die u nodig hebt.

Meer informatie over het [stoppen van replicatie naar een replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Er is geen automatische failover tussen bron-en replica servers.

Omdat replicatie asynchroon is, is er sprake van een vertraging tussen de bron en de replica. De hoeveelheid vertraging kan worden beïnvloed door een aantal factoren, zoals hoe zwaar de werk belasting die wordt uitgevoerd op de bron server en de latentie tussen data centers. In de meeste gevallen varieert replicavertraging tussen enkele seconden en een paar minuten. U kunt uw werkelijke replicatie vertraging bijhouden met behulp van de metrische *replica vertraging*, die beschikbaar is voor elke replica. Met deze metriek wordt de tijd weer gegeven sinds de laatste geplayte trans actie. U wordt aangeraden om te bepalen wat uw gemiddelde vertraging is door uw replica vertraging te bestuderen gedurende een bepaalde periode. U kunt een waarschuwing instellen voor replica vertraging, zodat u actie kunt ondernemen als deze buiten het verwachte bereik komt.

> [!Tip]
> Als u een failover naar de replica doorzoekt, geeft de vertraging op het moment dat u de replica loskoppelt van de bron aan hoeveel gegevens er verloren zijn gegaan.

Nadat u hebt vastgesteld dat u een failover naar een replica wilt,

1. Stop de replicatie naar de replica.

   Deze stap is nodig om de replica-server in staat te stellen schrijf bewerkingen te accepteren. Als onderdeel van dit proces wordt de replica server ontkoppeld van de primaire. Nadat u de replicatie stoppen hebt gestart, duurt het back-end doorgaans ongeveer twee minuten. Zie de sectie [Replicatie stoppen](#stop-replication) in dit artikel voor meer informatie over de implicaties van deze actie.

2. Ga naar de (voormalige) replica van uw toepassing.

   Elke server heeft een unieke connection string. Werk uw toepassing bij zodat deze verwijst naar de (voormalige) replica in plaats van de primaire.

Nadat uw toepassing Lees-en schrijf bewerkingen heeft verwerkt, hebt u de failover voltooid. De uitval tijd van uw toepassings ervaring is afhankelijk van wanneer u een probleem detecteert en de stappen 1 en 2 hierboven uitvoert.

## <a name="considerations-and-limitations"></a>Overwegingen en beperkingen

### <a name="pricing-tiers"></a>Prijscategorieën

Het lezen van replica's is momenteel alleen beschikbaar in de prijs Categorieën Algemeen en geoptimaliseerd voor geheugen.

> [!NOTE]
> De kosten voor het uitvoeren van de replica server zijn gebaseerd op de regio waarin de replica-server wordt uitgevoerd.

### <a name="source-server-restart"></a>Bron server opnieuw opstarten

Wanneer u een replica maakt voor een bron die geen bestaande replica's heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd dit in overweging en voer deze bewerkingen uit tijdens een rustige periode.

### <a name="new-replicas"></a>Nieuwe replica's

Er wordt een lees replica gemaakt als een nieuwe Azure Database for MariaDB-server. Een bestaande server kan niet worden gemaakt in een replica. Het is niet mogelijk om een replica van een andere Lees replica te maken.

### <a name="replica-configuration"></a>Replica configuratie

Een replica wordt gemaakt met behulp van dezelfde server configuratie als de primaire. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de bron server worden gewijzigd: generatie van compute, vCores, opslag, back-up van Bewaar periode en Engine versie van MariaDB. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.

> [!IMPORTANT]
> Voordat een configuratie van een bronserver wordt bijgewerkt naar nieuwe waarden, moet u de configuratie van de replica bijwerken naar gelijke of hogere waarden. Met deze actie zorgt u ervoor dat de replica alle wijzigingen kan aanbrengen die zijn aangebracht in de primaire.

Firewall regels en parameter instellingen worden overgenomen van de bron server naar de replica wanneer de replica wordt gemaakt. Daarna zijn de regels van de replica onafhankelijk.

### <a name="stopped-replicas"></a>Gestopte replica's

Als u de replicatie tussen een bron server en een lees replica stopt, wordt de gestopte replica een zelfstandige server die zowel lees-als schrijf bewerkingen accepteert. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

### <a name="deleted-source-and-standalone-servers"></a>Verwijderde bron-en zelfstandige servers

Wanneer een bron server wordt verwijderd, wordt replicatie gestopt met alle replica's lezen. Deze replica's worden automatisch zelfstandige servers en kunnen Lees-en schrijf bewerkingen accepteren. De bron server zelf wordt verwijderd.

### <a name="user-accounts"></a>Gebruikersaccounts

Gebruikers op de bron server worden gerepliceerd naar de Lees replica's. U kunt alleen verbinding maken met een lees replica met behulp van de beschik bare gebruikers accounts op de bron server.

### <a name="server-parameters"></a>Serverparameters

Om problemen met de synchronisatie van gegevens en mogelijk verlies of beschadiging van gegevens te voorkomen, worden bepaalde serverparameters vergrendeld zodat ze niet kunnen worden bijgewerkt bij gebruik van replica's voor lezen.

De volgende server parameters zijn vergrendeld op de bron-en replica servers:

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

De [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) para meter is vergrendeld op de replica servers.

Als u een van de bovenstaande para meters op de bron server wilt bijwerken, verwijdert u de replica servers, werkt u de parameter waarde op de primaire en maakt u de replica's opnieuw.

### <a name="other"></a>Anders

* Het maken van een replica van een replica wordt niet ondersteund.
* In-Memory tabellen kunnen ertoe leiden dat replica's niet meer synchroon zijn. Dit is een beperking van de MariaDB-replicatie technologie.
* Zorg ervoor dat de bron Server tabellen primaire sleutels hebben. Het ontbreken van primaire sleutels kan leiden tot replicatie latentie tussen de bron en de replica's.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken en beheren van Lees replica's met behulp van de Azure Portal](howto-read-replicas-portal.md)
* Meer informatie over [het maken en beheren van Lees replica's met behulp van Azure CLI en rest API](howto-read-replicas-cli.md)
