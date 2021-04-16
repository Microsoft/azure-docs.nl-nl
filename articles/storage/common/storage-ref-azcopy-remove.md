---
title: azcopy remove | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd221215d6be3c14ce1200e8bd374a97cb7608a0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503011"
---
# <a name="azcopy-remove"></a>azcopy remove

Verwijder blobs of bestanden uit een Azure-opslagaccount.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="examples"></a>Voorbeelden

Eén blob verwijderen met behulp van een SAS-token:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Verwijder een volledige virtuele map met behulp van een SAS-token:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Verwijder alleen de blobs in een virtuele map, maar verwijder geen subdirectory's of blobs binnen deze subdirectory's:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Verwijder een subset van blobs in een virtuele map (bijvoorbeeld: verwijder alleen JPG- en PDF-bestanden, of als de naam van de blob `exactName` is):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Verwijder een volledige virtuele map, maar sluit bepaalde blobs uit van het bereik (bijvoorbeeld: elke blob die begint met foo of eindigt met een balk):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Verwijder specifieke blobs en virtuele map door hun relatieve paden (NIET met URL gecodeerd) in een bestand te plaatsen:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Eén bestand verwijderen uit een Blob Storage account met een hiërarchische naamruimte (opnemen/uitsluiten wordt niet ondersteund):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Eén map verwijderen uit een Blob Storage account met een hiërarchische naamruimte (opnemen/uitsluiten wordt niet ondersteund):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opties

**--delete-snapshots-tekenreeks** Standaard mislukt de verwijderbewerking als een blob momentopnamen heeft. Geef op om de hoofd-blob en alle momentopnamen te verwijderen. U kunt ook opgeven dat alleen de momentopnamen worden verwijderd, maar de `include` `only` hoofd-blob behouden.

**--exclude-path-tekenreeks** Sluit deze paden uit bij het verwijderen. Deze optie biedt geen ondersteuning voor jokertekens (*). Controleert het relatieve pad voorvoegsel. Bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf`

**--exclude-pattern string** Exclude files where the name matches the pattern list. Bijvoorbeeld: `*.jpg` ; `*.pdf` ;`exactName`

**--force-if-read-only**   Bij het verwijderen van Azure Files bestand of map, moet u de verwijdering geforceerd laten werken, zelfs als voor het bestaande object het kenmerk alleen-lezen is ingesteld.

**--help**   voor verwijderen.

**--include-path string** Include only these paths when removing. Deze optie biedt geen ondersteuning voor jokertekens (*). Controleert het relatieve pad voorvoegsel. Bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf`

**--include-pattern string** Include only files where the name matches the pattern list. Bijvoorbeeld: *`.jpg` ;* `.pdf` ;`exactName`

**--list-of-files** string Definieert de locatie van een bestand, dat de lijst bevat met bestanden en mappen die moeten worden verwijderd. De relatieve paden moeten worden begrensd door regeluitbreekingen en de paden mogen NIET url-gecodeerd zijn. 

**--list-of-versions-tekenreeks** Hiermee geeft u een bestand op waarin elke versie-id op een afzonderlijke regel wordt vermeld. Zorg ervoor dat de bron moet wijzen naar één blob en dat alle versie-ID's die in het bestand zijn opgegeven met deze vlag alleen bij de bron-blob horen. Opgegeven versie-ID's van de opgegeven blob worden verwijderd uit Azure Storage. 

**--log-level string** Define the log verbosity for the log file. Beschikbare niveaus zijn: `INFO` (alle aanvragen/antwoorden), `WARNING` (trage reacties), (alleen mislukte `ERROR` aanvragen) en `NONE` (geen uitvoerlogboeken). `INFO`(standaard) `INFO`(standaard)

**--recursief**    Bekijk subdirectorieën recursief bij het synchroniseren tussen de directories.

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps float|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-achtervoegsels tekenreeks   |Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u hier alleen Microsoft Azure zetten. Scheid meerdere vermeldingen met punt-dubbele punts.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
