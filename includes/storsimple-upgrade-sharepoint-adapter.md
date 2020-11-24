---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3467a5d5daa300f82c7b81641ab7e262259d9285
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563870"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Upgrade share point 2010 naar share point 2013 en installeer vervolgens de StorSomple-adapter voor share point
> [!IMPORTANT]
> Bestanden die eerder zijn verplaatst naar externe opslag via de resource structuur, zijn pas beschikbaar als de upgrade is voltooid en de resource structuur functie opnieuw is ingeschakeld. Als u de gevolgen van de gebruiker wilt beperken, voert u een upgrade of herinstallatie uit tijdens een gepland onderhouds venster.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Share point 2010 bijwerken naar share point 2013 en vervolgens de adapter installeren
1. In de share point 2010-Farm noteert u het pad van de BLOB-opslag voor de externe BLOBs en de inhouds databases waarvoor de resource structuur is ingeschakeld. 
2. De nieuwe share point 2013-Farm installeren en configureren. 
3. Data bases, toepassingen en site verzamelingen verplaatsen van de share point 2010-Farm naar de nieuwe share point 2013-farm. Ga voor instructies naar [overzicht van het upgrade proces naar share point 2013](/SharePoint/upgrade-and-update/overview-of-the-upgrade-process).
4. Installeer de StorSimple-adapter voor share point op de nieuwe farm. Ga naar [de StorSimple-adapter voor share point installeren](#install-the-storsimple-adapter-for-sharepoint) voor procedures.
5. Met behulp van de informatie die u in stap 1 hebt genoteerd, schakelt u de resource structuur in voor dezelfde set inhouds databases en geeft u het pad op naar de BLOB-opslag die is gebruikt in de share point 2010-installatie. Ga naar [resource structuur configureren](#configure-rbs) voor procedures. Nadat u deze stap hebt voltooid, moeten eerder externe bestanden toegankelijk zijn vanuit de nieuwe farm. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor share point bijwerken
> [!IMPORTANT]
> U moet deze upgrade plannen om te worden uitgevoerd tijdens een gepland onderhouds venster om de volgende redenen:
> 
> * Voorheen externe inhoud is pas beschikbaar als de adapter opnieuw wordt geïnstalleerd.
> * Alle inhoud die naar de site is geüpload nadat u de vorige versie van de StorSimple-adapter voor share point hebt verwijderd, maar voordat u de nieuwe versie installeert, wordt opgeslagen in de inhouds database. Nadat u de nieuwe adapter hebt geïnstalleerd, moet u deze inhoud verplaatsen naar het StorSimple-apparaat. U kunt de micro soft `RBS Migrate()` Power shell-cmdlet opgenomen in share point gebruiken om de inhoud te migreren. Zie [inhoud migreren naar of uit een resource structuur](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14))voor meer informatie. 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor share point bijwerken
1. Verwijder de vorige versie van de StorSimple-adapter voor share point.
   
   > [!NOTE]
   > Hiermee wordt de resource structuur voor de inhouds databases automatisch uitgeschakeld. Bestaande BLOBs blijven echter op het StorSimple-apparaat. Omdat de resource structuur is uitgeschakeld en de BLOBs niet zijn gemigreerd naar de inhouds databases, zullen aanvragen voor deze BLOBs mislukken. 
   > 
   > 
2. Installeer de nieuwe StorSimple-adapter voor share point. De nieuwe adapter herkent automatisch de inhouds databases die eerder zijn in-of uitgeschakeld voor de resource structuur en zullen de vorige instellingen gebruiken.