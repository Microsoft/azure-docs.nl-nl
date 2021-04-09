---
title: Batterij vervangen door Microsoft Azure StorSimple serie apparaat 8000
description: Hierin wordt beschreven hoe u de module back-upbatterij op uw StorSimple-apparaat kunt verwijderen, vervangen en onderhouden.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023768"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Noodaccumodule van StorSimple-apparaat vervangen

## <a name="overview"></a>Overzicht
De primaire behuizing voor voeding en koeling module (PCM) op uw Microsoft Azure StorSimple-apparaat heeft een extra batterij pakket. Dit pakket biedt voeding zodat het StorSimple-apparaat gegevens kan opslaan als er geen netstroom meer is voor de primaire behuizing. Dit batterij pakket wordt de *module back-upbatterij* genoemd. De module back-upbatterij bestaat alleen voor de primaire behuizing in uw StorSimple-apparaat (de EBOD-behuizing bevat geen module voor back-upbatterij).

In deze zelfstudie wordt het volgende uitgelegd:

* De module back-upbatterij verwijderen
* Een nieuwe batterij module voor back-ups installeren
* De module back-upbatterij onderhouden

> [!IMPORTANT]
> Voordat u een back-upbatterij module verwijdert en vervangt, raadpleegt u de informatie over beveiliging in de [Inleiding op StorSimple vervanging](storsimple-8000-hardware-component-replacement.md)van het hardware-onderdeel.


## <a name="remove-the-backup-battery-module"></a>De module back-upbatterij verwijderen
De module back-upbatterij voor uw StorSimple-apparaat is een veld-vervangbaar-eenheid. Voordat deze in de PCM wordt geïnstalleerd, moet de batterij module worden opgeslagen in de oorspronkelijke verpakking. Voer de volgende stappen uit om de back-upbatterij te verwijderen.

#### <a name="to-remove-the-backup-battery-module"></a>De module back-upbatterij verwijderen
1. Ga in het Azure Portal naar de Blade van uw StorSimple Apparaatbeheer-service. Ga naar **apparaten** en selecteer uw apparaat in de lijst met apparaten. Navigeren om de status van hardware te **controleren**  >  . Bekijk de status van de accu onder **gedeelde onderdelen**.
2. Identificeer de PCM waarin de accu is mislukt. In afbeelding 1 wordt de weer gave van het StorSimple-apparaat weer gegeven.
   
    ![Backplane van modules voor primaire behuizing van apparaten](./media/storsimple-battery-replacement/IC740994.png)
   
    **Afbeelding 1** Achtergrond van primair apparaat met PCM-en controller modules
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
   
    Zoals aangegeven door nummer 3 in afbeelding 2, heeft de bewakings indicator op PCM 0, die overeenkomt met **accu fout** , lichter.
   
    ![Backplane van bewakings indicator-Led's van Device PCM](./media/storsimple-battery-replacement/IC740992.png)
   
    **Afbeelding 2** Achterkant van PCM met de Led's voor de bewakings indicator
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Netstroom storing |
   | 2 |Fout met ventilator |
   | 3 |Accu fout |
   | 4 |PCM OK |
   | 5 |Stroom storing in gelijkstroom |
   | 6 |Accu in orde |
3. Als u de PCM met een defecte batterij wilt verwijderen, volgt u de stappen in [een PCM verwijderen](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Terwijl de PCM is verwijderd, til en roteert u de module batterij modules omhoog, zoals aangegeven in de volgende afbeelding, en haalt u deze op om de accu te verwijderen.
   
    ![Batterij verwijderen uit PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Afbeelding 3** De batterij verwijderen uit de PCM
5. Plaats de module in het veld-vervangbaar eenheids pakket.
6. Retour neer de defecte eenheid naar micro soft voor de juiste onderhouds-en verwerking.

## <a name="install-a-new-backup-battery-module"></a>Een nieuwe batterij module voor back-ups installeren
Voer de volgende stappen uit om de vervangende batterij module in de PCM te installeren in de primaire behuizing van uw StorSimple-apparaat.

#### <a name="to-install-the-battery-module"></a>De batterij module installeren
1. Plaats de module back-upbatterij in de juiste stand in de PCM.
2. Druk op de batterij module op alle manier om de connector te seat.
3. Vervang de PCM in de primaire behuizing door de richt lijnen te volgen in [een module voor voeding en koeling vervangen op uw StorSimple-apparaat](storsimple-8000-power-cooling-module-replacement.md).
4. Nadat de vervanging is voltooid, gaat u naar uw apparaat en gaat u naar de status van de hardware **bewaken**  >   in de Azure Portal. Controleer de status van de accu om er zeker van te zijn dat de installatie is geslaagd. Een groene status geeft aan dat de accu in orde is.

## <a name="maintain-the-backup-battery-module"></a>De module back-upbatterij onderhouden
In uw StorSimple-apparaat biedt de module back-upbatterij stroom naar de controller tijdens een stroom storings gebeurtenis. Hiermee kan het StorSimple-apparaat essentiële gegevens opslaan voordat ze op een gecontroleerde manier worden afgesloten. Met twee volledig opgeladen batterijen in de PCMs kan het systeem twee opeenvolgende verlies gebeurtenissen verwerken.

In de Azure Portal geeft de **status** van de hardware onder de Blade **monitor** aan of de accu defect is of dat het einde van de levens duur nadert. De accu status wordt aangegeven door de **batterij in PCM 0** of **batterij in PCM 1** onder **gedeelde onderdelen**. Op deze Blade wordt een **GEdegradeerde** status weer gegeven voor het einde van de levens duur en **is** het einde van de levens duur bereikt.

> [!NOTE]
> De accu **kan rapporteren wanneer** er eenvoudigweg moet worden gefactureerd.


Als de status **verslechterd** wordt weer gegeven, raden we u aan de volgende cursus van actie uit te voeren:

* Het systeem heeft mogelijk een recent stroom verlies ondervonden of de accu's kunnen periodiek onderhoud ondergaan. Bekijk het systeem gedurende 12 uur voordat u doorgaat.
  
  * Als de status nog steeds wordt **GEdegradeerd** na 12 uur doorlopende verbinding met de wisselstroom capaciteit van de controllers en de PCMs, moet de accu worden vervangen. Neem [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor een nieuwe batterij module voor back-ups.
  * Als de status van 12 uur OK is, is de accu operationeel en hebt u alleen onderhouds kosten nodig.
* Als er geen stroom uitval van netstroom is en de PCM is ingeschakeld en aangesloten op netstroom, moet de accu worden vervangen. [Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om een vervangende back-upbatterij module te best Ellen.

> [!IMPORTANT]
> De defecte batterij verwijderen volgens nationale en regionale voor Schriften.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [vervangen van StorSimple](storsimple-8000-hardware-component-replacement.md).

