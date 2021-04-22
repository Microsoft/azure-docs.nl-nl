---
title: Servicelagen voor algemeen gebruik en bedrijfskritiek
titleSuffix: Azure SQL Database & SQL Managed Instance
description: In dit artikel worden de servicelagen Algemeen en Bedrijfskritiek beschreven in het aankoopmodel op basis van vCore dat wordt gebruikt door Azure SQL Database en Azure SQL Managed Instance.
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
ms.openlocfilehash: d4053628247cc01851aa19b66514398da0660a81
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107883558"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL Database en Azure SQL Managed Instance-servicelagen
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database en Azure SQL Managed Instance zijn gebaseerd op SQL Server-database-enginearchitectuur die is aangepast voor de cloudomgeving om een beschikbaarheid van 99,99 procent te garanderen, zelfs als er een infrastructuurfout is. Er worden twee servicelagen gebruikt door Azure SQL Database en Azure SQL Managed Instance, elk met een ander architectuurmodel. Deze servicelagen zijn:

- [Algemeen gebruik,](service-tier-general-purpose.md)dat is ontworpen voor budgetgerichte workloads.
- [Bedrijfskritiek,](service-tier-business-critical.md)dat is ontworpen voor workloads met lage latentie met hoge tolerantie voor fouten en snelle failovers.

Azure SQL Database heeft een extra servicelaag: 

- [Hyperscale,](service-tier-hyperscale.md)dat is ontworpen voor de meeste zakelijke workloads, biedt uiterst schaalbare opslag, uitschalen van leesmogelijkheden en snelle mogelijkheden voor databaseherstel.

In dit artikel worden de verschillen beschreven tussen de servicelagen, opslag en back-upoverwegingen voor de algemene en bedrijfskritieke servicelagen in het aankoopmodel op basis van vCore.

## <a name="service-tier-comparison"></a>Vergelijking van servicelagen

In de volgende tabel worden de belangrijkste verschillen tussen servicelagen voor de nieuwste generatie (Gen5) beschreven. Houd er rekening mee dat de kenmerken van de servicelaag mogelijk anders zijn in SQL Database en SQL Managed Instance.

|-| Resourcetype | Algemeen gebruik |  Hyperscale | Bedrijfskritiek |
|:---:|:---:|:---:|:---:|:---:|
| **Ideaal voor** | |  Biedt budgetgeoriënteerde, evenwichtige reken- en opslagopties. | De meeste zakelijke workloads. Automatisch schalen van de opslaggrootte tot 100 TB, vloeiend verticaal en horizontaal schalen van rekenkracht, snel databaseherstel. | OLTP-toepassingen met hoge transactiesnelheid en lage I/O-latentie. Biedt de hoogste tolerantie voor fouten en snelle failovers met behulp van meerdere synchrone bijgewerkte replica's.|
|  **Beschikbaar in resourcetype:** ||SQL Database/SQL Managed Instance | Eén Azure SQL Database | SQL Database/SQL Managed Instance |
| **Rekenkracht**| SQL Database | 1 tot 80 vCores | 1 tot 80 vCores | 1 tot 80 vCores |
| | SQL Managed Instance | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | SQL Managed Instance groepen | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | N.v.t. |
| **Opslagtype** | Alles | Premium externe opslag (per exemplaar) | Ontkoppelde opslag met lokale SSD-cache (per exemplaar) | Supersnelle lokale SSD-opslag (per exemplaar) |
| **Databasegrootte** | SQL Database | 5 GB – 4 TB | Maximaal 100 TB | 5 GB – 4 TB |
| | SQL Managed Instance  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **Opslaggrootte** | SQL Database | 5 GB – 4 TB | Maximaal 100 TB | 5 GB – 4 TB |
| | SQL Managed Instance  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **TempDB-grootte** | SQL Database | [32 GB per vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | SQL Managed Instance  | [24 GB per vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | N.v.t. | Maximaal 4 TB- [beperkt door de opslaggrootte](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Schrijfdoorvoer voor logboeken** | SQL Database | [1,875 MB/s per vCore (maximaal 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (maximaal 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | SQL Managed Instance | [3 MB/s per vCore (maximaal 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | N.v.t. | [4 MB/s per vcore (maximaal 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Beschikbaarheid**|Alles| 99,99% |  [99,95% met één secundaire replica, 99,99% met meer replica's](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% met zone-redundante individuele database](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Back-ups**|Alles|RA-GRS, 7-35 dagen (standaard 7 dagen). De maximale retentie voor de Basic-laag is 7 dagen. | RA-GRS, 7 dagen, constant herstel naar een bepaald tijdstip | RA-GRS, 7-35 dagen (standaard 7 dagen) |
|**In-memory OLTP** | | N.v.t. | N.v.t. | Beschikbaar |
|**Alleen-lezen replica's**| | 0 ingebouwd <br> 0 - 4 met [geo-replicatie](active-geo-replication-overview.md) | 0 - 4 ingebouwd | 1 ingebouwd, inbegrepen in prijs <br> 0 - 4 met [geo-replicatie](active-geo-replication-overview.md) |
|**Prijzen/facturering** | SQL Database | [Er worden kosten in rekening gebracht voor vCore, gereserveerde](https://azure.microsoft.com/pricing/details/sql-database/single/) opslag en back-upopslag. <br/>Er worden geen kosten in rekening gebracht voor IOPS. | [Er worden kosten in rekening](https://azure.microsoft.com/pricing/details/sql-database/single/) gebracht voor vCore voor elke replica en gebruikte opslag. <br/>Er worden nog geen kosten in rekening gebracht voor IOPS. | [Er worden kosten in rekening gebracht voor vCore, gereserveerde](https://azure.microsoft.com/pricing/details/sql-database/single/) opslag en back-upopslag. <br/>Er worden geen kosten in rekening gebracht voor IOPS. |
|| SQL Managed Instance | [Er worden kosten in rekening gebracht voor vCore, gereserveerde](https://azure.microsoft.com/pricing/details/sql-database/managed/) opslag en back-upopslag. <br/>Er worden geen kosten in rekening gebracht voor IOPS| N.v.t. | [Er worden kosten in rekening gebracht voor vCore, gereserveerde](https://azure.microsoft.com/pricing/details/sql-database/managed/) opslag en back-upopslag. <br/>Er worden geen kosten in rekening gebracht voor IOPS.| 
|**Kortingsmodellen**| | [Gereserveerde exemplaren](reserved-capacity-overview.md)<br/>[Azure Hybrid Benefit](../azure-hybrid-benefit.md) (niet beschikbaar voor dev/test-abonnementen)<br/>[Enterprise-](https://azure.microsoft.com/offers/ms-azr-0148p/) [en Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test-abonnementen| [Azure Hybrid Benefit](../azure-hybrid-benefit.md) (niet beschikbaar voor dev/test-abonnementen)<br/>[Enterprise-](https://azure.microsoft.com/offers/ms-azr-0148p/) [en Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test-abonnementen| [Gereserveerde exemplaren](reserved-capacity-overview.md)<br/>[Azure Hybrid Benefit](../azure-hybrid-benefit.md) (niet beschikbaar voor dev/test-abonnementen)<br/>[Enterprise-](https://azure.microsoft.com/offers/ms-azr-0148p/) [en Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test-abonnementen|

Zie de gedetailleerde verschillen tussen de servicelagen in [Azure SQL Database (vCore),](resource-limits-vcore-single-databases.md) [single Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md), [pooled Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md)en Azure SQL Managed Instance-pagina's [voor meer informatie.](../managed-instance/resource-limits.md)

> [!NOTE]
> Zie [Hyperscale-servicelaag](service-tier-hyperscale.md)voor informatie over de servicelaag hyperscale in het aankoopmodel op basis van vCore. Zie Aankoopmodellen en [resources](purchasing-models.md)voor een vergelijking van het aankoopmodel op basis van vCore met het aankoopmodel op basis van DTU.

## <a name="data-and-log-storage"></a>Gegevens- en logboekopslag

De volgende factoren zijn van invloed op de hoeveelheid opslag die wordt gebruikt voor gegevens- en logboekbestanden en zijn van toepassing op Algemeen en Bedrijfskritiek. Zie [Hyperscale-servicelaag](service-tier-hyperscale.md)voor meer informatie over gegevens- en logboekopslag in Hyperscale.

- De toegewezen opslag wordt gebruikt door gegevensbestanden (MDF) en logboekbestanden (LDF).
- Elke rekenkracht van één database ondersteunt een maximale databasegrootte, met een standaard maximale grootte van 32 GB.
- Wanneer u de vereiste grootte voor één database (de grootte van het MDF-bestand) configureert, wordt automatisch 30 procent meer opslagruimte toegevoegd om LDF-bestanden te ondersteunen.
- U kunt een individuele databasegrootte tussen 10 GB en het ondersteunde maximum selecteren.
  - Voor opslag in de servicelagen Standard of Algemeen gebruik vergroot of verkleint u de grootte in stappen van 10 GB.
  - Voor opslag in de premium- of bedrijfskritieke servicelagen vergroot of verkleint u de grootte in stappen van 250 GB.
- In de servicelaag algemeen gebruik maakt `tempdb` gebruik van een gekoppelde SSD. Deze opslagkosten zijn opgenomen in de vCore-prijs.
- In de bedrijfskritieke servicelaag deelt de gekoppelde SSD met de MDF- en LDF-bestanden en worden de opslagkosten `tempdb` `tempdb` opgenomen in de vCore-prijs.
- In de servicelaag DTU Premium deelt `tempdb` de gekoppelde SSD met MDF- en LDF-bestanden.
- De opslaggrootte voor een SQL Managed Instance moet worden opgegeven in veelvouden van 32 GB.


> [!IMPORTANT]
> Er worden kosten in rekening gebracht voor de totale opslag die is toegewezen voor MDF- en LDF-bestanden.

Als u de huidige totale grootte van uw MDF- en LDF-bestanden wilt bewaken, gebruikt [u sp_spaceused](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Als u de huidige grootte van de afzonderlijke MDF- en LDF-bestanden wilt controleren, gebruikt [u sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte vrij te maken. Zie Bestandsruimte beheren [in Azure SQL Database.](file-space-manage.md)

## <a name="backups-and-storage"></a>Back-ups en opslag

Opslag voor databaseback-ups wordt toegewezen ter ondersteuning van de mogelijkheden voor herstel naar een bepaald tijdstip (PITR) en langetermijnretentie [(LTR)](long-term-retention-overview.md) van SQL Database en SQL Managed Instance. Deze opslag wordt afzonderlijk toegewezen voor elke database en wordt gefactureerd als twee afzonderlijke kosten per database.

- **PITR:** afzonderlijke databaseback-ups worden automatisch gekopieerd naar geografisch redundante opslag met leestoegang [(RA-GRS).](../../storage/common/geo-redundant-design.md) De opslaggrootte neemt dynamisch toe wanneer er nieuwe back-ups worden gemaakt. De opslag wordt gebruikt door wekelijkse volledige back-ups, dagelijkse differentiële back-ups en back-ups van transactielogboekbestanden, die elke vijf minuten worden gekopieerd. Het opslagverbruik is afhankelijk van de wijzigingssnelheid van de database en de retentieperiode voor back-ups. U kunt een afzonderlijke bewaarperiode configureren voor elke database tussen 7 en 35 dagen. Er wordt zonder extra kosten een minimale opslagbedrag geboden dat gelijk is aan 100 procent (1x) van de databasegrootte. Voor de meeste databases is dit aantal voldoende om 7 dagen aan back-ups op te slaan.
- **LTR:** u hebt ook de mogelijkheid om langetermijnretentie van volledige back-ups voor maximaal tien jaar te configureren [voor SQL Managed Instance](long-term-retention-overview.md). Als u een LTR-beleid in stelt, worden deze back-ups automatisch opgeslagen in RA-GRS-opslag, maar u kunt bepalen hoe vaak de back-ups worden gekopieerd. Als u wilt voldoen aan verschillende nalevingsvereisten, kunt u verschillende bewaarperioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. De configuratie die u kiest, bepaalt hoeveel opslag wordt gebruikt voor LTR-back-ups. Als u de kosten van LTR-opslag wilt schatten, kunt u de LTR-prijscalculator gebruiken. Zie langetermijnretentie voor [SQL Database informatie.](long-term-retention-overview.md)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de specifieke reken- en opslaggrootten die beschikbaar zijn in de servicelagen Algemeen gebruik en Bedrijfskritiek: 

- [Resourcelimieten op basis van vCore voor Azure SQL Database.](resource-limits-vcore-single-databases.md)
- [Resourcelimieten op basis van vCore voor pooldatabases in Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [Resourcelimieten op basis van vCore voor Azure SQL Managed Instance.](../managed-instance/resource-limits.md)
