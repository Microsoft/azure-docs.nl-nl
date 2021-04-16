---
title: azcopy list | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ca436ee440ad7c1dd2caee3a65790f8b630fd949
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503283"
---
# <a name="azcopy-list"></a>azcopy list

Een lijst met de entiteiten in een bepaalde resource.

## <a name="synopsis"></a>Synopsis

Alleen blobcontainers worden ondersteund in de huidige release.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Help-inhoud voor de lijstopdracht weer te geven.|
|--machine readable|Een lijst met bestandsgrootten in bytes.|
|--mega-units|Geeft eenheden weer in orders van 1000, niet 1024.|
|--running-tally|Telt het totale aantal bestanden en hun grootte.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps float|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-suffixes string   |Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u alleen de Microsoft Azure hier zetten. Scheid meerdere vermeldingen met punt-dubbele punt.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
