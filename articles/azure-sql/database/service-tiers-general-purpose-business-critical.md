---
title: Algemene service lagen voor algemeen gebruik en bedrijfs kritieke services
titleSuffix: Azure SQL Database & SQL Managed Instance
description: In het artikel worden de laag algemeen en bedrijfskritische service-lagen beschreven in het op vCore gebaseerde aankoop model dat wordt gebruikt door Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 12/14/2020
ms.openlocfilehash: 9ee7440b10bc348d3ba87a4779208791a7b0e9ac
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97512025"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Service lagen van Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database en Azure SQL Managed instance zijn gebaseerd op SQL Server data base engine-architectuur die is aangepast voor de cloud omgeving om 99,99 procent Beschik baarheid te garanderen, zelfs als er sprake is van een infrastructuur fout. Er worden twee service lagen gebruikt door Azure SQL Database en Azure SQL Managed instance, elk met een ander architectuur model. Deze service lagen zijn:

- [Algemeen gebruik](service-tier-general-purpose.md), dat is ontworpen voor budget gerichte workloads.
- [Bedrijfs kritiek](service-tier-business-critical.md), die is ontworpen voor workloads met lage latentie met hoge tolerantie voor fouten en snelle failovers.

Azure SQL Database heeft een extra servicelaag: 

- [Grootschalige](service-tier-hyperscale.md), dat is ontworpen voor de meeste bedrijfs werkbelastingen, met uiterst schaal bare opslag, lees mogelijkheden voor uitschalen en snelle database herstel.

In dit artikel worden de verschillen beschreven tussen de service lagen, opslag en back-upaandachtspunten voor de laag algemeen en bedrijfskritische service lagen in het op vCore gebaseerde aankoop model.

## <a name="service-tier-comparison"></a>Service tier-vergelijking

In de volgende tabel worden de belangrijkste verschillen tussen service lagen voor de nieuwste generatie (GEN5) beschreven. Houd er rekening mee dat de kenmerken van de servicelaag in SQL Database en SQL Managed instance verschillend kunnen zijn.

|-| Resourcetype | Algemeen gebruik |  Hyperscale | Bedrijfskritiek |
|:---:|:---:|:---:|:---:|:---:|
| **Ideaal voor** | |  Biedt berekenings-en opslag opties voor budget gericht evenwicht. | De meeste zakelijke workloads. Opslag grootte automatisch schalen tot 100 TB, vloeiend verticaal en horizon taal berekenen, snel terugzetten van de data base. | OLTP-toepassingen met hoge transactie snelheden en lage IO-latentie. Biedt de hoogste flexibiliteit voor fouten en snelle failovers met behulp van meerdere synchroon bijgewerkte replica's.|
|  **Beschikbaar in resource type:** ||Door SQL Database/SQL beheerd exemplaar | Enkele Azure SQL Database | Door SQL Database/SQL beheerd exemplaar |
| **Reken grootte**| SQL Database | 1 tot 80 vCores | 1 tot 80 vCores | 1 tot 80 vCores |
| | SQL Managed Instance | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Door SQL beheerde exemplaar groepen | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | N.v.t. |
| **Opslag type** | Alles | Premium externe opslag (per instantie) | Niet-gekoppelde opslag met lokale SSD-cache (per instantie) | Super snelle lokale SSD-opslag (per instantie) |
| **Databasegrootte** | SQL Database | 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| | SQL Managed Instance  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **Opslag grootte** | SQL Database | 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| | SQL Managed Instance  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **TempDB-grootte** | SQL Database | [32 GB per vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | SQL Managed Instance  | [24 GB per vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | N.v.t. | Tot 4 TB, [beperkt door de opslag grootte](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Schrijf doorvoer vastleggen in logboek** | SQL Database | [1,875 MB/s per vCore (Maxi maal 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (max. 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | SQL Managed Instance | [3 MB/s per vCore (Maxi maal 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | N.v.t. | [4 MB/s per VCore (max. 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Beschikbaarheid**|Alles| 99,99% |  [99,95% met één secundaire replica, 99,99% met meer replica's](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% met zone redundante single data base](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Back-ups**|Alles|RA-GRS, 7-35 dagen (standaard zeven dagen). De maximale Bewaar periode voor de Basic-laag is 7 dagen. | RA-GRS, 7 dagen, constant tijdstip herstel (PITR) | RA-GRS, 7-35 dagen (standaard 7 dagen) |
|**OLTP in het geheugen** | | N.v.t. | N.v.t. | Beschikbaar |
|**Alleen-lezen replica's**| | 0 ingebouwde <br> 0-4 met [geo-replicatie](active-geo-replication-overview.md) | 0-4-ingebouwde | 1 ingebouwde, inclusief prijs <br> 0-4 met [geo-replicatie](active-geo-replication-overview.md) |
|**Prijzen/facturering** | SQL Database | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>Voor IOPS worden geen kosten in rekening gebracht. | [vCore voor elke replica en gebruikte opslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>IOPS nog niet gefactureerd. | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>Voor IOPS worden geen kosten in rekening gebracht. |
|| SQL Managed Instance | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/managed/) worden in rekening gebracht. <br/>Er worden geen IOPS in rekening gebracht| N.v.t. | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/managed/) worden in rekening gebracht. <br/>Voor IOPS worden geen kosten in rekening gebracht.| 
|**Kortings modellen**| | [Gereserveerde exemplaren](reserved-capacity-overview.md)<br/>[Azure Hybrid Benefit](../azure-hybrid-benefit.md) (niet beschikbaar in dev/test-abonnementen)<br/>Ontwikkel-en test abonnementen voor [ondernemingen](https://azure.microsoft.com/offers/ms-azr-0148p/) en [betalen naar gebruik](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Azure Hybrid Benefit](../azure-hybrid-benefit.md) (niet beschikbaar in dev/test-abonnementen)<br/>Ontwikkel-en test abonnementen voor [ondernemingen](https://azure.microsoft.com/offers/ms-azr-0148p/) en [betalen naar gebruik](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Gereserveerde exemplaren](reserved-capacity-overview.md)<br/>[Azure Hybrid Benefit](../azure-hybrid-benefit.md) (niet beschikbaar in dev/test-abonnementen)<br/>Ontwikkel-en test abonnementen voor [ondernemingen](https://azure.microsoft.com/offers/ms-azr-0148p/) en [betalen naar gebruik](https://azure.microsoft.com/offers/ms-azr-0023p/)|

Zie de gedetailleerde verschillen tussen de service lagen in [Azure SQL database (vCore)](resource-limits-vcore-single-databases.md), [Single Azure SQL database (DTU)](resource-limits-dtu-single-databases.md), [gepoolde Azure SQL database (DTU)](resource-limits-dtu-single-databases.md)en [Azure SQL Managed instance](../managed-instance/resource-limits.md) -pagina's voor meer informatie.

> [!NOTE]
> Zie [grootschalige service tier](service-tier-hyperscale.md)(grootschalige) voor meer informatie over de servicelaag in het op vCore gebaseerde aankoop model. Zie [Inkoop modellen en resources](purchasing-models.md)voor een vergelijking van het op vCore gebaseerde aankoop model met het DTU-gebaseerde aankoop model.

## <a name="data-and-log-storage"></a>Gegevens-en logboek opslag

De volgende factoren zijn van invloed op de hoeveelheid opslag die wordt gebruikt voor gegevens-en logboek bestanden en geldt voor Algemeen en Bedrijfskritiek. Zie [grootschalige-servicelaag](service-tier-hyperscale.md)voor meer informatie over gegevens en logboek opslag in grootschalige.

- De toegewezen opslag wordt gebruikt door gegevens bestanden (MDF) en logboek bestanden (LDF).
- Elke reken grootte van één data base ondersteunt een maximale database grootte, met een standaard maximale grootte van 32 GB.
- Wanneer u de vereiste grootte voor één data base (de grootte van het MDF-bestand) configureert, wordt er automatisch 30 procent extra opslag toegevoegd ter ondersteuning van LDF-bestanden.
- U kunt de grootte van één data base tussen 10 GB en het ondersteunde maximum selecteren.
  - Verg root of verklein de grootte in stappen van 10 GB voor opslag in de service lagen standaard of algemeen.
  - Verg root of verklein de grootte in stappen van 250 GB voor opslag in de essentiële of bedrijfs kritieke service lagen.
- In de servicelaag voor algemeen gebruik wordt `tempdb` een bijgevoegde SSD gebruikt. deze opslag kosten worden opgenomen in de vCore prijs.
- In de laag bedrijfskritische service `tempdb` deelt u de gekoppelde SSD met de MDF-en LDF-bestanden en `tempdb` worden de opslag kosten opgenomen in de vCore-prijs.
- De opslag grootte voor een SQL Managed instance moet worden opgegeven in veelvouden van 32 GB.


> [!IMPORTANT]
> Er worden kosten in rekening gebracht voor de totale opslag ruimte die is toegewezen voor MDF-en LDF-bestanden.

Gebruik [sp_spaceused](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)om de huidige totale grootte van uw MDF-en LDF-bestanden te bewaken. Gebruik [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)om de huidige grootte van de afzonderlijke MDF-en LDF-bestanden te bewaken.

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

## <a name="backups-and-storage"></a>Back-ups en opslag

Opslag voor back-ups van data bases wordt toegewezen ter ondersteuning van de mogelijkheden voor PITR (Point-in-time Restore) en [ltr (Long-term retention)](long-term-retention-overview.md) van SQL database en SQL Managed instance. Deze opslag wordt afzonderlijk voor elke Data Base toegewezen en gefactureerd als twee afzonderlijke kosten per data base.

- **PITR**: afzonderlijke database back-ups worden automatisch naar [geografisch redundante opslag met lees toegang (RA-GRS)](../../storage/common/geo-redundant-design.md) gekopieerd. De opslag grootte neemt dynamisch toe wanneer er nieuwe back-ups worden gemaakt. De opslag wordt gebruikt door wekelijkse volledige back-ups, dagelijkse differentiële back-ups en back-ups van transactie logboeken, die elke vijf minuten worden gekopieerd. Het opslag verbruik is afhankelijk van de frequentie waarmee de data base wordt gewijzigd en de retentie periode voor back-ups. U kunt tussen 7 en 35 dagen een afzonderlijke Bewaar periode configureren voor elke Data Base. Er wordt geen extra kosten in rekening gebracht voor een minimale opslag hoeveelheid die gelijk is aan 100 procent (1x) van de grootte van de data base. Voor de meeste data bases is deze hoeveelheid voldoende om zeven dagen back-ups op te slaan.
- **LTR**: u hebt ook de mogelijkheid om lange termijn retentie van volledige back-ups te configureren voor Maxi maal tien jaar (deze functie is in [beperkte open bare preview-versie van SQL Managed instance](long-term-retention-overview.md#sql-managed-instance-support). Als u een LTR-beleid instelt, worden deze back-ups automatisch opgeslagen in RA-GRS-opslag, maar u kunt bepalen hoe vaak de back-ups worden gekopieerd. Als u wilt voldoen aan verschillende nalevings vereisten, kunt u verschillende Bewaar perioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. De configuratie die u kiest, bepaalt hoeveel opslag ruimte wordt gebruikt voor V.L.N.R.-back-ups. U kunt de LTR-prijs calculator gebruiken om de kosten van V.L.N.R.-opslag te schatten. Zie [SQL database lange termijn retentie](long-term-retention-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de specifieke berekenings-en opslag grootten die beschikbaar zijn in de laag algemeen en bedrijfskritische service lagen: 

- [vCore resource limieten voor Azure SQL database](resource-limits-vcore-single-databases.md).
- [vCore resource limieten voor gegroepeerde Data bases in Azure SQL database](resource-limits-vcore-elastic-pools.md).
- [vCore op basis van resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md).
