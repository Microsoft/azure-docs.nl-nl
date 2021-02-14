---
title: Bedrijfs continuïteit-Azure Database for PostgreSQL-één server
description: In dit artikel wordt een beschrijving gegeven van de bedrijfs continuïteit (herstel naar een bepaald tijdstip, de storing in het Data Center, geo-Restore, replica's) bij het gebruik van Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: f8d8547c2d4900e6258f7360c50d8dfc17157832
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517241"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Overzicht van bedrijfs continuïteit met Azure Database for PostgreSQL-één server

In dit overzicht worden de mogelijkheden beschreven die Azure Database for PostgreSQL biedt voor bedrijfs continuïteit en herstel na nood gevallen. Meer informatie over opties voor het herstellen van verstorende gebeurtenissen die gegevens verlies kunnen veroorzaken of ervoor zorgen dat uw data base en toepassing niet meer beschikbaar zijn. Meer informatie over wat u moet doen wanneer een gebruiker of toepassings fout van invloed is op de gegevens integriteit, een Azure-regio een storing heeft of als uw toepassing onderhoud vereist.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Functies die u kunt gebruiken om bedrijfs continuïteit te bieden

Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld nadat de gebeurtenis is verstoord. Dit is de beoogde herstel tijd (RTO). U moet ook inzicht krijgen in de maximale hoeveelheid recente gegevens updates (tijds interval) die de toepassing kan afnemen bij het herstellen na het verstorings proces. Dit is het beoogde herstel punt (RPO).

Azure Database for PostgreSQL biedt functies voor bedrijfscontinuïteit die geografisch redundante back-ups bevatten met de mogelijkheid om geo-herstel te initiëren en leesreplica's in een andere regio te implementeren. Elk heeft verschillende kenmerken voor de herstel tijd en het mogelijke gegevens verlies. Met de functie voor [geo-Restore](concepts-backup.md) wordt een nieuwe server gemaakt met behulp van de back-upgegevens die vanuit een andere regio worden gerepliceerd. De totale tijd die nodig is om te herstellen en te herstellen, is afhankelijk van de grootte van de data base en het aantal logboeken dat moet worden hersteld. De totale tijd voor het instellen van de server varieert van enkele minuten tot enkele uren. Bij het [lezen van replica's](concepts-read-replicas.md)worden transactie logboeken van de primaire replica asynchroon naar de replica gestreamd. In het geval van een storing in de primaire Data Base als gevolg van een fout in zone-of regio niveau, biedt failover naar de replica een kortere RTO en minder gegevens verlies.

> [!NOTE]
> De vertraging tussen de primaire en de replica is afhankelijk van de latentie tussen de sites, de hoeveelheid gegevens die moet worden verzonden en de belangrijkste voor de schrijf belasting van de primaire server. Zware schrijf werkbelastingen kunnen aanzienlijke vertraging veroorzaken. 
>
> Omdat de replicatie wordt gebruikt voor lees-replica's, **mag deze niet** worden beschouwd als een oplossing met hoge beschik BAARHEID (ha), omdat de hogere lags een hogere RTO en RPO kunnen betekenen. Lees replica's kunnen fungeren als een HA-alternatief voor werk belastingen waarbij de vertraging kleiner wordt gemaakt door de piek en niet-piek tijden van de werk belasting. Andere Lees replica's zijn bedoeld voor echte Lees-en schaal bare, zware werk belastingen en voor herstel na nood gevallen.

De volgende tabel vergelijkt RTO en RPO in een **typische werkbelasting** scenario:

| **Mogelijkheid** | **Basic** | **Algemeen doel** | **Geoptimaliseerd voor geheugen** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Herstel naar een bepaald tijdstip vanuit back-up | Elk herstel punt binnen de Bewaar periode <br/> RTO-varieert <br/>RPO < 15 minuten| Elk herstel punt binnen de Bewaar periode <br/> RTO-varieert <br/>RPO < 15 minuten | Elk herstel punt binnen de Bewaar periode <br/> RTO-varieert <br/>RPO < 15 minuten |
| Geo-herstel van geo-gerepliceerde back-ups | Niet ondersteund | RTO-varieert <br/>RPO < 1 uur | RTO-varieert <br/>RPO < 1 uur |
| Leesreplica's | RTO-minuten * <br/>RPO < 5 min * | RTO-minuten * <br/>RPO < 5 min *| RTO-minuten * <br/>RPO < 5 min *|

 \* RTO en RPO kunnen in sommige gevallen **veel hoger zijn** , afhankelijk van verschillende factoren, waaronder de latentie tussen sites, de hoeveelheid gegevens die moet worden verzonden en de belang rijke schrijf belasting van de primaire data base. 

## <a name="recover-a-server-after-a-user-or-application-error"></a>Een server herstellen na een gebruikers-of toepassings fout

U kunt de back-ups van de service gebruiken om een server te herstellen van verschillende verstorende gebeurtenissen. Een gebruiker kan per ongeluk enkele gegevens verwijderen, onbedoeld een belang rijke tabel neerzetten of zelfs een volledige data base neerzetten. Een toepassing kan per ongeluk goede gegevens overschrijven met onjuiste gegevens als gevolg van een defect van de toepassing, enzovoort.

U kunt een **herstel punt op tijd** uitvoeren om een kopie van uw server te maken naar een bekend, goed moment. Dit tijdstip moet binnen de retentie periode voor back-ups vallen die u voor uw server hebt geconfigureerd. Nadat de gegevens zijn teruggezet naar de nieuwe server, kunt u de oorspronkelijke server vervangen door de zojuist herstelde server of de benodigde gegevens van de herstelde server naar de oorspronkelijke server kopiëren.

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld. Gebruik [Azure resource Lock](../azure-resource-manager/management/lock-resources.md) om onbedoelde verwijdering van uw server te voor komen.

## <a name="recover-from-an-azure-data-center-outage"></a>Herstellen van een Azure Data Center-storing

Hoewel zeldzaam, kan er een storing optreden in een Azure-datacenter. Wanneer er zich een storing voordoet, wordt er een probleem ondervinden dat een bedrijf niet langer dan een paar minuten kan duren.

Een optie is om te wachten tot de server weer online is wanneer de storing van het Data Center wordt overschreden. Dit werkt voor toepassingen die de server gedurende een bepaalde periode offline kunnen hebben, bijvoorbeeld een ontwikkel omgeving. Wanneer een Data Center een storing heeft, weet u niet hoe lang de onderbreking kan duren. deze optie werkt dus alleen als u de server enige tijd niet nodig hebt.

## <a name="geo-restore"></a>Geo-herstel

De functie voor geo-Restore herstelt de server met behulp van geo-redundante back-ups. De back-ups worden gehost in de [gekoppelde regio](../best-practices-availability-paired-regions.md)van uw server. U kunt terugzetten van deze back-ups naar een andere regio. Met geo-Restore wordt een nieuwe server gemaakt met de gegevens van de back-ups. Meer informatie over geo-Restore vindt u in het [artikel back-ups maken en herstellen](concepts-backup.md).

> [!IMPORTANT]
> Geo-herstel is alleen mogelijk als u de server hebt ingericht met geografisch redundante back-upopslag. Als u wilt overschakelen van lokaal redundant naar geo-redundante back-ups voor een bestaande server, moet u een dump nemen met pg_dump van uw bestaande server en deze herstellen op een nieuwe server die is geconfigureerd met geografisch redundante back-ups.

## <a name="cross-region-read-replicas"></a>Meerdere regio's replica's lezen
U kunt Kruis regio's gebruiken om replica's te verg Roten om uw bedrijfs continuïteit en herstel na nood gevallen te verbeteren. Het lezen van replica's wordt asynchroon bijgewerkt met behulp van de fysieke replicatie technologie van PostgreSQL. Dit kan leiden tot een vertraging van de primaire. Meer informatie over het lezen van replica's, beschik bare regio's en hoe u een failover kunt uitvoeren vanuit het [artikel concepten van replica's lezen](concepts-read-replicas.md). 

## <a name="faq"></a>Veelgestelde vragen
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Waar worden klant gegevens Azure Database for PostgreSQL opgeslagen?
Azure Database for PostgreSQL verplaatsen of opslaan van klant gegevens wordt standaard niet uitgevoerd in de regio waarin deze is geïmplementeerd. Klanten kunnen er echter voor kiezen om [geo-redundante back-ups](concepts-backup.md#backup-redundancy-options) in te scha kelen of om [meerdere regio's](concepts-read-replicas.md#cross-region-replication) te maken voor het opslaan van gegevens in een andere regio.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [automatische back-ups in azure database for PostgreSQL](concepts-backup.md). 
- Meer informatie over het herstellen met behulp van [de Azure Portal](howto-restore-server-portal.md) of [de Azure cli](howto-restore-server-cli.md).
- Meer informatie over het [lezen van replica's in azure database for PostgreSQL](concepts-read-replicas.md).
