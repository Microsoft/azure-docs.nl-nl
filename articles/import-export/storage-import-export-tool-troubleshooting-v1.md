---
title: Problemen met importeren en exporteren oplossen in azure import/export | Microsoft Docs
description: Meer informatie over het verwerken van veelvoorkomende problemen bij het gebruik van Azure import/export.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706298"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Problemen oplossen in azure import/export
In dit artikel wordt beschreven hoe u veelvoorkomende problemen oplost bij het importeren en exporteren van gegevens in azure import/export.

## <a name="a-copy-session-failed-what-i-should-do"></a>Een Kopieer sessie is mislukt. Wat moet ik doen?  

Wanneer een Kopieer sessie mislukt, hebt u twee opties:  
* Als de fout opnieuw kan worden geprobeerd, bijvoorbeeld als de netwerk share gedurende een korte periode offline was en nu weer online is, kunt u de Kopieer sessie hervatten.
* Als de fout niet opnieuw kan worden geprobeerd, bijvoorbeeld als u de verkeerde directory van het bron bestand hebt opgegeven in de opdracht regel parameters, moet u de Kopieer sessie afbreken.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Ik kan een Kopieer sessie niet hervatten of afbreken.

Als de Kopieer sessie de eerste kopie sessie voor een station is, wordt het volgende fout bericht weer gegeven: "de eerste Kopieer sessie kan niet worden hervat of worden afgebroken." In dit geval kunt u het oude logboek bestand verwijderen en de opdracht opnieuw uitvoeren.  

Als een Kopieer sessie niet de eerste is voor een station, kan de sessie altijd worden hervat of afgebroken.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Ik ben het logboek bestand kwijt. Kan ik de taak nog steeds maken?

Het logboek bestand voor een station bevat volledige sessie-informatie uit de gegevens kopie. Wanneer u bestanden toevoegt aan het station, wordt het logboek bestand gebruikt om een import taak te maken. Als u het logboek bestand kwijtraakt, moet u alle kopie sessies voor het station opnieuw uitvoeren.

## <a name="next-steps"></a>Volgende stappen

* [Het Azure-hulp programma voor importeren/exporteren instellen](storage-import-export-tool-setup-v1.md)
* [Harde schijven voorbereiden voor een import taak](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Taak status controleren met kopie logboek bestanden](storage-import-export-tool-reviewing-job-status-v1.md)
* [Een import taak herstellen](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Een export taak herstellen](storage-import-export-tool-repairing-an-export-job-v1.md)