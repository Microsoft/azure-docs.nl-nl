---
title: Opdracht regel opties Azure Storage Explorer | Microsoft Docs
description: Documentatie over Azure Storage Explorer-opdracht regel opties voor opstarten
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745411"
---
# <a name="azure-storage-explorer-command-line-options"></a>Opdracht regel opties Azure Storage Explorer

Microsoft Azure Storage Explorer heeft een reeks opdracht regel opties die kunnen worden toegevoegd bij het starten van de toepassing. De meeste opdracht regel opties zijn bedoeld voor fout opsporing of het oplossen van problemen.

## <a name="command-line-options"></a>Opdrachtregelopties
Optie  | Beschrijving
:------- | :-----------
`--debug`/`--prod`  | Start de toepassing in debug of productie modus. In de foutopsporingsmodus worden de lokale bijlage gegevens opgeslagen in de lokale opslag van de toepassing en worden deze niet versleuteld. Verborgen eigenschappen worden weer gegeven in het deel venster Eigenschappen voor geselecteerde resource knooppunten. Het uitgebreidheids niveau van het logboek wordt ingesteld op debug-berichten afdrukken die de interne installatie logica van Storage Explorer weer geven. De standaardwaarde is `--prod`.
`--lang`  | De toepassing met een bepaalde taal starten. Bijvoorbeeld `--lang="zh-Hans"`.
`--disable-gpu` | Start de toepassing zonder GPU-versnelling.
`--auto-open-dev-tools` | Laat de toepassing het venster hulpprogram ma's voor ontwikkel aars openen zodra het browser venster wordt weer gegeven. Deze optie is handig als u op een regel in de opstart code van het browser venster een scheidings punt wilt aanraken.
`--verbosity` | Stel het uitgebreidheids niveau van Storage Explorer logboek registratie in. Ondersteunde uitbreidings niveaus zijn onder andere,,,, `debug` `verbose` `info` `warn` `error` en `silent` . Bijvoorbeeld `--verbosity=verbose`. Bij het uitvoeren van de productie modus is het standaard niveau uitgebreid `info` . Bij het uitvoeren van de foutopsporingsmodus is het niveau van de uitgebreide logboek registratie altijd `debug` .
`--log-dir` | Stel de map in voor het opslaan van logboek bestanden. Bijvoorbeeld `--log-dir=path_to_a_directory`.

Een voor beeld van het starten van Storage Explorer met aangepaste opdracht regel opties

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Deze opdracht regel opties kunnen in nieuwe Storage Explorer versies worden gewijzigd.

## <a name="when-to-use-command-line-options"></a>Wanneer u opdracht regel opties wilt gebruiken

U kunt een aantal opdracht regel opties gebruiken om Storage Explorer aan te passen. Voor de opties die corresponderende gebruikers instellingen hebben, zoals `--lang` . U kunt het beste de gebruikers instellingen gebruiken in plaats van de opdracht regel optie. 

De andere opdracht regel opties kunnen nuttig zijn voor het opsporen van fouten en het oplossen van problemen. Als u een probleem ondervindt in Storage Explorer, kunt u het probleem in de foutopsporingsmodus oplossen om meer gedetailleerde informatie te krijgen om te onderzoeken.