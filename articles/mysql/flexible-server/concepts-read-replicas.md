---
title: Replica's lezen-Azure Database for MySQL-flexibele server
description: "Meer informatie over het lezen van replica's in Azure Database for MySQL flexibele server: het maken van replica's, het verbinden van replica's, het controleren van replicatie en het stoppen van de replicatie."
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: fa7cc9b9a09bfd2bc503640272b5e7ac3a0a7b58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251298"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Replica's in Azure Database for MySQL-flexibele server lezen

> [!IMPORTANT]
> Het lezen van replica's in Azure Database for MySQL-flexibele server is in preview.

MySQL is een van de populaire database-engines voor het uitvoeren van webtoepassingen en mobiele toepassingen op het internet. Veel van onze klanten gebruiken MySQL voor hun online onderwijsservices, videostreamingservices, oplossingen voor digitale betalingen, e-commerceplatforms, gamingservices, nieuwsportalen en websites voor de overheid of gezondheidszorg. Deze services moeten kunnen presteren en schalen als het verkeer op de web- of mobiele toepassing toeneemt.

Aan de kant van de toepassingen wordt de toepassing doorgaans ontwikkeld in Java of PHP en gemigreerd om te worden uitgevoerd op virtuele machine schaal sets van Azure of Azure-app Services of worden in containers gezet om te worden uitgevoerd op Azure Kubernetes service (AKS). Met de virtuele machineschaalset, App Service of AKS als onderliggende infrastructuur, wordt het schalen van toepassingen vereenvoudigd doordat nieuwe VM's direct wordt ingericht en de staatloze onderdelen van toepassingen worden gerepliceerd om aan de aanvragen te kunnen voldoen. De database wordt echter vaak een knelpunten omdat het een gecentraliseerd staatloos onderdeel is.

Met de functie leesreplica kunt u gegevens van een flexibele Azure Database for MySQL-server repliceren naar een server voor alleen-lezen. U kunt van de bron server naar Maxi maal **tien** replica's repliceren. Replica's worden asynchroon bijgewerkt met behulp van de systeemeigen, op de positie van het binlog-bestand (binair logboekbestand) gebaseerde replicatietechnologie van het MySQL-systeem. Meer informatie over binlog-replicatie vindt u in het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Replica's zijn nieuwe servers die u op dezelfde manier beheert als uw bron Azure Database for MySQL flexibele servers. U maakt facturerings kosten voor elke Lees replica op basis van de ingerichte Compute in vCores en Storage in GB/maand. Ga voor meer informatie naar het overzicht van [prijzen](./concepts-compute-storage.md#pricing).

Zie de [MySQL-replicatie documentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)voor meer informatie over MySQL-replicatie functies en-problemen.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
>

## <a name="common-use-cases-for-read-replica"></a>Veelvoorkomende use-cases voor het lezen van replica's

De functie voor het lezen van replica's helpt bij het verbeteren van de prestaties en schaal baarheid van Lees bare werk belastingen. Lees werkbelastingen kunnen worden geïsoleerd voor de replica's, terwijl schrijf werkbelastingen kunnen worden omgeleid naar de bron.

Veelvoorkomende scenario's zijn:

* Het schalen van Lees-workloads die afkomstig zijn van de toepassing met behulp van een lichte verbindings proxy zoals [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) of op basis van een op micro services gebaseerd patroon voor het uitschalen van uw Lees query's die afkomstig zijn uit de toepassing om replica's te lezen
* BI of analytische rapportage werk belastingen kunnen replica's als gegevens bron voor rapportage gebruiken
* Voor IoT of productie scenario waarbij telemetriegegevens worden opgenomen in de MySQL-data base-engine terwijl meerdere Lees replica's worden gebruikt voor de rapportage van gegevens

Omdat replica's alleen-lezen zijn, worden ze niet rechtstreeks op de bron gereduceerd. Deze functie is niet gericht op schrijfintensieve werkbelastingen.

De functie replica lezen maakt gebruik van MySQL-asynchrone replicatie. De functie is niet bedoeld voor synchrone replicatie scenario's. Er is een meet bare vertraging tussen de bron en de replica. De gegevens op de replica worden uiteindelijk consistent met de gegevens op de bron. Gebruik deze functie voor werk belastingen die deze vertraging kunnen bevatten.

## <a name="create-a-replica"></a>Replica's maken

Als een bron server geen bestaande replica servers heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie.

Wanneer u de werk stroom voor het maken van de replica start, wordt er een lege Azure Database for MySQL-server gemaakt. De nieuwe server wordt gevuld met de gegevens die zich op de bron server bevonden. De aanmaak tijd is afhankelijk van de hoeveelheid gegevens op de bron en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van een paar minuten tot enkele uren.

> [!NOTE]
> Lees replica's worden gemaakt met dezelfde server configuratie als de bron. De configuratie van de replica server kan worden gewijzigd nadat deze is gemaakt. De replica server wordt altijd gemaakt in dezelfde resource groep, dezelfde locatie en hetzelfde abonnement als de bron server. Als u een replica server wilt maken naar een andere resource groep of een ander abonnement, kunt u [de replica-server verplaatsen](../../azure-resource-manager/management/move-resource-group-and-subscription.md) nadat u deze hebt gemaakt. Het is raadzaam om de configuratie van de replica server te behouden in gelijke of hogere waarden dan de bron om ervoor te zorgen dat de replica de bron kan blijven gebruiken.

Meer informatie over [het maken van een lees replica in de Azure Portal](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica

Bij het maken neemt een replica de connectiviteits methode van de bron server over. U kunt de connectiviteits methode van de replica niet wijzigen. Als bron server bijvoorbeeld een **persoonlijke toegang heeft (VNet-integratie),** kan de replica geen **toegang hebben tot de open bare IP-adressen**.

De replica neemt het beheerders account over van de bron server. Alle gebruikers accounts op de bron server worden gerepliceerd naar de replica's die worden gelezen. U kunt alleen verbinding maken met een lees replica met behulp van de beschik bare gebruikers accounts op de bron server.

U kunt verbinding maken met de replica door de hostnaam en een geldig gebruikers account te gebruiken, net zoals bij een gewone Azure Database for MySQL flexibele server. Voor een server met de naam **myreplica** met de gebruikers naam **myadmin** van de beheerder kunt u verbinding maken met de replica met behulp van de MySQL cli:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

Voer bij de prompt het wacht woord voor het gebruikers account in.

## <a name="monitor-replication"></a>Replicatie controleren

Azure Database for MySQL flexibele server biedt de **replicatie vertraging in seconden** metric in azure monitor. Deze metriek is alleen beschikbaar voor replica's. Deze metrische gegevens worden berekend met behulp van de `seconds_behind_master` beschik bare metrische gegevens in de opdracht van MySQL `SHOW SLAVE STATUS` . Stel een waarschuwing in om u te informeren wanneer de replicatie vertraging een waarde bereikt die niet geschikt is voor uw werk belasting.

Als u de replicatie vertraging toeneemt, raadpleegt u het [oplossen van problemen met replicatie latentie](./../howto-troubleshoot-replication-latency.md) om problemen op te lossen en mogelijke oorzaken te achterhalen.

## <a name="stop-replication"></a>Replicatie stoppen

U kunt de replicatie tussen een bron en een replica stoppen. Nadat de replicatie tussen een bron server en een lees replica is gestopt, wordt de replica een zelfstandige server. De gegevens op de zelfstandige server zijn de gegevens die beschikbaar zijn op de replica op het moment dat de opdracht stop-replicatie werd gestart. De zelfstandige server is niet actief op de bron server.

Wanneer u ervoor kiest om de replicatie naar een replica te stoppen, gaan alle koppelingen naar de vorige bron en andere replica's verloren. Er is geen automatische failover tussen een bron en de replica.

> [!IMPORTANT]
> De zelfstandige server kan niet opnieuw in een replica worden gemaakt.
> Voordat u de replicatie op een lees replica stopt, moet u ervoor zorgen dat de replica over alle gegevens beschikt die u nodig hebt.

Meer informatie over het [stoppen van replicatie naar een replica](how-to-read-replicas-portal.md).

## <a name="failover"></a>Failover

Er is geen automatische failover tussen bron-en replica servers.

Het lezen van replica's is bedoeld voor het schalen van intensieve workloads voor lezen en is niet ontworpen om te voldoen aan de vereisten voor hoge Beschik baarheid van een server. Er is geen automatische failover tussen bron-en replica servers. Het stoppen van de replicatie bij het lezen van een replica om deze online te brengen in de lees schrijf modus is de manier waarop deze hand matige failover wordt uitgevoerd.

Omdat replicatie asynchroon is, is er sprake van een vertraging tussen de bron en de replica. De hoeveelheid vertraging kan worden beïnvloed door veel factoren, zoals hoe zwaar de werk belasting op de bron server wordt uitgevoerd en de latentie tussen data centers. In de meeste gevallen varieert replicavertraging tussen enkele seconden en een paar minuten. U kunt uw werkelijke replicatie vertraging bijhouden met behulp van de metrische *replica vertraging*, die beschikbaar is voor elke replica. Met deze metriek wordt de tijd weer gegeven sinds de laatste geplayte trans actie. U wordt aangeraden om te bepalen wat uw gemiddelde vertraging is door uw replica vertraging te bestuderen gedurende een bepaalde periode. U kunt een waarschuwing instellen voor replica vertraging, zodat u actie kunt ondernemen als deze buiten het verwachte bereik komt.

> [!Tip]
> Als u een failover naar de replica doorzoekt, geeft de vertraging op het moment dat u de replica loskoppelt van de bron aan hoeveel gegevens er verloren zijn gegaan.

Nadat u hebt vastgesteld dat u een failover naar een replica wilt doen:

1. Replicatie naar de replica stoppen<br/>
   Deze stap is nodig om de replica-server in staat te stellen schrijf bewerkingen te accepteren. Als onderdeel van dit proces wordt de replica server ontkoppeld van de bron. Nadat u de replicatie stoppen hebt gestart, duurt het back-end doorgaans ongeveer twee minuten. Zie de sectie [Replicatie stoppen](#stop-replication) in dit artikel voor meer informatie over de implicaties van deze actie.

2. Uw toepassing naar de (voormalige) replica laten wijzen<br/>
   Elke server heeft een unieke connection string. Werk uw toepassing bij zodat deze verwijst naar de (voormalige) replica in plaats van de bron.

Nadat uw toepassing Lees-en schrijf bewerkingen heeft verwerkt, hebt u de failover voltooid. De uitval tijd van uw toepassings ervaring is afhankelijk van wanneer u een probleem detecteert en de stappen 1 en 2 hierboven uitvoert.

## <a name="considerations-and-limitations"></a>Overwegingen en beperkingen

| Scenario | Beperking/overweging |
|:-|:-|
| Replica op server met zone-redundante HA ingeschakeld | Niet ondersteund |
| Replicatie tussen verschillende regio's lezen | Niet ondersteund |
| Prijzen | De kosten voor het uitvoeren van de replica server zijn gebaseerd op de regio waarin de replica-server wordt uitgevoerd. |
| Bron server opnieuw opstarten | Wanneer u een replica maakt voor een bron die geen bestaande replica's heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd er rekening mee dat u deze bewerkingen uitvoert tijdens een rustige periode |
| Nieuwe replica's | Er wordt een lees replica gemaakt als een nieuwe Azure Database for MySQL flexibele server. Een bestaande server kan niet worden gemaakt in een replica. U kunt geen replica van een andere Lees replica maken |
| Replica configuratie | Een replica wordt gemaakt met behulp van dezelfde server configuratie als de bron. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de bron server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De compute-laag kan ook onafhankelijk worden gewijzigd.<br> <br> **BELANGRIJK**  <br> -Voordat een configuratie van de bron server wordt bijgewerkt naar nieuwe waarden, moet u de replica configuratie bijwerken naar gelijke of hogere waarden. Met deze actie wordt ervoor gezorgd dat in de replica alle wijzigingen worden doorgevoerd die in de bronserver zijn aangebracht. <br/> De verbindings methode en parameter instellingen worden overgenomen van de bron server naar de replica wanneer de replica wordt gemaakt. Daarna zijn de regels van de replica onafhankelijk. |
| Gestopte replica's | Als u de replicatie tussen een bron server en een lees replica stopt, wordt de gestopte replica een zelfstandige server die zowel lees-als schrijf bewerkingen accepteert. De zelfstandige server kan niet opnieuw in een replica worden gemaakt. |
| Verwijderde bron-en zelfstandige servers | Wanneer een bron server wordt verwijderd, wordt replicatie gestopt met alle replica's lezen. Deze replica's worden automatisch zelfstandige servers en kunnen Lees-en schrijf bewerkingen accepteren. De bron server zelf wordt verwijderd. |
| Gebruikersaccounts | Gebruikers op de bron server worden gerepliceerd naar de Lees replica's. U kunt alleen verbinding maken met een lees replica met behulp van de beschik bare gebruikers accounts op de bron server. |
| Serverparameters | Om problemen met de synchronisatie van gegevens en mogelijk verlies of beschadiging van gegevens te voorkomen, worden bepaalde serverparameters vergrendeld zodat ze niet kunnen worden bijgewerkt bij gebruik van replica's voor lezen. <br> De volgende server parameters zijn vergrendeld op de bron-en replica servers:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> De [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) para meter is vergrendeld op de replica servers. <br> Als u een van de bovenstaande para meters op de bron server wilt bijwerken, verwijdert u replica servers, werkt u de parameter waarde bij op de bron en maakt u de replica's opnieuw. |
| Anders | Het maken van een replica van een replica wordt niet ondersteund. <br> -In-Memory tabellen kunnen ertoe leiden dat replica's niet meer synchroon zijn. Dit is een beperking van de MySQL-replicatie technologie. Meer informatie vindt u in de [referentie documentatie voor mysql](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) . <br>-Zorg ervoor dat de bron Server tabellen primaire sleutels hebben. Het ontbreken van primaire sleutels kan leiden tot replicatie latentie tussen de bron en de replica's.<br>-Bekijk de volledige lijst met MySQL-replicatie beperkingen in de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken en beheren van Lees replica's met behulp van de Azure Portal](how-to-read-replicas-portal.md)
* Meer informatie over [het maken en beheren van Lees replica's met behulp van Azure cli](how-to-read-replicas-cli.md)