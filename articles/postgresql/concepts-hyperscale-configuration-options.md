---
title: Configuratie opties – grootschalige (Citus)-Azure Database for PostgreSQL
description: Opties voor een grootschalige (Citus)-Server groep, waaronder compute, opslag en regio's van knoop punten.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165608"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Configuratie opties Azure Database for PostgreSQL – grootschalige (Citus)

## <a name="compute-and-storage"></a>Berekeningen en opslag
 
U kunt de berekenings-en opslag instellingen onafhankelijk selecteren voor worker-knoop punten en het coördinator knooppunt in een grootschalige (Citus)-Server groep.  Reken bronnen worden weer gegeven als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. De opslag grootte voor inrichting heeft betrekking op de capaciteit die beschikbaar is voor de coördinator en worker-knoop punten in uw grootschalige (Citus)-Server groep. De opslag omvat database bestanden, tijdelijke bestanden, transactie logboeken en de post gres-server Logboeken.
 
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

## <a name="regions"></a>Regio's
Grootschalige (Citus)-Server groepen zijn beschikbaar in de volgende Azure-regio's:

* Amerikaanse
    * Canada - midden
    * VS - centraal
    * VS - oost
    * VS - oost 2
    * VS - noord-centraal
    * VS - west 2
* Azië en Stille Oceaan:
    * Australië - oost
    * Japan - oost
    * Korea - centraal
    * Azië - zuidoost
* Europa
    * Europa - noord
    * Verenigd Koninkrijk Zuid
    * Europa -west

Sommige van deze regio's kunnen niet eerst worden geactiveerd op alle Azure-abonnementen. Als u een regio uit de bovenstaande lijst wilt gebruiken en deze niet in uw abonnement wilt zien, of als u een regio wilt gebruiken die niet in deze lijst voor komt, opent u een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-and-limitations"></a>Limieten en beperkingen

In de volgende sectie worden de capaciteits-en functionele limieten in de grootschalige-service (Citus) beschreven.

### <a name="maximum-connections"></a>Maximum aantal verbindingen

Elke PostgreSQL-verbinding (zelfs niet-inactief) gebruikt ten minste 10 MB aan geheugen. Daarom is het belang rijk om gelijktijdige verbindingen te beperken. Dit zijn de limieten die we hebben gekozen om knoop punten in orde te blijven:

* Coördinator knooppunt
   * Maximum aantal verbindingen: 300
   * Maximum aantal gebruikers verbindingen: 297
* Worker-knoop punt
   * Maximum aantal verbindingen: 600
   * Maximum aantal gebruikers verbindingen: 597

Pogingen om verbinding te maken buiten deze limieten, mislukken met een fout. Het systeem reserveert drie verbindingen voor bewakings knooppunten. Daarom zijn er drie minder verbindingen beschikbaar voor gebruikers query's dan het totale aantal verbindingen.

Het tot stand brengen van nieuwe verbindingen kost tijd. Dat werkt op de meeste toepassingen, waardoor veel korte verbindingen worden aangevraagd. U kunt het beste een verbindings groep gebruiken, zowel om niet-actieve trans acties te verminderen als bestaande verbindingen opnieuw te gebruiken. Ga voor meer informatie naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

### <a name="storage-scaling"></a>Opslag schalen

Opslag op coördinator-en worker-knoop punten kan worden geschaald (verhoogd), maar kan niet omlaag worden geschaald.

### <a name="storage-size"></a>Opslag grootte

Er worden Maxi maal 2 TiB opslag ruimte ondersteund op coördinator-en worker-knoop punten. Zie de beschik bare opslag opties en de berekening van IOPS [hierboven](#compute-and-storage) voor de grootte van knoop punten en clusters.

### <a name="database-creation"></a>Data base maken

De Azure Portal biedt referenties om verbinding te maken met precies één data base per grootschalige-Server groep (Citus), de- `citus` Data Base. Het maken van een andere data base is momenteel niet toegestaan en de opdracht CREATE data base mislukt met een fout.

## <a name="pricing"></a>Prijzen
Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/)voor services voor de meest actuele prijs informatie.
Voor een overzicht van de kosten voor de gewenste configuratie [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) , worden de maandelijkse kosten weer gegeven op het tabblad **configureren** op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de Azure-prijs calculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de optie **items toevoegen**, vouw de categorie **data bases** uit en kies **Azure database for PostgreSQL – grootschalige (Citus)** om de opties aan te passen.
 
## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [maken van een grootschalige (Citus)-Server groep in de portal](quickstart-create-hyperscale-portal.md).
