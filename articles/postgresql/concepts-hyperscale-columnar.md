---
title: Voor beeld van tabel opslag in kolom vorm-grootschalige (Citus)-Azure Database for PostgreSQL
description: Gegevens comprimeren met behulp van kolom opslag (preview-versie)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023967"
---
# <a name="columnar-table-storage-preview"></a>Tabel opslag in kolom vorm (preview-versie)

> [!IMPORTANT]
> Kolom opslag in tabel vorm in grootschalige (Citus) is momenteel beschikbaar als preview-versie. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

Azure Database for PostgreSQL-grootschalige (Citus) biedt ondersteuning voor gegevens opslag in tabel vorm met alleen-lezen voor analyse-en gegevensopslag werkbelastingen. Wanneer kolommen (in plaats van rijen) opeenvolgend worden opgeslagen op schijf, worden gegevens meer compressible en kunnen query's sneller een subset van kolommen aanvragen.

Als u kolom opslag wilt gebruiken, geeft u op `USING columnar` bij het maken van een tabel:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Met grootschalige (Citus) worden rijen tijdens het invoegen omgezet in kolom opslag in ' Stripes '. Elke stripe bevat de gegevens van één trans actie, of 150000 rijen, afhankelijk van wat kleiner is.  (De Stripe-grootte en andere para meters van een tabel kolom kunnen worden gewijzigd met de functie [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) .)

Met de volgende instructie worden bijvoorbeeld alle vijf rijen in dezelfde stripe geplaatst, omdat alle waarden in één trans actie worden ingevoegd:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Het is raadzaam om waar mogelijk grote strepen te maken, omdat grootschalige (Citus) kolom gegevens afzonderlijk per stripe comprimeert. We kunnen feiten over onze kolom tabel, zoals compressie frequentie, aantal stroken en gemiddelde rijen per stripe, zien met behulp van `VACUUM VERBOSE` :

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

De uitvoer laat zien dat grootschalige (Citus) het zstd-compressie algoritme heeft gebruikt voor het verkrijgen van 1.31 x-gegevens compressie. De compressie frequentie vergelijkt a) de grootte van de ingevoegde gegevens die in het geheugen zijn klaargezet tegen b) de grootte van de gegevens die in de uiteindelijke Stripe zijn gecomprimeerd.

Als gevolg van hoe deze wordt gemeten, kan de compressie frequentie niet overeenkomen met de grootte van de rij-en kolom opslag voor een tabel. De enige manier om dit verschil te vinden is het maken van een rij-en kolom tabel met dezelfde gegevens en vergelijken:

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

Voor onze kleine tabel gebruikt de kolom opslag feitelijk meer ruimte, maar naarmate de gegevens groeien, wordt de compressie gewonnen.

## <a name="example"></a>Voorbeeld

Kolom opslag werkt goed met het partitioneren van tabellen. Zie de documentatie van de Citus-engine in de community voor een voor beeld en [archivering met behulp van de kolom opslag](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage).

## <a name="gotchas"></a>Gotchas

* Kolom opslag comprimeert per stripe. Stroken worden per trans actie gemaakt. Als u één rij per trans actie invoegt, worden de afzonderlijke rijen in hun eigen strepen geplaatst. De compressie en prestaties van één rij stroken zijn erger dan een tabelrij. U kunt altijd bulksgewijs invoegen in een tabel kolom.
* Als u een klein aantal columnarize hebt, bent u vastgelopen.
  De enige oplossing is het maken van een nieuwe kolom tabel en het kopiëren van gegevens van het origineel in één trans actie:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* Fundamenteel niet-compressible gegevens kunnen een probleem zijn, hoewel columns-opslag nog steeds nuttig is wanneer u specifieke kolommen selecteert. Het is niet nodig om de andere kolommen in het geheugen te laden.
* In een gepartitioneerde tabel met een combi natie van rij-en kolom partities moeten updates zorgvuldig worden gericht. Filter ze om alleen te raken met de rij partities.
   * Als de bewerking is gericht op een specifieke rijlabels (bijvoorbeeld `UPDATE p2 SET i = i + 1` ), wordt deze voltooid. als deze is gericht op een opgegeven kolom partitie (bijvoorbeeld `UPDATE p1 SET i = i + 1` ), mislukt deze.
   * Als de bewerking is gericht op de gepartitioneerde tabel en een WHERE-component bevat die alle kolom partities uitsluit (bijvoorbeeld `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'` ), slaagt deze.
   * Als de bewerking is gericht op de gepartitioneerde tabel, maar niet op de partitie sleutel kolommen wordt gefilterd, mislukt deze. Zelfs als er sprake is van WHERE-componenten die alleen in kolom partities overeenkomen met rijen, is het niet voldoende. de partitie sleutel moet ook worden gefilterd.

## <a name="limitations"></a>Beperkingen

Deze functie heeft nog steeds een aantal belang rijke beperkingen. Zie [limieten en beperkingen voor grootschalige (Citus)](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Volgende stappen

* Bekijk een voor beeld van kolom opslag in een Citus [tijds Erie-zelf studie](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (externe koppeling).
