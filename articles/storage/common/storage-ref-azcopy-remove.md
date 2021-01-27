---
title: azcopy verwijderen | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d28a20a6afc78939491e56f46a32782b4c0844cc
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879000"
---
# <a name="azcopy-remove"></a>azcopy remove

Blobs of bestanden verwijderen uit een Azure-opslag account.

## <a name="synopsis"></a>Samen vatting

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

Eén BLOB verwijderen met behulp van een SAS-token:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Een volledige virtuele map verwijderen met een SAS-token:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Verwijder alleen de blobs in een virtuele map, maar verwijder geen submappen of blobs in deze submappen:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Verwijder een subset blobs in een virtuele map (bijvoorbeeld: alleen jpg-en PDF-bestanden verwijderen of de naam van de BLOB `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Verwijder een volledige virtuele map, maar sluit bepaalde blobs uit van het bereik (bijvoorbeeld: elke blob die begint met foo of eindigt op de balk):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Verwijder specifieke blobs en virtuele mappen door hun relatieve paden (niet met URL-code ring) in een bestand te plaatsen:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Eén bestand verwijderen uit een Blob Storage account met een hiërarchische naam ruimte (insluit/niet ondersteund):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Eén map verwijderen uit een Blob Storage account met een hiërarchische naam ruimte (insluit/niet ondersteund):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opties

**--Delete-snap shots** string standaard wordt de bewerking delete mislukt als een BLOB moment opnamen bevat. Geef `include` op om de hoofd-Blob en alle bijbehorende moment opnamen te verwijderen; u kunt ook opgeven `only` dat alleen de moment opnamen moeten worden verwijderd, maar dat de root-BLOB wordt bewaard.

**--exclude-** teken reeks van het pad deze paden uitsluiten tijdens het verwijderen. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad-voor voegsel gecontroleerd. Bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf`

**--exclude-patroon** teken reeks bestanden uitsluiten waarbij de naam overeenkomt met de patroon lijst. Bijvoorbeeld: `*.jpg` ; `*.pdf` ;`exactName`

**--Force-alleen-lezen**   Wanneer u een Azure Files bestand of map verwijdert, dwingt u het verwijderen af, zelfs als het bestaande object het kenmerk alleen-lezen heeft.

**--Help**   Help voor verwijderen.

**--include-** teken reeks bevat alleen deze paden bij het verwijderen. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad-voor voegsel gecontroleerd. Bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf`

**--include-patroon** teken reeks alleen bestanden opnemen waarvan de naam overeenkomt met de patroon lijst. Bijvoorbeeld: *`.jpg` ;* `.pdf` ;`exactName`

**--lijst-of-files** teken reeks definieert de locatie van een bestand, dat de lijst met bestanden en mappen bevat die moeten worden verwijderd. De relatieve paden moeten worden gescheiden door regel einden en de paden mogen geen URL-gecodeerd zijn. 

**--lijst-van-versie** teken reeks geeft een bestand op waarin elke versie-id wordt weer gegeven op een afzonderlijke regel. Zorg ervoor dat de bron moet verwijzen naar een enkele Blob en dat alle versie-id's die zijn opgegeven in het bestand met deze vlag, alleen bij de bron-BLOB horen. Opgegeven versie-id's van de opgegeven BLOB worden verwijderd uit Azure Storage. 

**--** teken reeks op logboek niveau Definieer het logboek bestand uitgebreider voor het logbestand. Beschik bare niveaus zijn: `INFO` (alle aanvragen/antwoorden), ( `WARNING` trage reacties), `ERROR` (alleen mislukte aanvragen) en `NONE` (geen uitvoer Logboeken). (standaard `INFO` ) (standaard `INFO` )

**--recursief**    In submappen recursief bekijken bij het synchroniseren tussen directory's.

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps-float|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|
|--vertrouwd-micro soft-achtervoegsels teken reeks   |Hiermee geeft u aanvullende domein achtervoegsels op waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
