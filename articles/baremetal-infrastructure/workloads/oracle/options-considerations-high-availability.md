---
title: Opties of Oracle BareMetal Infrastructure-servers
description: Meer informatie over de opties en overwegingen voor Oracle BareMetal Infrastructure-servers.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590258"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Opties voor Oracle BareMetal Infrastructure-servers

In dit artikel overwegen we opties en aanbevelingen om het hoogste niveau van beveiliging en prestaties te krijgen die Oracle op BareMetal Infrastructure-servers uitvoeren. 

## <a name="data-guard-protection-modes"></a>Data Guard-beveiligingsmodi

Data Guard biedt beveiliging die alleen beschikbaar is via Oracle Real Applications Cluster (RAC), logische replicatie (zoals GoldenGate) en replicatie op basis van opslag. 

| Beschermingsmodus | Description |
| --- | --- |
| **Maximale prestaties** | De standaardbeveiligingsmodus. Het biedt het hoogste niveau van beveiliging zonder de prestaties van de primaire database te beïnvloeden. Gegevens worden beschouwd als vastgelegd zodra ze naar de redo-stroom van de primaire database zijn geschreven. Deze wordt vervolgens asynchroon gerepliceerd naar de stand-bydatabase. Over het algemeen ontvangt de stand-bydatabase deze binnen enkele seconden, maar er wordt geen garantie gegeven voor dat effect. Deze modus voldoet doorgaans aan de bedrijfsvereisten (naast vertragingsbewaking) zonder dat er netwerkconnectiviteit met lage latentie tussen de primaire sites en stand-bysites nodig is.<br /><br />Het biedt de beste operationele persistentie; Het biedt echter geen garantie voor geen gegevensverlies.   |
| **Maximale beschikbaarheid** | Biedt het hoogste niveau van beveiliging zonder dat dit van invloed is op de beschikbaarheid van de primaire database. Gegevens worden nooit beschouwd als vastgelegd in de primaire database totdat ze ook zijn vastgelegd in ten minste één stand-bydatabase. Als de wijzigingen in de primaire database niet naar ten minste één stand-bydatabase kunnen worden geschreven, wordt de modus Maximumprestaties teruggelijnd in plaats van dat deze niet meer beschikbaar is. <br /><br />Hiermee kan de service doorgaan als de stand-bysite niet beschikbaar is. Als slechts één site werkt, blijft er slechts één kopie van de gegevens behouden totdat de tweede site online is en de synchronisatie opnieuw tot stand is gebracht. |
| **Maximale beveiliging** | Biedt een vergelijkbaar beveiligingsniveau voor maximale beschikbaarheid. De primaire database wordt afgesloten met de toegevoegde functie als de wijzigingen voor opnieuw doen niet naar ten minste één stand-bydatabase kunnen worden geschreven. Dit zorgt ervoor dat gegevensverlies niet kan optreden, maar ten koste gaat van een kwetsbaardere beschikbaarheid. |

>[!IMPORTANT]
>Als u een RPO (Recovery Point Objective) van nul nodig hebt, raden we de configuratie Voor maximale beschikbaarheid aan. RPO kan vervolgens worden gegarandeerd, zelfs wanneer er meerdere fouten optreden. Bijvoorbeeld, zelfs in het geval van een netwerkstoring van de primaire database, gevolgd door het verlies van de primaire database enige tijd later, terwijl de netwerkstoring nog steeds van kracht is.

### <a name="data-guard-deployment-patterns"></a>Data Guard-implementatiepatronen

Met Oracle kunt u meerdere bestemmingen configureren voor het genereren van redo's, waardoor meerdere stand-bydatabases mogelijk zijn. De meest voorkomende configuratie wordt weergegeven in de volgende afbeelding, een individuele stand-bydatabase in een andere regio.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Diagram met de standaardImplementatie van Data Guard van Oracle.":::

Data Guard is geconfigureerd in de modus Maximale prestaties voor een standaardimplementatie. Deze configuratie biedt bijna realtime gegevensreplicatie via asynchron redo-transport. De stand-bydatabase hoeft niet te worden uitgevoerd binnen een RAC-implementatie, maar we raden u aan te voldoen aan de prestatieeisen van de primaire site.

We raden een implementatie aan zoals die wordt weergegeven in de volgende afbeelding voor omgevingen waarvoor strikte uptime of een RPO van nul is vereist. De maximale beschikbaarheidsconfiguratie bestaat uit een lokale stand-bydatabase die redo in synchrone modus toe passen en een tweede stand-bydatabase die wordt uitgevoerd in een externe regio.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Diagram met de maximale beschikbaarheid van Data Guard-implementatie.":::

U kunt een lokale stand-bydatabase maken wanneer de prestaties van de toepassing worden teruggeslagen door het uitvoeren van de database- en toepassingsservers in afzonderlijke regio's. In deze configuratie wordt een lokale stand-bydatabase gebruikt wanneer gepland of ongepland onderhoud nodig is op het primaire cluster. U kunt deze databases uitvoeren met synchrone replicatie omdat ze zich in dezelfde regio, zodat er geen gegevens verloren gaan tussen deze databases.

### <a name="data-guard-configuration-considerations"></a>Overwegingen bij de configuratie van Data Guard

Data Guard Broker moet worden geïmplementeerd, omdat het de implementatie van een Data Guard-configuratie vereenvoudigt en ervoor zorgt dat aan de best practices wordt gehouden. Het biedt functionaliteit voor prestatiebewaking en vereenvoudigt de procedures voor overstappen, failovers en herinstantiatie aanzienlijk.

Met Data Guard kunt u een waarnemerproces uitvoeren dat alle databases in een Data Guard-configuratie bewaakt om de beschikbaarheid van de database te bepalen. Als een primaire database uitvalt, kan de Data Guard Observer automatisch een failover naar een stand-bydatabase in de configuratie starten. U kunt de Data Guard-waarnemer met meerdere waarnemers implementeren op basis van het aantal fysieke sites (maximaal drie). 

Deze waarnemer moet zich bevinden in de infrastructuur die ondersteuning biedt voor de toepassingslaag. De primaire waarnemer moet altijd bestaan op de fysieke site waar de primaire database zich niet bevindt. We raden u aan om voorzichtig te zijn bij het automatiseren van failoverbewerkingen die worden geactiveerd door een Data Guard-waarnemer. Zorg er eerst voor dat uw toepassingen zijn ontworpen en getest om acceptabele service te bieden wanneer de database op een afzonderlijke locatie wordt uitgevoerd.

Als de toepassing alleen lokaal kan worden uitgevoerd, moet failover naar de secundaire site handmatig zijn. Omgevingen die hoge beschikbaarheidsniveaus vereisen (99,99% of 99,999% uptime) moeten zowel een lokale als externe stand-bydatabase gebruiken, zoals wordt weergegeven in de vorige afbeelding. In dergelijke gevallen wordt de parameter FastStartFailoverTarget alleen ingesteld op de lokale stand-bydatabase.

Voor alle toepassingen die ondersteuning bieden voor toegang tot meerdere sitetoepassingen/databases, wordt FastStartFailoverTarget ingesteld op alle stand-bydatabases in de Data Guard-configuratie.

### <a name="active-data-guard"></a>Active Data Guard

Oracle Active Data Guard (ADG) is een superset van eenvoudige Data Guard-mogelijkheden die zijn opgenomen in Oracle Database Enterprise Edition. Het biedt de volgende toegevoegde functies, die worden gebruikt in de Oracle Exadata-implementatie:

- Unieke beschadigingsdetectie en automatisch herstel.
- Snelle failover naar de gesynchroniseerde replica van productie: handmatig of automatisch.
- Productieworkload offloaden naar een gesynchroniseerde stand-by geopend alleen-lezen.
- Rolling upgrades voor databases en stand-by. Eerste patching met behulp van fysieke stand-by.
- Incrementele back-ups offloaden naar stand-by.
- Beveiliging tegen gegevensherstel zonder gegevensverlies over elke afstand zonder dat dit van invloed is op de prestaties.

Het whitepaper dat beschikbaar [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) is op biedt een goed overzicht van de voorgaande functies, zoals wordt weergegeven in de volgende afbeelding.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Diagram met een overzicht van de Active Data Guard-functies van Oracle.":::

## <a name="backup-recommendations"></a>Aanbevelingen voor back-ups

Zorg ervoor dat u een back-up van uw databases hebt. Gebruik de functies voor herstellen en herstellen om een database te herstellen naar hetzelfde of een ander systeem, of om databasebestanden te herstellen.

Het is belangrijk om een strategie voor back-upherstel te maken om uw Oracle Database-apparaatdatabases te beschermen tegen gegevensverlies. Een dergelijk verlies kan het gevolg zijn van een fysiek probleem met een schijf die een fout veroorzaakt bij het lezen of schrijven naar een schijfbestand dat is vereist om de database uit te voeren. Gebruikersfouten kunnen ook leiden tot gegevensverlies. De back-upfunctie biedt de mogelijkheid om herstel naar een bepaald tijdstip (PITR) te herstellen van de **database, SCN-herstel (System Change Number)** en de meest recente herstelfunctie . U kunt een back-upbeleid maken in de browser Gebruikersinterface of via de opdrachtregelinterface.

De volgende back-upopties zijn beschikbaar:

- Back-up naar NFS-opslagvolume (Fast Recovery Area-FRA- /u98).
- Met Azure NetApp Files SnapCenter – momentopname.

Proces om rekening mee te houden:

- Handmatige of automatische back-ups.
- Automatische back-ups worden geschreven naar NFS-opslagvolumes (bijvoorbeeld /u98).
- Back-ups worden uitgevoerd tussen 12:00 uur en 6:00 uur in de tijdzone van het databasesysteem.
- Huidige bewaarperioden: 7, 15, 30, 45 en 60 dagen.

- Database herstellen vanuit een back-up die is opgeslagen in objectopslag:
  - Naar de laatst bekende goede staat met zo weinig mogelijk gegevensverlies.
  - Met behulp van de opgegeven tijdstempel.
  - Gebruik de opgegeven SCN.
  - BackupReport: maakt gebruik _van SCN vanuit een back-uprapport in plaats van een opgegeven SCN._

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Diagram met een back-up van de database van de klant naar FRA (/u98) en niet-FRA (/u95).":::

### <a name="backup-policy"></a>Back-upbeleid

Het back-upbeleid definieert de back-updetails. Wanneer u een back-upbeleid maakt, definieert u de bestemming voor databaseback-ups FRA (NFS-locatie) en definieert u het herstelvenster.

Standaard wordt het basiscompressiealgoritme gebruikt. Bij het gebruik van LOW-, MEDIUM- of HIGH-compressiealgoritmen voor schijf- of NFS-back-upbeleid, zijn er licentieoverwegingen.

### <a name="backup-levels"></a>Back-upniveaus

Geef het back-upniveau op wanneer u een back-up wilt maken.

- Niveau 0 - Volledig
- Niveau 1: incrementeel
- LongTerm/Archievelog: gebruik, met uitzondering van het bewaarbeleid voor back-ups, niet-FRA-locatie (bijvoorbeeld /u95).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het herstellen van uw Oracle-database wanneer er een fout optreedt:

> [!div class="nextstepaction"]
> [Uw Oracle-database herstellen op azure BareMetal-infrastructuur](oracle-high-availability-recovery.md)
