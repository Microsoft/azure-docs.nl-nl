---
title: Update 5 op StorSimple 8000 Series-apparaat installeren | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u StorSimple 8000 Series Update 5 op uw StorSimple 8000 Series-apparaat installeert.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: bbac6eade634ffcfdc47ae3d22b32e0bd429b7c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513178"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Update 5 op uw StorSimple-apparaat installeren

## <a name="overview"></a>Overzicht

In deze zelf studie wordt uitgelegd hoe u update 5 installeert op een StorSimple-apparaat waarop een eerdere software versie wordt uitgevoerd via de Azure Portal en met behulp van de hotfix-methode. De methode hotfix wordt gebruikt wanneer u update 5 probeert te installeren op een apparaat met vóór update 3 versies. De methode hotfix wordt ook gebruikt wanneer een gateway is geconfigureerd op een andere netwerk interface dan DATA 0 van het StorSimple-apparaat en u probeert bij te werken vanaf een software versie van vóór de update 1.

Update 5 bevat software van het type apparaat, Storport en Spaceport, beveiligings updates voor het besturings systeem en updates van het besturings systeem, en firmware-updates voor de schijf.  De software voor het apparaat, Spaceport, Storport, Security en andere updates van het besturings systeem zijn niet-verstorende updates. De niet-verstorende of reguliere updates kunnen worden toegepast via de Azure Portal of via de hotfix-methode. De firmware-updates van de schijf zijn onderbreekde updates en worden toegepast wanneer het apparaat in de onderhouds modus wordt uitgevoerd via de hotfix-methode met behulp van de Windows Power shell-interface van het apparaat.

> [!IMPORTANT]
> * Update 5 is een verplichte update die direct moet worden geïnstalleerd. Zie [Update 5 Release Notes](storsimple-update5-release-notes.md)(Engelstalig) voor meer informatie.
> * Vóór de installatie wordt een set hand matig en automatische controles uitgevoerd om te bepalen wat de status is van de apparaatstatus en de netwerk verbinding. Deze controle vooraf wordt alleen uitgevoerd als u de updates van de Azure Portal toepast.
> * Het wordt ten zeerste aangeraden bij het bijwerken van een apparaat met versies voorafgaand aan update 3 de updates te installeren met behulp van de methode voor hotfixes. Als u problemen ondervindt, moet u [een ondersteunings ticket registreren](storsimple-8000-contact-microsoft-support.md).
> * U wordt aangeraden de software en andere regel matige updates te installeren via de Azure Portal. Ga naar de Windows Power shell-interface van het apparaat (om updates te installeren) als de gateway controle vóór de update mislukt in de portal. Afhankelijk van de versie die u bijwerkt, kunnen de updates vier uur (of hoger) duren om te installeren. De updates voor de onderhouds modus moeten worden geïnstalleerd via de Windows Power shell-interface van het apparaat. Als updates voor de onderhouds modus worden onderbroken, resulteert dit in een uitval tijd voor uw apparaat.
> * Als u de optionele StorSimple-Snapshot Manager uitvoert, moet u ervoor zorgen dat u de Snapshot Manager-versie hebt bijgewerkt naar Update 5 voordat u het apparaat bijwerkt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Update 5 installeren via de Azure Portal
Voer de volgende stappen uit om uw apparaat bij te werken naar [Update 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Micro soft haalt extra diagnostische gegevens van het apparaat op. Als het operationele team apparaten identificeert die problemen ondervinden, zijn we beter uitgerust met het verzamelen van gegevens van het apparaat en het vaststellen van problemen.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Controleer of de **StorSimple 8000 Series Update 5 (6.3.9600.17845)** op uw apparaat wordt uitgevoerd. De **laatst bijgewerkte datum** moet worden gewijzigd.

U ziet nu dat de updates voor de onderhouds modus beschikbaar zijn (dit bericht kan Maxi maal 24 uur na de installatie van de updates worden weer gegeven). De stappen voor het installeren van de update voor de onderhouds modus worden beschreven in de volgende sectie.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Update 5 als hotfix installeren

De software versies waarvoor een upgrade kan worden uitgevoerd met behulp van de hotfix-methode zijn:

* Update 0,1, 0,2, 0,3
* Update 1, 1,1, 1,2
* Update 2, 2,1, 2,2
* Update 3, 3,1
* Update 4

> [!NOTE] 
> De aanbevolen methode voor het installeren van Update 5 is via de Azure Portal bij het bijwerken van update 3 en hoger. Gebruik deze procedure bij het bijwerken van een apparaat met versies voorafgaand aan update 3. U kunt deze procedure ook gebruiken als u de controle van de gateway mislukt wanneer u probeert de updates te installeren via de Azure Portal. De controle mislukt als u een gateway hebt die is toegewezen aan een niet-DATA 0-netwerk interface en op uw apparaat een software versie van vóór update 1 wordt uitgevoerd.

De hotfix-methode omvat de volgende drie stappen:

1. Down load de hotfixes uit de Microsoft Update catalogus.
2. Installeer en controleer de hotfixes in de normale modus.
3. Installeer en controleer de update voor de onderhouds modus.

#### <a name="download-updates-for-your-device"></a>Updates voor uw apparaat downloaden

U moet de volgende hotfixes downloaden en installeren in de voorgeschreven volg orde en de voorgestelde mappen:

| Volgorde | KB | Beschrijving | Updatetype | Installatietijd |Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software-update<br> Down load zowel _HcsSoftwareUpdate.exe_ als _CisMSDAgent.exe_ |Regelmatig <br></br>Niet-verstorend |~ 25 minuten |FirstOrderUpdate|

Bij het bijwerken van een apparaat met update 4 hoeft u alleen de cumulatieve updates van het besturings systeem te installeren als tweede order updates.

| Volgorde | KB | Beschrijving | Updatetype | Installatietijd |Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 2. |KB4025336 |Pakket met cumulatieve updates voor het besturings systeem <br> Windows Server 2012 R2-versie downloaden |Regelmatig <br></br>Niet-verstorend |- |SecondOrderUpdate|

Als u een installatie uitvoert vanaf een apparaat met update 3 of eerder, installeert u het volgende naast de cumulatieve updates.

| Volgorde | KB | Beschrijving | Updatetype | Installatietijd |Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 2b. |KB4011841 <br> KB4011842 |Stuur programma-en firmware-updates voor LSI <br> EIGENSCHAPPEN van firmware-update (versie 3,38) |Regelmatig <br></br>Niet-verstorend |~ 3 uur <br> (inclusief 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Pakket met beveiligings updates voor besturings systeem <br> Windows Server 2012 R2-versie downloaden |Regelmatig <br></br>Niet-verstorend |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Pakket met updates van besturings systemen <br> Windows Server 2012 R2-versie downloaden |Regelmatig <br></br>Niet-verstorend |- |SecondOrderUpdate|


Mogelijk moet u ook updates voor de schijf firmware installeren boven op alle updates die in de voor gaande tabellen worden weer gegeven. U kunt controleren of u de firmware-updates van de schijf nodig hebt door de cmdlet uit te voeren `Get-HcsFirmwareVersion` . Als u deze firmware versies uitvoert:,,,,,, `XMGJ` `XGEG` `KZ50` `F6C2` `VR08` `N003` `0107` , hoeft u deze updates niet te installeren.

| Volgorde | KB | Beschrijving | Updatetype | Installatietijd | Installeren in map|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Schijf firmware |Onderhoud <br></br>Verstorende |~ 30 minuten | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Bij het bijwerken vanaf update 4 wordt de totale installatie tijd bijna vier uur.
> * Voordat u deze procedure gebruikt om de update toe te passen, moet u ervoor zorgen dat beide controllers online zijn en dat alle hardware-onderdelen in orde zijn.

Voer de volgende stappen uit om de hotfixes te downloaden en te installeren.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Update 5-release](storsimple-update5-release-notes.md).

