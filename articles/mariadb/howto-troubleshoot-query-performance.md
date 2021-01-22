---
title: Problemen met de query prestaties oplossen-Azure Database for MariaDB
description: Meer informatie over het gebruik van uitleg om de prestaties van query's in Azure Database for MariaDB op te lossen.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 8c996520a77a107017aecad30c221da9ec69137c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664712"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>UITLEGGEN hoe u de prestaties van query's in Azure Database for MariaDB kunt bepalen
**Uitleg** is een handig hulp programma voor het optimaliseren van query's. De instructie uitleg kan worden gebruikt om informatie op te halen over hoe SQL-instructies worden uitgevoerd. In de volgende uitvoer ziet u een voor beeld van de uitvoering van een uitleg-instructie.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Zoals in dit voor beeld kan worden gezien, is de waarde van *Key* null. Deze uitvoer betekent dat MariaDB geen indexen kan vinden die zijn geoptimaliseerd voor de query en een volledige tabel scan uitvoert. We gaan deze query optimaliseren door een index toe te voegen aan de **id-** kolom.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

In de nieuwe uitleg ziet u dat MariaDB nu gebruikmaakt van een index om het aantal rijen te beperken tot 1, die op zijn beurt de zoek tijd aanzienlijk verkort.
 
## <a name="covering-index"></a>Bedekte index
Een bedekte index bestaat uit alle kolommen van een query in de index om het ophalen van waarden uit gegevens tabellen te verminderen. Hier volgt een voor beeld in de volgende **Group by** -instructie.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Zoals u kunt zien in de uitvoer, worden er door MariaDB geen indexen gebruikt, omdat er geen geschikte indexen beschikbaar zijn. Er wordt ook *gebruikgemaakt van tijdelijke; Het gebruik van bestanden sorteren*, wat betekent dat MariaDB een tijdelijke tabel maakt om te voldoen aan de component **Group by** .
 
Het maken van een index op kolom **C2** heeft alleen geen verschil en MariaDB moet nog steeds een tijdelijke tabel maken:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

In dit geval kan een **gedekte index** op zowel **C1** als **C2** worden gemaakt, waarbij de waarde **C2** direct in de index wordt toegevoegd om verdere gegevens zoekactie te elimineren.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Zoals hierboven wordt weer gegeven, gebruikt MariaDB nu de gedekte index en vermijdt u het maken van een tijdelijke tabel. 

## <a name="combined-index"></a>Gecombineerde index
Een gecombineerde index bevat waarden uit meerdere kolommen en kan worden beschouwd als een matrix van rijen die zijn gesorteerd door het samen voegen van de waarden van de geïndexeerde kolommen. Deze methode kan nuttig zijn in een **Group by** -instructie.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MariaDB voert een *Bestands Sorteer* bewerking uit die tamelijk langzaam is, vooral wanneer het een groot aantal rijen moet sorteren. Als u deze query wilt optimaliseren, kunt u een gecombineerde index maken op beide kolommen die worden gesorteerd.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

In de uitleg ziet u nu dat MariaDB een gecombineerde index kan gebruiken om extra Sorteer bewerkingen te voor komen omdat de index al is gesorteerd.
 
## <a name="conclusion"></a>Conclusie
 
Door gebruik te maken van uitleg en verschillende typen indexen kunnen de prestaties aanzienlijk toenemen. Het gebruik van een index voor de tabel betekent niet noodzakelijkerwijs dat MariaDB dit voor uw query's zou kunnen gebruiken. Valideer uw hypo theses altijd met uitleg en Optimaliseer uw query's met behulp van indexen.

## <a name="next-steps"></a>Volgende stappen
- Ga naar [micro soft Q&een vraag pagina](/answers/topics/azure-database-mariadb.html) of [stack overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb)als u op zoek bent naar peer antwoorden op uw vragen of als u een nieuwe vraag/antwoord wilt plaatsen.