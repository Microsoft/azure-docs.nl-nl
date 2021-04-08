---
title: azcopy load CLFS | Microsoft Docs
titleSuffix: Azure Storage
description: In dit artikel vindt u Naslag informatie voor de azcopy load CLFS opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878372"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Lokale gegevens worden overgebracht naar een container en opgeslagen in de CLFS-indeling (avere Cloud file) van micro soft.

## <a name="synopsis"></a>Samen vatting

Met de laad opdracht kopieert u gegevens naar Azure Blob-opslag containers en slaat u die gegevens vervolgens op in de CLFS-indeling (avere Cloud file) van micro soft. De eigen CLFS-indeling wordt gebruikt door de Azure HPC-cache en avere vFXT voor Azure-producten.

Als u gebruik wilt maken van deze opdracht, installeert u de benodigde extensie via: PIP3 install clfsload ~ = 1.0.23. Zorg ervoor dat CLFSLoad.py zich in het pad bevindt. Ga voor meer informatie over deze stap naar [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Deze opdracht is een eenvoudige optie voor het verplaatsen van bestaande gegevens naar de Cloud opslag voor gebruik met specifieke micro soft High-Performance Computing-cache producten. 

Omdat deze producten een eigen indeling voor het Cloud-bestands systeem gebruiken om gegevens te beheren, kunnen deze gegevens niet worden geladen via de systeem eigen Kopieer opdracht. 

In plaats daarvan moeten de gegevens worden geladen via het cache product zelf of via deze laad opdracht, waarbij de juiste indeling wordt gebruikt.
Met deze opdracht kunt u gegevens overdragen zonder gebruik te maken van de cache. Bijvoorbeeld om opslag vooraf in te vullen of om bestanden toe te voegen aan een werkset zonder de cache belasting te verg Roten.

Het doel is een lege Azure Storage-container. Wanneer de overdracht is voltooid, kan de doel container worden gebruikt met een Azure HPC-cache-instantie of avere vFXT voor Azure-cluster.

> [!NOTE] 
> Dit is een preview-versie van de laad opdracht. Meld eventuele problemen op de AzCopy github opslag plaats.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

Een volledige directory laden in een container met een SAS in CLFS-indeling:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Opties

**--compressie-type** teken reeks Geef het compressie type op dat moet worden gebruikt voor de overdrachten. Beschik bare waarden zijn: `DISABLED` , `LZ4` . (standaard `LZ4` )

**--Help**    voor de `azcopy load clfs` opdracht.

**--** teken reeks op logboek niveau definiëren de logboek uitgebreidheids voor het logboek bestand, beschik bare niveaus: `DEBUG` ,, `INFO` `WARNING` , `ERROR` . (standaard `INFO` )

**--aantal-fouten** uint32 het maximum aantal overdrachts fouten opgeven dat mag worden toegestaan. Als er voldoende fouten optreden, stopt u de taak onmiddellijk.

**--nieuwe sessie**   Start een nieuwe taak in plaats van een bestaand item te hervatten waarvan de tracerings gegevens worden bewaard op `--state-path` . (standaard instelling waar)

**--pres Erve-hardlinks**    Vaste koppelings relaties behouden.

**--status-padtekenreeks** vereist pad naar een lokale map voor het bijhouden van de taak status. Het pad moet verwijzen naar een bestaande map om een taak te hervatten. Deze moet leeg zijn voor een nieuwe taak.

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps-float|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|
|--vertrouwd-micro soft-achtervoegsels teken reeks   | Hiermee geeft u aanvullende domein achtervoegsels op waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
