---
title: azcopy Bank | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy bank opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282004"
---
# <a name="azcopy-benchmark"></a>azcopy-Bench Mark

Voert een prestatie-benchmark door het uploaden of downloaden van test gegevens naar of van een opgegeven bestemming. Voor uploads worden de test gegevens automatisch gegenereerd.

De Bench Mark-opdracht voert hetzelfde proces uit als ' kopie ', met uitzonde ring van: 

  - In plaats van zowel de bron-als de doel parameters te vereisen, neemt Bench Mark slechts één. Dit is de BLOB-container, de Azure Files share of het Azure Data Lake Storage Gen2 bestands systeem dat u wilt uploaden naar of downloaden.

  - De para meter mode beschrijft of AzCopy uploads naar of down loads van een gegeven doel moet testen. Geldige waarden zijn ' Upload ' en ' down load '. De standaard waarde is ' Upload '.

  - Voor het uploaden van benchmarks wordt de Payload beschreven door opdracht regel parameters, die bepalen hoeveel bestanden automatisch worden gegenereerd en hoe belang rijk de bestanden zijn. Het generatie proces vindt volledig in het geheugen plaats. De schijf wordt niet gebruikt.

  - Voor down loads bestaat de payload uit welke bestanden al bestaan op de bron. (Zie voor beeld hieronder hoe test bestanden genereren, indien nodig).
  
  - Slechts enkele van de optionele para meters die beschikbaar zijn voor de Kopieer opdracht worden ondersteund.
  
  - Aanvullende diagnostische gegevens worden gemeten en gerapporteerd.
  
  - Voor uploads is het standaard gedrag om de overgebrachte gegevens aan het einde van de test uitvoering te verwijderen.  Voor down loads worden de gegevens nooit lokaal opgeslagen.

De Bench Mark-modus wordt automatisch afgestemd op het aantal parallelle TCP-verbindingen dat de maximale door Voer geeft. Het nummer wordt aan het einde weer gegeven. Stel de omgevings variabele AZCOPY_CONCURRENCY_VALUE in op een specifiek aantal verbindingen om autotuning te voor komen. 

Alle gebruikelijke verificatie typen worden ondersteund. De meest geschikte benadering voor het uploaden van benchmarking is doorgaans om een lege container met een SAS-token te maken en SAS-verificatie te gebruiken. (Voor de download modus moet een set test gegevens aanwezig zijn in de doel container.)

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy benchmark [destination] [flags]
```

Voer een bench Mark-test uit met de standaard parameters (geschikt voor benchmarking-netwerken tot 1 Gbps): '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Voer een bench Mark-test uit die 100 bestanden uploadt, elk 2 GiB in grootte: (geschikt voor benchmarking op een snel netwerk, bijvoorbeeld 10 Gbps): '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Voer een bench Mark-test uit, maar gebruik 50.000-bestanden, elke 8 MiB in grootte en bereken de MD5-hashes (op dezelfde manier als de `--put-md5` vlag dit doet in de Kopieer opdracht). Het doel van `--put-md5` wanneer de benchmarking is om te testen of de MD5-berekening van invloed is op de door Voer voor het geselecteerde aantal bestanden en de grootte:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Een bench Mark-test uitvoeren waarmee bestaande bestanden van een doel worden gedownload

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Voer een upload uit waarmee de overgebrachte bestanden niet worden verwijderd. (Deze bestanden kunnen vervolgens fungeren als de payload voor een download test)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Opties

**--BLOB-type** teken reeks definieert het type BLOB bij het doel. Wordt gebruikt om benchmarking van verschillende BLOB-typen toe te staan. Gelijk aan de para meter met dezelfde naam in de Kopieer opdracht (standaard ' detect ').

**--block-size-MB** float gebruik deze blok grootte (opgegeven in MIB). De standaard waarde wordt automatisch berekend op basis van de bestands grootte. Decimale breuken zijn toegestaan, bijvoorbeeld 0,25. Gelijk aan de para meter met dezelfde naam in de Kopieer opdracht.

**--Check-length**  Controleer de lengte van een bestand op de bestemming na de overdracht. Als de bron en bestemming niet overeenkomen, wordt de overdracht als mislukt gemarkeerd. (standaard instelling waar)

**--Delete-test-data**  Indien true, worden de benchmark gegevens verwijderd aan het einde van de Bench Mark-uitvoeringsrun.  Stel deze waarde in op ONWAAR als u de gegevens op het doel wilt blijven gebruiken. u kunt deze bijvoorbeeld voor hand matige tests buiten de Bench Mark-modus (standaard True).

**--Bestands telling** uint.  Het aantal automatisch gegenereerde gegevens bestanden dat moet worden gebruikt (standaard 100).

**--Help**  Hulp voor Bank

**--** teken reeks op logboek niveau definiëren de logboek uitgebreidheids voor het logboek bestand, beschik bare niveaus: info (alle aanvragen/antwoorden), waarschuwing (trage antwoorden), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard INFO)

**--modus** teken reeks definieert of Azcopy uploads of down loads van dit doel moet testen. Geldige waarden zijn ' Upload ' en ' down load '. De standaard optie is ' Upload '. (standaard ' Upload ')

**--aantal-mappen** uint als dit groter is dan 0, kunt u mappen maken om de gegevens op te delen.

**--put-MD5**  Maak een MD5-hash van elk bestand en sla de hash op als de content-MD5-eigenschap van de BLOB/het doel bestand. (Standaard wordt de hash niet gemaakt.) Gelijk aan de para meter met dezelfde naam in de Kopieer opdracht.

**--grootte:** de teken reeks grootte per bestand van elk automatisch gegenereerd gegevens bestand. Dit moet een getal zijn dat onmiddellijk wordt gevolgd door K, M of G. bijv. 12k of 200G (standaard ' 250M ').

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

**--Cap-Mbps-float**  De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**--** de teken reeks indeling van het uitvoer type van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is ' text '. (standaard tekst).

**--vertrouwd-micro soft-achtervoegsels** teken reeks geeft aanvullende domein achtervoegsels aan waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.


## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
