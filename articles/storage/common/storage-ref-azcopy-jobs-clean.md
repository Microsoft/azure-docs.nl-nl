---
title: azcopy-taken opschonen | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy-taken opschonen.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4a2c717601747e15556608559450b35bb934410b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879102"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Alle logboek bestanden en abonnementen voor alle taken verwijderen

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opties

**--Help**                Help voor schonen.

**--met-status** teken reeks verwijdert alleen de taken met deze status, beschik bare waarden: `Canceled` ,, `Completed` `Failed` , `InProgress` , `All` (standaard `All` )

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

**--Cap-Mbps-float**      De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**--** de teken reeks indeling van het uitvoer type van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is ' text '. (standaard tekst)

**--vertrouwd-micro soft-achtervoegsels** teken reeks geeft aanvullende domein achtervoegsels aan waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
