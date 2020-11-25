---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 358e92d8e43473c168e24be9f4af504e6ffcc37a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027094"
---
*De cache opwarmen*  
De schijf met alleen-lezen host-caching kan hogere IOPS geven dan de schijf limiet. Als u dit maximale Lees prestaties wilt ophalen uit de cache van de host, moet u eerst de cache van deze schijf opwarmen. Dit zorgt ervoor dat de Lees-IOs die het benchmarking-hulp programma op CacheReads volume plaatst, een treffer voor de cache krijgt, en niet rechtstreeks de schijf. Het aantal cache treffers resulteert in extra IOPS van de schijf met één cache.

> [!IMPORTANT]
> Telkens wanneer de virtuele machine opnieuw wordt opgestart, moet u de cache opwarmen voordat u benchmarking uitvoert.

## <a name="iometer"></a>Iometer

[Down load het Iometer-hulp programma](http://sourceforge.net/projects/iometer/files/iometer-stable/1.1.0/iometer-1.1.0-win64.x86_64-bin.zip/download) op de VM.

### <a name="test-file"></a>Test bestand

Iometer maakt gebruik van een test bestand dat is opgeslagen op het volume waarop u de benchmarking test uitvoert. Hiermee worden lees-en schrijf bewerkingen op dit test bestand uitgevoerd om de schijf-IOPS en de door voer te meten. Iometer maakt dit test bestand als u er geen hebt gegeven. Maak een 200 GB-test bestand met de naam iobw. TST op de CacheReads-en NoCacheWrites-volumes.

### <a name="access-specifications"></a>Toegangs specificaties

De specificaties, i/o-grootte van de aanvraag,% lezen/schrijven,% wille keurig/opeenvolgend worden geconfigureerd op het tabblad ' toegangs specificaties ' in Iometer. Maak een toegangs specificatie voor elk van de scenario's die hieronder worden beschreven. Maak de toegangs specificaties en ' Save ' met een geschikte naam zoals – RandomWrites \_ 8K, RandomReads \_ 8K. Selecteer de bijbehorende specificatie bij het uitvoeren van het test scenario.

Hieronder ziet u een voor beeld van toegangs specificaties voor het maximale aantal schrijf-IOPS-scenario's.  
    ![Voor beeld van toegangs specificaties voor maximale schrijf-IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

### <a name="maximum-iops-test-specifications"></a>Maximale IOPS-test specificaties

Gebruik kleinere aanvraag grootte om de maximale IOPs te demonstreren. Gebruik de grootte van de 8K-aanvraag en maak specificaties voor wille keurige schrijf bewerkingen en lees bewerkingen.

| Toegangs specificatie | Aanvraag grootte | Wille keurige | Wie |
| --- | --- | --- | --- |
| RandomWrites \_ 8K |8 K |100 |0 |
| RandomReads \_ 8K |8 K |100 |100 |

### <a name="maximum-throughput-test-specifications"></a>Specificaties voor maximale doorvoer snelheid

Gebruik grotere aanvraag grootte om de maximale door voer te demonstreren. Gebruik de aanvraag grootte van 64 K en maak specificaties voor wille keurige schrijf bewerkingen en lees bewerkingen.

| Toegangs specificatie | Aanvraag grootte | Wille keurige | Wie |
| --- | --- | --- | --- |
| RandomWrites \_ 64k |64 K |100 |0 |
| RandomReads \_ 64k |64 K |100 |100 |

### <a name="run-the-iometer-test"></a>De Iometer-test uitvoeren

Voer de onderstaande stappen uit om de cache op te schonen

1. Maak twee toegangs specificaties met de waarden die hieronder worden weer gegeven,

   | Name | Aanvraag grootte | Wille keurige | Wie |
   | --- | --- | --- | --- |
   | RandomWrites \_ 1MB |1 MB |100 |0 |
   | RandomReads \_ 1MB |1 MB |100 |100 |
1. Voer de Iometer-test uit voor het initialiseren van de cache schijf met de volgende para meters. Gebruik drie worker-threads voor het doel volume en een wachtrij diepte van 128. Stel de duur ' uitvoerings tijd ' van de test in op 2 uur op het tabblad ' test Setup '.

   | Scenario | Doel volume | Name | Duur |
   | --- | --- | --- | --- |
   | Cache schijf initialiseren |CacheReads |RandomWrites \_ 1MB |2 uur |
1. Voer de Iometer-test uit voor het opwarmen van de cache schijf met de volgende para meters. Gebruik drie worker-threads voor het doel volume en een wachtrij diepte van 128. Stel de duur ' uitvoerings tijd ' van de test in op 2 uur op het tabblad ' test Setup '.

   | Scenario | Doel volume | Name | Duur |
   | --- | --- | --- | --- |
   | Cache schijf opwarmen |CacheReads |RandomReads \_ 1MB |2 uur |

Nadat de cache schijf is opgewarmd, gaat u verder met de test scenario's die hieronder worden weer gegeven. Als u de Iometer-test wilt uitvoeren, gebruikt u ten minste drie worker-threads voor **elk** doel volume. Voor elke worker-thread selecteert u het doel volume, stelt u de wachtrij diepte in en selecteert u een van de opgeslagen test specificaties, zoals wordt weer gegeven in de onderstaande tabel, om het bijbehorende test scenario uit te voeren. De tabel toont ook de verwachte resultaten voor IOPS en door Voer wanneer deze tests worden uitgevoerd. Voor alle scenario's wordt een kleine i/o-grootte van 8 KB en een hoge wachtrij diepte van 128 gebruikt.

| Test scenario | Doel volume | Naam | Resultaat |
| --- | --- | --- | --- |
| Met maximaal IOPS lezen |CacheReads |RandomWrites \_ 8K |50.000 IOPS |
| Met maximaal IOPS schrijven |NoCacheWrites |RandomReads \_ 8K |64.000 IOPS |
| Met maximaal Gecombineerde IOPS |CacheReads |RandomWrites \_ 8K |100.000 IOPS |
| NoCacheWrites |RandomReads \_ 8K | &nbsp; | &nbsp; |
| Met maximaal Gelezen MB/sec. |CacheReads |RandomWrites \_ 64k |524 MB/sec. |
| Met maximaal Geschreven bytes per seconde |NoCacheWrites |RandomReads \_ 64k |524 MB/sec. |
| Gecombineerde MB/sec. |CacheReads |RandomWrites \_ 64k |1000 MB/sec. |
| NoCacheWrites |RandomReads \_ 64k | &nbsp; | &nbsp; |

Hieronder vindt u scherm afbeeldingen van de resultaten van de Iometer-test voor gecombineerde IOPS-en doorvoer scenario's.

### <a name="combined-reads-and-writes-maximum-iops"></a>Gecombineerde Lees-en schrijf bewerkingen maximum aantal IOPS

![Gecombineerde Lees-en schrijf bewerkingen maximum aantal IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

### <a name="combined-reads-and-writes-maximum-throughput"></a>Gecombineerde Lees-en schrijf bewerkingen maximale door Voer

![Gecombineerde Lees-en schrijf bewerkingen maximale door Voer](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

## <a name="fio"></a>FIO

FIO is een populair hulp programma voor bench Mark-opslag op de virtuele Linux-machines. Het biedt de flexibiliteit om verschillende i/o-grootten, sequentiële of wille keurige Lees-en schrijf bewerkingen te selecteren. Er worden werkthreads of processen geïnitieerd om de opgegeven I/O-bewerkingen uit te voeren. U kunt het type I/O-bewerkingen opgeven dat elke werk thread moet uitvoeren met behulp van taak bestanden. Er is één taak bestand per scenario gemaakt, zoals in de onderstaande voor beelden. U kunt de specificaties in deze taak bestanden wijzigen in Bench Mark verschillende werk belastingen die worden uitgevoerd op Premium Storage. In de voor beelden gebruiken we een standaard-DS 14-VM met **Ubuntu**. Gebruik dezelfde configuratie die wordt beschreven in het begin van de benchmarking-sectie en de cache op te schonen voordat u de benchmarking tests uitvoert.

Voordat u begint, moet u [FIO downloaden](https://github.com/axboe/fio) en installeren op de virtuele machine.

Voer de volgende opdracht uit voor Ubuntu,

```
apt-get install fio
```

We gebruiken vier worker-threads voor schrijf bewerkingen en vier werkthreads om Lees bewerkingen op de schijven uit te sturen. De schrijf werkers sturen verkeer op het ' cache-volume ', dat 10 schijven heeft waarvoor cache is ingesteld op ' geen '. De Lees werkers sturen verkeer op het ' readcache-volume, waarvoor één schijf met cache is ingesteld op ' ReadOnly '.

### <a name="maximum-write-iops"></a>Maximale schrijf-IOPS

Maak het taak bestand met de volgende specificaties om het maximale aantal schrijf-IOPS op te halen. Noem deze fiowrite.ini.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Let op de belang rijke dingen die in overeenstemming zijn met de ontwerp richtlijnen die in eerdere secties worden besproken. Deze specificaties zijn essentieel voor het aansturen van maximum aantal IOPS,  

* Een hoge wachtrij diepte van 256.  
* Een kleine blok grootte van 8 KB.  
* Meerdere threads die wille keurige schrijf bewerkingen uitvoeren.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden te starten.  

```
sudo fio --runtime 30 fiowrite.ini
```

Terwijl de test wordt uitgevoerd, kunt u het aantal schrijf-IOPS zien dat de VM en Premium-schijven leveren. Zoals u in het onderstaande voor beeld ziet, levert de DS14-VM de maximale schrijf-IOPS limiet van 50.000 IOPS op.  
    ![Aantal schrijf-IOPS-VM'S en Premium-schijven worden geleverd.](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

### <a name="maximum-read-iops"></a>Maximale Lees-IOPS

Maak het taak bestand met de volgende specificaties om maximale Lees-IOPS te verkrijgen. Noem deze fioread.ini.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Let op de belang rijke dingen die in overeenstemming zijn met de ontwerp richtlijnen die in eerdere secties worden besproken. Deze specificaties zijn essentieel voor het aansturen van maximum aantal IOPS,

* Een hoge wachtrij diepte van 256.  
* Een kleine blok grootte van 8 KB.  
* Meerdere threads die wille keurige schrijf bewerkingen uitvoeren.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden te starten.

```
sudo fio --runtime 30 fioread.ini
```

Terwijl de test wordt uitgevoerd, kunt u het aantal gelezen IOPS zien dat de VM en Premium-schijven worden geleverd. Zoals u in het voor beeld hieronder ziet, wordt de DS14-VM meer dan 64.000 Lees-IOPS geleverd. Dit is een combi natie van de schijf-en cache prestaties.  
    ![Scherm opname van het aantal schrijf-IOPS-VM'S en Premium-schijven die worden geleverd.](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

### <a name="maximum-read-and-write-iops"></a>Maximale IOPS voor lezen en schrijven

Maak het taak bestand met de volgende specificaties om het maximale aantal gecombineerde Lees-en schrijf bewerkingen te verkrijgen. Noem deze fioreadwrite.ini.

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Let op de belang rijke dingen die in overeenstemming zijn met de ontwerp richtlijnen die in eerdere secties worden besproken. Deze specificaties zijn essentieel voor het aansturen van maximum aantal IOPS,

* Een hoge wachtrij diepte van 128.  
* Een kleine blok grootte van 4 KB.  
* Meerdere threads die wille keurige Lees-en schrijf bewerkingen uitvoeren.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden te starten.

```
sudo fio --runtime 30 fioreadwrite.ini
```

Terwijl de test wordt uitgevoerd, kunt u het aantal gecombineerde Lees-en schrijf-IOPS zien dat de VM en Premium-schijven leveren. Zoals u in het voor beeld hieronder ziet, wordt de DS14-VM meer dan 100.000 gecombineerde Lees-en schrijf-IOPS geleverd. Dit is een combi natie van de schijf-en cache prestaties.  
    ![Gecombineerde Lees-en schrijf-IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

### <a name="maximum-combined-throughput"></a>Maximale gecombineerde door Voer

Als u de maximale gecombineerde Lees-en schrijf doorvoer wilt ophalen, gebruikt u een grotere blok grootte en een grote wachtrij diepte waarbij meerdere threads Lees-en schrijf bewerkingen uitvoeren. U kunt een blok grootte van 64 KB en wachtrij diepte van 128 gebruiken.
