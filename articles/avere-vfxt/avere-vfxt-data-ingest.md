---
title: Gegevens verplaatsen naar avere vFXT voor Azure
description: Gegevens toevoegen aan een nieuw opslag volume voor gebruik met de avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 76bbe60397ebb01aed5694d933b3067f778a4c21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505593"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Gegevens verplaatsen naar het vFXT-cluster-parallelle gegevens opname

Nadat u een nieuw vFXT-cluster hebt gemaakt, is het mogelijk dat uw eerste taak gegevens naar een nieuw opslag volume in azure verplaatst. Als uw gebruikelijke methode voor het verplaatsen van gegevens echter een eenvoudige Kopieer opdracht van de ene client geeft, zult u waarschijnlijk een trage Kopieer snelheid zien. Kopiëren met één thread is geen goede optie voor het kopiëren van gegevens naar de back-avere van de vFXT-cluster.

Omdat de avere vFXT voor Azure cluster een schaal bare cache voor meerdere clients is, is de snelste en efficiëntste manier om gegevens te kopiëren, met meerdere clients. Deze techniek parallelizes opname van de bestanden en objecten.

![Diagram van het proces van het verplaatsen van meerdere clients en gegevens verplaatsing met meerdere threads: linksboven, een pictogram voor on-premises hardwarematige opslag bevat meerdere pijlen. De pijlen verwijzen naar vier client machines. Van elke client computer worden drie pijlen naar de avere vFXT. Vanuit de avere vFXT wijst u meerdere pijlen naar Blob Storage.](media/avere-vfxt-parallel-ingest.png)

De ``cp`` ``copy`` opdrachten of die vaak worden gebruikt voor het overdragen van gegevens van het ene opslag systeem naar het andere, zijn processen met één thread waarmee slechts één bestand tegelijk wordt gekopieerd. Dit betekent dat de bestands server slechts één bestand tegelijk opneemt. Dit is een afval van de resources van het cluster.

In dit artikel worden strategieën beschreven voor het maken van een multi-client, multi-threaded bestand voor het kopiëren van gegevens naar het avere vFXT-cluster. Hierin worden de concepten van bestands overdracht en beslissings punten uitgelegd die kunnen worden gebruikt voor het efficiënt kopiëren van gegevens met meerdere clients en eenvoudige Kopieer opdrachten.

Er wordt ook een aantal hulpprogram ma's beschreven die kunnen helpen. Het ``msrsync`` hulp programma kan worden gebruikt om het proces voor het delen van een gegevensset in buckets gedeeltelijk te automatiseren en opdrachten te gebruiken ``rsync`` . Het ``parallelcp`` script is nog een hulp programma waarmee de bron directory wordt gelezen en automatisch Kopieer opdrachten worden gekopieerd. Het ``rsync`` hulp programma kan ook in twee fasen worden gebruikt om een snellere kopie te bieden die nog steeds consistentie van de gegevens biedt.

Klik op de koppeling om naar een sectie te gaan:

* [Voor beeld van hand matig kopiëren](#manual-copy-example) : een uitgebreide uitleg met behulp van Copy-opdrachten
* [Voor beeld van twee fasen rsync](#use-a-two-phase-rsync-process)
* [Gedeeltelijk geautomatiseerd (msrsync)-voor beeld](#use-the-msrsync-utility)
* [Voor beeld van parallel kopiëren](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>VM-sjabloon voor gegevens opname

Een resource manager-sjabloon is beschikbaar op GitHub om automatisch een virtuele machine te maken met de hulpprogram ma's voor parallelle gegevens opname die in dit artikel worden vermeld.

![diagram waarin meerdere pijlen worden weer gegeven van Blob Storage, hardware-opslag en Azure-bestands bronnen. De pijlen verwijzen naar een ' data consumer VM ' en van daaruit, meerdere pijlen verwijzen naar het avere vFXT](media/avere-vfxt-ingestor-vm.png)

De VM van de gegevens opname maakt deel uit van een zelf studie waarbij de zojuist gemaakte VM het avere vFXT-cluster koppelt en het Boots trap script van het cluster downloadt. Lees [Boots trap een data consumer-VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) voor meer informatie.

## <a name="strategic-planning"></a>Strategische planning

Bij het ontwerpen van een strategie voor het parallel kopiëren van gegevens, moet u inzicht hebben in de bestands grootte, het aantal bestanden en de diepte van de map.

* Wanneer bestanden klein zijn, zijn de belang rijke gegevens bestanden per seconde.
* Wanneer bestanden groot zijn (10MiBi of hoger), is de belang rijke hoeveelheid bytes per seconde.

Elk kopieer proces heeft een doorvoer snelheid en een snelheid waarmee bestanden worden overgedragen, wat kan worden gemeten door de tijds duur van de Kopieer opdracht te bepalen en de bestands grootte en het aantal bestanden te bepalen. Uitleg over het meten van de tarieven valt buiten het bereik van dit document, maar het is belang rijk om te begrijpen of u met kleine of grote bestanden gaat omgaan.

## <a name="manual-copy-example"></a>Voor beeld van hand matig kopiëren

U kunt hand matig een kopie met meerdere threads maken op een client door meer dan één Kopieer opdracht tegelijk op de achtergrond uit te voeren op basis van vooraf gedefinieerde sets van bestanden of paden.

De Linux/UNIX- ``cp`` opdracht bevat het argument voor het behouden van de ``-p`` meta gegevens van eigendom en mtime. Het toevoegen van dit argument aan de onderstaande opdrachten is optioneel. (Het toevoegen van het argument verhoogt het aantal systeem aanroepen van de client naar het doel bestandssysteem voor het wijzigen van meta gegevens.)

In dit eenvoudige voor beeld worden twee bestanden parallel gekopieerd:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Na het geven van deze opdracht geeft de `jobs` opdracht aan dat er twee threads worden uitgevoerd.

### <a name="predictable-filename-structure"></a>Voorspel bare bestands naam structuur

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

### <a name="unknown-filename-structure"></a>Onbekende bestands naam structuur

Als uw bestands naamgevings structuur niet voorspelbaar is, kunt u bestanden groeperen op mapnamen.

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

### <a name="when-to-add-mount-points"></a>Wanneer koppel punten toevoegen?

Nadat u voldoende parallelle threads hebt uitgevoerd op een enkel koppelings punt van een bestemmings bestandssysteem, is er een punt waar het toevoegen van meer threads geen verdere door Voer biedt. (De door Voer wordt gemeten in bestanden/seconde of bytes per seconde, afhankelijk van het type gegevens.) Of erger dat er een doorvoer vermindering kan optreden bij over-threading.

Als dit gebeurt, kunt u aan client zijde koppel punten toevoegen aan andere IP-adressen van het vFXT-cluster met behulp van hetzelfde extern pad voor het koppelen van het bestands systeem:

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

### <a name="when-to-add-clients"></a>Wanneer clients toevoegen

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

### <a name="create-file-manifests"></a>Bestands manifesten maken

Wanneer u de bovenstaande benaderingen (meerdere exemplaren per doel, meerdere-threads per client, meerdere clients per netwerk bron bestandssysteem) wilt weten, kunt u deze aanbeveling overwegen: bestands manifesten samen stellen en deze vervolgens gebruiken voor het kopiëren van opdrachten op meerdere clients.

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
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
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

## <a name="use-a-two-phase-rsync-process"></a>Een rsync-proces in twee fasen gebruiken

Het standaard ``rsync`` hulpprogramma werkt niet goed voor het vullen van Cloud opslag via het avere vFXT voor Azure-systeem omdat er een groot aantal bewerkingen voor het maken en wijzigen van bestanden wordt gegenereerd om de integriteit van gegevens te garanderen. U kunt de optie echter gebruiken ``--inplace`` ``rsync`` om de meer voorzichtigere Kopieer procedure over te slaan als u deze uitvoert met een tweede run waarmee de bestands integriteit wordt gecontroleerd.

Met een standaard ``rsync`` Kopieer bewerking wordt een tijdelijk bestand gemaakt en gevuld met gegevens. Als de gegevens overdracht is voltooid, wordt de naam van het tijdelijke bestand gewijzigd in de oorspronkelijke bestands naam. Deze methode garandeert consistentie, zelfs als de bestanden tijdens het kopiëren worden geopend. Deze methode genereert echter meer schrijf bewerkingen, waardoor de bestands verplaatsing via de cache verloopt.

Met de optie ``--inplace`` schrijft u het nieuwe bestand rechtstreeks op de uiteindelijke locatie. Bestanden zijn niet gegarandeerd consistent tijdens de overdracht, maar dat is niet belang rijk als u een opslag systeem gebeuren voor later gebruik.

De tweede ``rsync`` bewerking fungeert als een consistentie controle van de eerste bewerking. Omdat de bestanden al zijn gekopieerd, is de tweede fase een snelle scan om ervoor te zorgen dat de bestanden op de doel computer overeenkomen met de bestanden op de bron. Als bestanden niet overeenkomen, worden ze opnieuw gekopieerd.

U kunt beide fasen samen in één opdracht uitgeven:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Deze methode is een eenvoudige en time-outwaarde methode voor gegevens sets tot het aantal bestanden dat de interne directory manager kan verwerken. (Dit zijn meestal 200.000.000 bestanden voor een cluster met drie knoop punten, 500.000.000 bestanden voor een cluster met zes knoop punten, enzovoort.)

## <a name="use-the-msrsync-utility"></a>Het hulp programma msrsync gebruiken

Het ``msrsync`` hulp programma kan ook worden gebruikt om gegevens te verplaatsen naar een back-end-kern bestand voor het avere-cluster. Dit hulp programma is ontworpen om het bandbreedte gebruik te optimaliseren door meerdere parallelle processen uit te voeren ``rsync`` . Het is beschikbaar via GitHub op <https://github.com/jbd/msrsync> .

``msrsync`` Hiermee wordt de bron directory opgesplitst in afzonderlijke buckets en worden vervolgens afzonderlijke ``rsync`` processen uitgevoerd op elke Bucket.

Voor bereiding van het testen met behulp van een virtuele machine met vier kernen wordt de beste efficiëntie weer gegeven wanneer u 64 processen Gebruik de ``msrsync`` optie ``-p`` om het aantal processen in te stellen op 64.

U kunt ook het ``--inplace`` argument gebruiken met- ``msrsync`` opdrachten. Als u deze optie gebruikt, overweeg dan om een tweede opdracht (net als bij [rsync](#use-a-two-phase-rsync-process), hierboven beschreven) uit te voeren om de gegevens integriteit te waarborgen.

``msrsync`` kan alleen schrijven naar en van lokale volumes. De bron en het doel moeten toegankelijk zijn als lokale koppels in het virtuele netwerk van het cluster.

Als u wilt gebruiken ``msrsync`` om een Azure-Cloud volume te vullen met een avere-cluster, volgt u deze instructies:

1. Installeren ``msrsync`` en voldoen aan de vereisten (rsync en Python 2,6 of hoger)
1. Bepaal het totale aantal bestanden en mappen dat moet worden gekopieerd.

   Gebruik bijvoorbeeld het hulp programma avere ``prime.py`` met de argumenten ```prime.py --directory /path/to/some/directory``` (beschikbaar op URL downloaden <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> ).

   Als ``prime.py`` u dit niet gebruikt, kunt u het aantal items met het ``find`` hulp programma GNU als volgt berekenen:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Deel het aantal items door 64 om het aantal items per proces te bepalen. Gebruik dit nummer met de ``-f`` optie om de grootte van de buckets in te stellen wanneer u de opdracht uitvoert.

1. Geef de ``msrsync`` opdracht om bestanden te kopiëren:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Als u gebruikt ``--inplace`` , voegt u een tweede uitvoering toe zonder de optie om te controleren of de gegevens correct zijn gekopieerd:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Deze opdracht is bijvoorbeeld ontworpen om 11.000-bestanden in 64 processen van/test/Source-Repository naar/mnt/vfxt/repository te verplaatsen:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Het script voor parallelle kopieën gebruiken

Het ``parallelcp`` script kan ook handig zijn voor het verplaatsen van gegevens naar de back-vFXT van uw cluster.

In het onderstaande script wordt het uitvoer bare bestand toegevoegd `parallelcp` . (Dit script is ontworpen voor Ubuntu; als u een andere distributie gebruikt, moet u dit ``parallel`` afzonderlijk installeren.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Voor beeld van parallel kopiëren

In dit voor beeld wordt het script voor parallelle kopieën gebruikt om te compileren ``glibc`` met behulp van bron bestanden uit het avere-cluster.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

De bron bestanden worden opgeslagen op het koppel punt van het avere-cluster en de object bestanden worden opgeslagen op de lokale vaste schijf.

Dit script maakt gebruik van een parallel Copy-script hierboven. De optie ``-j`` wordt gebruikt met ``parallelcp`` en ``make`` om parallel Lise ring te verkrijgen.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
