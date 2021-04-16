---
title: azcopy env | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 71a4c27b84a16a4acb37c196ccd8ee571c2b2468
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503487"
---
# <a name="azcopy-env"></a>azcopy env

Toont de omgevingsvariabelen die het gedrag van AzCopy kunnen configureren. Zie Configuratie-instellingen voor [AzCopy v10 (Azure Storage) voor een volledige lijst met omgevingsvariabelen.](storage-ref-azcopy-configuration-settings.md)

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Als u een omgevingsvariabele in stelt met behulp van de opdrachtregel, kan die variabele worden gelezen in de opdrachtregelgeschiedenis. U kunt variabelen wissen die referenties uit de opdrachtregelgeschiedenis bevatten. Als u wilt voorkomen dat variabelen in uw geschiedenis worden weergegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevingsvariabele in te stellen.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Toont Help-inhoud voor de env-opdracht. |
|--show-sensitive|Geeft gevoelige/geheime omgevingsvariabelen weer.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps float|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-suffixes string  | Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u alleen de Microsoft Azure hier zetten. Scheid meerdere vermeldingen met punt-dubbele punt.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
