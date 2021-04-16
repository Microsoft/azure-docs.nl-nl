---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy load clfs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebf04531f29e18f9d120ca2efa17244c4282084c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503266"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Brengt lokale gegevens over naar een container en slaat deze op in de INDELING Avere Cloud FileSystem (CLFS) van Microsoft.

## <a name="synopsis"></a>Synopsis

Met de laadopdracht worden gegevens gekopieerd naar Azure Blob Storage-containers en worden die gegevens vervolgens opgeslagen in de INDELING Avere Cloud FileSystem (CLFS) van Microsoft. De eigen CLFS-indeling wordt gebruikt door de Azure HPC Cache en Avere vFXT for Azure producten.

Als u deze opdracht wilt gebruiken, installeert u de benodigde extensie via: pip3 install clfsload~=1.0.23. Zorg ervoor CLFSLoad.py zich in uw PAD. Ga naar voor meer informatie over deze [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) stap.

Deze opdracht is een eenvoudige optie voor het verplaatsen van bestaande gegevens naar cloudopslag voor gebruik met specifieke High Performance Computing Cache-producten van Microsoft. 

Omdat deze producten gebruikmaken van een eigen bestandssysteemindeling in de cloud om gegevens te beheren, kunnen die gegevens niet worden geladen via de systeemeigen kopieeropdracht. 

In plaats daarvan moeten de gegevens worden geladen via het cacheproduct zelf of via deze laadopdracht, die de juiste eigen indeling gebruikt.
Met deze opdracht kunt u gegevens overdragen zonder de cache te gebruiken. Bijvoorbeeld om de opslag vooraf in te vullen of om bestanden toe te voegen aan een werkset zonder de cachebelasting te verhogen.

Het doel is een lege Azure Storage Container. Wanneer de overdracht is voltooid, kan de doelcontainer worden gebruikt met een Azure HPC Cache-exemplaar of Avere vFXT for Azure cluster.

> [!NOTE] 
> Dit is een preview-versie van de load-opdracht. Meld eventuele problemen in de AzCopy Github-opslagplaats.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="examples"></a>Voorbeelden

Laad een volledige map in een container met een SAS in CLFS-indeling:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Opties

**--compression-type tekenreeks** geef het compressietype op dat moet worden gebruikt voor de overdrachten. Beschikbare waarden zijn: `DISABLED` , `LZ4` . `LZ4`(standaard)

**--help**    voor de `azcopy load clfs` opdracht .

**--log-level** string Define the log verbosity for the log file, available levels: `DEBUG` , , , , `INFO` `WARNING` `ERROR` . `INFO`(standaard)

**--max-errors** uint32 Geef het maximum aantal te tolereren overdrachtsfouten op. Als er voldoende fouten optreden, stopt u de taak onmiddellijk.

**--new-session**   Start een nieuwe taak in plaats van door te gaan met een bestaande taak waarvan de traceringsgegevens worden bewaard op `--state-path` . (standaard true)

**--preserve-hardlinks**    Behoudt harde koppelingsrelaties.

**--state-path string** Required path to a local directory for job state tracking. Het pad moet naar een bestaande map wijzen om een taak te hervatten. Deze moet leeg zijn voor een nieuwe taak.

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps float|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-suffixes string   | Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u alleen de Microsoft Azure hier zetten. Scheid meerdere vermeldingen met punt-dubbele punt.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
