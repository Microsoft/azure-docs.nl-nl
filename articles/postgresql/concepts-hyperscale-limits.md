---
title: Limieten en beperkingen – grootschalige (Citus)-Azure Database for PostgreSQL
description: Huidige limieten voor grootschalige (Citus)-Server groepen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023945"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Limieten en beperkingen voor Azure Database for PostgreSQL – grootschalige (Citus)

In de volgende sectie worden de capaciteits-en functionele limieten in de grootschalige-service (Citus) beschreven.

## <a name="maximum-connections"></a>Maximum aantal verbindingen

Elke PostgreSQL-verbinding (zelfs niet-inactief) gebruikt ten minste 10 MB aan geheugen. Daarom is het belang rijk om gelijktijdige verbindingen te beperken. Dit zijn de limieten die we hebben gekozen om knoop punten in orde te blijven:

* Coördinator knooppunt
   * Maximum aantal verbindingen: 300
   * Maximum aantal gebruikers verbindingen: 297
* Worker-knoop punt
   * Maximum aantal verbindingen: 600
   * Maximum aantal gebruikers verbindingen: 597

> [!NOTE]
> In een server groep waarvoor [Preview-functies](hyperscale-preview-features.md) zijn ingeschakeld, zijn de verbindings limieten voor de coördinator iets anders:
>
> * Maximum aantal verbindingen van coördinator knooppunt
>    * 300 voor 0-3 vCores
>    * 500 voor 4-15 vCores
>    * 1000 voor 16-vCores

Pogingen om verbinding te maken buiten deze limieten, mislukken met een fout. Het systeem reserveert drie verbindingen voor bewakings knooppunten. Daarom zijn er drie minder verbindingen beschikbaar voor gebruikers query's dan het totale aantal verbindingen.

### <a name="connection-pooling"></a>Groepsgewijze verbinding

Het tot stand brengen van nieuwe verbindingen kost tijd. Dat werkt op de meeste toepassingen, waardoor veel korte verbindingen worden aangevraagd. U kunt het beste een verbindings groep gebruiken, zowel om niet-actieve trans acties te verminderen als bestaande verbindingen opnieuw te gebruiken. Ga voor meer informatie naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

U kunt uw eigen verbindings groep uitvoeren of PgBouncer gebruiken die worden beheerd door Azure.

#### <a name="managed-pgbouncer-preview"></a>Beheerde PgBouncer (preview-versie)

> [!IMPORTANT]
> De Managed PgBouncer-verbindings groep in grootschalige (Citus) is momenteel beschikbaar als preview-versie. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

Met verbindings Pools zoals PgBouncer kunnen meer clients tegelijk verbinding maken met het coördinator knooppunt. Toepassingen maken verbinding met de Pooler en de Pooler stuurt opdrachten door naar de doel database.

Wanneer clients verbinding maken via PgBouncer, wordt het aantal verbindingen dat actief kan worden uitgevoerd in de data base niet gewijzigd. In plaats daarvan PgBouncer wacht rijen overtollige verbindingen en worden ze uitgevoerd wanneer de data base gereed is.

Grootschalige (Citus) biedt nu een beheerd exemplaar van PgBouncer voor Server groepen (in preview-versie). Het ondersteunt Maxi maal 2.000 gelijktijdige client verbindingen.
Voer de volgende stappen uit om verbinding te maken via PgBouncer:

1. Ga naar de pagina **verbindings reeksen** voor uw server groep in de Azure Portal.
2. Schakel het selectie vakje **PgBouncer-verbindings reeksen** in. (De vermelde verbindings reeksen worden gewijzigd.)
3. Werk de client toepassingen bij om verbinding te maken met de nieuwe teken reeks.

## <a name="storage-scaling"></a>Opslag schalen

Opslag op coördinator-en worker-knoop punten kan worden geschaald (verhoogd), maar kan niet omlaag worden geschaald.

## <a name="storage-size"></a>Opslag grootte

Er worden Maxi maal 2 TiB opslag ruimte ondersteund op coördinator-en worker-knoop punten. Zie de beschik bare opslag opties en de berekening van IOPS [hierboven](concepts-hyperscale-configuration-options.md#compute-and-storage) voor de grootte van knoop punten en clusters.

## <a name="database-creation"></a>Data base maken

De Azure Portal biedt referenties om verbinding te maken met precies één data base per grootschalige-Server groep (Citus), de- `citus` Data Base. Het maken van een andere data base is momenteel niet toegestaan en de opdracht CREATE data base mislukt met een fout.

## <a name="columnar-storage"></a>Kolom opslag

Grootschalige (Citus) heeft deze beperkingen momenteel met [kolom tabellen](concepts-hyperscale-columnar.md):

* Compressie bevindt zich op de schijf, niet in het geheugen
* Alleen toevoegen (geen ondersteuning voor bijwerken/verwijderen)
* Geen ruimte vrijmaken (bijvoorbeeld: gerollte back-uptransacties verbruiken mogelijk nog steeds schijf ruimte)
* Geen index-ondersteuning, index scans of bitmap-index scans
* Geen tidscans
* Geen voorbeeld scans
* Geen ondersteuning voor pop-upwaarschuwingen (grote waarden worden inline ondersteund)
* Er is geen ondersteuning voor ON-CONFLICT instructies (behalve acties zonder een doel opgegeven).
* Geen ondersteuning voor tuple-vergren delingen (SELECT... SELECTEER VOOR DELEN... VOOR UPDATE)
* Geen ondersteuning voor serialiseerbaar isolatie niveau
* Ondersteuning voor PostgreSQL-Server versies 12 + alleen
* Geen ondersteuning voor refererende sleutels, unieke beperkingen of uitsluitings beperkingen
* Geen ondersteuning voor logische decodering
* Geen ondersteuning voor parallelle scans binnen knoop punten
* Geen ondersteuning voor AFTER... VOOR elke rij triggers
* Geen niet-geregistreerde kolom tabellen
* Geen tabellen met een tijdelijke kolom

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een grootschalige (Citus)-Server groep in de portal](quickstart-create-hyperscale-portal.md).
