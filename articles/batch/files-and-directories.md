---
title: Bestanden en mappen in Azure Batch
description: Meer informatie over bestanden en mappen en hoe deze worden gebruikt in een Azure Batch werk stroom vanuit een ontwikkelings oogpunt.
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: eafea6c234c3b261521f8a791b7a03e25388f02a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87552630"
---
# <a name="files-and-directories-in-azure-batch"></a>Bestanden en mappen in Azure Batch

In Azure Batch heeft elke taak een werkmap waaronder deze bestanden en mappen kan maken. Deze werkmap kan worden gebruikt voor het opslaan van het programma dat wordt uitgevoerd door de taak, de gegevens die erdoor worden verwerkt en de uitvoer van de verwerking die wordt uitgevoerd. Alle bestanden en mappen van een taak zijn het eigendom van de taakgebruiker.

De Batch-service geeft een deel van het bestandssysteem in een knooppunt weer als de *hoofdmap*. Deze hoofdmap bevindt zich op het tijdelijke opslag station van de virtuele machine, niet rechtstreeks op het station van het besturings systeem.

Taken hebben toegang tot de hoofdmap door te verwijzen naar de omgevingsvariabele `AZ_BATCH_NODE_ROOT_DIR`. Zie [Omgevingsinstellingen voor taken](jobs-and-tasks.md#environment-settings-for-tasks) voor meer informatie over het gebruik van omgevingsvariabelen.

## <a name="root-directory-structure"></a>Hoofd directory-structuur

De hoofdmap bevat de volgende mapstructuur:

![Scherm afbeelding van de mapstructuur van het reken knooppunt.](media\files-and-directories\node-folder-structure.png)

- **toepassingen**: bevat informatie over de details van toepassings pakketten die op het reken knooppunt zijn geïnstalleerd. Taken kunnen toegang krijgen tot deze map door te verwijzen naar de omgevingsvariabele `AZ_BATCH_APP_PACKAGE`.

- **fsmounts**: de map bevat alle bestands systemen die op een reken knooppunt zijn gekoppeld. Taken kunnen toegang krijgen tot deze map door te verwijzen naar de omgevingsvariabele `AZ_BATCH_NODE_MOUNTS_DIR`. Zie [een virtueel bestands systeem koppelen aan een batch-pool](virtual-file-mount.md)voor meer informatie.

- **gedeeld**: deze map biedt lees-/schrijftoegang tot *alle* taken die in een knooppunt worden uitgevoerd. Taken die in het knooppunt worden uitgevoerd, kunnen in deze map bestanden maken, lezen, bijwerken en verwijderen. Taken kunnen toegang krijgen tot deze map door te verwijzen naar de omgevingsvariabele `AZ_BATCH_NODE_SHARED_DIR`.

- **startup**: deze map wordt door een begintaak gebruikt als werkmap. Alle bestanden die door de begintaak naar het knooppunt zijn gedownload, worden hier opgeslagen. De begintaak kan in deze map bestanden maken, lezen, bijwerken en verwijderen. Taken kunnen toegang krijgen tot deze map door te verwijzen naar de omgevingsvariabele `AZ_BATCH_NODE_STARTUP_DIR`.

- **vluchtig**: deze map is voor interne doel einden. Er is geen garantie dat bestanden in deze map of de map zelf in de toekomst aanwezig zullen zijn.

- **werk items**: deze map bevat de mappen voor taken en hun taken op het reken knooppunt.

    In de map **werk items** wordt een map **taken** gemaakt voor elke taak die op het knoop punt wordt uitgevoerd. U kunt toegang krijgen tot deze map door te verwijzen naar de `AZ_BATCH_TASK_DIR` omgevings variabele.

    In elke **taken** lijst maakt de batch-service een werkmap ( `wd` ) waarvan het unieke pad wordt opgegeven door de `AZ_BATCH_TASK_WORKING_DIR` omgevings variabele. Deze map biedt lees-/schrijftoegang tot de taak. De taak kan in deze map bestanden maken, lezen, bijwerken en verwijderen. Deze map wordt behouden op basis van de *RetentionTime*-beperking die is opgegeven voor de taak.

    De `stdout.txt` `stderr.txt` bestanden en worden tijdens de uitvoering van de taak naar de map **taken** geschreven.

> [!IMPORTANT]
> Wanneer een knooppunt uit de pool wordt verwijderd, worden alle bestanden verwijderd die in het knooppunt zijn opgeslagen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [afhandelen en detecteren van fouten](error-handling.md) in azure batch.