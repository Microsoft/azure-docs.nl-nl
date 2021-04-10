---
title: Overzicht van redundante hoge Beschik baarheid in zone met Azure Database for MySQL flexibele server
description: Meer informatie over de concepten van redundante hoge Beschik baarheid in zones met Azure Database for MySQL flexibele server
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 6629beacb5c3edc6fe1d21509051b915c0894479
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109689"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Concepten met hoge Beschik baarheid in Azure Database for MySQL flexibele server (preview-versie)

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Met Azure Database for MySQL flexibele server (preview) kunt u hoge Beschik baarheid configureren met automatische failover met behulp van **zone redundante** maximale Beschik baarheid. Bij een implementatie in een zone-redundante configuratie wordt door de flexibele server automatisch een stand-byreplica in een andere beschikbaarheidszone ingericht en beheerd. Met behulp van replicatie op opslag niveau worden de gegevens **synchroon gerepliceerd** naar de stand-by-server in de secundaire zone om gegevens verlies na een failover mogelijk te maken. De failover is volledig transparant van de client toepassing en vereist geen gebruikers acties. De stand-by-server is niet beschikbaar voor lees-of schrijf bewerkingen, maar is een passieve stand-by om snelle failover mogelijk te maken. De failover-tijden variëren doorgaans van 60-120 seconden.

De configuratie van de redundante hoge Beschik baarheid van een zone maakt automatische failover mogelijk tijdens geplande gebeurtenissen, zoals door de gebruiker geïnitieerde schaal reken bewerkingen, en niet-geplande gebeurtenissen, zoals onderliggende hardware-en software fouten, netwerk fouten en zelfs problemen met de beschikbaarheids zone.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="weer gave van zone redundante hoge Beschik baarheid":::

## <a name="zone-redundancy-architecture"></a>Architectuur voor zone redundantie

De primaire server wordt geïmplementeerd in de regio en een specifieke beschikbaarheids zone. Als de maximale Beschik baarheid is gekozen, wordt een stand-by replica server met dezelfde configuratie als die van de primaire server automatisch geïmplementeerd, inclusief Compute-laag, reken grootte, opslag grootte en netwerk configuratie. De logboek gegevens worden synchroon gerepliceerd naar de stand-by replica om ervoor te zorgen dat er geen gegevens verloren gaan in geval van fouten. Automatische back-ups, zowel moment opnamen en logboek back-ups, worden uitgevoerd vanaf de primaire database server. 

De status van de HA wordt voortdurend bewaakt en gerapporteerd op de pagina overzicht.

De verschillende replicatie statussen worden hieronder weer gegeven:

| **Status** | **Beschrijving** |
| :----- | :------ |
| <b>NotEnabled | Zone redundante HA is niet ingeschakeld |
| <b>CreatingStandby | In het proces van het maken van een nieuwe stand-by |
| <b>ReplicatingData | Nadat de stand-by is gemaakt, wordt deze opgeteld bij de primaire server. |
| <b>FailingOver | Er wordt een failover van de primaire server naar de stand-bymodus uitgevoerd. |
| <b>In orde | Zone redundante HA bevindt zich in de stationaire staat en in orde. |
| <b>RemovingStandby | Op basis van de actie van de gebruiker wordt de stand-by replica in het proces verwijderd.| 

## <a name="advantages"></a>Voordelen

Hier volgen enkele voor delen voor het gebruik van de functie zone redundantie HA: 

- De stand-by replica wordt geïmplementeerd in een exacte VM-configuratie als primair, zoals vCores, opslag, netwerk instellingen (VNET, firewall), enzovoort.
- Mogelijkheid om de stand-by replica te verwijderen door hoge Beschik baarheid uit te scha kelen.
- Automatische back-ups zijn gebaseerd op moment opnamen, uitgevoerd vanaf de primaire database server en opgeslagen in een zone redundante opslag.
- In het geval van een failover wordt Azure Database for MySQL flexibele server automatisch een failover naar de stand-by-replica als hoge Beschik baarheid is ingeschakeld. De instelling maximale Beschik baarheid controleert de primaire server en brengt deze weer online.
- Clients maken altijd verbinding met de primaire database server.
- Als er een fout is opgetreden in de data base of het knoop punt is mislukt, wordt het opnieuw opstarten geprobeerd eerst op hetzelfde knoop punt. Als dat niet lukt, wordt de automatische failover geactiveerd.
- De mogelijkheid om de server opnieuw op te starten om alle para meters voor de statische server op te halen.

## <a name="steady-state-operations"></a>Bewerkingen met stabiele status

Toepassingen zijn verbonden met de primaire server met behulp van de naam van de database server. De stand-by-replica gegevens worden niet weer gegeven voor directe toegang. Door voeren en schrijven worden alleen voor de toepassing bevestigd nadat de logboek bestanden op de schijf van de primaire server en de stand-by replica synchroon worden bewaard. Als gevolg van deze extra round-trip vereiste kunnen toepassingen verhoogde latentie voor schrijf bewerkingen en door voeringen verwachten. U kunt de status van de hoge Beschik baarheid in de portal bewaken.

## <a name="failover-process"></a>Failoverproces 
Voor bedrijfs continuïteit moet u een failover-proces hebben voor geplande en niet-geplande gebeurtenissen. 

### <a name="planned-events"></a>Geplande gebeurtenissen

Geplande downtime-gebeurtenissen bevatten activiteiten die zijn gepland door Azure, zoals periodieke software-updates, secundaire versie-upgrades of die worden geïnitieerd door klanten, zoals schaal berekening en schaal van opslag. Al deze wijzigingen worden eerst toegepast op de stand-by replica. Gedurende die tijd blijven de toepassingen toegang tot de primaire server. Zodra de stand-by replica is bijgewerkt, worden primaire server verbindingen geleegd. er wordt een failover geactiveerd waarmee de stand-by replica als primair wordt geactiveerd en dezelfde database server naam is door de DNS-record bij te werken. Client verbindingen zijn niet meer verbonden en ze moeten opnieuw verbinding maken en kunnen de bewerkingen hervatten. Er wordt een nieuwe stand-by-server in dezelfde zone gemaakt als de oude primaire. De totale failover-tijd wordt verwacht 60-120 s. 

>[!NOTE]
> Bij het schalen van de berekening worden de secundaire replica server, gevolgd door de primaire server, geschaald. Er is geen failover betrokken.

### <a name="failover-process---unplanned-events"></a>Failover-proces-niet-geplande gebeurtenissen
Ongeplande downtime van services omvatten software fouten die of infrastructuur fouten zoals compute-, netwerk-, opslag storingen of energie storingen invloed hebben op de beschik baarheid van de data base. Als de data base niet beschikbaar is, is de replicatie naar de stand-by replica ernstig en wordt de stand-by replica geactiveerd om de primaire data base te zijn. DNS is bijgewerkt en de clients maken vervolgens opnieuw verbinding met de database server en hervatten hun bewerkingen. De verwachte failover-tijd duurt 60-120 s. Afhankelijk van de activiteit op de primaire database server op het moment van de failover, zoals grote trans acties en herstel tijd, kan de failover echter langer duren.

## <a name="schedule-maintenance-window"></a>Onderhouds venster plannen 

Flexibele servers bieden onderhouds plannings mogelijkheden waarbij u een periode van 30 minuten kunt kiezen op een dag van uw voor keur gedurende welke het onderhouds proces van Azure werkt, zoals patches of secundaire versie-upgrades. Voor uw flexibele servers die zijn geconfigureerd met maximale Beschik baarheid, worden deze onderhouds activiteiten eerst uitgevoerd op de stand-by replica. 

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip 
Net als de flexibele server is geconfigureerd in hoge Beschik baarheid gegevens synchroon repliceert, is de stand-by-server up-to-date met de primaire. Eventuele gebruikers fouten op de primaire, zoals een onbedoelde verwijdering van een tabel of onjuiste updates, worden goed gerepliceerd naar de stand-by. Daarom kunt u geen stand-by gebruiken om dergelijke logische fouten te herstellen. Als u deze logische fouten wilt herstellen, moet u de herstel bewerking naar een bepaald tijdstip uitvoeren voordat de fout optrad. Wanneer u de data base herstelt die is geconfigureerd met hoge Beschik baarheid, wordt een nieuwe database server teruggezet als een nieuwe flexibele server met een door de gebruiker opgegeven naam. Vervolgens kunt u het object van de database server exporteren en importeren naar uw productie database server. Als u uw database server wilt klonen voor test-en ontwikkelings doeleinden of als u voor andere doel einden wilt herstellen, kunt u het meest recente herstel punt of een aangepast herstel punt kiezen. Met de herstel bewerking wordt een flexibele server met één zone gemaakt.

## <a name="mitigate-downtime"></a>Uitval tijd beperken 
Wanneer u de functie voor zone redundantie HA niet gebruikt, moet u nog steeds uitval tijd voor uw toepassing kunnen beperken. Het plannen van uitval tijd van services zoals geplande patches, secundaire versie-upgrades of de door de gebruiker gestarte bewerkingen kunnen worden uitgevoerd als onderdeel van het geplande onderhouds venster. Geplande uitval tijd van de service, zoals geplande patches, secundaire versie-upgrades of de door de gebruiker gestarte bewerkingen, zoals schaal berekening, loopt uitval tijd. Als u de gevolgen van toepassingen voor door Azure geïnitieerde onderhouds taken wilt beperken, kunt u ervoor kiezen om deze te plannen op de dag van de week en de tijd die het minst van invloed op de toepassing is. 

Tijdens ongeplande uitval gebeurtenissen, zoals het vastlopen van de data base of de server fout, wordt de betrokken server opnieuw opgestart binnen dezelfde zone. Als er een probleem optreedt, kunt u de Data Base op een andere zone binnen de regio herstellen als de hele zone wordt beïnvloed. 

## <a name="things-to-know-with-zone-redundancy"></a>Wat u moet weten met zone redundantie 

Hier volgen enkele overwegingen waarmee u rekening moet houden wanneer u de maximale Beschik baarheid van zone redundantie gebruikt: 

- Hoge Beschik baarheid kan **alleen** worden ingesteld tijdens een flexibele tijd voor het maken van de server.
- Hoge Beschik baarheid wordt niet ondersteund in een breek bare Compute-laag.
- Als gevolg van synchrone replicatie naar een andere beschikbaarheids zone, kan de primaire database server verhoogde schrijf-en doorvoer latentie ervaren.
- De stand-by replica kan niet worden gebruikt voor alleen-lezen query's.
- Afhankelijk van de activiteit op de primaire server op het moment van de failover, kan het tot 60-120 seconden of langer duren voordat de failover is voltooid.
- Als u de primaire database server opnieuw opstart om dynamische parameter wijzigingen op te halen, wordt de stand-by replica ook opnieuw gestart.
- Het configureren van Lees replica's voor zone redundante servers met hoge Beschik baarheid wordt niet ondersteund.
- Het configureren van door de klant geïnitieerde beheer taken kan niet worden geautomatiseerd tijdens het onderhouds venster.
- Geplande gebeurtenissen, zoals schaal berekeningen en secundaire versie-upgrades, worden tegelijkertijd zowel in de primaire als in de stand-bymodus uitgevoerd. 


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
- Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
- Meer informatie over [back-up en herstel](./concepts-backup-restore.md)
