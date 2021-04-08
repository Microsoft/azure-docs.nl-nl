---
title: Gegevens opname in de Azure HPC-cache-hand matig kopiëren
description: CP-opdrachten gebruiken om gegevens te verplaatsen naar een Blob-opslag doel in azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: f96a0fa264124f9d050e667b003d98579da63b77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87092334"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Gegevens opname van de Azure HPC-cache-hand matige Kopieer methode

In dit artikel vindt u gedetailleerde instructies voor het hand matig kopiëren van gegevens naar een BLOB storage-container voor gebruik met de Azure HPC-cache. Er wordt gebruikgemaakt van parallelle bewerkingen met meerdere threads om de Kopieer snelheid te optimaliseren.

Lees [gegevens verplaatsen naar Azure Blob Storage](hpc-cache-ingest.md)voor meer informatie over het verplaatsen van gegevens naar Blob Storage voor uw Azure HPC-cache.

## <a name="simple-copy-example"></a>Voor beeld van eenvoudige kopie

U kunt hand matig een kopie met meerdere threads maken op een client door meer dan één Kopieer opdracht tegelijk op de achtergrond uit te voeren op basis van vooraf gedefinieerde sets van bestanden of paden.

De Linux/UNIX- ``cp`` opdracht bevat het argument voor het behouden van de ``-p`` meta gegevens van eigendom en mtime. Het toevoegen van dit argument aan de onderstaande opdrachten is optioneel. (Het toevoegen van het argument verhoogt het aantal bestandssysteem aanroepen dat vanaf de client naar het doel bestandssysteem wordt verzonden voor het wijzigen van meta gegevens.)

In dit eenvoudige voor beeld worden twee bestanden parallel gekopieerd:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Na het geven van deze opdracht geeft de `jobs` opdracht aan dat er twee threads worden uitgevoerd.

## <a name="copy-data-with-predictable-file-names"></a>Gegevens kopiëren met voorspel bare bestands namen

Als uw bestands namen voorspelbaar zijn, kunt u expressies gebruiken om threads voor parallelle kopieën te maken.

Als uw directory bijvoorbeeld 1000 bestanden bevat die opeenvolgend zijn genummerd `0001` `1000` , kunt u de volgende expressies gebruiken om tien parallelle threads te maken die elk een kopie hebben van 100 bestanden:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Gegevens kopiëren met ongestructureerde bestands namen

Als de naamgevings structuur van het bestand niet voorspelbaar is, kunt u bestanden groeperen op mapnamen.

In dit voor beeld worden volledige mappen verzameld voor verzen ding naar ``cp`` opdrachten die als achtergrond taken worden uitgevoerd:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Nadat de bestanden zijn verzameld, kunt u opdrachten voor parallel kopiëren uitvoeren om de submappen en alle bijbehorende inhoud recursief te kopiëren:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Wanneer koppel punten toevoegen?

Nadat u voldoende parallelle threads hebt uitgevoerd op een enkel doel bestandssysteem koppel punt, is er een punt waar het toevoegen van meer threads geen verdere door Voer biedt. (De door Voer wordt gemeten in bestanden/seconde of bytes per seconde, afhankelijk van het type gegevens.) Of erger dat er een doorvoer vermindering kan optreden bij over-threading.

Als dit gebeurt, kunt u aan client zijde koppel punten toevoegen aan andere Azure HPC-cache koppel adressen met behulp van hetzelfde externe bestandssysteempad:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Door aan client zijde koppel punten toe te voegen, kunt u extra Kopieer opdrachten opsplitsen naar de extra `/mnt/destination[1-3]` koppel punten, waardoor verdere parallellisme wordt bereikt.

Als uw bestanden bijvoorbeeld erg groot zijn, kunt u de Kopieer opdrachten definiëren voor het gebruik van verschillende doel paden en meer opdrachten parallel verzenden van de client die de kopie uitvoert.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

In het bovenstaande voor beeld worden alle drie de doel koppelings punten gericht op het kopiëren van client bestanden.

## <a name="when-to-add-clients"></a>Wanneer clients toevoegen

Ten slotte, wanneer u de mogelijkheden van de client hebt bereikt, worden er door het toevoegen van meer Kopieer threads of extra koppel punten geen extra bestanden per seconde of aantal bytes per seconde verhoogd. In dat geval kunt u een andere client met dezelfde set koppel punten implementeren waarop de eigen sets van het kopiëren van bestanden worden uitgevoerd.

Voorbeeld:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Bestands manifesten maken

Wanneer u de bovenstaande benaderingen (meerdere exemplaren per doel, meerdere-threads per client, meerdere clients per netwerk bron bestand systeem) hebt begrepen, moet u deze aanbeveling overwegen: bestands manifesten samen stellen en deze vervolgens gebruiken voor het kopiëren van opdrachten op meerdere clients.

In dit scenario wordt de UNIX- ``find`` opdracht gebruikt voor het maken van manifesten van bestanden of mappen:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Dit resultaat omleiden naar een bestand: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Vervolgens kunt u het manifest door lopen met behulp van BASH-opdrachten om bestanden te tellen en de grootte van de submappen te bepalen:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Ten slotte moet u de daad werkelijke Kopieer opdrachten voor het kopiëren naar de clients.

Als u vier clients hebt, gebruikt u deze opdracht:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Als u vijf clients hebt, gebruikt u ongeveer als volgt:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

En voor zes.... Extrapolatie naar behoefte.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Er worden *n* resulterende bestanden weer geven, één voor elk van uw *N* -clients met de namen van het pad naar de vier directory's die zijn verkregen als onderdeel van de uitvoer van de `find` opdracht.

Gebruik elk bestand om de Kopieer opdracht te bouwen:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

In het bovenstaande vindt u *N* bestanden, elk met een Kopieer opdracht per regel, die als een bash-script kan worden uitgevoerd op de client.

Het doel is om meerdere threads van deze scripts gelijktijdig per client uit te voeren op meerdere clients.
