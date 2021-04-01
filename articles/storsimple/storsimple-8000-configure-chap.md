---
title: CHAP configureren voor StorSimple 8000 Series-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u de Challenge Handshake Authentication Protocol (CHAP) configureert op een StorSimple-apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 694d63dacedd8ffcd075e73bb91b5ab0a089b58c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766499"
---
# <a name="configure-chap-for-your-storsimple-device"></a>CHAP configureren voor uw StorSimple-apparaat

In deze zelf studie wordt uitgelegd hoe u CHAP configureert voor uw StorSimple-apparaat. De procedure die in dit artikel wordt beschreven, is van toepassing op apparaten uit de StorSimple 8000-serie.

CHAP staat voor Challenge Handshake Authentication Protocol. Het is een verificatie schema dat door servers wordt gebruikt om de identiteit van externe clients te valideren. De verificatie is gebaseerd op een gedeeld wacht woord of geheim. CHAP kan één manier (unidirectioneel) of wederzijds (bidirectioneel) zijn. Eenrichtings CHAP is wanneer het doel een initiator verifieert. In wederzijds of reverse CHAP verifieert het doel de initiator en vervolgens verifieert de initiator het doel. Initiator verificatie kan worden geïmplementeerd zonder doel verificatie. Doel verificatie kan echter alleen worden geïmplementeerd als de authenticatie van de initiator ook is geïmplementeerd.

Als best practice wordt u aangeraden CHAP te gebruiken om iSCSI-beveiliging te verbeteren.

> [!NOTE]
> Houd er wel van uit dat IPSEC momenteel niet wordt ondersteund op StorSimple-apparaten.

De CHAP-instellingen op het StorSimple-apparaat kunnen op de volgende manieren worden geconfigureerd:

* Eenrichtings-of eenrichtings verificatie
* Bidirectionele of wederzijdse of omgekeerde verificatie

In elk van deze gevallen moet de portal voor het apparaat en de iSCSI-initiator software van de server worden geconfigureerd. De gedetailleerde stappen voor deze configuratie worden beschreven in de volgende zelf studie.

## <a name="unidirectional-or-one-way-authentication"></a>Eenrichtings-of eenrichtings verificatie

Bij een eenrichtings verificatie verifieert het doel de initiator. Voor deze verificatie moet u de instellingen van de CHAP-initiator op het StorSimple-apparaat en de iSCSI-initiator software op de host configureren. De gedetailleerde procedures voor uw StorSimple-apparaat en Windows-host worden hierna beschreven.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Uw apparaat configureren voor eenrichtings verificatie

1. Ga in het Azure Portal naar uw StorSimple Apparaatbeheer-service. Klik op **apparaten** en selecteer en klik op het apparaat waarvoor u CHAP wilt configureren. Ga naar **apparaatinstellingen > beveiliging**. Klik op de Blade **beveiligings instellingen** op **CHAP**.
   
    ![CHAP Initiator](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. In de **CHAP** -Blade en in de sectie **CHAP initiator** :
   
   1. Geef een gebruikers naam op voor uw CHAP-initiator.
   2. Geef een wacht woord op voor uw CHAP-initiator.
      
      > [!IMPORTANT]
      > De CHAP-gebruikers naam moet minder dan 233 tekens bevatten. Het CHAP-wacht woord moet tussen de 12 en 16 tekens lang zijn. Een langere gebruikers naam of wacht woord resulteert in een verificatie fout op de Windows-host.
   
   3. Bevestig het wachtwoord.

       ![CHAP Initiator 2](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klik op **Opslaan**. Er wordt een bevestigings bericht weer gegeven. Klik op **OK** om de wijzigingen op te slaan.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Eenrichtings verificatie configureren op de Windows-hostserver
1. Start de iSCSI-initiator op de Windows-hostserver.
2. In het venster **Eigenschappen iSCSI-initiator** voert u de volgende stappen uit:
   
   1. Klik op het tabblad **detectie** .
      
       ![Eigenschappen iSCSI-initiator](./media/storsimple-configure-chap/IC740944.png)
   2. Klik op **Portal ontdekken**.
3. In het dialoog venster **doel Portal detecteren** :
   
   1. Geef het IP-adres van uw apparaat op.
   2. Klik op **Geavanceerd**.
      
       ![Doel Portal detecteren](./media/storsimple-configure-chap/IC740945.png)
4. In het dialoog venster **Geavanceerde instellingen** :
   
   1. Schakel het selectie vakje **CHAP-aanmelding inschakelen in** .
   2. Geef in het veld **naam** de gebruikers naam op die u hebt opgegeven voor de CHAP initiator in het Azure Portal.
   3. Geef in het veld **doel geheim** het wacht woord op dat u hebt opgegeven voor de CHAP initiator in het Azure Portal.
   4. Klik op **OK**.
      
       ![Geavanceerde instellingen algemeen](./media/storsimple-configure-chap/IC740946.png)
5. Op het tabblad **doelen** van het venster **Eigenschappen van iSCSI-initiator** moet de apparaatstatus worden weer gegeven als **verbonden**. Als u een StorSimple 1200-apparaat gebruikt, wordt elk volume gekoppeld als een iSCSI-doel. Daarom moeten stap 3-4 voor elk volume worden herhaald.
   
    ![Volumes die zijn gekoppeld als afzonderlijke doelen](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Als u de iSCSI-naam wijzigt, wordt de nieuwe naam gebruikt voor nieuwe iSCSI-sessies. Nieuwe instellingen worden pas gebruikt voor bestaande sessies als u zich afmeldt en opnieuw aanmeldt.

Ga naar [aanvullende overwegingen](#additional-considerations)voor meer informatie over het configureren van CHAP op de Windows-hostserver.

## <a name="bidirectional-or-mutual-authentication"></a>Bidirectionele of wederzijdse verificatie

In bidirectionele verificatie verifieert het doel de initiator en vervolgens verifieert de initiator het doel. Voor deze procedure moet de gebruiker de instellingen van de CHAP-initiator, de instellingen voor omgekeerde CHAP op het apparaat en de iSCSI-initiator software op de host configureren. In de volgende procedures worden de stappen beschreven voor het configureren van wederzijdse verificatie op het apparaat en op de Windows-host.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Uw apparaat configureren voor wederzijdse verificatie

1. Ga in het Azure Portal naar uw StorSimple Apparaatbeheer-service. Klik op **apparaten** en selecteer en klik op het apparaat waarvoor u CHAP wilt configureren. Ga naar **apparaatinstellingen > beveiliging**. Klik op de Blade **beveiligings instellingen** op **CHAP**.
   
    ![CHAP-doel](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Schuif omlaag op deze pagina en in de sectie **CHAP-doel** :
   
   1. Geef een **omgekeerde CHAP-gebruikers naam** op voor uw apparaat.
   2. Geef een **Reverse CHAP-wacht woord** op voor uw apparaat.
   3. Bevestig het wachtwoord.
3. In het gedeelte **CHAP initiator** :
   
   1. Geef een **gebruikers naam** op voor het apparaat.
   2. Geef een **wacht woord** op voor uw apparaat.
   3. Bevestig het wachtwoord.

       ![CHAP Initiator 3](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klik op **Opslaan**. Er wordt een bevestigings bericht weer gegeven. Klik op **OK** om de wijzigingen op te slaan.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Bidirectionele verificatie configureren op de Windows-hostserver

1. Start de iSCSI-initiator op de Windows-hostserver.
2. Klik in het venster **Eigenschappen van iSCSI-initiator** op het tabblad **configuratie** .
3. Klik op **CHAP**.
4. In het dialoog venster **wederzijds CHAP-geheim van iSCSI-initiator** :
   
   1. Typ het **omgekeerde CHAP-wacht woord** dat u hebt geconfigureerd in de Azure Portal.
   2. Klik op **OK**.
      
       ![wederzijds CHAP-geheim van iSCSI-initiator](./media/storsimple-configure-chap/IC740949.png)
5. Klik op het tabblad **doelen** .
6. Klik op de knop **Verbinding maken**. 
7. Klik in het dialoog venster **verbinding maken met doel** op **Geavanceerd**.
8. In het dialoog venster **Geavanceerde eigenschappen** :
   
   1. Schakel het selectie vakje **CHAP-aanmelding inschakelen in** .
   2. Geef in het veld **naam** de gebruikers naam op die u hebt opgegeven voor de CHAP initiator in het Azure Portal.
   3. Geef in het veld **doel geheim** het wacht woord op dat u hebt opgegeven voor de CHAP initiator in het Azure Portal.
   4. Schakel het selectie vakje **wederzijdse verificatie uitvoeren** in.
      
       ![Geavanceerde instellingen wederzijdse verificatie](./media/storsimple-configure-chap/IC740950.png)
   5. Klik op **OK** om de CHAP-configuratie te volt ooien

Ga naar [aanvullende overwegingen](#additional-considerations)voor meer informatie over het configureren van CHAP op de Windows-hostserver.

## <a name="additional-considerations"></a>Aanvullende overwegingen

De functie **snelle verbinding** biedt geen ondersteuning voor verbindingen waarvoor CHAP is ingeschakeld. Wanneer CHAP is ingeschakeld, moet u ervoor zorgen dat u de knop **verbinding maken** gebruikt die beschikbaar is op het tabblad **doelen** om verbinding te maken met een doel.

![Verbinding maken met doel](./media/storsimple-configure-chap/IC740947.png)

Schakel in het dialoog venster **verbinding maken met doel** dat wordt weer gegeven het selectie vakje **deze verbinding aan de lijst met favoriete doelen toevoegen** in. Deze selectie zorgt ervoor dat telkens wanneer de computer opnieuw wordt opgestart, wordt geprobeerd de verbinding met de iSCSI-doel doelen te herstellen.

## <a name="errors-during-configuration"></a>Fouten tijdens de configuratie

Als uw CHAP-configuratie onjuist is, ziet u waarschijnlijk een fout bericht over een **verificatie** fout.

## <a name="verification-of-chap-configuration"></a>Verificatie van de CHAP-configuratie

U kunt controleren of CHAP wordt gebruikt door de volgende stappen uit te voeren.

#### <a name="to-verify-your-chap-configuration"></a>De CHAP-configuratie controleren
1. Klik op **favoriete doelen**.
2. Selecteer het doel waarvoor u verificatie hebt ingeschakeld.
3. Klik op **Details**.
   
    ![eigenschappen favoriete doelen iSCSI-initiator](./media/storsimple-configure-chap/IC740951.png)
4. In het dialoog venster **doel Details van favoriet** ziet u de vermelding in het veld **verificatie** . Als de configuratie is geslaagd, moet **CHAP** worden gedicteerd.
   
    ![Details van favoriet doel](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [StorSimple-beveiliging](storsimple-8000-security.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

