---
title: Beheer van StorSimple Virtual array Web UI | Microsoft Docs
description: Hierin wordt beschreven hoe u basis taken voor het beheer van apparaten uitvoert via de webgebruikersinterface van de StorSimple-virtuele matrix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 6bea9c9b7807ffb32b6071c968d3186965477009
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000926"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>De Web-UI gebruiken voor het beheren van uw virtuele StorSimple-matrix
![proces stroom instellen](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Overzicht
De zelf studies in dit artikel zijn van toepassing op de Microsoft Azure StorSimple virtuele matrix (ook wel het StorSimple on-premises virtuele apparaat genoemd) met de versie van de algemene Beschik baarheid (GA) van maart 2016. In dit artikel worden enkele van de complexe werk stromen en beheer taken beschreven die kunnen worden uitgevoerd op de virtuele StorSimple-matrix. U kunt de virtuele StorSimple-matrix beheren met behulp van de gebruikers interface van de StorSimple Manager-service (aangeduid als de portal-gebruikers interface) en de lokale web-UI voor het apparaat. Dit artikel is gericht op de taken die u kunt uitvoeren met behulp van de Web-UI.

Dit artikel bevat de volgende zelfstudies:

* De versleutelings sleutel voor service gegevens ophalen
* Problemen met de installatie van de Web-UI oplossen
* Een logboek pakket genereren
* Apparaat uitschakelen of opnieuw opstarten

## <a name="get-the-service-data-encryption-key"></a>De versleutelings sleutel voor service gegevens ophalen
Er wordt een versleutelings sleutel voor service gegevens gegenereerd wanneer u uw eerste apparaat registreert bij de StorSimple Manager-service. Deze sleutel is vervolgens vereist bij de service registratie sleutel om extra apparaten te registreren bij de StorSimple Manager-service.

Als u uw versleutelings sleutel voor service gegevens verkeerd hebt gehaald en deze moet ophalen, voert u de volgende stappen uit in de lokale web-UI van het apparaat dat is geregistreerd bij uw service.

#### <a name="to-get-the-service-data-encryption-key"></a>De versleutelings sleutel voor service gegevens ophalen
1. Verbinding maken met de lokale web-UI. Ga naar **configuratie**-  >  **Cloud instellingen**.
2. Klik onder aan de pagina op **versleutelings sleutel voor service gegevens ophalen**. Er wordt een sleutel weer gegeven. Kopieer deze sleutel en sla deze op.
   
    ![versleutelings sleutel voor service gegevens ophalen 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Problemen met de installatie van de Web-UI oplossen
In sommige gevallen wanneer u het apparaat configureert via de lokale web-UI, worden er mogelijk fouten optreden. Als u dergelijke fouten wilt vaststellen en oplossen, kunt u de diagnostische tests uitvoeren.

#### <a name="to-run-the-diagnostic-tests"></a>De diagnostische tests uitvoeren
1. Ga in de lokale web-UI naar **probleemoplossings**  >  **testen**.
   
    ![Diagnostische gegevens uitvoeren 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Klik onder aan de pagina op **diagnostische tests uitvoeren**. Hiermee worden tests gestart om mogelijke problemen met uw netwerk, apparaat, webproxy, tijd of Cloud instellingen te diagnosticeren. Er wordt een melding weer gegeven dat er tests worden uitgevoerd op het apparaat.
3. Nadat de tests zijn voltooid, worden de resultaten weer gegeven. In het volgende voor beeld worden de resultaten van diagnostische tests weer gegeven. Houd er rekening mee dat de webproxy-instellingen niet op dit apparaat zijn geconfigureerd, waardoor de webproxy test niet is uitgevoerd. Alle andere tests voor netwerk instellingen, DNS-server en tijd instellingen zijn geslaagd.
   
    ![Diagnostische gegevens uitvoeren 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Een logboek pakket genereren
Een logboek pakket bestaat uit alle relevante logboeken die Microsoft Ondersteuning kunnen helpen bij het oplossen van problemen met apparaten. In deze release kan een logboek pakket worden gegenereerd via de lokale webgebruikersinterface.

#### <a name="to-generate-the-log-package"></a>Het logboek pakket genereren
1. Ga in de lokale web-UI naar **Troubleshooting**  >  **systeem logboeken** voor probleem oplossing.
   
    ![logboek pakket 1 genereren](./media/storsimple-ova-web-ui-admin/image31.png)
2. Klik onder aan de pagina op **logboek pakket maken**. Er wordt een pakket van de systeem logboeken gemaakt. Dit kan enkele minuten duren.
   
    ![logboek pakket 2 genereren](./media/storsimple-ova-web-ui-admin/image32.png)
   
    U krijgt een melding wanneer het pakket is gemaakt en de pagina wordt bijgewerkt om de datum en tijd op te geven waarop het pakket is gemaakt.
   
    ![logboek pakket 3 genereren](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klik op **logboek pakket downloaden**. Er wordt een gezipt pakket gedownload op uw systeem.
   
    ![logboek pakket 4 genereren](./media/storsimple-ova-web-ui-admin/image34.png)
4. U kunt het gedownloade logboek pakket uitpakken en de logboek bestanden van het systeem weer geven.

## <a name="shut-down-and-restart-your-device"></a>Sluit het apparaat af en start het opnieuw op
U kunt uw virtuele apparaat afsluiten of opnieuw starten met de lokale webinterface. U kunt het beste de volumes of shares offline halen op de host en vervolgens het apparaat. Hierdoor is de kans op gegevens beschadiging beperkt. 

#### <a name="to-shut-down-your-virtual-device"></a>Het virtuele apparaat afsluiten
1. Ga in de lokale webinterface naar **onderhoud**  >  **energie-instellingen**.
2. Klik onder aan de pagina op **Afsluiten**.
   
    ![apparaat afsluiten 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Er wordt een waarschuwing weer gegeven waarin wordt vermeld dat het apparaat wordt onderbroken door een stop bewerking, wat resulteert in een uitval tijd. Klik op het vinkje ![vinkje](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![waarschuwing voor afsluiten van apparaat](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Er wordt een melding weer gegeven dat het afsluiten is gestart.
   
    ![afsluiten van apparaat gestart](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Het apparaat wordt nu afgesloten. Als u uw apparaat wilt starten, moet u dit doen via Hyper-V-beheer.

#### <a name="to-restart-your-virtual-device"></a>Het virtuele apparaat opnieuw opstarten
1. Ga in de lokale webinterface naar **onderhoud**  >  **energie-instellingen**.
2. Klik onder aan de pagina op **opnieuw opstarten**.
   
    ![apparaat opnieuw opstarten](./media/storsimple-ova-web-ui-admin/image36.png)
3. Er wordt een waarschuwing weer gegeven met de mede deling dat het apparaat opnieuw moet worden opgestart, waardoor alle IOss worden onderbroken, wat leidt tot uitval tijd. Klik op het vinkje ![vinkje](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![waarschuwing voor opnieuw opstarten](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Er wordt een melding weer gegeven dat de computer opnieuw moet worden opgestart.
   
    ![opnieuw opstarten gestart](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Wanneer de computer opnieuw wordt opgestart, wordt de verbinding met de gebruikers interface verbroken. U kunt de herstart controleren door de gebruikers interface periodiek te vernieuwen. U kunt ook de status van het apparaat opnieuw starten bewaken via Hyper-V-beheer.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het gebruik van de StorSimple Manager-service om uw apparaat te beheren](storsimple-virtual-array-manager-service-administration.md).

