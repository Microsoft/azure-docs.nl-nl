---
title: StorSimple Snapshot Manager MMC-menu acties | Microsoft Docs
description: Hierin wordt beschreven hoe u de standaard acties van het menu micro soft Management Console (MMC) in StorSimple Snapshot Manager gebruikt.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 01064c3668b673baea7aedd9a65c92b03c48dc10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90056000"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Gebruik de acties van het MMC-menu in StorSimple Snapshot Manager

## <a name="overview"></a>Overzicht
In StorSimple Snapshot Manager worden de volgende acties weer gegeven in alle actie menu's en alle variaties van het deel venster **acties** .

* Weergave
* Nieuw venster vanaf hier 
* Vernieuwen 
* Lijst exporteren 
* Help 

Deze acties maken deel uit van de micro soft Management Console (MMC) en zijn niet specifiek voor StorSimple Snapshot Manager. In deze zelf studie worden deze acties beschreven en wordt uitgelegd hoe u deze kunt gebruiken in StorSimple Snapshot Manager.

## <a name="view"></a>Weergave
U kunt de **weergave** optie gebruiken om de weer gave van het deel venster met **resultaten** te wijzigen en de weer gave van het console venster te wijzigen. 

#### <a name="to-change-the-results-pane-view"></a>De weer gave van het resultaten venster wijzigen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** met de rechter muisknop op een knoop punt of vouw het knoop punt uit en klik met de rechter muisknop op een item in het **resultaten** venster en klik vervolgens op de **weergave** optie. 
3. Klik op **kolommen toevoegen/verwijderen** om de kolommen toe te voegen aan of te verwijderen die worden weer gegeven in het **resultaten** venster. Het dialoog venster **kolommen toevoegen/verwijderen** wordt weer gegeven.
   
    ![Kolommen toevoegen aan of verwijderen uit het resultaten venster](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Vul het formulier als volgt in:
   
   * Selecteer items in de lijst **beschik bare** kolommen en klik op **toevoegen** om ze toe te voegen aan de lijst met **weer gegeven kolommen** . 
   * Klik op items in de lijst met **weer gegeven kolommen** en klik op **verwijderen** om ze uit de lijst te verwijderen. 
   * Selecteer een item in de lijst met **weer gegeven** kolommen en klik op **omhoog** of **omlaag** om het item omhoog of omlaag te verplaatsen in de lijst. 
   * Klik op **standaard waarden herstellen** om terug te keren naar de standaard configuratie van het **resultaten** venster. 
5. Wanneer u klaar bent met uw selecties, klikt u op **OK**. 

#### <a name="to-change-the-console-window-view"></a>De weer gave van het console venster wijzigen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** met de rechter muisknop op een knoop punt, klik op **weer geven** en klik vervolgens op **aanpassen**. Het dialoog venster **aanpassen** wordt weer gegeven.
   
    ![Het console venster aanpassen](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Schakel de selectie vakjes in of uit om items in het console venster weer te geven of te verbergen. Wanneer u klaar bent met uw selecties, klikt u op **OK**.

## <a name="new-window-from-here"></a>Nieuw venster vanaf hier
U kunt het **nieuwe venster van de volgende** optie gebruiken om een nieuw console venster te openen.

#### <a name="to-open-a-new-console-window"></a>Een nieuw console venster openen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** met de rechter muisknop op een knoop punt en klik vervolgens op **nieuw venster**. 
   
    Er wordt een nieuw venster weer gegeven met alleen het bereik dat u hebt geselecteerd. Als u bijvoorbeeld met de rechter muisknop op het knoop punt **back-upbeleid** , in het venster Nieuw wordt alleen het knoop punt **back-upbeleid** weer gegeven in het deel venster **bereik** en een lijst met gedefinieerde back-upbeleid in het **resultaten** venster. Zie het volgende voorbeeld
   
    ![Nieuw venster vanaf hier](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Vernieuwen
U kunt de actie **vernieuwen** gebruiken om het console venster bij te werken.

#### <a name="to-update-the-console-window"></a>Het console venster bijwerken
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. In het deel venster **bereik** klikt u met de rechter muisknop op een knoop punt of vouwt u het knoop punt uit en klikt u met de rechter muisknop op een item in het **resultaten** venster en klikt u vervolgens op **vernieuwen**. 

## <a name="export-list"></a>Lijst exporteren
U kunt de actie **lijst exporteren** gebruiken om een lijst op te slaan in een bestand met door komma's gescheiden waarden (CSV). U kunt bijvoorbeeld de lijst met back-upbeleid of de back-catalogus exporteren. U kunt het CSV-bestand vervolgens importeren in een spreadsheet toepassing voor analyse.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Een lijst opslaan in een bestand met door komma's gescheiden waarden (CSV)
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten. 
2. In het deel venster **bereik** klikt u met de rechter muisknop op een knoop punt of vouwt u het knoop punt uit en klikt u met de rechter muisknop op een item in het **resultaten** venster en klikt u vervolgens op **lijst exporteren**. 
3. Het dialoog venster **lijst exporteren** wordt weer gegeven. Vul het formulier als volgt in: 
   
   1. Typ in het vak **Bestands naam** een naam voor het CSV-bestand of klik op de pijl om deze te selecteren in de vervolg keuzelijst.
   2. Klik in het vak **Opslaan als type** op de pijl en selecteer een bestands type in de vervolg keuzelijst.
   3. Als u alleen geselecteerde items wilt opslaan, selecteert u de rijen en klikt u vervolgens op het selectie vakje **alleen geselecteerde rijen opslaan** . Als u alle geëxporteerde lijsten wilt opslaan, schakelt u het selectie vakje **alleen geselecteerde rijen opslaan** uit.
   4. Klik op **Opslaan**.
      
      ![Lijst exporteren als een bestand met door komma's gescheiden waarden](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Help
U kunt het menu **Help** gebruiken om de beschik bare online-Help voor StorSimple Snapshot Manager en de MMC weer te geven.

#### <a name="to-view-available-online-help"></a>Beschik bare online-Help weer geven
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. In het deel venster **bereik** klikt u met de rechter muisknop op een knoop punt of vouwt u het knoop punt uit en klikt u met de rechter muisknop op een item in het **resultaten** venster en klikt u vervolgens op **Help**. 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [StorSimple-gebruikers interface van Snapshot Manager](storsimple-use-snapshot-manager.md).
* Meer informatie over [het gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).

