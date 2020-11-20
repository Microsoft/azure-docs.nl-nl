---
title: Een Microsoft Azure StorSimple virtuele matrix deactiveren en verwijderen | Microsoft Docs
description: Hierin wordt beschreven hoe u het StorSimple-apparaat uit de service verwijdert door het eerst te deactiveren en vervolgens te verwijderen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 84fd1a2d3d0dc4ce6960469ea3212b8ca1d4d07c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956769"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Een virtuele StorSimple-matrix deactiveren en verwijderen

## <a name="overview"></a>Overzicht

Wanneer u een virtuele StorSimple-matrix deactiveert, verbreekt u de verbinding tussen het apparaat en de bijbehorende StorSimple-Apparaatbeheer service. In deze zelfstudie wordt het volgende uitgelegd:

* Een apparaat deactiveren 
* Een gedeactiveerd apparaat verwijderen

De informatie in dit artikel is alleen van toepassing op virtuele StorSimple-matrices. Ga voor meer informatie over de 8000-serie naar hoe u [een apparaat deactiveert of verwijdert](./storsimple-8000-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Wanneer deactiveren?

Deactivering is een permanente bewerking en kan niet ongedaan worden gemaakt. U kunt een gedeactiveerd apparaat niet meer registreren bij de StorSimple-Apparaatbeheer service. Mogelijk moet u een virtuele StorSimple-matrix deactiveren en verwijderen in de volgende scenario's:

* **Geplande failover** : uw apparaat is online en u wilt een failover uitvoeren voor het apparaat. Als u van plan bent om bij te werken naar een groter apparaat, moet u mogelijk een failover uitvoeren voor uw apparaat. Nadat het eigendom van de gegevens is overgedragen en de failover is voltooid, wordt het bron apparaat automatisch verwijderd.
* Niet- **geplande failover** : uw apparaat is offline en u moet een failover uitvoeren voor het apparaat. Dit scenario kan zich voordoen tijdens een nood geval wanneer er sprake is van een storing in het Data Center en uw primaire apparaat niet actief is. U wilt een failover van het apparaat naar een secundair apparaat uitvoeren. Nadat het eigendom van de gegevens is overgedragen en de failover is voltooid, wordt het bron apparaat automatisch verwijderd.
* **Buiten gebruik stellen** : u wilt het apparaat buiten gebruik stellen. Hiervoor moet u het apparaat eerst deactiveren en vervolgens verwijderen. Wanneer u een apparaat deactiveert, hebt u geen toegang meer tot gegevens die lokaal zijn opgeslagen. U kunt alleen toegang krijgen tot de gegevens die zijn opgeslagen in de Cloud en deze herstellen. Als u van plan bent de apparaatgegevens na het deactiveren te houden, moet u een Cloud momentopname maken van al uw gegevens voordat u een apparaat deactiveert. Met deze Cloud momentopname kunt u alle gegevens in een later stadium herstellen.

## <a name="deactivate-a-device"></a>Een apparaat deactiveren

Voer de volgende stappen uit om uw apparaat te deactiveren.

#### <a name="to-deactivate-the-device"></a>Het apparaat deactiveren

1. Ga in uw service naar **Management >-apparaten**. Klik op de Blade **apparaten** op en selecteer het apparaat dat u wilt deactiveren.
   
    ![Apparaat selecteren om te deactiveren](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Klik op de Blade van uw **apparaat-dash board** op **... Meer** en in de lijst, selecteert u **deactiveren**.
   
    ![Klik op deactiveren](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Typ op de Blade **deactiveren** de naam van het apparaat en klik vervolgens op **deactiveren**. 
   
    ![Deactiveren bevestigen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Het deactiveren proces wordt gestart en duurt enkele minuten.
   
    ![Deactiveren wordt uitgevoerd](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Na de deactivering wordt de lijst met apparaten vernieuwd.
   
    ![Deactiveren volt ooien](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    U kunt nu dit apparaat verwijderen.

## <a name="delete-the-device"></a>Het apparaat verwijderen

Een apparaat moet eerst worden gedeactiveerd om het te kunnen verwijderen. Als u een apparaat verwijdert, wordt dit verwijderd uit de lijst met apparaten die zijn verbonden met de service. De service kan het verwijderde apparaat vervolgens niet meer beheren. De gegevens die zijn gekoppeld aan het apparaat blijven echter in de Cloud. Bij deze gegevens worden de kosten in rekening gebracht.

Voer de volgende stappen uit om het apparaat te verwijderen.

#### <a name="to-delete-the-device"></a>Het apparaat verwijderen

1. Ga in uw StorSimple-Apparaatbeheer naar **beheer > apparaten**. Selecteer op de Blade **apparaten** een gedeactiveerd apparaat dat u wilt verwijderen.
2. Klik in de Blade van het **dash board** op **... Meer** en klik vervolgens op **verwijderen**.
   
   ![Te verwijderen apparaat selecteren](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Typ op de Blade **verwijderen** de naam van uw apparaat om het verwijderen te bevestigen en klik vervolgens op **verwijderen**. Als u het apparaat verwijdert, worden de Cloud gegevens die zijn gekoppeld aan het apparaat niet verwijderd. 
   
   ![De verwijdering bevestigen](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Het verwijderen begint en duurt enkele minuten.
   
   ![Verwijderen wordt uitgevoerd](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Nadat het apparaat is verwijderd, kunt u de bijgewerkte lijst met apparaten weer geven.

## <a name="next-steps"></a>Volgende stappen

* Ga voor meer informatie over het uitvoeren van een failover naar [failover en herstel na nood gevallen van uw virtuele StorSimple-matrix](storsimple-virtual-array-failover-dr.md).

* Als u meer wilt weten over het gebruik van de StorSimple Apparaatbeheer-service, gaat u naar [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple virtuele matrix te beheren](storsimple-virtual-array-manager-service-administration.md).