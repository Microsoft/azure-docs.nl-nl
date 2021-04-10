---
title: AzCopy configureren, optimaliseren en problemen oplossen met Azure Storage | Microsoft Docs
description: AzCopy met Azure Storage configureren, optimaliseren en problemen hiermee oplossen. Wijzig de locatie van of verwijder de plannings-en logboek bestanden. Wijzig het standaard logboek niveau.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ad9e5665204dbd3f99f83af3578b1996814d6fa0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728840"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configureren, optimaliseren en problemen oplossen in AzCopy

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u bij het uitvoeren van geavanceerde configuratietaken en helpt u bij het oplossen van problemen die kunnen optreden tijdens het gebruik van AzCopy.

> [!NOTE]
> Als u op zoek bent naar inhoud die u helpt aan de slag te gaan met AzCopy, raadpleegt u een van de volgende artikelen:
> - [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
> - [Gegevens overdragen met AzCopy en blob-opslag](./storage-use-azcopy-v10.md#transfer-data)
> - [Gegevens overdragen met AzCopy en bestandopslag](storage-use-azcopy-files.md)
> - [Gegevens overdragen met AzCopy en Amazon S3-opslag](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Stel de omgevings variabele in om de proxy-instellingen voor AzCopy te configureren `HTTPS_PROXY` . Als u AzCopy uitvoert in Windows, detecteert AzCopy proxyinstellingen automatisch. U hoeft deze instelling dus niet te gebruiken in Windows. Als u ervoor kiest om deze instelling te gebruiken in Windows, wordt automatische detectie overschreven.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | In een opdracht prompt gebruikt u: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> In Power shell gebruikt u: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **MacOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Momenteel biedt AzCopy geen ondersteuning voor proxy's waarvoor authenticatie met NTLM of Kerberos is vereist.

### <a name="bypassing-a-proxy"></a>Een proxy overs Laan ###

Als u AzCopy uitvoert in Windows en u wilt weten dat u _geen_ proxy kunt gebruiken (in plaats van de instellingen automatisch te detecteren), gebruikt u deze opdrachten. Met deze instellingen zal AzCopy niet zoeken naar of proberen om een proxy te gebruiken.

| Besturingssysteem | Omgeving | Opdracht  |
|--------|-----------|----------|
| **Windows** | Opdracht prompt (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Op andere besturings systemen hoeft u de HTTPS_PROXY variabele niet uit te laten staan als u geen proxy wilt gebruiken.

## <a name="optimize-performance"></a>Prestaties optimaliseren

U kunt benchmark prestaties en vervolgens opdrachten en omgevings variabelen gebruiken om een optimale balans te vinden tussen prestaties en Resource verbruik.

Deze sectie helpt u bij het uitvoeren van deze optimalisatie taken:

> [!div class="checklist"]
> * Bench Mark-tests uitvoeren
> * Door Voer optimaliseren
> * Geheugen gebruik optimaliseren 
> * Bestands synchronisatie optimaliseren

### <a name="run-benchmark-tests"></a>Bench Mark-tests uitvoeren

U kunt een benchmark test voor prestaties uitvoeren op specifieke BLOB-containers of bestands shares om algemene prestatie statistieken te bekijken en knel punten met betrekking tot identiteits prestaties. U kunt de test uitvoeren door gegenereerde test gegevens te uploaden of te downloaden. 

Gebruik de volgende opdracht om een bench Mark-test voor prestaties uit te voeren.

| Syntaxis/voor beeld  |  Code |
|--------|-----------|
| **Syntaxis** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Voorbeeld** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

Met deze opdracht wordt een prestatie Bench Mark uitgevoerd door test gegevens naar een opgegeven bestemming te uploaden. De test gegevens worden in het geheugen gegenereerd, geüpload naar het doel en vervolgens verwijderd uit de bestemming nadat de test is voltooid. U kunt opgeven hoeveel bestanden er moeten worden gegenereerd en welke grootte u wilt gebruiken met optionele opdracht parameters.

Als u de test liever uitvoert door gegevens te downloaden, stelt u de `mode` para meter in op `download` . Zie [azcopy Bench Mark](storage-ref-azcopy-bench.md)voor gedetailleerde naslag documentatie. 

### <a name="optimize-throughput"></a>Door Voer optimaliseren

U kunt de `cap-mbps` vlag in uw opdrachten gebruiken om een plafond te plaatsen op basis van het gegevens aantal door voer. Met de volgende opdracht wordt bijvoorbeeld een taak en een CapsLock-door voer naar `10` megabits (MB) per seconde hervat. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

De doorvoer kan afnemen wanneer kleine bestanden worden overgebracht. U kunt de door Voer verhogen door de `AZCOPY_CONCURRENCY_VALUE` omgevings variabele in te stellen. Met deze variabele geeft u het aantal gelijktijdige aanvragen op dat kan plaatsvinden.  

Als uw computer minder dan 5 Cpu's heeft, wordt de waarde van deze variabele ingesteld op `32` . Anders is de standaardwaarde gelijk aan 16 vermenigvuldigd met het aantal CPU's. De maximale standaard waarde van deze variabele is `3000` , maar u kunt deze waarde ook op een hoger of lager niveau instellen. 

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, kunt u lezen welke waarde wordt gebruikt door te kijken naar het begin van een AzCopy-logboek bestand. De geselecteerde waarde, en de reden dat deze is geselecteerd, worden daar gerapporteerd.

Voordat u deze variabele instelt, wordt u aangeraden een bench Mark-test uit te voeren. In het Bench Mark-test proces wordt de aanbevolen gelijktijdigheids waarde gerapporteerd. Als uw netwerk voorwaarden en nettoladingen verschillen, stelt u deze variabele in op het woord `AUTO` in plaats van op een bepaald getal. Hierdoor wordt AzCopy altijd hetzelfde automatische afstemmings proces uitgevoerd dat wordt gebruikt in Bench Mark-tests.

### <a name="optimize-memory-use"></a>Geheugen gebruik optimaliseren

Stel de `AZCOPY_BUFFER_GB` omgevings variabele in om de maximale hoeveelheid van het systeem geheugen op te geven dat u wilt gebruiken voor buffers bij het downloaden en uploaden van bestanden. Deze waarde in gigabytes (GB) uitdrukken.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Taak tracking maakt altijd extra overhead in het geheugen gebruik. De hoeveelheid is afhankelijk van het aantal overdrachten in een taak. Buffers zijn het grootste onderdeel van het geheugen gebruik. U kunt de overhead beheren door `AZCOPY_BUFFER_GB` te gebruiken om ongeveer aan uw vereisten te voldoen, maar er is geen vlag voor een strikt Cap-geheugen gebruik beschikbaar.

### <a name="optimize-file-synchronization"></a>Bestands synchronisatie optimaliseren

De [Sync](storage-ref-azcopy-sync.md) -opdracht identificeert alle bestanden op de bestemming en vergelijkt vervolgens bestands namen en tijdstippen die het laatst zijn gewijzigd voordat de synchronisatie bewerking wordt gestart. Als u een groot aantal bestanden hebt, kunt u de prestaties verbeteren door deze bewerking vooraf uit te scha kelen. 

U kunt dit doen door in plaats daarvan de azcopy-opdracht [copy](storage-ref-azcopy-copy.md) te gebruiken en de `--overwrite` vlag in te stellen op `ifSourceNewer` . AzCopy vergelijkt bestanden als ze worden gekopieerd zonder dat er vooraf scans en vergelijkingen worden uitgevoerd. Dit biedt een prestatie rand in gevallen waarin een groot aantal bestanden moet worden vergeleken.

Met de [azcopy Copy](storage-ref-azcopy-copy.md) opdracht worden geen bestanden van de bestemming verwijderd. Als u de bestanden op de bestemming wilt verwijderen wanneer deze niet meer op de bron aanwezig zijn, gebruikt u de [azcopy Sync](storage-ref-azcopy-sync.md) -opdracht met de `--delete-destination` vlag ingesteld op een waarde van `true` of `prompt` . 

## <a name="troubleshoot-issues"></a>Problemen oplossen

AzCopy maakt logboek- en planbestanden voor elke taak. U kunt de logboeken gebruiken om potentiële problemen te onderzoeken en op te lossen. 

De logboeken bevatten de status van fout ( `UPLOADFAILED` , `COPYFAILED` , en `DOWNLOADFAILED` ), het volledige pad en de reden van de fout.

De logboek-en plan bestanden bevinden zich standaard in de `%USERPROFILE%\.azcopy` map in Windows of `$HOME$\.azcopy` in de map op Mac en Linux, maar u kunt deze locatie desgewenst wijzigen.

De relevante fout is niet noodzakelijkerwijs de eerste fout die in het bestand wordt weer gegeven. Voor fouten, zoals netwerk fouten, time-outs en fouten bij de server bezet, zal AzCopy Maxi maal 20 keer opnieuw proberen en wordt het proces voor nieuwe pogingen meestal voltooid.  De eerste fout die u ziet kan iets onschadelijk zijn.  In plaats van de eerste fout in het bestand te bekijken, zoekt u naar de fouten die bijna `UPLOADFAILED` , `COPYFAILED` of `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Wanneer u een aanvraag indient om Microsoft Ondersteuning (of het probleem met een derde partij op te lossen), deelt u de geredigeerde versie van de opdracht die u wilt uitvoeren. Dit zorgt ervoor dat de SAS niet per ongeluk met iedereen wordt gedeeld. U kunt de geredigeerde versie vinden aan het begin van het logboek bestand.

### <a name="review-the-logs-for-errors"></a>De logboeken controleren op fouten

Met de volgende opdracht worden alle fouten `UPLOADFAILED` uit het logboek opgehaald `04dc9ca9-158f-7945-5933-564021086c79` :

**Windows (Power shell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Taken weer geven en hervatten

Bij elke overdrachts bewerking wordt een AzCopy-taak gemaakt. Gebruik de volgende opdracht om de geschiedenis van taken weer te geven:

```
azcopy jobs list
```

Als u de taak statistieken wilt weer geven, gebruikt u de volgende opdracht:

```
azcopy jobs show <job-id>
```

Als u de overdrachten wilt filteren op status, gebruikt u de volgende opdracht:

```
azcopy jobs show <job-id> --with-status=Failed
```

Gebruik de volgende opdracht om een mislukte/geannuleerde taak te hervatten. Met deze opdracht wordt de id samen met het SAS-token gebruikt, omdat dit om veiligheids redenen niet permanent is:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Plaats argumenten van het pad, zoals het SAS-token met enkele aanhalings tekens (' '). Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

Wanneer u een taak hervat, zoekt AzCopy naar het job plan-bestand. In het plan bestand worden alle bestanden vermeld die zijn geïdentificeerd voor verwerking toen de taak voor het eerst werd gemaakt. Wanneer u een taak hervat, probeert AzCopy alle bestanden over te dragen die worden vermeld in het plan bestand dat niet al is overgedragen.

## <a name="change-the-location-of-the-plan-and-log-files"></a>De locatie van het plan en de logboek bestanden wijzigen

Plan-en logboek bestanden bevinden zich standaard in de `%USERPROFILE%\.azcopy` map op Windows of in de `$HOME/.azcopy` map op Mac en Linux. U kunt deze locatie wijzigen.

### <a name="change-the-location-of-plan-files"></a>De locatie van plan bestanden wijzigen

Gebruik een van deze opdrachten.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | Zo`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> In een opdracht prompt gebruikt u:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, worden de bestanden plannen naar de standaard locatie geschreven.

### <a name="change-the-location-of-log-files"></a>De locatie van de logboek bestanden wijzigen

Gebruik een van deze opdrachten.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | Zo`$env:AZCOPY_LOG_LOCATION="<value>"` <br> In een opdracht prompt gebruikt u:: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, worden logboeken naar de standaard locatie geschreven.

## <a name="change-the-default-log-level"></a>Het standaard logboek niveau wijzigen

AzCopy-logboek niveau is standaard ingesteld op `INFO` . Als u de uitgebreidheid van het logboek wilt beperken om schijf ruimte te besparen, kunt u deze instelling overschrijven met behulp van de ``--log-level`` optie. 

Beschik bare logboek niveaus zijn: `NONE` ,, `DEBUG` `INFO` ,,, en `WARNING` `ERROR` `PANIC` `FATAL` .

## <a name="remove-plan-and-log-files"></a>Plan-en logboek bestanden verwijderen

Als u alle plannings-en logboek bestanden van uw lokale computer wilt verwijderen om schijf ruimte te besparen, gebruikt u de `azcopy jobs clean` opdracht.

Als u de plannings-en logboek bestanden wilt verwijderen die zijn gekoppeld aan één taak, gebruikt u `azcopy jobs rm <job-id>` . Vervang de `<job-id>` tijdelijke aanduiding in dit voor beeld door de taak-id van de taak.
