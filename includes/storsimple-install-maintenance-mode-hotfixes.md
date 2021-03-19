---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176065"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Hotfixes voor onderhouds modus installeren via Windows PowerShell voor StorSimple
> [!IMPORTANT]
> In de onderhouds modus moet u de hotfix eerst op één controller Toep assen en vervolgens op de andere controller.
> 
> 

1. Plaats het apparaat in de onderhouds modus. Zie [stap 2: de onderhouds modus invoeren](../articles/storsimple/storsimple-update-device.md#step2) voor instructies over het invoeren van de onderhouds modus.
2. Als u de hotfix wilt Toep assen, typt u:
   
     `Start-HcsHotfix` 
3. Geef het pad naar de gedeelde netwerkmap met de hotfixbestanden op wanneer u hierom wordt gevraagd.
4. U wordt gevraagd om een bevestiging. Typ **j** om door te gaan met de installatie van de hotfix.
5. Nadat u de hotfix op één controller hebt toegepast, meldt u zich aan bij de andere controller. Pas de hotfix toe zoals u voor de vorige controller hebt gedaan.
6. Nadat de hotfixes zijn toegepast, sluit u de onderhouds modus. Zie [stap 4: onderhouds modus afsluiten](../articles/storsimple/storsimple-update-device.md#step4) voor instructies.

