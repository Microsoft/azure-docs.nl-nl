---
title: azcopy jobs clean | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy jobs clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 01bb7e37965abacac8105689bcb5ae52c548d647
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503419"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Alle logboek- en planbestanden voor alle taken verwijderen

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="examples"></a>Voorbeelden

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opties

**--help**                Help voor ops schonen.

**--with-status** string (tekenreeks met status) Verwijder alleen de taken met deze status, beschikbare waarden: `Canceled` , , , , `Completed` `Failed` `InProgress` `All` (standaard `All` )

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

**--cap-mbps float**      Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.

**--output-type** string Format of the command's output. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'. (standaard 'text')

**--trusted-microsoft-suffixes** string Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u alleen de Microsoft Azure hier zetten. Scheid meerdere vermeldingen met punt-dubbele punt.

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
