---
title: Failover en patching - Redis Cache
description: Meer informatie over failover, patching en het update proces voor Azure cache voor redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: cc7c70fa2e7131f09f621e992d537e0b120061ef
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210730"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover en Patching voor Azure cache voor redis

Als u robuuste en geslaagde client toepassingen wilt bouwen, is het belang rijk om de failover in de context van de Azure-cache voor de redis-service te begrijpen. Een failover kan deel uitmaken van geplande beheer bewerkingen of kan worden veroorzaakt door ongeplande hardware-of netwerk storingen. Een veelvoorkomend gebruik van de cache-failover wordt geleverd wanneer de Management service de Azure-cache voor binaire redis-bestanden bijwerkt. In dit artikel wordt beschreven wat een failover is, hoe deze plaatsvindt tijdens patches en hoe u een flexibele client toepassing bouwt.

## <a name="what-is-a-failover"></a>Wat is een failover?

Laten we beginnen met een overzicht van failover voor Azure cache voor redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Een snelle samen vatting van de cache architectuur

Een cache bestaat uit meerdere virtuele machines met afzonderlijke, persoonlijke IP-adressen. Elke virtuele machine, ook wel een knoop punt genoemd, is verbonden met een gedeelde load balancer met één virtueel IP-adres. Elk knoop punt voert het redis-server proces uit en is toegankelijk met behulp van de hostnaam en de redis-poorten. Elk knoop punt wordt beschouwd als een primair of replica knooppunt. Wanneer een client toepassing verbinding maakt met een cache, loopt het verkeer via deze load balancer en wordt het automatisch doorgestuurd naar het primaire knoop punt.

In een Basic-cache is het één knoop punt altijd een primaire. In een Standard-of Premium-cache zijn er twee knoop punten: een is geselecteerd als de primaire en de andere is de replica. Omdat de standaard-en Premium-cache meerdere knoop punten hebben, is het mogelijk dat er een knoop punt niet beschikbaar is terwijl de andere aanvragen blijven verwerken. Geclusterde caches worden gemaakt van veel Shards, elk met afzonderlijke primaire en replica knooppunten. Een Shard is mogelijk niet actief terwijl de andere beschikbaar blijven.

> [!NOTE]
> Een Basic-cache heeft geen meerdere knoop punten en biedt geen SLA (Service Level Agreement) voor de beschik baarheid. Basis caches worden alleen aanbevolen voor ontwikkelings-en test doeleinden. Gebruik een Standard-of Premium-cache voor een implementatie met meerdere knoop punten om de beschik baarheid te verg Roten.

### <a name="explanation-of-a-failover"></a>Uitleg van een failover

Er treedt een failover op wanneer een replica-knoop punt een primair knoop punt wordt, en de bestaande verbindingen worden gesloten door het oude primaire knoop punt. Nadat het primaire knoop punt een back-up heeft gemaakt, ziet u de wijziging in rollen en degradeert zichzelf om een replica te worden. Vervolgens wordt er verbinding gemaakt met de nieuwe primaire en worden gegevens gesynchroniseerd. Een failover kan gepland of niet-gepland zijn.

Een *geplande failover* vindt plaats tijdens systeem updates, zoals redis patches of besturingssysteem upgrades, en beheer bewerkingen, zoals schalen en opnieuw opstarten. Omdat de knoop punten voorafgaande kennisgeving van de update ontvangen, kunnen ze hun rollen gezamenlijk wisselen en snel de load balancer van de wijziging bijwerken. Een geplande failover eindigt doorgaans in minder dan 1 seconde.

Een niet- *geplande failover* kan optreden als gevolg van hardwarestoringen, netwerk storingen of andere onverwachte storingen in het primaire knoop punt. Het replica-knoop punt bevordert zichzelf tot primaire, maar het proces duurt langer. Een replica knooppunt moet eerst detecteren dat het primaire knoop punt niet beschikbaar is voordat het failoverproces kan initiëren. Het replica knooppunt moet er ook voor zorgen dat deze niet-geplande fout niet tijdelijk of lokaal is, om een onnodige failover te voor komen. Deze vertraging in de detectie houdt in dat een niet-geplande failover doorgaans binnen 10 tot 15 seconden wordt voltooid.

## <a name="how-does-patching-occur"></a>Hoe wordt patching uitgevoerd?

De Azure cache for redis-service werkt uw cache regel matig bij met de nieuwste platform functies en-oplossingen. Als u een cache wilt patchen, volgt de service deze stappen:

1. De Management service selecteert één knoop punt waarvoor een patch moet worden uitgevoerd.
1. Als het geselecteerde knoop punt een primair knoop punt is, wordt het bijbehorende replica knooppunt gezamenlijk gepromoot. Deze promotie wordt beschouwd als een geplande failover.
1. Het geselecteerde knoop punt wordt opnieuw opgestart om de nieuwe wijzigingen te maken en er wordt een back-up van gemaakt als replica knooppunt.
1. Het replica knooppunt maakt verbinding met het primaire knoop punt en synchroniseert gegevens.
1. Wanneer de gegevens synchronisatie is voltooid, wordt het patch proces herhaald voor de resterende knoop punten.

Omdat patching een geplande failover is, kan het replica knooppunt snel worden gepromoveerd tot een primair en worden er aanvragen voor onderhoud en nieuwe verbindingen gestart. Basis caches hebben geen replica knooppunt en zijn pas beschikbaar als de update is voltooid. Elk Shard van een geclusterde cache wordt afzonderlijk gerepareerd en sluit geen verbindingen met een andere Shard.

> [!IMPORTANT]
> Knoop punten worden een voor een patch uitgevoerd om gegevens verlies te voor komen. Voor basis caches geldt een verlies van gegevens. Geclusterde caches worden één Shard per keer patched.

Meerdere caches in dezelfde resource groep en regio worden ook een patch per keer uitgevoerd.  Voor caches die zich in verschillende resource groepen of verschillende regio's bevinden, kan tegelijkertijd een patch worden uitgevoerd.

Omdat volledige gegevens synchronisatie plaatsvindt voordat het proces wordt herhaald, zullen gegevens verlies waarschijnlijk niet optreden wanneer u een Standard-of Premium-cache gebruikt. U kunt gegevens verlies verder beveiligen door gegevens te [exporteren](cache-how-to-import-export-data.md#export) en [persistentie](cache-how-to-premium-persistence.md)in te scha kelen.

## <a name="additional-cache-load"></a>Extra cache belasting

Wanneer een failover optreedt, moeten de standaard-en Premium-caches gegevens repliceren van het ene knoop punt naar het andere. Deze replicatie veroorzaakt een aantal belasting verhogingen in het server geheugen en de CPU. Als het cache-exemplaar al zwaar is geladen, kunnen client toepassingen de latentie verhogen. In uitzonderlijke gevallen kunnen client toepassingen time-outuitzonderingen ontvangen. [Configureer](cache-configure.md#memory-policies) de instelling van de cache om de impact van deze extra belasting te verminderen `maxmemory-reserved` .

## <a name="how-does-a-failover-affect-my-client-application"></a>Wat is de invloed van een failover op mijn client toepassing?

Het aantal fouten dat door de client toepassing wordt weer gegeven, is afhankelijk van het aantal bewerkingen dat in die verbinding in behandeling was op het moment van de failover. Alle verbindingen die via het knoop punt zijn gerouteerd en waarmee de verbindingen zijn verbroken, krijgen fouten te zien. Veel client bibliotheken kunnen verschillende typen fouten genereren wanneer er verbindingen worden verbroken, inclusief time-outuitzonderingen, verbindings uitzonderingen of socket-uitzonde ringen. Het aantal en type uitzonde ringen is afhankelijk van waar in het codepad de aanvraag zich bevindt wanneer de cache de verbindingen sluit. Een bewerking die bijvoorbeeld een aanvraag verzendt, maar geen antwoord heeft ontvangen wanneer de failover optreedt, kan een time-outuitzondering opleveren. Nieuwe aanvragen voor het gesloten verbindings object ontvangen verbindings uitzonderingen totdat de verbinding is hersteld.

De meeste client bibliotheken proberen opnieuw verbinding te maken met de cache als deze zijn geconfigureerd. Onvoorziene fouten kunnen echter af en toe de bibliotheek objecten in een onherstelbare status plaatsen. Als fouten blijven bestaan gedurende een vooraf geconfigureerde hoeveelheid tijd, moet het verbindings object opnieuw worden gemaakt. In Microsoft.NET en andere object gerichte talen kunt u de verbinding opnieuw maken zonder dat de toepassing opnieuw hoeft te worden opgestart met behulp van [een luie \<T\> patroon](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Hoe kan ik mijn toepassing robuust maken?

Omdat u failovers niet volledig kunt voor komen, schrijft u uw client toepassingen voor tolerantie voor verbindings onderbrekingen en mislukte aanvragen. Hoewel de meeste client bibliotheken automatisch opnieuw verbinding maken met het cache-eind punt, proberen enkele daarvan mislukte aanvragen opnieuw uit te voeren. Afhankelijk van het toepassings scenario kan het zinvol zijn om opnieuw proberen logica te gebruiken met uitstel.

Als u de tolerantie van een client toepassing wilt testen, gebruikt u [opnieuw opstarten](cache-administration.md#reboot) als hand matige trigger voor verbindings onderbrekingen. Daarnaast wordt u aangeraden updates op een cache te [plannen](cache-administration.md#schedule-updates) . Vertel de beheer service om redis runtime patches toe te passen tijdens opgegeven wekelijkse Windows. Deze vensters zijn doorgaans Peri Oden waarin het verkeer van de client toepassing laag is, om potentiële incidenten te voor komen.

### <a name="can-i-be-notified-in-advance-of-a-planned-maintenance"></a>Kan ik van tevoren een melding ontvangen over een gepland onderhoud?

Azure cache voor redis publiceert nu meldingen op een openbaar/geabonneerd-kanaal met de naam [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) ongeveer 30 seconden vóór geplande updates. Dit zijn runtime meldingen en ze zijn speciaal gebouwd voor toepassingen die circuit onderbrekers kunnen gebruiken om de cache-of buffer opdrachten te omzeilen, bijvoorbeeld tijdens geplande updates. Het is geen mechanisme waarmee u dagen of uren van tevoren kan worden gewaarschuwd.

### <a name="client-network-configuration-changes"></a>Client netwerk-configuratie wijzigingen

Bepaalde netwerk configuratie wijzigingen aan de client zijde kunnen fouten met betrekking tot ' geen verbinding beschikbaar ' veroorzaken. Deze wijzigingen kunnen het volgende omvatten:

- Het virtuele IP-adres van een client toepassing wisselen tussen fase ring en productie sleuven.
- De grootte of het aantal exemplaren van uw toepassing schalen.

Dergelijke wijzigingen kunnen een connectiviteits probleem veroorzaken dat minder dan een minuut duurt. De verbinding van de client toepassing met andere externe netwerk bronnen gaat waarschijnlijk verloren naast de Azure-cache voor de redis-service.

## <a name="next-steps"></a>Volgende stappen

- [Updates](cache-administration.md#schedule-updates) voor uw cache plannen.
- Toepassings tolerantie testen door het [opnieuw opstarten](cache-administration.md#reboot)te gebruiken.
- Geheugen reserveringen en-beleid [configureren](cache-configure.md#memory-policies) .
