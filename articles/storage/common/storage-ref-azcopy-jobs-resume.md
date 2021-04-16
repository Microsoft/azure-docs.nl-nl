---
title: azcopy jobs resume | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy jobs resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 497aa3a77397a8a587badc0911176cd092eb1f23
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503334"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Hervat de bestaande taak met de opgegeven taak-id.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|--destination-sas string|Doel-SAS van de bestemming voor de opgegeven taak-id.|
|--exclude string|Filter: Sluit deze mislukte overdracht(en) uit wanneer u de taak hervat. Bestanden moeten worden gescheiden door ';'.|
|-h, --help|Help-inhoud voor de hervatten opdracht.|
|--include string|Filter: neem alleen deze mislukte overdracht(en) op wanneer de taak wordt hervat. Bestanden moeten worden gescheiden door ';'.|
|--source-sas string |bron-SAS van de bron voor de opgegeven taak-id.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps float|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-suffixes string   |Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u alleen de Microsoft Azure hier zetten. Scheid meerdere vermeldingen met punt-dubbele punt.|

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
