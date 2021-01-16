---
title: Replica's lezen-Azure Database for MySQL
description: "Meer informatie over het lezen van replica's in Azure Database for MySQL: het kiezen van regio's, het maken van replica's, het verbinden van replica's, het bewaken van replicatie en het stoppen van de replicatie."
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2021
ms.custom: references_regions
ms.openlocfilehash: c380a3edb556adb72d067cb2910c8afbf66b99a0
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250261"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Leesreplica's in Azure Database for MySQL

Met de functie leesreplica kunt u gegevens van een Azure Database for MySQL-server repliceren naar een alleen-lezen server. U kunt maximaal vijf replica's van de bronserver repliceren. Replica's worden asynchroon bijgewerkt met behulp van de systeemeigen, op de positie van het binlog-bestand (binair logboekbestand) gebaseerde replicatietechnologie van het MySQL-systeem. Meer informatie over binlog-replicatie vindt u in het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Replica's zijn nieuwe servers die u op dezelfde manier beheert als gewone Azure Database for MySQL servers. Voor elke Lees replica wordt u gefactureerd voor de ingerichte Compute in vCores en Storage in GB/maand.

Zie de [MySQL-replicatie documentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)voor meer informatie over MySQL-replicatie functies en-problemen.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
>

## <a name="when-to-use-a-read-replica"></a>Wanneer moet u een lees replica gebruiken?

De functie voor het lezen van replica's helpt bij het verbeteren van de prestaties en schaal baarheid van Lees bare werk belastingen. Lees werkbelastingen kunnen worden geïsoleerd voor de replica's, terwijl schrijf werkbelastingen kunnen worden omgeleid naar de bron.

Een veelvoorkomend scenario is om BI-en analytische werk belastingen de Lees replica te laten gebruiken als gegevens bron voor rapportage.

Omdat replica's alleen-lezen zijn, worden ze niet rechtstreeks op de bron gereduceerd. Deze functie is niet gericht op schrijfintensieve werkbelastingen.

De functie replica lezen maakt gebruik van MySQL-asynchrone replicatie. De functie is niet bedoeld voor synchrone replicatie scenario's. Er is een meet bare vertraging tussen de bron en de replica. De gegevens op de replica worden uiteindelijk consistent met de gegevens op de bron. Gebruik deze functie voor werk belastingen die deze vertraging kunnen bevatten.

## <a name="cross-region-replication"></a>Replicatie in meerdere regio's

U kunt een lees replica maken in een andere regio dan de bron server. Replicatie tussen regio's kan handig zijn voor scenario's zoals het plannen van herstel na nood gevallen of gegevens dichter bij uw gebruikers te brengen.

U kunt een bron server in een [Azure database for MySQL regio](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)hebben.  Een bron server kan een replica hebben in het gekoppelde gebied of in de universele replica regio's. In de volgende afbeelding ziet u welke replica regio's er beschikbaar zijn, afhankelijk van de bron regio.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Replica regio's lezen":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universele replica regio's

U kunt in een van de volgende regio's een lees replica maken, ongeacht waar de bron server zich bevindt. De ondersteunde regio's voor universele replica's zijn:

Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, VS, Azië-oost, VS-Oost, VS-Oost 2, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Noord-Centraal VS, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, UK-west, Europa-west, VS-West, VS-West-Centraal vs.

### <a name="paired-regions"></a>Gekoppelde regio's

Naast de universele replica regio's, kunt u een lees replica maken in het gekoppelde Azure-gebied van de bron server. Als u het paar van uw regio niet weet, kunt u meer informatie vinden in het [artikel gekoppelde regio's in azure](../best-practices-availability-paired-regions.md).

Als u verschillende regio's replica's gebruikt voor het plannen van herstel na nood gevallen, raden we u aan om de replica in het gekoppelde gebied te maken in plaats van een van de andere regio's. Gekoppelde regio's vermijden gelijktijdige updates en geven geen prioriteiten voor fysieke isolatie en gegevens locatie.  

Er zijn echter beperkingen om rekening mee te houden: 

* Regionale Beschik baarheid: Azure Database for MySQL is beschikbaar in Frankrijk-centraal, UAE-noord en Duitsland-centraal. De gekoppelde regio's zijn echter niet beschikbaar.

* Uni-directionele paren: sommige Azure-regio's zijn in slechts één richting gekoppeld. Deze regio's omvatten West-India, Brazilië-zuid en US Gov-Virginia.
   Dit betekent dat een bron server in West-India een replica kan maken in India-zuid. Een bron server in India-zuid kan echter geen replica maken in West-India. Dit komt doordat de secundaire regio van West-India India-zuid is, maar de secundaire regio van het India-zuid niet West-India is.

## <a name="create-a-replica"></a>Replica's maken

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MySQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de bron server zich in een van deze prijs categorieën bevindt.

Als een bron server geen bestaande replica servers heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie.

Wanneer u de werk stroom voor het maken van de replica start, wordt er een lege Azure Database for MySQL-server gemaakt. De nieuwe server wordt gevuld met de gegevens die zich op de bron server bevonden. De aanmaak tijd is afhankelijk van de hoeveelheid gegevens op de bron en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van een paar minuten tot enkele uren. De replica server wordt altijd gemaakt in dezelfde resource groep en hetzelfde abonnement als de bron server. Als u een replica server wilt maken naar een andere resource groep of een ander abonnement, kunt u [de replica-server verplaatsen](../azure-resource-manager/management/move-resource-group-and-subscription.md) nadat u deze hebt gemaakt.

Elke replica is ingeschakeld voor [automatische groei](concepts-pricing-tiers.md#storage-auto-grow)van opslag. Met de functie voor automatisch uitbreiden kan de replica de gegevens repliceren, en wordt voor komen dat er een onderbreking in de replicatie wordt veroorzaakt door problemen met de opslag.

Meer informatie over [het maken van een lees replica in de Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica

Bij het maken neemt een replica de firewall regels van de bron server over. Daarna zijn deze regels onafhankelijk van de bron server.

De replica neemt het beheerders account over van de bron server. Alle gebruikers accounts op de bron server worden gerepliceerd naar de replica's die worden gelezen. U kunt alleen verbinding maken met een lees replica met behulp van de beschik bare gebruikers accounts op de bron server.

U kunt verbinding maken met de replica door de hostnaam en een geldig gebruikers account te gebruiken, net zoals bij een gewone Azure Database for MySQL-server. Voor een server met de naam **myreplica** met de gebruikers naam **myadmin** van de beheerder kunt u verbinding maken met de replica met behulp van de MySQL cli:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Voer bij de prompt het wacht woord voor het gebruikers account in.

## <a name="monitor-replication"></a>Replicatie controleren

Azure Database for MySQL levert de **replicatie vertraging in seconden** metric in azure monitor. Deze metriek is alleen beschikbaar voor replica's. Deze metrische gegevens worden berekend met behulp van de `seconds_behind_master` beschik bare metrische gegevens in de opdracht van MySQL `SHOW SLAVE STATUS` . Stel een waarschuwing in om u te informeren wanneer de replicatie vertraging een waarde bereikt die niet geschikt is voor uw werk belasting.

Als u de replicatie vertraging toeneemt, raadpleegt u het [oplossen van problemen met replicatie latentie](howto-troubleshoot-replication-latency.md) om problemen op te lossen en mogelijke oorzaken te achterhalen.

## <a name="stop-replication"></a>Replicatie stoppen

U kunt de replicatie tussen een bron en een replica stoppen. Nadat de replicatie tussen een bron server en een lees replica is gestopt, wordt de replica een zelfstandige server. De gegevens op de zelfstandige server zijn de gegevens die beschikbaar zijn op de replica op het moment dat de opdracht stop-replicatie werd gestart. De zelfstandige server is niet actief op de bron server.

Wanneer u ervoor kiest om de replicatie naar een replica te stoppen, gaan alle koppelingen naar de vorige bron en andere replica's verloren. Er is geen automatische failover tussen een bron en de replica.

> [!IMPORTANT]
> De zelfstandige server kan niet opnieuw in een replica worden gemaakt.
> Voordat u de replicatie op een lees replica stopt, moet u ervoor zorgen dat de replica over alle gegevens beschikt die u nodig hebt.

Meer informatie over het [stoppen van replicatie naar een replica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Er is geen automatische failover tussen bron-en replica servers.

Omdat de replicatie asynchroon is, is er sprake van een vertraging tussen de bron en de replica. De hoeveelheid vertraging kan worden beïnvloed door veel factoren, zoals hoe zwaar de werk belasting op de bron server wordt uitgevoerd en de latentie tussen data centers. In de meeste gevallen varieert replicavertraging tussen enkele seconden en een paar minuten. U kunt uw werkelijke replicatie vertraging bijhouden met behulp van de metrische *replica vertraging*, die beschikbaar is voor elke replica. Met deze metriek wordt de tijd weer gegeven sinds de laatste geplayte trans actie. U wordt aangeraden om te bepalen wat uw gemiddelde vertraging is door uw replica vertraging te bestuderen gedurende een bepaalde periode. U kunt een waarschuwing instellen voor replica vertraging, zodat u actie kunt ondernemen als deze buiten het verwachte bereik komt.

> [!Tip]
> Als u een failover naar de replica doorzoekt, geeft de vertraging op het moment dat u de replica loskoppelt van de bron aan hoeveel gegevens er verloren zijn gegaan.

Nadat u hebt vastgesteld dat u een failover naar een replica wilt:

1. Replicatie naar de replica stoppen<br/>
   Deze stap is nodig om de replica-server in staat te stellen schrijf bewerkingen te accepteren. Als onderdeel van dit proces wordt de replica server ontkoppeld van de bron. Nadat u de replicatie stoppen hebt gestart, duurt het back-end doorgaans ongeveer twee minuten. Zie de sectie [Replicatie stoppen](#stop-replication) in dit artikel voor meer informatie over de implicaties van deze actie.

2. Uw toepassing naar de (voormalige) replica laten wijzen<br/>
   Elke server heeft een unieke connection string. Werk uw toepassing bij zodat deze verwijst naar de (voormalige) replica in plaats van de bron.

Nadat uw toepassing Lees-en schrijf bewerkingen heeft verwerkt, hebt u de failover voltooid. De uitval tijd van uw toepassings ervaring is afhankelijk van wanneer u een probleem detecteert en de eerder genoemde stappen 1 en 2 uitvoert.

## <a name="global-transaction-identifier-gtid"></a>Algemene trans actie-id (GTID)

Global Trans Action Identifier (GTID) is een unieke id die is gemaakt met elke vastgelegde trans actie op een bron server en is standaard uitgeschakeld in Azure Database for MySQL. GTID wordt alleen ondersteund in versies 5,7 en 8,0 en alleen op servers die ondersteuning bieden voor opslag van Maxi maal 16 TB. Raadpleeg voor meer informatie over GTID en hoe deze worden gebruikt in replicatie, de replicatie van MySQL [met GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) -documentatie.

MySQL ondersteunt twee typen trans acties: GTID trans acties (aangeduid met GTID) en anonieme trans acties (er is geen GTID toegewezen)

De volgende server parameters zijn beschikbaar voor het configureren van GTID: 

|**Server parameter**|**Beschrijving**|**Standaardwaarde**|**Waarden**|
|--|--|--|--|
|`gtid_mode`|Hiermee wordt aangegeven of GTIDs worden gebruikt om trans acties te identificeren. Wijzigingen tussen modi kunnen slechts één stap per keer in oplopende volg orde worden uitgevoerd (bijvoorbeeld `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`: Zowel nieuwe als replicatie transacties moeten anoniem zijn <br> `OFF_PERMISSIVE`: Nieuwe trans acties zijn anoniem. Gerepliceerde trans acties kunnen anoniem of GTID-trans acties zijn. <br> `ON_PERMISSIVE`: Nieuwe trans acties zijn GTID trans acties. Gerepliceerde trans acties kunnen anoniem of GTID-trans acties zijn. <br> `ON`: Zowel nieuwe als gerepliceerde trans acties moeten GTID trans acties zijn.|
|`enforce_gtid_consistency`|Dwingt consistentie van GTID af door alleen de instructies toe te staan die op transactionele veilige wijze kunnen worden geregistreerd. Deze waarde moet worden ingesteld op `ON` voordat u GTID-replicatie inschakelt. |`OFF`|`OFF`: Alle trans acties mogen GTID-consistentie schenden.  <br> `ON`: Geen enkele trans actie mag GTID-consistentie schenden. <br> `WARN`: Alle trans acties mogen GTID-consistentie schenden, maar er wordt een waarschuwing gegenereerd. | 

> [!NOTE]
> Nadat GTID is ingeschakeld, kunt u deze niet meer uitschakelen. Als u GTID wilt uitschakelen, neemt u contact op met de ondersteuning. 

Als u GTID wilt inschakelen en het consistentie gedrag wilt configureren, moet `gtid_mode` `enforce_gtid_consistency` u de para meters en server bijwerken met behulp van de [Azure Portal](howto-server-parameters.md), [Azure cli](howto-configure-server-parameters-using-cli.md)of [Power shell](howto-configure-server-parameters-using-powershell.md).

Als GTID is ingeschakeld op een bron server ( `gtid_mode` = aan), is voor nieuw gemaakte replica's ook GTID ingeschakeld en wordt GTID-replicatie gebruikt. Om replicatie consistent te blijven, kunt u niet bijwerken `gtid_mode` op de bron-of replica server (s).

## <a name="considerations-and-limitations"></a>Overwegingen en beperkingen

### <a name="pricing-tiers"></a>Prijscategorieën

Het lezen van replica's is momenteel alleen beschikbaar in de prijs Categorieën Algemeen en geoptimaliseerd voor geheugen.

> [!NOTE]
> De kosten voor het uitvoeren van de replica server zijn gebaseerd op de regio waarin de replica-server wordt uitgevoerd.

### <a name="source-server-restart"></a>Bron server opnieuw opstarten

Wanneer u een replica maakt voor een bron die geen bestaande replica's heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd dit in overweging en voer deze bewerkingen uit tijdens een rustige periode.

### <a name="new-replicas"></a>Nieuwe replica's

Er wordt een lees replica gemaakt als een nieuwe Azure Database for MySQL-server. Een bestaande server kan niet worden gemaakt in een replica. Het is niet mogelijk om een replica van een andere Lees replica te maken.

### <a name="replica-configuration"></a>Replica configuratie

Een replica wordt gemaakt met behulp van dezelfde server configuratie als de bron. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de bron server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.

> [!IMPORTANT]
> Voordat een configuratie van een bronserver wordt bijgewerkt naar nieuwe waarden, moet u de configuratie van de replica bijwerken naar gelijke of hogere waarden. Met deze actie wordt ervoor gezorgd dat in de replica alle wijzigingen worden doorgevoerd die in de bronserver zijn aangebracht.

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

* [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
* [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

De [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) para meter is vergrendeld op de replica servers.

Als u een van de bovenstaande para meters op de bron server wilt bijwerken, verwijdert u replica servers, werkt u de parameter waarde bij op de bron en maakt u de replica's opnieuw.

### <a name="gtid"></a>GTID

GTID wordt ondersteund op:

* MySQL-versies 5,7 en 8,0.
* Servers die ondersteuning bieden voor opslag van Maxi maal 16 TB. Raadpleeg het artikel [prijs categorie](concepts-pricing-tiers.md#storage) voor de volledige lijst met regio's die ondersteuning bieden voor 16 TB opslag.

GTID is standaard uitgeschakeld. Nadat GTID is ingeschakeld, kunt u deze niet meer uitschakelen. Als u GTID wilt uitschakelen, neemt u contact op met de ondersteuning.

Als GTID is ingeschakeld op een bron server, is voor nieuw gemaakte replica's ook GTID ingeschakeld en wordt GTID-replicatie gebruikt. Om replicatie consistent te blijven, kunt u niet bijwerken `gtid_mode` op de bron-of replica server (s).

### <a name="other"></a>Anders

* Het maken van een replica van een replica wordt niet ondersteund.
* In-Memory tabellen kunnen ertoe leiden dat replica's niet meer synchroon zijn. Dit is een beperking van de MySQL-replicatie technologie. Meer informatie vindt u in de [referentie documentatie voor mysql](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) .
* Zorg ervoor dat de bron Server tabellen primaire sleutels hebben. Het ontbreken van primaire sleutels kan leiden tot replicatie latentie tussen de bron en de replica's.
* Bekijk de volledige lijst met MySQL-replicatie beperkingen in de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken en beheren van Lees replica's met behulp van de Azure Portal](howto-read-replicas-portal.md)
* Meer informatie over [het maken en beheren van Lees replica's met behulp van Azure CLI en rest API](howto-read-replicas-cli.md)