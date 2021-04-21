---
title: Overzicht van zone-redundante hoge beschikbaarheid met Azure Database for MySQL Flexible Server
description: Meer informatie over de concepten van zone-redundante hoge beschikbaarheid met Azure Database for MySQL Flexible Server
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 5b5e1491d7f76cd4cff76d0c9a1af4daa49fa483
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812998"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Concepten voor hoge beschikbaarheid in Azure Database for MySQL Flexible Server (preview)

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Azure Database for MySQL Flexibele server (preview) kunt u hoge beschikbaarheid configureren met automatische failover met behulp **van zone-redundante** hoge beschikbaarheid. Bij een implementatie in een zone-redundante configuratie wordt door de flexibele server automatisch een stand-byreplica in een andere beschikbaarheidszone ingericht en beheerd. Met replicatie op opslagniveau worden de gegevens **synchroon** gerepliceerd naar de stand-byserver in de secundaire zone om geen gegevensverlies na een failover mogelijk te maken. De failover is volledig transparant vanuit de clienttoepassing en vereist geen gebruikersacties. De stand-byserver is niet beschikbaar voor lees- of schrijfbewerkingen, maar is een passieve stand-by om snelle failover mogelijk te maken. De failovertijden variëren doorgaans van 60 tot 120 seconden.

Zone-redundante configuratie voor hoge beschikbaarheid maakt automatische failover mogelijk tijdens geplande gebeurtenissen, zoals door de gebruiker geïnitieerde rekenbewerkingen, en ongeplande gebeurtenissen, zoals onderliggende hardware- en softwarefouten, netwerkfouten en zelfs beschikbaarheidszonefouten.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="weergave van zone-redundante hoge beschikbaarheid":::

## <a name="zone-redundancy-architecture"></a>Architectuur voor zone-redundantie

De primaire server wordt geïmplementeerd in de regio en een specifieke beschikbaarheidszone. Wanneer de hoge beschikbaarheid wordt gekozen, wordt er automatisch een stand-byreplicaserver geïmplementeerd met dezelfde configuratie als die van de primaire server, waaronder de rekenlaag, rekengrootte, opslaggrootte en netwerkconfiguratie. De logboekgegevens worden synchroon gerepliceerd naar de stand-byreplica om ervoor te zorgen dat er geen gegevens verloren gaan in een storingssituatie. Automatische back-ups, zowel momentopnamen als logboekback-ups, worden uitgevoerd vanaf de primaire databaseserver. 

De status van de ha-status wordt continu gecontroleerd en gerapporteerd op de overzichtspagina.

Hieronder vindt u de verschillende replicatiestatussen:

| **Status** | **Beschrijving** |
| :----- | :------ |
| <b>NotEnabled | Zone-redundante ha is niet ingeschakeld |
| <b>CreatingStandby | Tijdens het maken van een nieuwe stand-by |
| <b>ReplicatingData | Nadat de stand-by is gemaakt, wordt de primaire server inhaalslag gemaakt. |
| <b>FailingOver | De databaseserver wordt uitgevoerd van de primaire naar de stand-by. |
| <b>In orde | Zone-redundante ha heeft een stabiele status en is in orde. |
| <b>RemovingStandby | Op basis van de actie van de gebruiker wordt de stand-byreplica verwijderd.| 

## <a name="advantages"></a>Voordelen

Hier zijn enkele voordelen voor het gebruik van zone-redundantie HA-functie: 

- Stand-byreplica wordt geïmplementeerd in een exacte VM-configuratie als die van de primaire VM, zoals vCores, opslag, netwerkinstellingen (VNET, firewall), enzovoort.
- Mogelijkheid om stand-byreplica's te verwijderen door hoge beschikbaarheid uit te zetten.
- Automatische back-ups worden op momentopnamen gebaseerd, uitgevoerd vanaf de primaire databaseserver en opgeslagen in een zone-redundante opslag.
- In het geval van failover wordt Azure Database for MySQL server automatisch overgeslagen naar de stand-byreplica als hoge beschikbaarheid is ingeschakeld. De installatie van hoge beschikbaarheid controleert de primaire server en brengt deze weer online.
- Clients maken altijd verbinding met de primaire databaseserver.
- Als er een crash- of knooppuntfout in de database is, wordt eerst geprobeerd opnieuw op te starten op hetzelfde knooppunt. Als dat mislukt, wordt de automatische failover geactiveerd.
- Mogelijkheid om de server opnieuw op te starten om wijzigingen in statische serverparameters op te halen.

## <a name="steady-state-operations"></a>Bewerkingen met een stabiele status

Toepassingen zijn verbonden met de primaire server met behulp van de naam van de databaseserver. De stand-by replicagegevens worden niet beschikbaar gemaakt voor directe toegang. Commits en schrijfingen worden pas naar de toepassing bevestigd nadat de logboekbestanden op zowel de schijf van de primaire server als de stand-byreplica synchroon zijn opgeslagen. Vanwege deze aanvullende retourvereiste kunnen toepassingen een verhoogde latentie verwachten voor schrijf- en commits. U kunt de status van de hoge beschikbaarheid bewaken in de portal.

## <a name="failover-process"></a>Failoverproces 
Voor bedrijfscontinuïteit moet u een failoverproces hebben voor geplande en ongeplande gebeurtenissen. 

### <a name="planned-events"></a>Geplande gebeurtenissen

Geplande downtimegebeurtenissen omvatten activiteiten die door Azure zijn gepland, zoals periodieke software-updates, kleine versie-upgrades of die worden geïnitieerd door klanten, zoals reken- en schaalbewerkingen. Al deze wijzigingen worden eerst toegepast op de stand-byreplica. Gedurende die tijd hebben de toepassingen nog steeds toegang tot de primaire server. Zodra de stand-byreplica is bijgewerkt, worden de primaire serververbindingen leeggestopt. Er wordt een failover geactiveerd waarmee de stand-byreplica wordt geactiveerd als primaire replica met dezelfde databaseservernaam door de DNS-record bij te werken. Clientverbindingen worden verbroken en ze moeten opnieuw verbinding maken en kunnen hun bewerkingen hervatten. Er wordt een nieuwe stand-byserver tot stand gebracht in dezelfde zone als de oude primaire server. De totale failovertijd is naar verwachting 60-120 s. 

>[!NOTE]
> In het geval van de rekenschaalbewerking schalen schalen we de secundaire replicaserver gevolgd door de primaire server. Er is geen failover bij betrokken.

### <a name="failover-process---unplanned-events"></a>Failoverproces - niet-geplande gebeurtenissen
Niet-geplande serviceuitvaltijd omvat softwarefouten die of infrastructuurfouten, zoals reken-, netwerk-, opslagfouten of stroomstoringen, van invloed zijn op de beschikbaarheid van de database. Als de database niet beschikbaar is, wordt de replicatie naar de stand-byreplica verwijderd en wordt de stand-byreplica geactiveerd om de primaire database te zijn. DNS wordt bijgewerkt en clients maken vervolgens opnieuw verbinding met de databaseserver en hervatten hun bewerkingen. De totale failovertijd duurt naar verwachting 60-120 s. Afhankelijk van de activiteit in de primaire databaseserver op het moment van de failover, zoals grote transacties en hersteltijd, kan de failover echter langer duren.

### <a name="forced-failover"></a>Geforceerd failover
Azure Database for MySQL geforceerd failover kunt u handmatig een failover forcen, zodat u de functionaliteit kunt testen met uw toepassingsscenario's en u kunt voorbereiden in geval van uitval. Met geforceerde failover wordt de stand-byserver de primaire server door een failover te activeren waarmee de stand-byreplica wordt geactiveerd om de primaire server met dezelfde databaseservernaam te worden door de DNS-record bij te werken. De oorspronkelijke primaire server wordt opnieuw opgestart en overgeschakeld naar de stand-byreplica. Clientverbindingen worden verbroken en moeten opnieuw worden verbonden om de bewerkingen te hervatten. Afhankelijk van de huidige workload en het laatste controlepunt wordt de totale failovertijd gemeten. Over het algemeen wordt verwacht dat deze tussen de 60 en 120 ligt.

## <a name="schedule-maintenance-window"></a>Onderhoudsvenster plannen 

Flexibele servers bieden de mogelijkheid om onderhoud te plannen, waarbij u een venster van 30 minuten kunt kiezen op een dag van uw voorkeur waarin het Azure-onderhoud werkt, zoals patching of kleine versie-upgrades. Voor uw flexibele servers die zijn geconfigureerd met hoge beschikbaarheid, worden deze onderhoudsactiviteiten eerst uitgevoerd op de stand-byreplica. 

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip 
Omdat flexibele servers synchroon worden geconfigureerd in hoge beschikbaarheid, worden gegevens gerepliceerd en is de stand-byserver up-to-date met de primaire server. Eventuele gebruikersfouten op de primaire computer, zoals een onbedoelde daling van een tabel of onjuiste updates, worden naar de stand-by gerepliceerd. Daarom kunt u stand-by niet gebruiken om dergelijke logische fouten te herstellen. Als u van deze logische fouten wilt herstellen, moet u herstel naar een bepaald tijdstip uitvoeren naar het tijdstip voordat de fout zich voordeed. Wanneer u de database die is geconfigureerd met hoge beschikbaarheid herstelt met behulp van de functie voor herstel naar een bepaald tijdstip van flexibele server, wordt een nieuwe databaseserver hersteld als een nieuwe flexibele server met een door de gebruiker opgegeven naam. Vervolgens kunt u het -object exporteren van de databaseserver en het importeren naar uw productiedatabaseserver. Op dezelfde manier kunt u het meest recente herstelpunt of een aangepast herstelpunt kiezen als u uw databaseserver wilt klonen voor test- en ontwikkelingsdoeleinden, of als u wilt herstellen voor andere doeleinden. Met de herstelbewerking wordt een flexibele server met één zone gemaakt.

## <a name="mitigate-downtime"></a>Downtime beperken 
Wanneer u de functie zone-redundantie ha niet gebruikt, moet u nog steeds in staat zijn om downtime voor uw toepassing te beperken. Serviceonderbrekingen plannen, zoals geplande patches, kleine versie-upgrades of door de gebruiker geïnitieerde bewerkingen, kunnen worden uitgevoerd als onderdeel van geplande onderhoudsvensters. Geplande serviceuitvaltijd, zoals geplande patches, kleine versie-upgrades of door de gebruiker geïnitieerde bewerkingen, zoals schaalreken, veroorzaken downtime. Als u de gevolgen van de toepassing voor door Azure geïnitieerde onderhoudstaken wilt beperken, kunt u ervoor kiezen om deze te plannen op de dag van de week en het tijdstip die de toepassing het minst beïnvloeden. 

Tijdens niet-geplande downtimegebeurtenissen, zoals het vastlopen van de database of het mislukken van de server, wordt de betrokken server opnieuw opgestart binnen dezelfde zone. Hoewel dit zelden gebeurt, kunt u de database in een andere zone in de regio herstellen als dit van invloed is op de hele zone. 

## <a name="things-to-know-with-zone-redundancy"></a>Wat u moet weten met zone-redundantie 

Hier zijn enkele overwegingen om rekening mee te houden wanneer u zone-redundantie met hoge beschikbaarheid gebruikt: 

- Hoge beschikbaarheid kan **alleen worden** ingesteld tijdens het maken van flexibele servers.
- Hoge beschikbaarheid wordt niet ondersteund in een burstable rekenlaag.
- Vanwege synchrone replicatie naar een andere beschikbaarheidszone kan de primaire databaseserver verhoogde schrijf- en commit-latentie ervaren.
- Stand-byreplica kan niet worden gebruikt voor alleen-lezen query's.
- Afhankelijk van de activiteit op de primaire server op het moment van de failover, kan het tot 60-120 seconden of langer duren voordat de failover is voltooid.
- Als u de primaire databaseserver opnieuw opstart om wijzigingen in statische parameters op te halen, wordt ook de stand-byreplica opnieuw opgestart.
- Het configureren van leesreplica's voor zone-redundante servers met hoge beschikbaarheid wordt niet ondersteund.
- Het configureren van door de klant geïnitieerde beheertaken kan niet worden geautomatiseerd tijdens het beheerde onderhoudsvenster.
- Geplande gebeurtenissen, zoals het schalen van rekenkracht en kleine versie-upgrades, vinden op hetzelfde moment plaats in zowel de primaire als de stand-bymodus. 


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bedrijfscontinuïteit](./concepts-business-continuity.md)
- Meer informatie over [zone-redundante hoge beschikbaarheid](./concepts-high-availability.md)
- Meer informatie over [back-up en herstel](./concepts-backup-restore.md)
