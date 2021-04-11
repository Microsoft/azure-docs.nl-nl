---
title: Configuratie opties – grootschalige (Citus)-Azure Database for PostgreSQL
description: Opties voor een grootschalige (Citus)-Server groep, waaronder compute, opslag en regio's van knoop punten.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: ae416c9acd03b3ee239a858aae550fb87293465a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012782"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Configuratie opties Azure Database for PostgreSQL – grootschalige (Citus)

## <a name="compute-and-storage"></a>Berekeningen en opslag
 
U kunt de berekenings-en opslag instellingen onafhankelijk selecteren voor worker-knoop punten en het coördinator knooppunt in een grootschalige (Citus)-Server groep.  Reken bronnen worden weer gegeven als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. De opslag grootte voor inrichting heeft betrekking op de capaciteit die beschikbaar is voor de coördinator en worker-knoop punten in uw grootschalige (Citus)-Server groep. De opslag omvat database bestanden, tijdelijke bestanden, transactie logboeken en de post gres-server Logboeken.

### <a name="standard-tier"></a>Standaardlaag
 
| Resource              | Worker-knoop punt           | Coördinator knooppunt      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Geheugen per vCore, GiB | 8                     | 4                     |
| Opslag grootte, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Opslagtype          | Algemeen gebruik (SSD) | Algemeen gebruik (SSD) |
| IOPS                  | Maxi maal 3 IOPS/GiB      | Maxi maal 3 IOPS/GiB      |

De totale hoeveelheid RAM-geheugen in één grootschalige-knoop punt (Citus) is gebaseerd op het geselecteerde aantal vCores.

| vCores | Eén worker-knoop punt, GiB-RAM | Coördinator knooppunt, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

De totale hoeveelheid opslag ruimte die u hebt ingericht, definieert ook de I/O-capaciteit die beschikbaar is voor elke werk nemer en coördinator knooppunt.

| Opslag grootte, TiB | Maximum aantal IOPS |
|-------------------|--------------|
| 0,5               | 1536        |
| 1                 | 3072        |
| 2                 | 6.148        |

Voor het hele grootschalige-cluster (Citus) worden de geaggregeerde IOPS uit de volgende waarden gebruikt:

| Worker-knoop punten | 0,5 TiB, totaal aantal IOPS | 1 TiB, totaal aantal IOPS | 2 TiB, totaal aantal IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3072               | 6144             | 12.296            |
| 3            | 4.608               | 9.216             | 18.444            |
| 4            | 6144               | 12.288            | 24.592            |
| 5            | 7.680               | 15.360            | 30.740            |
| 6            | 9.216               | 18.432            | 36.888            |
| 7            | 10.752              | 21.504            | 43.036            |
| 8            | 12.288              | 24.576            | 49.184            |
| 9            | 13.824              | 27.648            | 55.332            |
| 10           | 15.360              | 30.720            | 61.480            |
| 11           | 16.896              | 33.792            | 67.628            |
| 12           | 18.432              | 36.864            | 73.776            |
| 13           | 19.968              | 39.936            | 79.924            |
| 14           | 21.504              | 43.008            | 86.072            |
| 15           | 23.040              | 46.080            | 92.220            |
| 16           | 24.576              | 49.152            | 98.368            |
| 17           | 26.112              | 52.224            | 104.516           |
| 18           | 27.648              | 55.296            | 110.664           |
| 19           | 29.184              | 58.368            | 116.812           |
| 20           | 30.720              | 61.440            | 122.960           |

### <a name="basic-tier-preview"></a>Basic-laag (preview-versie)

> [!IMPORTANT]
> De grootschalige (Citus) Basic-laag is momenteel beschikbaar als preview-versie.  Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

De grootschalige (Citus) [Basic-laag](concepts-hyperscale-tiers.md) is een server groep met slechts één knoop punt.  Omdat er geen onderscheid tussen coördinator-en worker-knoop punten is, is het minder gecompliceerd om reken-en opslag resources te kiezen.

| Resource              | Beschik bare opties     |
|-----------------------|-----------------------|
| Compute, vCores       | 2, 4, 8               |
| Geheugen per vCore, GiB | 4                     |
| Opslag grootte, GiB     | 128, 256, 512         |
| Opslagtype          | Algemeen gebruik (SSD) |
| IOPS                  | Maxi maal 3 IOPS/GiB      |

De totale hoeveelheid RAM-geheugen in één grootschalige-knoop punt (Citus) is gebaseerd op het geselecteerde aantal vCores.

| vCores | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

De totale hoeveelheid opslag ruimte die u hebt ingericht, definieert ook de I/O-capaciteit die beschikbaar is voor het basis knooppunt van de laag.

| Opslag grootte, GiB | Maximum aantal IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1536        |

## <a name="regions"></a>Regio's
Grootschalige (Citus)-Server groepen zijn beschikbaar in de volgende Azure-regio's:

* Amerikaanse
    * Canada - midden
    * VS - centraal
    * VS-Oost *
    * VS - oost 2
    * VS - noord-centraal
    * VS - west 2
* Azië en Stille Oceaan:
    * Australië - oost
    * Japan East
    * Korea - centraal
    * Azië - zuidoost
* Europa
    * Europa - noord
    * Verenigd Koninkrijk Zuid
    * Europa -west

( \* = ondersteunt [Preview-functies](hyperscale-preview-features.md))

Sommige van deze regio's kunnen niet eerst worden geactiveerd op alle Azure-abonnementen. Als u een regio uit de bovenstaande lijst wilt gebruiken en deze niet in uw abonnement wilt zien, of als u een regio wilt gebruiken die niet in deze lijst voor komt, opent u een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Prijzen
Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/)voor services voor de meest actuele prijs informatie.
Voor een overzicht van de kosten voor de gewenste configuratie [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) , worden de maandelijkse kosten weer gegeven op het tabblad **configureren** op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de Azure-prijs calculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de optie **items toevoegen**, vouw de categorie **data bases** uit en kies **Azure database for PostgreSQL – grootschalige (Citus)** om de opties aan te passen.
 
## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [maken van een grootschalige (Citus)-Server groep in de portal](quickstart-create-hyperscale-portal.md).
