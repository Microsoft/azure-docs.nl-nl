---
title: De prestaties van AzCopy v10 optimaliseren met Azure Storage | Microsoft Docs
description: Dit artikel helpt u bij het optimaliseren van de prestaties van AzCopy v10 met Azure Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509082"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>De prestaties van AzCopy optimaliseren met Azure Storage

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u de prestaties te optimaliseren.

> [!NOTE]
> Zie Aan de slag met AzCopy als u inhoud zoekt om aan de slag te gaan [met AzCopy](storage-use-azcopy-v10.md)

U kunt prestaties benchmarken en vervolgens opdrachten en omgevingsvariabelen gebruiken om een optimale balans te vinden tussen prestaties en resourceverbruik.

## <a name="run-benchmark-tests"></a>Benchmarktests uitvoeren

U kunt een prestatiebenchmarktest uitvoeren op specifieke blobcontainers of bestands shares om algemene prestatiestatistieken weer te geven en prestatieknelpunten te identificeren. U kunt de test uitvoeren door gegenereerde testgegevens te uploaden of te downloaden. 

Gebruik de volgende opdracht om een prestatiebenchmarktest uit te voeren.

**Syntaxis**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Voorbeeld**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

Met deze opdracht wordt een prestatiebenchmark uitgevoerd door testgegevens te uploaden naar een opgegeven bestemming. De testgegevens worden gegenereerd in het geheugen, geüpload naar het doel en vervolgens verwijderd uit het doel nadat de test is voltooid. U kunt opgeven hoeveel bestanden moeten worden gegenereerd en welke grootte u wilt dat ze zijn met behulp van optionele opdrachtparameters.

Als u deze test liever wilt uitvoeren door gegevens te downloaden, stelt u de `mode` parameter in op `download` . Zie [azcopy benchmark voor gedetailleerde referentie docs.](storage-ref-azcopy-bench.md) 

## <a name="optimize-for-large-numbers-of-small-files"></a>Optimaliseren voor grote aantallen kleine bestanden

De doorvoer kan afnemen bij het overdragen van kleine bestanden, met name bij het overdragen van grote aantallen bestanden. Om de prestaties te maximaliseren, verkleint u de grootte van elke taak. Voor download- en uploadbewerkingen verhoogt u de gelijktijdigheid, vermindert u de logboekactiviteit en zet u functies uit die hoge prestatiekosten met zich mee brengen.

#### <a name="reduce-the-size-of-each-job"></a>De grootte van elke taak verkleinen

Voor optimale prestaties moet u ervoor zorgen dat elke taken minder dan 10 miljoen bestanden over dragen. Taken die meer dan 50 miljoen bestanden overdragen, kunnen slecht presteren omdat het AzCopy-mechanisme voor het bijhouden van taken een aanzienlijke hoeveelheid overhead met zich mee kan dragen. Als u de overhead wilt verminderen, kunt u grote taken opsplitsen in kleinere taken. 

Een manier om de grootte van een taak te verkleinen, is door het aantal bestanden te beperken dat wordt beïnvloed door een taak. U kunt opdrachtparameters gebruiken om dat te doen. Een taak kan bijvoorbeeld alleen een subset van de directories kopiëren met behulp van de parameter als `include path` onderdeel van de opdracht [azcopy copy.](storage-ref-azcopy-copy.md) 

Gebruik de `include-pattern` parameter om bestanden te kopiëren die een specifieke extensie hebben (bijvoorbeeld: `*.pdf` ). Gebruik in een afzonderlijke taak de parameter om alle bestanden te kopiëren `exclude-pattern` die geen extensie `*.pdf` hebben. Zie [Specifieke bestanden uploaden en](storage-use-azcopy-blobs-upload.md#upload-specific-files) Specifieke [blobs downloaden](storage-use-azcopy-blobs-download.md#download-specific-blobs) voor voorbeelden.

Nadat u hebt besloten hoe u grote taken opsplitst in kleinere taken, kunt u overwegen om taken uit te werken op meer dan één virtuele machine (VM).

#### <a name="increase-concurrency"></a>Gelijktijdigheid verhogen

Als u bestanden uploadt of downloadt, gebruikt u de omgevingsvariabele om het aantal gelijktijdige aanvragen op uw `AZCOPY_CONCURRENCY_VALUE` computer te verhogen. Stel deze variabele zo hoog mogelijk in zonder de prestaties van uw computer in gevaar te brengen. Zie de sectie Het aantal [](#increase-the-number-of-concurrent-requests) gelijktijdige aanvragen verhogen van dit artikel voor meer informatie over deze variabele.

Als u blobs kopieert tussen opslagaccounts, kunt u de waarde van de omgevingsvariabele instellen `AZCOPY_CONCURRENCY_VALUE` op een waarde die groter is dan `1000` . U kunt deze variabele hoog instellen omdat AzCopy gebruikmaakt van server-naar-server-API's, zodat gegevens rechtstreeks tussen opslagservers worden gekopieerd en de verwerkingskracht van uw computer niet wordt gebruikt.  

#### <a name="decrease-the-number-of-logs-generated"></a>Het aantal gegenereerde logboeken verlagen

U kunt de prestaties verbeteren door het aantal logboekgegevens te verminderen dat door AzCopy wordt gemaakt wanneer een bewerking wordt voltooid. Standaard registreert AzCopy alle activiteiten met betrekking tot een bewerking. Voor optimale prestaties kunt u overwegen om de `log-level` parameter van uw kopieer-, synchronisatie- of verwijderopdracht in te stellen op `ERROR` . Op die manier registreert AzCopy alleen fouten. Standaard is het waardelogboekniveau ingesteld op `INFO` . 

#### <a name="turn-off-length-checking"></a>Lengtecontrole uitschakelen 

Als u bestanden uploadt of downloadt, kunt u de van uw kopieer- en `--check-length` synchronisatieopdrachten instellen op `false` . Dit voorkomt dat AzCopy de lengte van een bestand controleert na een overdracht. AzCopy controleert standaard de lengte om ervoor te zorgen dat bron- en doelbestanden overeenkomen nadat een overdracht is voltooid. AzCopy voert deze controle uit na elke bestandsoverdracht. Deze controle kan de prestaties verslechteren wanneer taken grote aantallen kleine bestanden overdragen. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Gelijktijdig lokaal scannen in- (Linux)

Bestandsscans op sommige Linux-systemen worden niet snel genoeg uitgevoerd om alle parallelle netwerkverbindingen te verzadigen. In deze gevallen kunt u de `AZCOPY_CONCURRENT_SCAN` instellen op `true` . 

## <a name="increase-the-number-of-concurrent-requests"></a>Het aantal gelijktijdige aanvragen verhogen

U kunt de doorvoer verhogen door de `AZCOPY_CONCURRENCY_VALUE` omgevingsvariabele in te stellen. Met deze variabele geeft u het aantal gelijktijdige aanvragen op dat kan plaatsvinden.  

Als uw computer minder dan 5 CPU's heeft, wordt de waarde van deze variabele ingesteld op `32` . Anders is de standaardwaarde gelijk aan 16 vermenigvuldigd met het aantal CPU's. De maximale standaardwaarde van deze variabele is , maar u kunt deze waarde handmatig hoger of `3000` lager instellen. 

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, kunt u lezen welke waarde wordt gebruikt door te kijken naar het begin van een AzCopy-logboekbestand. De geselecteerde waarde en de reden waarom deze is geselecteerd, worden daar gerapporteerd.

Voordat u deze variabele in stelt, raden we u aan een benchmarktest uit te voeren. Tijdens het benchmarktestproces wordt de aanbevolen gelijktijdigheidswaarde rapporteren. Als de netwerkomstandigheden en nettoladingen variëren, kunt u deze variabele ook instellen op het woord `AUTO` in plaats van op een bepaald getal. Dit zorgt ervoor dat AzCopy altijd hetzelfde automatische afstemmingsproces kan uitvoeren dat wordt gebruikt in benchmarktests.

## <a name="limit-the-throughput-data-rate"></a>De doorvoersnelheid van gegevens beperken

U kunt de vlag `cap-mbps` in uw opdrachten gebruiken om een maximum te maken voor de doorvoersnelheid van gegevens. Met de volgende opdracht wordt bijvoorbeeld een taak hervat en wordt de doorvoer gelimieten naar `10` megabits (MB) per seconde. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Geheugengebruik optimaliseren

Stel de omgevingsvariabele in om de maximale hoeveelheid van uw systeemgeheugen op te geven die AzCopy moet gebruiken voor buffering bij het downloaden en `AZCOPY_BUFFER_GB` uploaden van bestanden. Druk deze waarde uit in gigabytes (GB).

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Taaktracking heeft altijd extra overhead in het geheugengebruik. De hoeveelheid varieert op basis van het aantal overdrachten in een taak. Buffers vormen het grootste onderdeel van het geheugengebruik. U kunt de overhead beheersen door ongeveer aan uw vereisten te voldoen, maar er is geen vlag beschikbaar om het algehele geheugengebruik `AZCOPY_BUFFER_GB` strikt te beperken.

## <a name="optimize-file-synchronization"></a>Bestandssynchronisatie optimaliseren

De [synchronisatieopdracht](storage-ref-azcopy-sync.md) identificeert alle bestanden op het doel en vergelijkt vervolgens de bestandsnamen en de laatst gewijzigde tijdstempels vóór het starten van de synchronisatiebewerking. Als u een groot aantal bestanden hebt, kunt u de prestaties verbeteren door deze verwerking van vooraan te elimineren. 

U doet dit door in plaats daarvan de [opdracht azcopy copy](storage-ref-azcopy-copy.md) te gebruiken en de vlag `--overwrite` in te stellen op `ifSourceNewer` . AzCopy vergelijkt bestanden terwijl ze worden gekopieerd zonder dat er scans en vergelijkingen van vooraan worden uitgevoerd. Dit biedt een betere prestaties in gevallen waarin er een groot aantal bestanden zijn om te vergelijken.

Met [de opdracht azcopy copy](storage-ref-azcopy-copy.md) worden geen bestanden van de bestemming verwijderd. Als u bestanden op de bestemming wilt verwijderen wanneer ze niet meer bestaan in de bron, gebruikt u de opdracht [azcopy sync](storage-ref-azcopy-sync.md) met de vlag ingesteld op een waarde van of `--delete-destination` `true` `prompt` .

## <a name="see-also"></a>Zie ook

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)