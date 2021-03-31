---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094625"
---
## <a name="warm-up-the-cache"></a>De cache opwarmen

De schijf met alleen-lezen host-caching kan hogere IOPS geven dan de schijf limiet. Als u dit maximale Lees prestaties wilt ophalen uit de cache van de host, moet u eerst de cache van deze schijf opwarmen. Dit zorgt ervoor dat de Lees-IOs die het benchmarking-hulp programma op CacheReads volume plaatst, een treffer voor de cache krijgt, en niet rechtstreeks de schijf. De cache treffers resulteren in meer IOPS van de schijf met één cache.

> [!IMPORTANT]
> Telkens wanneer de VM opnieuw wordt opgestart, moet u de cache opwarmen voordat u benchmarks uitvoert.

## <a name="diskspd"></a>DISKSPD

[Down load het DISKSP-hulp programma](https://github.com/Microsoft/diskspd) op de VM. DISKSPD is een hulp programma dat u kunt aanpassen om uw eigen synthetische werk belastingen te maken. Voor het uitvoeren van benchmark tests worden dezelfde instellingen gebruikt die hierboven worden beschreven. U kunt de specificaties wijzigen om verschillende werk belastingen te testen.

In dit voor beeld gebruiken we de volgende set Baseline-para meters:

- -c200G: maakt (of maakt opnieuw) het voorbeeld bestand dat in de test wordt gebruikt. De waarde kan worden ingesteld in bytes, KiB, MiB, GiB of blokken. In dit geval wordt een groot bestand van 200-GiB doel bestand gebruikt om de geheugen cache te minimaliseren.
- -W100: Hiermee geeft u het percentage bewerkingen op dat schrijf aanvragen (-W0 is gelijk aan 100% lezen).
- -b4K: Hiermee geeft u de blok grootte in bytes, KiB, MiB of GiB. In dit geval wordt de blok grootte van 4 KB gebruikt voor het simuleren van een wille keurige I/O-test.
- -F4: Hiermee stelt u een totaal van vier threads in.
- -r: geeft de wille keurige I/O-test aan (de para meter-s wordt overschreven).
- -o128: geeft het aantal openstaande I/O-aanvragen per doel per thread aan. Dit wordt ook wel de wachtrij diepte genoemd. In dit geval wordt 128 gebruikt om de CPU te stressren.
- -W7200: geeft de duur van de warme tijd aan voordat metingen worden gestart.
- -D30: Hiermee geeft u de duur van de test op, met inbegrip van warm-up.
- -Sh: software-en hardware-schrijf cache uitschakelen (gelijk aan-SUW).

Zie de [github-opslag plaats](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters)voor een volledige lijst met para meters.

### <a name="maximum-write-iops"></a>Maximale schrijf-IOPS
We gebruiken een hoge wachtrij diepte van 128, een kleine blok grootte van 8 KB en vier worker-threads voor het uitvoeren van schrijf bewerkingen. De schrijf werkers sturen verkeer op het volume ' NoCacheWrites ', dat drie schijven heeft waarvoor cache is ingesteld op ' geen '.

Voer de volgende opdracht uit gedurende 30 seconden warme en 30 seconden meting:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Resultaten geven aan dat de Standard_D8ds_v4 virtuele machine de maximum limiet voor het aantal schrijf-IOPS van 12.800 levert.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="Voor 3208642560 totaal aantal bytes, maximum totaal I/O's van 391680, met een totaal van 101,97 MiB/s en een totaal van 13052,65 I/O per seconde.":::

### <a name="maximum-read-iops"></a>Maximale Lees-IOPS

We gebruiken een hoge wachtrij diepte van 128, een kleine blok grootte van vier KB en vier worker-threads voor het uitvoeren van Lees bewerkingen. De Lees werkers sturen verkeer op het ' CacheReads-volume, waarvoor één schijf met cache is ingesteld op ' ReadOnly '.

Voer de volgende opdracht uit voor twee uur warme en 30 seconden meting:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Resultaten geven aan dat de Standard_D8ds_v4 virtuele machine de maximum limiet voor lees-IOPS van 77.000 levert.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="Voor 9652785152 totaal aantal bytes waren er 2356637 totaal I/O's, op 306,72 totale MiB/s, en in totaal 78521,23 I/O's per seconde.":::

### <a name="maximum-throughput"></a>Maximale doorvoer

Als u de maximale Lees-en schrijf doorvoer wilt instellen, kunt u overschakelen naar een grotere blok grootte van 64 KB.
## <a name="fio"></a>FIO

FIO is een populair hulp programma voor bench Mark-opslag op de virtuele Linux-machines. Het biedt de flexibiliteit om verschillende i/o-grootten, sequentiële of wille keurige Lees-en schrijf bewerkingen te selecteren. Er worden werkthreads of processen geïnitieerd om de opgegeven I/O-bewerkingen uit te voeren. U kunt het type I/O-bewerkingen opgeven dat elke werk thread moet uitvoeren met behulp van taak bestanden. Er is één taak bestand per scenario gemaakt, zoals in de onderstaande voor beelden. U kunt de specificaties in deze taak bestanden wijzigen in Bench Mark verschillende werk belastingen die worden uitgevoerd op Premium Storage. In de voor beelden wordt gebruikgemaakt van een Standard_D8ds_v4 waarop **Ubuntu** wordt uitgevoerd. Gebruik dezelfde installatie die wordt beschreven in het begin van de Bench Mark-sectie en de cache op te schonen voordat u de Bench Mark-tests uitvoert.

Voordat u begint, moet u [FIO downloaden](https://github.com/axboe/fio) en installeren op de virtuele machine.

Voer de volgende opdracht uit voor Ubuntu,

```
apt-get install fio
```

We gebruiken vier worker-threads voor schrijf bewerkingen en vier werkthreads om Lees bewerkingen op de schijven uit te sturen. De schrijf werkers sturen verkeer op het ' cache-volume ', dat drie schijven heeft waarvoor cache is ingesteld op ' geen '. De Lees werkers sturen verkeer op het ' readcache-volume, waarvoor één schijf met cache is ingesteld op ' ReadOnly '.

### <a name="maximum-write-iops"></a>Maximale schrijf-IOPS

Maak het taak bestand met de volgende specificaties om het maximale aantal schrijf-IOPS op te halen. Noem deze fiowrite.ini.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Let op de belang rijke dingen die in overeenstemming zijn met de ontwerp richtlijnen die in eerdere secties worden besproken. Deze specificaties zijn essentieel voor het aansturen van maximum aantal IOPS,  

* Een hoge wachtrij diepte van 256.  
* Een kleine blok grootte van 4 KB.  
* Meerdere threads die wille keurige schrijf bewerkingen uitvoeren.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden te starten.  

```
sudo fio --runtime 30 fiowrite.ini
```

Terwijl de test wordt uitgevoerd, kunt u het aantal schrijf-IOPS zien dat de VM en Premium-schijven leveren. Zoals u in het onderstaande voor beeld kunt zien, levert de Standard_D8ds_v4 VM de maximale schrijf-IOPS-limiet van 12.800 IOPS op.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Het aantal schrijf-IOPS-VM'S en Premium-Ssd's die worden geleverd, laat zien dat schrijf bewerkingen 13.1 k IOPS zijn.":::

### <a name="maximum-read-iops"></a>Maximale Lees-IOPS

Maak het taak bestand met de volgende specificaties om maximale Lees-IOPS te verkrijgen. Noem deze fioread.ini.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Let op de belang rijke dingen die in overeenstemming zijn met de ontwerp richtlijnen die in eerdere secties worden besproken. Deze specificaties zijn essentieel voor het aansturen van maximum aantal IOPS,

* Een hoge wachtrij diepte van 256.  
* Een kleine blok grootte van 4 KB.  
* Meerdere threads die wille keurige schrijf bewerkingen uitvoeren.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden te starten.

```
sudo fio --runtime 30 fioread.ini
```

Terwijl de test wordt uitgevoerd, kunt u het aantal gelezen IOPS zien dat de VM en Premium-schijven worden geleverd. Zoals u in het onderstaande voor beeld kunt zien, levert de Standard_D8ds_v4 VM meer dan 77.000 Lees-IOPS op. Dit is een combi natie van de schijf-en cache prestaties.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Scherm opname van het aantal schrijf-IOPS-VM'S en Premium-Ssd's die worden geleverd door Lees bewerkingen zijn 78.6 k.":::

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
rate_iops=3200
```

Let op de belang rijke dingen die in overeenstemming zijn met de ontwerp richtlijnen die in eerdere secties worden besproken. Deze specificaties zijn essentieel voor het aansturen van maximum aantal IOPS,

* Een hoge wachtrij diepte van 128.  
* Een kleine blok grootte van 4 KB.  
* Meerdere threads die wille keurige Lees-en schrijf bewerkingen uitvoeren.

Voer de volgende opdracht uit om de FIO-test gedurende 30 seconden te starten.

```
sudo fio --runtime 30 fioreadwrite.ini
```

Terwijl de test wordt uitgevoerd, kunt u het aantal gecombineerde Lees-en schrijf-IOPS zien dat de VM en Premium-schijven leveren. Zoals u in het onderstaande voor beeld kunt zien, levert de Standard_D8ds_v4 VM meer dan 90.000 gecombineerde Lees-en schrijf-IOPS op. Dit is een combi natie van de schijf-en cache prestaties.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Bij gecombineerde Lees-en schrijf-IOPS ziet u dat lees bewerkingen 78.3 k en schrijf bewerkingen 12,6 k IOPS zijn.":::

### <a name="maximum-combined-throughput"></a>Maximale gecombineerde door Voer

Als u de maximale gecombineerde Lees-en schrijf doorvoer wilt ophalen, gebruikt u een grotere blok grootte en een grote wachtrij diepte waarbij meerdere threads Lees-en schrijf bewerkingen uitvoeren. U kunt een blok grootte van 64 KB en wachtrij diepte van 128 gebruiken.