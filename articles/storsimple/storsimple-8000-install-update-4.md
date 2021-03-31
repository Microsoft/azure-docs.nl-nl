---
title: Update 4 installeren op het StorSimple 8000-serie apparaat | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u StorSimple 8000 Series update 4 installeert op uw StorSimple 8000 Series-apparaat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: ed6f9d58c5c54c88acf8e3a0e7fda7d2d65b8637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514314"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Update 4 installeren op uw StorSimple-apparaat

## <a name="overview"></a>Overzicht

In deze zelf studie wordt uitgelegd hoe u update 4 installeert op een StorSimple-apparaat waarop een eerdere software versie wordt uitgevoerd via de Azure Portal en met behulp van de hotfix-methode. De methode hotfix wordt gebruikt wanneer een gateway is geconfigureerd op een andere netwerk interface dan DATA 0 van het StorSimple-apparaat en u probeert bij te werken vanaf een software versie van vóór de update 1.

Update 4 bevat apparaatsoftware, eigenschappen van firmware, LSI driver en firmware, Storport en Spaceport, OS-beveiligings updates en een host van andere updates van het besturings systeem.  De apparaatsoftware, eigenschappen van firmware, Spaceport, Storport en andere updates van het besturings systeem zijn niet-verstorende updates. De niet-verstorende of reguliere updates kunnen worden toegepast via de Azure Portal of via de hotfix-methode. De firmware-updates van de schijf zijn onderbreekde updates en kunnen alleen via de hotfix-methode worden toegepast met behulp van de Windows Power shell-interface van het apparaat.

> [!IMPORTANT]
> * Vóór de installatie wordt een set hand matig en automatische controles uitgevoerd om te bepalen wat de status is van de apparaatstatus en de netwerk verbinding. Deze controle vooraf wordt alleen uitgevoerd als u de updates van de Azure Portal toepast.
> * U wordt aangeraden de software en andere regel matige updates te installeren via de Azure Portal. Ga naar de Windows Power shell-interface van het apparaat (om updates te installeren) als de gateway controle vóór de update mislukt in de portal. Afhankelijk van de versie die u bijwerkt, kunnen de updates vier uur (of hoger) duren om te installeren. De updates voor de onderhouds modus moeten ook worden geïnstalleerd via de Windows Power shell-interface van het apparaat. Als updates voor de onderhouds modus zijn onderbroken, resulteert dit in een uitval tijd voor uw apparaat.
> * Als u de optionele StorSimple-Snapshot Manager uitvoert, moet u ervoor zorgen dat u de Snapshot Manager-versie hebt bijgewerkt naar Update 4 voordat u het apparaat bijwerkt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Update 4 installeren via de Azure Portal
Voer de volgende stappen uit om uw apparaat bij te werken naar [Update 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Micro soft haalt extra diagnostische gegevens van het apparaat op. Als het operationele team apparaten identificeert die problemen ondervinden, zijn we beter uitgerust met het verzamelen van gegevens van het apparaat en het vaststellen van problemen. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Controleer of de **StorSimple 8000 Series update 4 (6.3.9600.17820)** op uw apparaat wordt uitgevoerd. De **datum van de laatste update** moet ook worden gewijzigd.

* U ziet nu dat de updates voor de onderhouds modus beschikbaar zijn (dit bericht kan Maxi maal 24 uur na de installatie van de updates worden weer gegeven). Updates voor onderhouds modus zijn storende updates die leiden tot uitval tijd van apparaten en kunnen alleen worden toegepast via de Windows Power shell-interface van uw apparaat.

* Down load de updates voor de onderhouds modus met behulp van de stappen in [voor het downloaden van hotfixes](#to-download-hotfixes) om KB4011837 te zoeken en te downloaden. Hiermee worden updates voor de schijf firmware geïnstalleerd (de andere updates moeten nu al worden geïnstalleerd). Volg de stappen in [hotfixes voor de onderhouds modus installeren en controleren](#to-install-and-verify-maintenance-mode-hotfixes) om de updates voor de onderhouds modus te installeren.

## <a name="install-update-4-as-a-hotfix"></a>Update 4 als hotfix installeren
De aanbevolen methode voor het installeren van update 4 is via de Azure Portal.

Gebruik deze procedure als u de gateway controle mislukt wanneer u probeert de updates te installeren via de Azure Portal. De controle mislukt, omdat er een gateway is toegewezen aan een niet-DATA 0-netwerk interface en op uw apparaat een software versie wordt uitgevoerd vóór update 1.

De software versies waarvoor een upgrade kan worden uitgevoerd met behulp van de hotfix-methode zijn:

* Update 0,1, 0,2, 0,3
* Update 1, 1,1, 1,2
* Update 2, 2,1, 2,2
* Update 3, 3,1


De hotfix-methode omvat de volgende drie stappen:

1. Down load de hotfixes uit de Microsoft Update catalogus.
2. Installeer en controleer de hotfixes in de normale modus.
3. Installeer en controleer de update voor de onderhouds modus.

#### <a name="download-updates-for-your-device"></a>Updates voor uw apparaat downloaden

U moet de volgende hotfixes downloaden en installeren in de voorgeschreven volg orde en de voorgestelde mappen:

| Volgorde | KB | Beschrijving | Updatetype | Installatietijd |Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Software-update |Regelmatig <br></br>Niet-verstorend |~ 25 minuten |FirstOrderUpdate|
| 2. |KB4011841 <br> KB4011842 |Stuur programma-en firmware-updates voor LSI <br> EIGENSCHAPPEN van firmware-update (versie 3,38) |Regelmatig <br></br>Niet-verstorend |~ 3 uur <br> (inclusief 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2b. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pakket met beveiligings updates voor besturings systeem <br> Windows Server 2012 R2 downloaden |Regelmatig <br></br>Niet-verstorend |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Pakket met updates van besturings systemen <br> Windows Server 2012 R2 downloaden |Regelmatig <br></br>Niet-verstorend |- |SecondOrderUpdate|

Mogelijk moet u ook updates voor de schijf firmware installeren boven op alle updates die in de voor gaande tabellen worden weer gegeven. U kunt controleren of u de firmware-updates van de schijf nodig hebt door de cmdlet uit te voeren `Get-HcsFirmwareVersion` . Als u deze firmware versies uitvoert:,,,,,, `XMGJ` `XGEG` `KZ50` `F6C2` `VR08` `N002` `0106` , hoeft u deze updates niet te installeren.

| Volgorde | KB | Beschrijving | Updatetype | Installatietijd | Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Schijf firmware |Onderhoud <br></br>Verstorende |~ 30 minuten | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Deze procedure hoeft slechts één keer te worden uitgevoerd om update 4 toe te passen. U kunt de Azure Portal gebruiken om volgende updates toe te passen.
> * Bij het bijwerken van update 3 of 3,1 is de totale installatie tijd bijna 4 uur.
> * Voordat u deze procedure gebruikt om de update toe te passen, moet u ervoor zorgen dat beide controllers online zijn en dat alle hardware-onderdelen in orde zijn.

Voer de volgende stappen uit om de hotfixes te downloaden en te installeren.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Update 4-release](storsimple-update4-release-notes.md).

