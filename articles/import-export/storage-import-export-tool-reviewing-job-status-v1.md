---
title: Status van Azure import/export-taak controleren-v1 | Microsoft Docs
description: Meer informatie over het gebruik van de logboek bestanden die zijn gemaakt met de import-of export taak om de status van de taak te bekijken.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: f634ceb60ae78d4d825c73bd2e98da2fb951b374
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706449"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>De status van een Azure import/export-taak controleren met behulp van Kopieer logboek bestanden
Wanneer de Microsoft Azure Import/Export-service de stations verwerkt die aan een import-of export taak zijn gekoppeld, worden de logboek bestanden gekopieerd naar het opslag account dat u hebt gebruikt voor het importeren of exporteren van blobs. Het logboek bestand bevat gedetailleerde status informatie over elk bestand dat is geïmporteerd of geëxporteerd. De service retourneert de URL voor elk exemplaar van het logboek bestand wanneer u de status van een voltooide taak doorzoekt. Zie [taak ophalen](/rest/api/storageimportexport/Jobs/Get)voor meer informatie.  

## <a name="example-urls"></a>Voor beeld-Url's

Hieronder vindt u voor beelden van Url's voor het kopiëren van logboek bestanden voor een import taak met twee stations:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Zie [import/export-service logboek bestands indeling](/previous-versions/azure/storage/common/storage-import-export-file-format-log) voor de indeling van Kopieer logboeken en de volledige lijst met status codes.  

## <a name="next-steps"></a>Volgende stappen

 * [Het Azure-hulp programma voor importeren/exporteren instellen](storage-import-export-tool-setup-v1.md)   
 * [Harde schijven voorbereiden voor een importtaak](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
 * [Een importtaak herstellen](./storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Een exporttaak herstellen](./storage-import-export-tool-repairing-an-export-job-v1.md)