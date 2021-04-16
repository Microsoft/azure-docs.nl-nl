---
title: Configuratieopties – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Opties voor een Hyperscale (Citus) servergroep, waaronder reken-, opslag- en regio's voor knooppunt.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: 1dd0666c2946896ed324fb3986bb7946890b73de
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388700"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL : Hyperscale (Citus)-configuratieopties

## <a name="compute-and-storage"></a>Berekeningen en opslag
 
U kunt de reken- en opslaginstellingen afzonderlijk selecteren voor werkknooppunten en het coördinatorknooppunt in een Hyperscale (Citus) servergroep.  Rekenbronnen worden geleverd als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. De opslaggrootte voor het inrichten verwijst naar de capaciteit die beschikbaar is voor de coördinator- en werkknooppunten in Hyperscale (Citus) servergroep. De opslag omvat databasebestanden, tijdelijke bestanden, transactielogboeken en de Postgres-serverlogboeken.

### <a name="standard-tier"></a>Standaardlaag
 
| Resource              | Werk knooppunt           | Coördinator-knooppunt      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Geheugen per vCore, GiB | 8                     | 4                     |
| Opslaggrootte, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Opslagtype          | Algemeen gebruik (SSD) | Algemeen gebruik (SSD) |
| IOPS                  | Maximaal 3 IOPS/GiB      | Maximaal 3 IOPS/GiB      |

De totale hoeveelheid RAM in één Hyperscale (Citus) knooppunt is gebaseerd op het geselecteerde aantal vCores.

| vCores | Eén werk knooppunt, GiB RAM | Coördinator-knooppunt, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

De totale hoeveelheid opslagruimte die u inrichten, definieert ook de I/O-capaciteit die beschikbaar is voor elk werk- en coördinator-knooppunt.

| Opslaggrootte, TiB | Maximum IOPS |
|-------------------|--------------|
| 0,5               | 1536        |
| 1                 | 3072        |
| 2                 | 6,148        |

Voor het hele Hyperscale (Citus) werkt de geaggregeerde IOPS uit met de volgende waarden:

| Werkknooppunten | 0,5 TiB, totaal aantal IOPS | 1 TiB, totaal aantal IOPS | 2 TiB, totaal aantal IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3072               | 6144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

### <a name="basic-tier-preview"></a>Basic-laag (preview)

> [!IMPORTANT]
> De Hyperscale (Citus) Basic-laag is momenteel in preview.  Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U ziet een volledige lijst met andere nieuwe functies in [preview-functies voor Hyperscale (Citus).](hyperscale-preview-features.md)

De Hyperscale (Citus) [Basic-laag](concepts-hyperscale-tiers.md) is een servergroep met slechts één knooppunt.  Omdat er geen onderscheid is tussen coördinator- en werkknooppunten, is het minder ingewikkeld om reken- en opslagbronnen te kiezen.

| Resource              | Beschikbare opties     |
|-----------------------|-----------------------|
| Compute, vCores       | 2, 4, 8               |
| Geheugen per vCore, GiB | 4                     |
| Opslaggrootte, GiB     | 128, 256, 512         |
| Opslagtype          | Algemeen gebruik (SSD) |
| IOPS                  | Maximaal 3 IOPS/GiB      |

De totale hoeveelheid RAM in één Hyperscale (Citus) knooppunt is gebaseerd op het geselecteerde aantal vCores.

| vCores | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

De totale hoeveelheid opslag die u inrichten, definieert ook de I/O-capaciteit die beschikbaar is voor het knooppunt van de basislaag.

| Opslaggrootte, GiB | Maximum IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1536        |

## <a name="regions"></a>Regio's
Hyperscale (Citus)-servergroepen zijn beschikbaar in de volgende Azure-regio's:

* Americas:
    * Brazilië - zuid
    * Canada - midden
    * VS - centraal
    * VS - oost *
    * VS - oost 2
    * VS - noord-centraal
    * VS - west 2
* Azië en Stille Oceaan:
    * Australië - oost
    * Japan East
    * Korea - centraal
    * Azië - zuidoost
* Europa:
    * Frankrijk - centraal
    * Europa - noord
    * Verenigd Koninkrijk Zuid
    * Europa -west

( \* = ondersteunt [preview-functies](hyperscale-preview-features.md))

Sommige van deze regio's worden mogelijk niet in eerste instantie geactiveerd voor alle Azure-abonnementen. Als u een regio uit de bovenstaande lijst wilt gebruiken en deze niet in uw abonnement ziet, of als u een regio wilt gebruiken die niet in deze lijst staat, opent u een [ondersteuningsaanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Prijzen
Zie de pagina met serviceprijzen voor de meest recente [prijsinformatie.](https://azure.microsoft.com/pricing/details/postgresql/)
Als u de kosten voor de [](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) configuratie die u wilt zien, Azure Portal de maandelijkse kosten op het tabblad Configureren op basis van de opties die u selecteert.  Als u geen Azure-abonnement hebt, kunt u de Azure-prijscalculator gebruiken om een geschatte prijs op te halen. Selecteer op [de website van de Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) de optie **Items** toevoegen, vouw de categorie **Databases** uit en kies Azure Database for PostgreSQL **- Hyperscale (Citus)** opties aan te passen.
 
## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het maken van Hyperscale (Citus) servergroep in de portal](quickstart-create-hyperscale-portal.md).
