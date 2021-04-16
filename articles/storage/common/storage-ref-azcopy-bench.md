---
title: azcopy bench | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy bench.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e49e787854069c2fcea30df7a43c3aacdd21b9e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502025"
---
# <a name="azcopy-benchmark"></a>azcopy benchmark

Voert een prestatiebenchmark uit door testgegevens te uploaden of te downloaden naar of van een opgegeven bestemming. Voor uploads worden de testgegevens automatisch gegenereerd.

Met de benchmarkopdracht wordt hetzelfde proces uitgevoerd als 'kopiëren', behalve: 

  - In plaats van zowel bron- als doelparameters te vereisen, is er slechts één benchmark nodig. Dit is de blobcontainer, Azure Files Share of Azure Data Lake Storage Gen2 bestandssysteem dat u wilt uploaden naar of downloaden.

  - De parameter 'mode' beschrijft of AzCopy uploads naar of downloads van een bepaald doel moet testen. Geldige waarden zijn Uploaden en Downloaden. De standaardwaarde is Uploaden.

  - Voor uploadbenchmarks wordt de nettolading beschreven door opdrachtregelparameters, die bepalen hoeveel bestanden automatisch worden gegenereerd en hoe belangrijk de bestanden zijn. Het generatieproces vindt volledig in het geheugen plaats. Schijf wordt niet gebruikt.

  - Voor downloads bestaat de nettolading uit alle bestanden die al bestaan in de bron. (Zie het onderstaande voorbeeld over het genereren van testbestanden, indien nodig).
  
  - Slechts enkele van de optionele parameters die beschikbaar zijn voor de kopieeropdracht worden ondersteund.
  
  - Aanvullende diagnostische gegevens worden gemeten en gerapporteerd.
  
  - Voor uploads is het standaardgedrag om de overgedragen gegevens te verwijderen aan het einde van de test.  Voor downloads worden de gegevens nooit lokaal opgeslagen.

De benchmarkmodus wordt automatisch afgestemd op het aantal parallelle TCP-verbindingen dat de maximale doorvoer biedt. Dit getal wordt aan het einde weergegeven. Als u automatische afstemming wilt voorkomen, stelt u AZCOPY_CONCURRENCY_VALUE omgevingsvariabele in op een specifiek aantal verbindingen. 

Alle gebruikelijke verificatietypen worden ondersteund. De handigste benadering voor het uploaden van benchmarks is echter meestal het maken van een lege container met een SAS-token en het gebruik van SAS-verificatie. (Voor de downloadmodus moet een set testgegevens aanwezig zijn in de doelcontainer.)

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [De prestaties van AzCopy v10 optimaliseren met Azure Storage](storage-use-azcopy-optimize.md)


## <a name="examples"></a>Voorbeelden

```azcopy
azcopy benchmark [destination] [flags]
```

Voer een benchmarktest uit met standaardparameters (geschikt voor benchmarkingnetwerken van maximaal 1 Gbps):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Voer een benchmarktest uit die 100 bestanden uploadt, elk 2 GiB groot: (geschikt voor benchmarking in een snel netwerk, bijvoorbeeld 10 Gbps):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Voer een benchmarktest uit, maar gebruik 50.000 bestanden, elk 8 MiB groot en bereken de MD5-hashes (op dezelfde manier als de vlag dit doet in de `--put-md5` kopieeropdracht). Het doel van het benchmarken is om te testen of MD5-berekening van invloed is op de doorvoer voor het geselecteerde aantal `--put-md5` bestanden en de geselecteerde grootte:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Een benchmarktest uitvoeren die bestaande bestanden van een doel downloadt

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Voer een upload uit die de overgedragen bestanden niet verwijdert. (Deze bestanden kunnen vervolgens fungeren als de nettolading voor een downloadtest)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Opties

**--blob-type** string Definieert het type blob op de bestemming. Wordt gebruikt om benchmarking van verschillende blobtypen toe te staan. Identiek aan de parameter met dezelfde naam in de kopieeropdracht (standaard 'Detecteren').

**--block-size-mb** float Gebruik deze blokgrootte (opgegeven in MiB). De standaardwaarde wordt automatisch berekend op basis van de bestandsgrootte. Decimale breuken zijn toegestaan, bijvoorbeeld 0,25. Identiek aan de parameter met dezelfde naam in de kopieeropdracht.

**--check-length**  Controleer de lengte van een bestand op de bestemming na de overdracht. Als de bron en het doel niet overeenkomen, wordt de overdracht gemarkeerd als mislukt. (standaard true)

**--delete-test-data**  Indien waar, worden de benchmarkgegevens verwijderd aan het einde van de benchmark-run.  Stel deze in op onwaar als u de gegevens op de bestemming wilt houden, bijvoorbeeld om deze te gebruiken voor handmatige tests buiten de benchmarkmodus (standaard true).

**--file-count** uint.  Het aantal automatisch gegenereerde gegevensbestanden dat moet worden gebruikt (standaard 100).

**--help**  Help voor bankje

**--log-level** string Define the log verbosity for the log file, available levels: INFO(all requests/responses), WARNING (slow responses), ERROR (only failed requests) and NONE (no output logs). (standaard 'INFO')

**--mode string** Defines if Azcopy should test uploads or downloads from this target. Geldige waarden zijn 'upload' en 'download'. De standaardoptie is 'uploaden'. (standaard 'uploaden')

**--aantal mappen uint** Als groter is dan 0, maakt u mappen om de gegevens te verdelen.

**--put-md5**  Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap Content-MD5 van de doelblob/het doelbestand. (De hash wordt standaard NIET gemaakt.) Identiek aan de parameter met dezelfde naam in de kopieeropdracht.

**--size-per-file** tekenreeks Grootte van elk automatisch gegenereerd gegevensbestand. Moet een getal zijn dat direct wordt gevolgd door K, M of bijvoorbeeld 12.000 of 200 G (standaard '250 miljoen').

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

**--cap-mbps float**  Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.

**--output-type** string Format of the command's output. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'. (standaard 'text').

**--trusted-microsoft-suffixes** string Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u alleen de Microsoft Azure hier zetten. Scheid meerdere vermeldingen met punt-dubbele punt.


## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
