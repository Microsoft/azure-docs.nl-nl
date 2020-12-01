---
title: Aanbevolen procedures voor MySQL-server-Azure Database for MySQL
description: In dit artikel worden de aanbevolen procedures beschreven voor het uitvoeren van uw MySQL-data base in Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355031"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Aanbevolen procedures voor Server bewerkingen op Azure Database for MySQL-één server

Meer informatie over de aanbevolen procedures voor het werken met Azure Database for MySQL. Wanneer we nieuwe mogelijkheden toevoegen aan het platform, blijven we zich richten op het verfijnen van de best practices die in deze sectie worden beschreven.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Operationele richt lijnen Azure Database for MySQL 

Hieronder vindt u operationele richt lijnen die moeten worden gevolgd bij het werken met uw Azure Database for MySQL om de prestaties van uw data base te verbeteren: 

* **Co-locatie**: als u de netwerk latentie wilt beperken, plaatst u de client en de database server in dezelfde Azure-regio.

* **Uw geheugen-, CPU-en opslag gebruik bewaken**: u kunt [waarschuwingen instellen](howto-alert-on-metric.md) om u te waarschuwen wanneer de gebruiks patronen veranderen of wanneer u de capaciteit van uw implementatie aanpakt, zodat u de systeem prestaties en beschik baarheid kunt hand haven. 

* **Uw data base-exemplaar omhoog schalen**: u kunt [Omhoog schalen](howto-create-manage-server-portal.md) wanneer u de limieten voor opslag capaciteit nadert. U moet enige buffer in opslag en geheugen hebben om een onvoorziene toename in de vraag van uw toepassingen te kunnen bieden. U kunt de functie voor [het automatisch verg Roten van opslag ook inschakelen](howto-auto-grow-storage-portal.md) op alleen om ervoor te zorgen dat de service de opslag in de buurt van de opslag limieten in het nabij stelt. 

* **Back-ups configureren**: Schakel [lokale of geo-redundante back-ups](howto-restore-server-portal.md#set-backup-configuration) in op basis van de vereiste van het bedrijf. Daarnaast wijzigt u de Bewaar periode op hoe lang de back-ups beschikbaar zijn voor bedrijfs continuïteit. 

* **I/o-capaciteit verg Roten**: als de werk belasting van uw data base meer I/o-bewerkingen vereist dan u hebt ingericht, worden herstel of andere transactionele handelingen voor uw data base traag. Voer een van de volgende handelingen uit om de I/O-capaciteit van een Server exemplaar te verhogen: 

    * Azure data base for MySQL zorgt voor het schalen van IOPS tegen het aantal drie IOPS per GB opslag ingericht. [Verg root de ingerichte opslag](howto-create-manage-server-portal.md#scale-storage-up) om de IOPS te schalen voor betere prestaties. 

    * Als u de ingerichte IOPS-opslag al gebruikt, voorziet u [extra doorvoer capaciteit](howto-create-manage-server-portal.md#scale-storage-up). 

* **Schaal berekening**: de database belasting kan ook worden beperkt vanwege cpu of geheugen en dit kan ernstige gevolgen hebben voor de verwerking van trans acties. Houd er rekening mee dat Compute (prijs categorie) omhoog of omlaag kan worden geschaald tussen [Algemeen of](concepts-pricing-tiers.md) alleen lagen geoptimaliseerd voor geheugen. 

* **Testen op failover**: de failover voor uw Server exemplaar hand matig testen om inzicht te krijgen in hoe lang het proces duurt voor uw gebruik en om ervoor te zorgen dat de toepassing die toegang heeft tot uw Server-exemplaar, automatisch verbinding kan maken met het nieuwe Server exemplaar na een failover.

* **Primaire sleutel gebruiken**: Zorg ervoor dat uw tabellen een primaire of unieke sleutel hebben als u op de Azure database for MySQL werkt. Dit helpt bij het maken van back-ups, replica's enz. en verbetert de prestaties.

* **TTL-waarde configureren**: als uw client toepassing de Domain Name Service (DNS)-gegevens van uw server exemplaren in de cache opslaat, stelt u een TTL-waarde (time-to-Live) in van minder dan 30 seconden. Omdat het onderliggende IP-adres van een Server exemplaar na een failover kan worden gewijzigd, kan het opslaan van de DNS-gegevens gedurende een langere periode leiden tot verbindings fouten als uw toepassing probeert verbinding te maken met een IP-adres dat niet meer in gebruik is.

* Gebruik groepsgewijze verbindingen om te voor komen dat de [maximum verbindings limieten worden bereikt](concepts-server-parameters.md#max_connections)en gebruik logica voor opnieuw proberen om onregelmatige verbindings problemen te voor komen. 

* Als u replica gebruikt, gebruikt u ProxySQL om de belasting tussen de primaire server en de Lees bare secundaire replica server [af te sluiten](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) . Zie de installatie stappen hier. </br> 

* Zorg ervoor dat u bij het inrichten van de resource [de autogrow](howto-auto-grow-storage-portal.md) voor uw Azure database for MySQL hebt ingeschakeld. Hiermee worden geen extra kosten toegevoegd en wordt de data base beschermd tegen opslag knelpunten die u kunt uitvoeren in. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>InnoDB gebruiken met Azure Database for MySQL

*   Als u de `ibdata1` functie gebruikt, een bestand met de naam van een systeem tabel ruimte kan niet worden gekrimpt of verwijderd door de gegevens uit de tabel te verwijderen of door de tabel te verplaatsen naar een bestand per tabel `tablespaces` .

* Voor een Data Base die groter is dan 1 TB, maakt u de tabel in **innodb_file_per_table** `tablespace` . Voor één tabel die groter is dan 1 TB, moet u de [partitie](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) tabel.

*   Voor een server met een groot aantal `tablespace` wordt het opstarten van de engine erg traag als gevolg van de sequentiële scan van de tabel ruimte tijdens het opstarten of failover van MySQL. 

* Stel innodb_file_per_table = aan in voordat u een tabel maakt als het totale tabel nummer kleiner is dan 500.

* Als u meer dan 500 tabellen in een Data Base hebt, kunt u de tabel grootte voor elke afzonderlijke tabel controleren. Voor een grote tabel moet u nog steeds gebruikmaken van het bestand tabel-per-tabel om te voor komen dat het bestand van de systeem ruimte de maximale opslag limiet bereikt.

> [!NOTE]
> Overweeg het gebruik van de systeem ruimte voor tabellen met een grootte die kleiner is dan 5 GB 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* De tabel [partitioneren](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) bij het maken van tabellen als u een zeer grote tabel hebt, kan dit mogelijk groter worden dan 1 TB.

* Gebruik meerdere MySQL-servers en verspreid de tabellen over die servers. Vermijd het plaatsen van te veel tabellen op één server als u ongeveer 10000 tabellen of meer hebt. 

## <a name="next-steps"></a>Volgende stappen
- [Aanbevolen procedures voor de prestaties van Azure Database for MySQL](concept-performance-best-practices.md)
- [Aanbevolen procedure voor het bewaken van uw Azure Database for MySQL](concept-monitoring-best-practices.md)
