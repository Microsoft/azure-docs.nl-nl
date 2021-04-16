---
title: azcopy jobs remove | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy jobs remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c6a4745c4059c81384448deba37495030c4bf3a3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503368"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Verwijder alle bestanden die zijn gekoppeld aan de opgegeven taak-id.

> [!NOTE] 
> U kunt de locatie aanpassen waar logboek- en planbestanden worden opgeslagen. Zie de [opdracht azcopy env](storage-ref-azcopy-env.md) voor meer informatie.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="examples"></a>Voorbeelden

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opties

**--help**                Help voor verwijderen.

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

**--cap-mbps float**      Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.

**--output-type** string Format of the command's output. De keuzes zijn onder andere: text, json. De standaardwaarde is `text`. `text`(standaard)

**--trusted-microsoft-suffixes** string Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor de beveiliging moet u alleen Microsoft Azure hier. Scheid meerdere vermeldingen met punt-dubbele punt.

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
