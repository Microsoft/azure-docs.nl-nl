---
title: Beheerders wachtwoord voor StorSimple virtuele matrix wijzigen | Microsoft Docs
description: Hierin wordt beschreven hoe u de Web-UI van de Azure Portal-of StorSimple virtuele matrix kunt gebruiken om het beheerders wachtwoord van het apparaat te wijzigen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bc8846d546faec194617ccb753cdbd105e16bf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85513617"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Wijzig de StorSimple van de virtuele matrix van het apparaat met StorSimple Apparaatbeheer

## <a name="overview"></a>Overzicht

Wanneer u de Windows Power shell-interface gebruikt voor toegang tot de virtuele StorSimple-matrix, moet u een beheerders wachtwoord voor het apparaat opgeven. Wanneer het StorSimple-apparaat voor het eerst wordt ingericht en gestart, is het standaard wachtwoord *Wachtwoord1*. Voor de beveiliging van uw gegevens verloopt het standaard wachtwoord de eerste keer dat u zich aanmeldt en moet u dit wacht woord wijzigen.

U kunt ook de lokale webgebruikersinterface of de Azure Portal gebruiken om het beheerders wachtwoord van het apparaat op elk gewenst moment te wijzigen nadat het apparaat in uw productie omgeving is geïmplementeerd. Elk van deze procedures wordt beschreven in dit artikel.

 ![Blade apparaten](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Het Azure Portal gebruiken om het wacht woord te wijzigen

Voer de volgende stappen uit om het beheerders wachtwoord van het apparaat te wijzigen via de Azure Portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Het beheerders wachtwoord voor het apparaat wijzigen via de Azure Portal

1. Selecteer uw service op de pagina landings aanvoer, dubbel klik op de service naam en klik vervolgens in de sectie **beheer** op **apparaten**. Hiermee opent u de Blade **apparaten** met een lijst met alle virtuele StorSimple-apparaten.

2. Op de Blade **apparaten** dubbelklikt u op het apparaat waarvoor het wacht woord moet worden gewijzigd.

3. Klik op de Blade **instellingen** voor uw apparaat op **beveiliging**.

4. Ga als volgt te werk op de Blade **beveiligings instellingen** :
   
   1. Schuif omlaag naar de sectie **wacht woord** van het apparaat. Geef een beheerders wachtwoord op dat uit 8 tot 15 tekens bestaat.
   2. Bevestig het wachtwoord.
   3. Klik op **Opslaan** boven aan de blade.

Het beheerders wachtwoord voor het apparaat wordt nu bijgewerkt. U kunt dit gewijzigde wacht woord gebruiken om lokaal toegang te krijgen tot het apparaat.

![Blade beveiligings instellingen](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>De lokale web-UI gebruiken om het wacht woord te wijzigen

Voer de volgende stappen uit om het beheerders wachtwoord van het apparaat te wijzigen via de lokale web-UI.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Het beheerders wachtwoord voor het apparaat wijzigen via de lokale webgebruikersinterface

1. Klik in de lokale web-UI op **onderhoud**  >  **wachtwoord wijziging** voor uw apparaat.
   
    ![Wachtwoord1 wijzigen](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Voer het **huidige wacht woord** in.
3. Geef een **Nieuw wacht woord** op. Het wacht woord moet ten minste acht tekens lang zijn. De naam moet 3 van 4 van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale letters.
   
    Uw wacht woord mag niet gelijk zijn aan de laatste 24 wacht woorden.
4. Voer het wachtwoord opnieuw in ter bevestiging.
   
    ![password2 wijzigen](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Klik onder aan de pagina op **Toep assen**. Het nieuwe wacht woord wordt nu toegepast. Als het wacht woord niet kan worden gewijzigd, ziet u de volgende fout:
   
    ![wachtwoord fout](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Nadat het wacht woord is bijgewerkt, wordt u hiervan op de hoogte gebracht. U kunt dit gewijzigde wacht woord vervolgens gebruiken om lokaal toegang te krijgen tot het apparaat.


## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [beheren van uw StorSimple Virtual array](storsimple-ova-web-ui-admin.md).

