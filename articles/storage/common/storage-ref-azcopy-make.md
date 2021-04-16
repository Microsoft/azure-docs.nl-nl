---
title: azcopy make | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e5b142d36266d5b6a333a0fa5cd233bac71849e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503028"
---
# <a name="azcopy-make"></a>azcopy make

Hiermee maakt u een container of bestands share.

## <a name="synopsis"></a>Synopsis

Maak een container of bestands share die wordt vertegenwoordigd door de opgegeven resource-URL.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Help-inhoud voor de make-opdracht tonen. |
|--quota-gb uint32|Hiermee geeft u de maximale grootte van de share in gigabytes (GB), nul betekent dat u het standaardquotum van de bestandsservice accepteert.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps float|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-achtervoegsels tekenreeks   |Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u hier alleen Microsoft Azure zetten. Scheid meerdere vermeldingen met punt-dubbele punts.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
