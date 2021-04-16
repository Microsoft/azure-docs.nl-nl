---
title: azcopy jobs show | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a14546d8bfc50531902b5150d38ee5ce8b8b5769
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503351"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Geeft gedetailleerde informatie weer voor de opgegeven taak-id.

## <a name="synopsis"></a>Synopsis

Als alleen de taak-id zonder vlag wordt opgegeven, wordt de voortgangssamenvatting van de taak geretourneerd.

Het aantal byte en het percentage voltooid dat wordt weergegeven wanneer u deze opdracht wordt uitgevoerd, weerspiegelt alleen bestanden die in de taak zijn voltooid. Ze geven geen gedeeltelijk voltooide bestanden weer.

Als de vlag is ingesteld, wordt de lijst met overdrachten in de taak met `with-status` de opgegeven waarde weergegeven.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Geeft Help-inhoud weer voor de opdracht show.|
|--with-status string|Vermeld alleen de overdrachten van de taak met deze status, beschikbare waarden: Gestart, Geslaagd, Mislukt|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps float|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-suffixes string   |Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u alleen de Microsoft Azure hier zetten. Scheid meerdere vermeldingen met punt-dubbele punt.|

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
