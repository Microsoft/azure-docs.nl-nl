---
title: Apparaten beheren met StorSimple Snapshot Manager | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple Snapshot Manager MMC-module gebruikt om verbinding te maken met StorSimple-apparaten en deze te beheren.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dbb7e835d110bcb6cb81fd5425a2aafbb578e692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022969"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>StorSimple Snapshot Manager gebruiken om verbinding te maken en StorSimple-apparaten te beheren
## <a name="overview"></a>Overzicht
U kunt knoop punten gebruiken in het StorSimple-deel venster voor het **bereik** van Snapshot Manager om geïmporteerde StorSimple te controleren en de aangesloten opslag apparaten te vernieuwen. Wanneer u bovendien op het knoop punt **apparaten** klikt, ziet u een lijst met verbonden apparaten en bijbehorende status informatie in het **resultaten** venster.

![Verbonden apparaten](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Afbeelding 1: StorSimple Snapshot Manager verbonden apparaat** 

Afhankelijk van de selecties van uw **weer gave** ziet u in het deel venster met **resultaten** de volgende informatie over elk apparaat. (Ga naar het [menu weer geven](storsimple-use-snapshot-manager.md#view-menu)voor meer informatie over het configureren van een weer gave.

| Kolom met resultaten | Beschrijving |
|:--- |:--- |
| Name |De naam van het apparaat zoals geconfigureerd in de klassieke Azure-Portal |
| Model |Het model nummer van het apparaat |
| Versie |De versie van de software die op het apparaat is geïnstalleerd |
| Status |Of het apparaat beschikbaar is |
| Laatst gesynchroniseerd op |De datum en tijd waarop het apparaat voor het laatst is gesynchroniseerd |
| Serienr. |Het serie nummer van het apparaat |

Als u met de rechter muisknop op het knoop punt **apparaten** in het deel venster **bereik** klikt, kunt u kiezen uit de volgende acties:

* Een apparaat toevoegen of vervangen
* Verbinding maken met een apparaat en de import bewerkingen controleren
* Verbonden apparaten vernieuwen

Als u op het knoop punt **apparaten** klikt en vervolgens met de rechter muisknop op een apparaatnaam in het **resultaten** venster, kunt u een van de volgende acties selecteren:

* Een apparaat verifiëren
* Apparaatdetails weergeven
* Een apparaat vernieuwen
* Een apparaatconfiguratie verwijderen
* Een apparaatwachtwoord wijzigen

> [!NOTE]
> Al deze acties zijn ook beschikbaar in het deel venster **acties** .


In deze zelf studie wordt uitgelegd hoe u StorSimple Snapshot Manager gebruikt om apparaten te verbinden en te beheren en om de volgende taken uit te voeren:

* Een apparaat toevoegen of vervangen
* Verbinding maken met een apparaat en de import bewerkingen controleren
* Verbonden apparaten vernieuwen
* Een apparaat verifiëren
* Apparaatdetails weergeven
* Een afzonderlijk apparaat vernieuwen
* Een apparaatconfiguratie verwijderen
* Een verlopen apparaatwachtwoord wijzigen
* Een mislukt apparaat vervangen

> [!NOTE]
> Ga naar [StorSimple Snapshot Manager gebruikers interface](storsimple-use-snapshot-manager.md)voor algemene informatie over het gebruik van de StorSimple Snapshot Manager-interface.


## <a name="add-or-replace-a-device"></a>Een apparaat toevoegen of vervangen
Gebruik de volgende procedure om een StorSimple-apparaat toe te voegen of te vervangen.

#### <a name="to-add-or-replace-a-device"></a>Een apparaat toevoegen of vervangen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik met de rechter muisknop op het knoop punt **apparaten** in het deel venster **bereik** en klik vervolgens op **een apparaat configureren**. Het dialoog venster **een apparaat configureren** wordt weer gegeven.
   
    ![Een StorSimple-apparaat configureren](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Selecteer in de vervolg keuzelijst **apparaat** het IP-adres van het apparaat of het virtuele apparaat. 
4. Typ in het tekstvak **wacht woord** het StorSimple Snapshot Manager wacht woord dat u hebt gemaakt voor het apparaat in de klassieke Azure-Portal. Klik op **OK**. StorSimple Snapshot Manager zoekt naar het apparaat dat u hebt geïdentificeerd. 
   
   * Als het apparaat beschikbaar is, wordt door StorSimple Snapshot Manager een verbinding toegevoegd.
   * Als het apparaat om de een of andere reden niet beschikbaar is, retourneert StorSimple Snapshot Manager een fout bericht. Klik op **OK** om het fout bericht te sluiten en klik vervolgens op **Annuleren** om het dialoog venster **een apparaat configureren** te sluiten.

## <a name="connect-a-device-and-verify-imports"></a>Verbinding maken met een apparaat en de import bewerkingen controleren
Gebruik de volgende procedure om verbinding te maken met een StorSimple-apparaat en te controleren of alle bestaande volume groepen waaraan back-ups zijn gekoppeld, zijn geïmporteerd.

#### <a name="to-connect-a-device-and-verify-imports"></a>Verbinding maken met een apparaat en de import bewerkingen controleren
1. Als u een apparaat wilt verbinden met StorSimple Snapshot Manager, volgt u de instructies in een apparaat toevoegen of vervangen. Wanneer verbinding wordt gemaakt met een apparaat, StorSimple Snapshot Manager reageert als volgt:
   
   * Als het apparaat om de een of andere reden niet beschikbaar is, retourneert StorSimple Snapshot Manager een fout bericht. 
   
   * Als het apparaat beschikbaar is, wordt door StorSimple Snapshot Manager een verbinding toegevoegd. Wanneer u het apparaat selecteert, wordt het weer gegeven in het **resultaten** venster en geeft het veld status aan dat het apparaat **beschikbaar** is. StorSimple Snapshot Manager alle volume groepen die zijn geconfigureerd voor het apparaat worden geïmporteerd, mits de volume groepen gekoppelde back-ups hebben. Het back-upbeleid is niet geïmporteerd. Volume groepen waaraan geen back-ups zijn gekoppeld, worden niet geïmporteerd.
2. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
3. Klik met de rechter muisknop op het bovenste knoop punt in het deel venster **bereik** en klik vervolgens op **in-/uitschakelen import weergave**.
   
    ![Schakel de optie invoer in-/uitschakelen](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Het dialoog venster in- **/uitschakelen** wordt weer gegeven, waarin de status van de geïmporteerde volume groepen en back-ups wordt weer gegeven. Klik op **OK**.

Nadat de volume groepen en back-ups zijn geïmporteerd, kunt u StorSimple Snapshot Manager gebruiken om ze te beheren, net zoals u volume groepen en back-ups beheert die u hebt gemaakt en geconfigureerd met StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Verbonden apparaten vernieuwen
Gebruik de volgende procedure om de verbonden StorSimple-apparaten te synchroniseren met StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Verbonden apparaten vernieuwen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** met de rechter muisknop op **apparaten** en klik vervolgens op **apparaten vernieuwen**. Hiermee worden de verbonden apparaten gesynchroniseerd met StorSimple Snapshot Manager zodat u de volume groepen en back-ups kunt weer geven, inclusief eventuele recente toevoegingen. 
   
    ![De StorSimple-apparaten vernieuwen](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Met de actie **apparaten vernieuwen** worden nieuwe volume groepen en eventuele gekoppelde back-ups van verbonden apparaten opgehaald. In tegens telling tot de actie **volumes opnieuw scannen** die beschikbaar is voor het knoop punt **volumes** , wordt het back-upregister niet teruggezet door **apparaten te vernieuwen** .

## <a name="authenticate-a-device"></a>Een apparaat verifiëren
Gebruik de volgende procedure om een StorSimple-apparaat te verifiëren met StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Een apparaat verifiëren
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** op **apparaten**.
3. Klik in het **resultaten** venster met de rechter muisknop op de naam van het apparaat en klik vervolgens op **verifiëren**.
4. Het dialoog venster **verificatie** wordt weer gegeven. Typ het wacht woord van het apparaat en klik vervolgens op **OK**.
   
    ![Het dialoog venster verifiëren](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Apparaatdetails weergeven
Gebruik de volgende procedure om de details van een StorSimple-apparaat weer te geven en, indien nodig, het apparaat opnieuw te synchroniseren met StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Details van het apparaat weer geven en opnieuw synchroniseren
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** op **apparaten**.
3. Klik in het **resultaten** venster met de rechter muisknop op de naam van het apparaat en klik vervolgens op **Details**.

4. het dialoog venster **Details van apparaat** wordt weer gegeven. In dit vak worden de naam, het model, de versie, het serie nummer, de status, het doel iSCSI Qualified Name (IQN) en de datum en tijd van de laatste synchronisatie weer gegeven.

* Klik op **Resync** om het apparaat te synchroniseren.
* Klik op **OK** of **Annuleren** om het dialoog venster te sluiten.
  
  ![Apparaatgegevens](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Een afzonderlijk apparaat vernieuwen
Gebruik de volgende procedure om een afzonderlijk StorSimple-apparaat te synchroniseren met StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Een apparaat vernieuwen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten. 
2. Klik in het deel venster **bereik** op **apparaten**. 
3. Klik in het **resultaten** venster met de rechter muisknop op de naam van het apparaat en klik vervolgens op **apparaat vernieuwen**. Hiermee wordt het apparaat gesynchroniseerd met StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Een apparaatconfiguratie verwijderen
Gebruik de volgende procedure om een individuele configuratie van een StorSimple-apparaat te verwijderen uit StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Een apparaatconfiguratie verwijderen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** op **apparaten**. 
3. Klik in het **resultaten** venster met de rechter muisknop op de naam van het apparaat en klik vervolgens op **verwijderen**. 
4. Het volgende bericht wordt weer gegeven. Klik op **Ja** om de configuratie te verwijderen of klik op **Nee** om de verwijdering te annuleren.
   
    ![Apparaatconfiguratie verwijderen](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Een verlopen apparaatwachtwoord wijzigen
U moet een wacht woord invoeren om een StorSimple-apparaat te verifiëren met StorSimple Snapshot Manager. U kunt dit wacht woord configureren wanneer u de Windows Power shell-interface gebruikt om het apparaat in te stellen. Het wacht woord kan echter verlopen. Als dit het geval is, kunt u de klassieke Azure-Portal gebruiken om het wacht woord te wijzigen. Omdat het apparaat is geconfigureerd in StorSimple Snapshot Manager voordat het wacht woord is verlopen, moet u het apparaat opnieuw verifiëren in StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Het verlopen wacht woord wijzigen
1. Start de StorSimple Manager-service in de klassieke Azure-Portal.
2. Klik op **apparaten**  >  **configureren** voor het apparaat.
3. Schuif omlaag naar de sectie StorSimple Snapshot Manager. Voer een wacht woord in van 14-15 tekens. Zorg ervoor dat het wacht woord een combi natie van hoofd letters, kleine letters, cijfers en speciale tekens bevat.
4. Voer het wacht woord opnieuw in om dit te bevestigen.
5. Klik op **Opslaan** onder aan de pagina.

#### <a name="to-re-authenticate-the-device"></a>Het apparaat opnieuw verifiëren
1. Start StorSimple Snapshot Manager.
2. Klik in het deel venster **bereik** op **apparaten**. Er wordt een lijst met geconfigureerde apparaten weer gegeven in het **resultaten** venster.
3. Selecteer het apparaat, klik met de rechter muisknop en klik vervolgens op **verifiëren**.
4. Voer in het venster **verifiëren** het nieuwe wacht woord in.
5. Selecteer het apparaat, klik met de rechter muisknop en selecteer **apparaat vernieuwen**. Hiermee wordt het apparaat gesynchroniseerd met StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Een mislukt apparaat vervangen
Als een StorSimple-apparaat mislukt en wordt vervangen door een apparaat voor stand-by (failover), gebruikt u de volgende stappen om verbinding te maken met het nieuwe apparaat en de bijbehorende back-ups weer te geven.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Verbinding maken met een nieuw apparaat na een failover
1. Configureer de iSCSI-verbinding met het nieuwe apparaat opnieuw. Ga naar ' stap 7: een volume koppelen, initialiseren en Format teren ' in [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor instructies.

> [!NOTE]
> Als het nieuwe StorSimple-apparaat hetzelfde IP-adres heeft als het oude, kunt u mogelijk verbinding maken met de oude configuratie.


1. Stop de micro soft StorSimple Management-service:
   
   1. Start Serverbeheer.
   2. Selecteer op het Serverbeheer-dash board de optie **Services** in het menu **extra** .
   3. Selecteer de **micro soft StorSimple Management-service** in het venster **Services** .
   4. Klik in het rechterdeel venster onder **micro soft StorSimple Management service** op **de service stoppen**.
2. Verwijder de configuratie-informatie met betrekking tot het oude apparaat:
   
   1. Ga in Verkenner naar C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Verwijder de bestanden in de map BACatalog.
3. Start de micro soft StorSimple Management-service opnieuw:
   
   1. Selecteer op het Serverbeheer-dash board de optie **Services** in het menu **extra** .
   2. Selecteer de **micro soft StorSimple Management-service** in het venster **Services** .
   3. Klik in het rechterdeel venster onder **micro soft StorSimple Management service** op **Start de service opnieuw**.
4. Start StorSimple Snapshot Manager.
5. Als u het nieuwe StorSimple-apparaat wilt configureren, voert u de stappen uit in stap 2: verbinding maken met een StorSimple-apparaat in [Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Klik met de rechter muisknop op het knoop punt op het hoogste niveau in het deel venster **bereik** (StorSimple Snapshot Manager in het voor beeld) en klik vervolgens op **in-/uitschakelen invoer weer geven**. 
7. Er wordt een bericht weer gegeven wanneer de geïmporteerde volume groepen en back-ups zichtbaar zijn in StorSimple Snapshot Manager. Klik op **OK**.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om volumes weer te geven en te beheren](storsimple-snapshot-manager-manage-volumes.md).

