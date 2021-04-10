---
title: azcopy-taken verwijderen | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy-taken verwijderen.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2744c2a082b5321fb671de08301981fd17396640
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879085"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Alle bestanden verwijderen die zijn gekoppeld aan de opgegeven taak-ID.

> [!NOTE] 
> U kunt de locatie aanpassen waarin logboek-en plan bestanden worden opgeslagen. Zie de [azcopy env](storage-ref-azcopy-env.md) -opdracht voor meer informatie.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opties

**--Help**                Help voor verwijderen.

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

**--Cap-Mbps-float**      De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**--** de teken reeks indeling van het uitvoer type van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaardwaarde is `text`. (standaard `text` )

**--vertrouwd-micro soft-achtervoegsels** teken reeks geeft aanvullende domein achtervoegsels aan waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
