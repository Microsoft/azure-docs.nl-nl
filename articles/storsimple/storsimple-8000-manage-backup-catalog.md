---
title: Uw StorSimple-back-upcatalogus beheren | Microsoft Docs
description: Hierin wordt uitgelegd hoe u de pagina StorSimple Apparaatbeheer service Backup Catalog kunt gebruiken om back-upsets weer te geven, te selecteren en te verwijderen.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 6ab4694159c6269f2f72d3d591147d43aef34a0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017369"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>De StorSimple Apparaatbeheer-service gebruiken voor het beheren van uw back-upcatalogus
## <a name="overview"></a>Overzicht
Op de Blade StorSimple Apparaatbeheer service **back-upcatalogus** worden alle back-upsets weer gegeven die worden gemaakt wanneer hand matig of geplande back-ups worden genomen. U kunt deze pagina gebruiken om alle back-ups voor een back-upbeleid of een volume weer te geven, back-ups te selecteren of te verwijderen of een back-up te gebruiken om een volume te herstellen of klonen.

In deze zelf studie wordt uitgelegd hoe u een back-upset kunt weer geven, selecteren en verwijderen. Als u wilt weten hoe u een back-up van uw apparaat kunt herstellen, gaat u naar [het herstellen van uw apparaat vanuit een back-upset](storsimple-8000-restore-from-backup-set-u2.md). Ga voor meer informatie over het klonen van een volume naar [een StorSimple-volume klonen](storsimple-8000-clone-volume-u2.md).

![Back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

De Blade **back-upcatalogus** biedt een query om de selectie van de back-upset te verfijnen. U kunt de opgehaalde back-upsets filteren, op basis van de volgende para meters:

* **Apparaat** : het apparaat waarop de back-upset is gemaakt.
* **Back-upbeleid of volume** : het back-upbeleid of het volume dat is gekoppeld aan deze back-upset.
* **Van en tot,** het datum-en tijds bereik waarop de back-upset is gemaakt.

De gefilterde back-upsets worden vervolgens getabeld op basis van de volgende kenmerken:

* **Naam** : de naam van het back-upbeleid of het volume dat is gekoppeld aan de back-upset.
* **Grootte** : de werkelijke grootte van de back-upset.
* **Gemaakt op** : de datum en tijd waarop de back-ups zijn gemaakt. 
* **Type** : back-upsets kunnen lokale moment opnamen of Cloud momentopnamen zijn. Een lokale moment opname is een back-up van alle volume gegevens die lokaal zijn opgeslagen op het apparaat, terwijl een Cloud momentopname verwijst naar de back-up van volume gegevens die zich in de cloud bevinden. Lokale moment opnamen bieden snellere toegang, terwijl Cloud momentopnamen worden gekozen voor gegevens tolerantie.
* **Gestart door** : de back-ups kunnen automatisch worden geïnitieerd door een planning of hand matig door een gebruiker. U kunt een back-upbeleid gebruiken voor het plannen van back-ups. U kunt ook de optie **back-up** maken gebruiken om hand matig een back-up te maken.

## <a name="list-backup-sets-for-a-backup-policy"></a>Back-upsets weer geven voor een back-upbeleid
Voer de volgende stappen uit om alle back-ups voor een back-upbeleid weer te geven.

#### <a name="to-list-backup-sets"></a>Back-upsets weer geven
1. Ga naar de StorSimple-Apparaatbeheer-service en klik op **back-upcatalogus**.

2. Filter de selecties als volgt:
   
   1. Geef het tijds bereik op.
   2. Selecteer het betreffende apparaat.
   3. Filteren op **back-upbeleid** om de bijbehorende back-ups weer te geven.
   3. Kies in de vervolg keuzelijst back-upbeleid **alle** om alle back-ups op het geselecteerde apparaat weer te geven.
   4. Klik op **Toep assen** om deze query uit te voeren.
      
      De back-ups die zijn gekoppeld aan het geselecteerde back-upbeleid, moeten worden weer gegeven in de lijst met back-upsets.

      ![Ga naar back-upcatalogus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Een back-upset selecteren
Voer de volgende stappen uit om een back-upset voor een volume of back-upbeleid te selecteren.

#### <a name="to-select-a-backup-set"></a>Een back-upset selecteren
1. Ga naar de StorSimple-Apparaatbeheer-service en klik op **back-upcatalogus**.
2. Filter de selecties als volgt:
   
   1. Geef het tijds bereik op. 
   2. Selecteer het betreffende apparaat. 
   3. Filter op volume of back-upbeleid voor de back-up die u wilt selecteren.
   4. Klik op **Toep assen** om deze query uit te voeren.
      
      De back-ups die zijn gekoppeld aan het geselecteerde volume of back-upbeleid, moeten worden weer gegeven in de lijst met back-upsets.

      ![Ga naar back-upcatalogus 2](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecteer en vouw een back-upset uit. U kunt nu de back-upsets zien die zijn gesplitst op de volumes die het bevat. De opties voor **terugzetten** en **verwijderen** zijn beschikbaar via het context menu (Klik met de rechter muisknop) voor de back-upset. U kunt een van deze acties uitvoeren op de back-upset die u hebt geselecteerd.

    ![Ga naar back-upcatalogus 3](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Een back-upset verwijderen
Verwijder een back-up wanneer u de gekoppelde gegevens niet meer wilt behouden. Voer de volgende stappen uit om een back-upset te verwijderen.

#### <a name="to-delete-a-backup-set"></a>Een back-upset verwijderen
 Ga naar de StorSimple-Apparaatbeheer-service en klik op **back-upcatalogus**.
1. Filter de selecties als volgt:
   
   1. Geef het tijds bereik op. 
   2. Selecteer het betreffende apparaat. 
   3. Filter op volume of back-upbeleid voor de back-up die u wilt selecteren.
   4. Klik op **Toep assen** om deze query uit te voeren.
      
      De back-ups die zijn gekoppeld aan het geselecteerde volume of back-upbeleid, moeten worden weer gegeven in de lijst met back-upsets.

      ![Ga naar back-upcatalogus 4](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Selecteer en vouw een back-upset uit. U kunt nu de back-upsets zien die zijn gesplitst op de volumes die het bevat. De opties voor **terugzetten** en **verwijderen** zijn beschikbaar via het context menu (Klik met de rechter muisknop) voor de back-upset. Klik met de rechter muisknop op de geselecteerde back-upset en selecteer **verwijderen** in het context menu.

    ![Ga naar back-upcatalogus 5](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Als u om bevestiging wordt gevraagd, raadpleegt u de weer gegeven informatie en klikt u op **verwijderen**. De geselecteerde back-up wordt definitief verwijderd.

    ![Ga naar back-upcatalogus 6](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. U ontvangt een melding wanneer de verwijdering wordt uitgevoerd en wanneer deze is voltooid. Nadat de verwijdering is voltooid, vernieuwt u de query op deze pagina. De verwijderde back-upset wordt niet meer weer gegeven in de lijst met back-upsets.

    ![Ga naar back-upcatalogus 7](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het gebruik van de back-catalogus om uw apparaat te herstellen vanuit een back-upset](storsimple-8000-restore-from-backup-set-u2.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

