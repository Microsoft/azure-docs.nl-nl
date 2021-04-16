---
title: Fouten zoeken & taken hervatten met logboeken in AzCopy (Azure Storage) | Microsoft Docs
description: Meer informatie over het gebruik van logboeken om fouten vast te stellen en om taken te hervatten die zijn onderbroken met behulp van planbestanden.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502892"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Fouten zoeken en taken hervatten met behulp van logboek- en planbestanden in AzCopy

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u logboeken te gebruiken om fouten vast te stellen en vervolgens planbestanden te gebruiken om taken te hervatten. In dit artikel wordt ook beschreven hoe u logboek- en planbestanden configureert door het verbossingsniveau en de standaardlocatie waar ze worden opgeslagen te wijzigen.

> [!NOTE]
> Zie Aan de slag met AzCopy als u inhoud zoekt om aan de slag te gaan [met AzCopy.](storage-use-azcopy-v10.md)

## <a name="log-and-plan-files"></a>Logboek- en planbestanden

AzCopy maakt *logboek-* *en planbestanden* voor elke taak. U kunt deze logboeken gebruiken om mogelijke problemen te onderzoeken en op te lossen. 

De logboeken bevatten de status van de fout ( , en ), het `UPLOADFAILED` volledige pad en de reden van de `COPYFAILED` `DOWNLOADFAILED` fout.

Standaard bevinden de logboek- en planbestanden zich in de map in Windows of in de map op Mac en Linux, maar u kunt `%USERPROFILE%\.azcopy` `$HOME$\.azcopy` die locatie wijzigen. 

De relevante fout is niet noodzakelijkerwijs de eerste fout die in het bestand wordt weergegeven. Voor fouten zoals netwerkfouten, time-outs en Server bezet-fouten wordt AzCopy maximaal 20 keer opnieuw uitgevoerd en meestal lukt het opnieuw.  De eerste fout die u ziet, kan een onschadelijke fout zijn die met succes opnieuw is uitgevoerd.  Zoek dus niet naar de eerste fout in het bestand, maar naar de fouten in de buurt `UPLOADFAILED` van `COPYFAILED` , of `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Wanneer u een aanvraag indient bij Microsoft-ondersteuning (of bij het oplossen van het probleem met een derde partij), deelt u de redacted versie van de opdracht die u wilt uitvoeren. Dit zorgt ervoor dat de SAS niet per ongeluk met iemand wordt gedeeld. U vindt de redacted versie aan het begin van het logboekbestand.

## <a name="review-the-logs-for-errors"></a>Controleer de logboeken op fouten

Met de volgende opdracht worden alle fouten met `UPLOADFAILED` de status uit het logboek op `04dc9ca9-158f-7945-5933-564021086c79` halen:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Taken weergeven en hervatten

Elke overdrachtsbewerking maakt een AzCopy-taak. Gebruik de volgende opdracht om de geschiedenis van taken weer te geven:

```
azcopy jobs list
```

Gebruik de volgende opdracht om de taakstatistieken weer te geven:

```
azcopy jobs show <job-id>
```

Gebruik de volgende opdracht om de overdrachten te filteren op status:

```
azcopy jobs show <job-id> --with-status=Failed
```

Gebruik de volgende opdracht om een mislukte/geannuleerde taak te hervatten. Deze opdracht maakt gebruik van de id, samen met het SAS-token, omdat het om veiligheidsredenen niet persistent is:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Sluit padargumenten, zoals het SAS-token, in met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

Wanneer u een taak hervat, bekijkt AzCopy het taakplanbestand. Het planbestand bevat alle bestanden die zijn geïdentificeerd voor verwerking toen de taak voor het eerst werd gemaakt. Wanneer u een taak hervat, probeert AzCopy alle bestanden over te dragen die worden vermeld in het planbestand dat nog niet is overgedragen.

## <a name="change-the-location-of-plan-files"></a>De locatie van planbestanden wijzigen

Gebruik een van deze opdrachten.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> Gebruik in een opdrachtprompt: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, worden planbestanden naar de standaardlocatie geschreven.

## <a name="change-the-location-of-log-files"></a>De locatie van logboekbestanden wijzigen

Gebruik een van deze opdrachten.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> Gebruik in een opdrachtprompt: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, worden logboeken naar de standaardlocatie geschreven.

## <a name="change-the-default-log-level"></a>Het standaardlogboekniveau wijzigen

Standaard is het AzCopy-logboekniveau ingesteld op `INFO` . Als u de hoeveelheid logboeken wilt verminderen om schijfruimte te besparen, overschrijft u deze instelling met behulp van de ``--log-level`` optie . 

Beschikbare logboekniveaus zijn: `NONE` , , , , , , en `DEBUG` `INFO` `WARNING` `ERROR` `PANIC` `FATAL` .

## <a name="remove-plan-and-log-files"></a>Plan- en logboekbestanden verwijderen

Als u alle plan- en logboekbestanden van uw lokale computer wilt verwijderen om schijfruimte te besparen, gebruikt u de `azcopy jobs clean` opdracht .

Als u de plan- en logboekbestanden wilt verwijderen die aan slechts één taak zijn gekoppeld, gebruikt u `azcopy jobs rm <job-id>` . Vervang de `<job-id>` tijdelijke aanduiding in dit voorbeeld door de taak-id van de taak.

## <a name="see-also"></a>Zie ook

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
