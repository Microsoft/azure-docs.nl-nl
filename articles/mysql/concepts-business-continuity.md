---
title: Bedrijfs continuïteit-Azure Database for MySQL
description: Meer informatie over bedrijfs continuïteit (herstel naar een bepaald tijdstip, de onderbreking van data centers, geo-Restore) bij het gebruik van Azure Database for MySQL service.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 15fde6e7558c685537d36f45bcc7e3ff341544ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542490"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Inzicht in de bedrijfs continuïteit in Azure Database for MySQL

In dit artikel worden de mogelijkheden beschreven die Azure Database for MySQL biedt voor bedrijfs continuïteit en herstel na nood gevallen. Meer informatie over opties voor het herstellen van verstorende gebeurtenissen die gegevens verlies kunnen veroorzaken of ervoor zorgen dat uw data base en toepassing niet meer beschikbaar zijn. Meer informatie over wat u moet doen wanneer een gebruiker of toepassings fout van invloed is op de gegevens integriteit, een Azure-regio een storing heeft of als uw toepassing onderhoud vereist.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Functies die u kunt gebruiken om bedrijfs continuïteit te bieden

Azure Database for MySQL biedt functies voor bedrijfs continuïteit, waaronder geautomatiseerde back-ups en de mogelijkheid voor gebruikers om geo-herstel te initiëren. Elk heeft verschillende kenmerken voor de geschatte herstel tijd (ERT) en het mogelijke gegevens verlies. De geschatte herstel tijd (ERT) is de geschatte duur voor de data base die volledig functioneel is na een herstel-of failover-aanvraag. Wanneer u deze opties kent, kunt u er een kiezen en ze samen gebruiken voor verschillende scenario's. Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld nadat de gebeurtenis is verstoord. Dit is de beoogde herstel tijd (RTO). U moet ook inzicht krijgen in de maximale hoeveelheid recente gegevens updates (tijds interval) die de toepassing kan afnemen bij het herstellen na het verstorings proces. Dit is het beoogde herstel punt (RPO).

De volgende tabel vergelijkt de ERT en RPO voor de beschik bare functies:

| **Mogelijkheid** | **Basic** | **Algemeen doel** | **Geoptimaliseerd voor geheugen** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Herstel naar een bepaald tijdstip vanuit back-up | Elk herstel punt binnen de Bewaar periode | Elk herstel punt binnen de Bewaar periode | Elk herstel punt binnen de Bewaar periode |
| Geo-herstel van geo-gerepliceerde back-ups | Niet ondersteund | ERT < 12 uur<br/>RPO < 1 uur | ERT < 12 uur<br/>RPO < 1 uur |

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Een server herstellen na een gebruikers-of toepassings fout

U kunt de back-ups van de service gebruiken om een server te herstellen van verschillende verstorende gebeurtenissen. Een gebruiker kan per ongeluk enkele gegevens verwijderen, onbedoeld een belang rijke tabel neerzetten of zelfs een volledige data base neerzetten. Een toepassing kan per ongeluk goede gegevens overschrijven met onjuiste gegevens als gevolg van een defect van de toepassing, enzovoort.

U kunt een herstel punt op tijd uitvoeren om een kopie van uw server te maken naar een bekend, goed moment. Dit tijdstip moet binnen de retentie periode voor back-ups vallen die u voor uw server hebt geconfigureerd. Nadat de gegevens zijn teruggezet naar de nieuwe server, kunt u de oorspronkelijke server vervangen door de zojuist herstelde server of de benodigde gegevens van de herstelde server naar de oorspronkelijke server kopiëren.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Herstellen van een regionale Data Center-storing in azure

Hoewel zeldzaam, kan er een storing optreden in een Azure-datacenter. Wanneer er zich een storing voordoet, wordt er een probleem ondervinden dat een bedrijf niet langer dan een paar minuten kan duren.

Een optie is om te wachten tot de server weer online is wanneer de storing van het Data Center wordt overschreden. Dit werkt voor toepassingen die de server gedurende een bepaalde periode offline kunnen hebben, bijvoorbeeld een ontwikkel omgeving. Wanneer Data Center een storing heeft, weet u niet hoe lang de onderbreking kan duren, dus deze optie werkt alleen als u de server enige tijd niet nodig hebt.

De andere optie is het gebruik van de functie geo-Restore van de Azure Database for MySQL waarmee de server wordt hersteld met behulp van geo-redundante back-ups. Deze back-ups zijn toegankelijk, zelfs wanneer de regio waarin uw server wordt gehost, offline is. U kunt van deze back-ups naar een andere regio herstellen en uw server weer online brengen.

> [!IMPORTANT]
> Geo-herstel is alleen mogelijk als u de server hebt ingericht met geografisch redundante back-upopslag. Als u wilt overschakelen van lokaal redundant naar geo-redundante back-ups voor een bestaande server, moet u een dump maken met behulp van mysqldump van uw bestaande server en deze herstellen op een nieuwe server die is geconfigureerd met geografisch redundante back-ups.

## <a name="cross-region-read-replicas"></a>Meerdere regio's replica's lezen

U kunt Kruis regio's gebruiken om replica's te verg Roten om uw bedrijfs continuïteit en herstel na nood gevallen te verbeteren. Lees replica's worden asynchroon bijgewerkt met de binaire logboek replicatie technologie van MySQL. Meer informatie over het lezen van replica's, beschik bare regio's en hoe u een failover kunt uitvoeren vanuit het [artikel concepten van replica's lezen](concepts-read-replicas.md). 

## <a name="faq"></a>Veelgestelde vragen
### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Waar worden klant gegevens Azure Database for MySQL opgeslagen?
Azure Database for MySQL verplaatsen of opslaan van klant gegevens wordt standaard niet uitgevoerd in de regio waarin deze is geïmplementeerd. Klanten kunnen er echter voor kiezen om [geo-redundante back-ups](concepts-backup.md#backup-redundancy-options) in te scha kelen of om [meerdere regio's](concepts-read-replicas.md#cross-region-replication) te maken voor het opslaan van gegevens in een andere regio.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [automatische back-ups in azure database for MySQL](concepts-backup.md).
- Meer informatie over het herstellen met behulp van [de Azure Portal](howto-restore-server-portal.md) of [de Azure cli](howto-restore-server-cli.md).
- Meer informatie over het [lezen van replica's in azure database for MySQL](concepts-read-replicas.md).
