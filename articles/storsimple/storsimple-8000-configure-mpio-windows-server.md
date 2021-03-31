---
title: MPIO configureren voor uw StorSimple-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u Multipath I/O (MPIO) configureert voor uw StorSimple-apparaat dat is verbonden met een host met Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 3d44fada1eddf2d3f80bec085d8a5bf751197eb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968805"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Multipath I/O voor uw StorSimple-apparaat configureren

In deze zelf studie worden de stappen beschreven die u moet volgen om de functie Multipath I/O (MPIO) te installeren en te gebruiken op een host waarop Windows Server 2012 R2 wordt uitgevoerd en die is verbonden met een fysiek StorSimple-apparaat. De richt lijnen in dit artikel zijn alleen van toepassing op fysieke apparaten uit de StorSimple 8000-serie. MPIO wordt momenteel niet ondersteund in een StorSimple Cloud Appliance.

Micro soft heeft ondersteuning ontwikkeld voor de functie Multipath I/O (MPIO) in Windows Server om Maxi maal beschik bare fout tolerante iSCSI-netwerk configuraties te bouwen. MPIO maakt gebruik van redundante fysieke padcomponenten, adapters, kabels en switches, om logische paden te maken tussen de server en het opslag apparaat. Als er een fout is opgetreden in een onderdeel waardoor een logisch pad niet kan worden uitgevoerd, maakt logica met meerdere paden een alternatief pad voor I/O, zodat toepassingen nog steeds toegang tot hun gegevens hebben. Afhankelijk van uw configuratie kan MPIO ook de prestaties verbeteren door de belasting voor al deze paden opnieuw in te verdelen. Zie [overzicht van MPIO](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725907(v=ws.11) "Overzicht en functies van MPIO")voor meer informatie.

Voor een hoge Beschik baarheid van uw StorSimple-oplossing moet MPIO worden geconfigureerd op uw StorSimple-apparaat. Wanneer MPIO is geïnstalleerd op uw hostservers met Windows Server 2012 R2, kunnen de servers vervolgens een koppeling, netwerk of interface fout verdragen.

## <a name="mpio-configuration-summary"></a>Overzicht van MPIO-configuratie

MPIO is een optionele functie in Windows Server en wordt niet standaard geïnstalleerd. Het moet als een functie via Serverbeheer worden geïnstalleerd.

Volg deze stappen voor het configureren van MPIO op uw StorSimple-apparaat:

* Stap 1: MPIO installeren op de Windows Server-host
* Stap 2: MPIO configureren voor StorSimple-volumes
* Stap 3: StorSimple-volumes op de host koppelen
* Stap 4: MPIO configureren voor hoge Beschik baarheid en taak verdeling

Elk van de voor gaande stappen wordt beschreven in de volgende secties.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Stap 1: MPIO installeren op de Windows Server-host

Voer de volgende procedure uit om deze functie te installeren op uw Windows Server-host.

#### <a name="to-install-mpio-on-the-host"></a>MPIO op de host installeren

1. Open Serverbeheer op uw Windows Server-host. Serverbeheer wordt standaard gestart wanneer een lid van de groep Administrators zich aanmeldt op een computer met Windows Server 2012 R2 of Windows Server 2012. Als de Serverbeheer nog niet is geopend, klikt u op **starten > Serverbeheer**.
   
   ![Serverbeheer](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klik op **Serverbeheer > dash board > functies en onderdelen toe te voegen**. Hiermee start u de wizard **functies en onderdelen toevoegen** .
   
   ![Wizard functies en onderdelen toevoegen 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Voer in de wizard **functies en onderdelen toevoegen** de volgende stappen uit:
   
   1. Klik op de pagina **Voordat u begint** op **Volgende**.
   2. Accepteer op de pagina **installatie type selecteren** de standaard instelling van de op **rollen gebaseerde of op onderdelen gebaseerde** installatie. Klik op **Volgende**.
   
       ![Wizard functies en onderdelen toevoegen 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Kies op de pagina **doel server selecteren** de optie **een server uit de Server groep selecteren**. De hostserver moet automatisch worden gedetecteerd. Klik op **Volgende**.
   4. Klik op de pagina **Serverfuncties selecteren** op **Volgende**.
   5. Selecteer op de pagina **onderdelen selecteren** de optie **Multipath I/O** en klik op **volgende**.
   
       ![Wizard functies en onderdelen toevoegen 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Bevestig de selectie op de pagina **installatie selecties bevestigen** en selecteer vervolgens **de doel server automatisch opnieuw opstarten indien nodig**, zoals hieronder wordt weer gegeven. Klik op **Installeren**.
   
       ![Wizard functies en onderdelen toevoegen 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. U ontvangt een melding wanneer de installatie is voltooid. Klik op **Sluiten** om de wizard te sluiten.
   
       ![Wizard functies en onderdelen toevoegen 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Stap 2: MPIO configureren voor StorSimple-volumes

MPIO moet worden geconfigureerd voor het identificeren van StorSimple-volumes. Voer de volgende stappen uit om MPIO te configureren om StorSimple-volumes te herkennen.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>MPIO configureren voor StorSimple-volumes

1. Open de **MPIO-configuratie**. Klik op **Serverbeheer > dash board > hulp middelen > MPIO**.
2. Selecteer in het dialoog venster **Eigenschappen van MPIO** het tabblad **multi-paden detecteren** .
3. Selecteer **ondersteuning voor iSCSI-apparaten toevoegen** en klik vervolgens op **toevoegen**.  
   ![MPIO-eigenschappen ontdekken meerdere paden](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Start de server opnieuw op wanneer dit wordt gevraagd.
5. Klik in het dialoog venster **Eigenschappen van MPIO** op het tabblad **MPIO-apparaten** . Klik op **toevoegen**.
    </br>![MPIO-eigenschappen MPIO-apparaten](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Voer in het dialoog venster **MPIO-ondersteuning toevoegen** onder **hardware-id apparaat** het serie nummer van het apparaat in. Als u het serie nummer van het apparaat wilt ophalen, opent u de StorSimple-Apparaatbeheer service. Navigeer naar **apparaten > dash board**. Het serie nummer van het apparaat wordt weer gegeven in het deel venster met de rechter **balk** van het dash board van het apparaat.
    </br>
    ![MPIO-ondersteuning toevoegen](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Start de server opnieuw op wanneer dit wordt gevraagd.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Stap 3: StorSimple-volumes op de host koppelen

Nadat MPIO is geconfigureerd op Windows Server, kunnen er volume (s) die op het StorSimple-apparaat zijn gemaakt, worden gekoppeld en kan vervolgens MPIO worden benut voor redundantie. Voer de volgende stappen uit om een volume te koppelen.

#### <a name="to-mount-volumes-on-the-host"></a>Volumes op de host koppelen

1. Open het venster **Eigenschappen van iSCSI-initiator** op de Windows Server-host. Klik op **Serverbeheer > dash board > Hulpprogram ma's > iSCSI-initiator**.
2. Klik in het dialoog venster **Eigenschappen van iSCSI-initiator** op het tabblad detectie en klik vervolgens op **doel Portal detecteren**.
3. Voer in het dialoog venster **doel Portal detecteren** de volgende stappen uit:
   
   1. Voer het IP-adres in van de gegevens poort van uw StorSimple-apparaat (Voer bijvoorbeeld DATA 0 in).
   2. Klik op **OK** om terug te keren naar het dialoog venster **Eigenschappen van iSCSI-initiator** .
     
      > [!IMPORTANT]
      > **Als u een privé netwerk gebruikt voor iSCSI-verbindingen, voert u het IP-adres in van de gegevens poort die is verbonden met het particuliere netwerk.**
    
4. Herhaal stap 2-3 voor een tweede netwerk interface (bijvoorbeeld gegevens 1) op het apparaat. Houd er rekening mee dat deze interfaces moeten worden ingeschakeld voor iSCSI. Zie [netwerk interfaces wijzigen](storsimple-8000-modify-device-config.md#modify-network-interfaces)voor meer informatie.
5. Selecteer het tabblad **doelen** in het dialoog venster **Eigenschappen van iSCSI-initiator** . Als het goed is, wordt de IQN van het StorSimple-apparaat weer geven onder **gedetecteerde doelen**.

   ![Tabblad doel eigenschappen iSCSI-initiator](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klik op **verbinding maken** om een iSCSI-sessie met uw StorSimple-apparaat tot stand te brengen. Het dialoog venster **verbinding maken met doel** wordt weer gegeven.
7. Schakel in het dialoog venster **verbinding maken met doel** het selectie vakje **meerdere paden inschakelen** in. Klik op **Geavanceerd**.
8. Voer in het dialoog venster **Geavanceerde instellingen** de volgende stappen uit:
   
   1. Selecteer **micro soft iSCSI-initiator** in de vervolg keuzelijst **lokale adapter** .
   2. Selecteer in de vervolg keuzelijst **INITIATOR IP** het IP-adres van de host.
   3. Selecteer in de vervolg keuzelijst IP van **doel Portal** het IP-adres van de apparaatinterface.
   4. Klik op **OK** om terug te keren naar het dialoog venster **Eigenschappen van iSCSI-initiator** .
9. Klik op **Eigenschappen**. Klik in het dialoog venster **Eigenschappen** op **sessie toevoegen**.
10. Schakel in het dialoog venster **verbinding maken met doel** het selectie vakje **meerdere paden inschakelen** in. Klik op **Geavanceerd**.
11. In het dialoog venster **Geavanceerde instellingen** :

    1. Selecteer micro soft iSCSI-initiator in de vervolg keuzelijst **lokale adapter** .
    2. Selecteer in de vervolg keuzelijst **INITIATOR IP** het IP-adres dat overeenkomt met de host. In dit geval verbindt u twee netwerk interfaces op het apparaat met één netwerk interface op de host. Daarom is deze interface hetzelfde als die van de eerste sessie.
    3. Selecteer in de vervolg keuzelijst **IP van doel Portal** het IP-adres voor de tweede gegevens interface die op het apparaat is ingeschakeld.
    4. Klik op **OK** om terug te keren naar het dialoog venster Eigenschappen van iSCSI-initiator. U hebt een tweede sessie aan het doel toegevoegd.
12. Open **computer beheer** door te navigeren naar **Serverbeheer > dash board > computer beheer**. Klik in het linkerdeel venster op **opslag > schijf beheer**. Het volume dat is gemaakt op het StorSimple-apparaat dat zichtbaar is voor deze host, wordt weer gegeven onder **schijf beheer** als nieuwe schijf of schijven.
13. Initialiseer de schijf en maak een nieuw volume. Selecteer tijdens het format teren een blok grootte van 64 KB.
    
    ![Schijfbeheer](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Klik onder **schijf beheer** met de rechter muisknop op de **schijf** en selecteer **Eigenschappen**.
15. Klik in het dialoog venster StorSimple-model # # # # **Eigenschappen multipath-schijf apparaat** op het tabblad **MPIO** .
    
    ![StorSimple 8100 Multi-Path-schijf DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Klik in de sectie **DSM-naam** op **Details** en controleer of de para meters zijn ingesteld op de standaard parameters. De standaard parameters zijn:
    
    * Controle periode van pad = 30
    * Aantal pogingen = 3
    * Bob verwijderen periode = 20
    * Interval voor opnieuw proberen = 1
    * Controleer of het pad is ingeschakeld = uitgeschakeld.

> [!NOTE]
> **Wijzig de standaard parameters niet.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Stap 4: MPIO configureren voor hoge Beschik baarheid en taak verdeling

Voor multi-path gebaseerde hoge Beschik baarheid en taak verdeling, moeten meerdere sessies hand matig worden toegevoegd om de verschillende beschik bare paden te declareren. Als de host bijvoorbeeld twee interfaces heeft die zijn verbonden met een iSCSI-netwerk en het apparaat twee interfaces heeft die zijn verbonden met het iSCSI-netwerk, hebt u vier sessies nodig die zijn geconfigureerd met de juiste permutaties (er zijn slechts twee sessies vereist als elke gegevens interface en host-interface zich op een ander IP-subnet bevinden en niet routeerbaar is).

**U wordt aangeraden ten minste 8 actieve parallelle sessies tussen het apparaat en uw toepassingshost te hebben.** Dit kan worden bereikt door 4 netwerk interfaces in te scha kelen op uw Windows Server-systeem. Gebruik fysieke netwerk interfaces of virtuele interfaces via Netwerkvirtualisatie technologieën op het niveau van de hardware of het besturings systeem op uw Windows Server-host. Met de twee netwerk interfaces op het apparaat resulteert deze configuratie in 8 actieve sessies. Deze configuratie helpt bij het optimaliseren van het apparaat en de Cloud doorvoer.

> [!IMPORTANT]
> **We raden u aan om 1 GbE-en 10 GbE-netwerk interfaces niet te combi neren. Als u twee netwerk interfaces gebruikt, moeten beide interfaces van hetzelfde type zijn.**

In de volgende procedure wordt beschreven hoe u sessies kunt toevoegen wanneer een StorSimple-apparaat met twee netwerk interfaces is verbonden met een host met twee netwerk interfaces. Dit geeft u slechts 4 sessies. Gebruik dezelfde procedure met een StorSimple-apparaat met twee netwerk interfaces die zijn verbonden met een host met vier netwerk interfaces. U moet 8 configureren in plaats van de vier sessies die hier worden beschreven.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>MPIO configureren voor hoge Beschik baarheid en taak verdeling

1. Een detectie van het doel uitvoeren: Klik in het dialoog venster **Eigenschappen van iSCSI-initiator** op het tabblad **detectie** op **Portal ontdekken**.
2. In het dialoog venster **verbinding maken met doel** voert u het IP-adres in van een van de netwerk interfaces van het apparaat.
3. Klik op **OK** om terug te keren naar het dialoog venster **Eigenschappen van iSCSI-initiator** .
4. In het dialoog venster **Eigenschappen van iSCSI-initiator** selecteert u het tabblad **doelen** , markeert u het gedetecteerde doel en klikt u vervolgens op **verbinding maken**. Het dialoog venster **verbinding maken met doel** wordt weer gegeven.
5. In het dialoog venster **verbinding maken met doel** :
   
   1. Zorg ervoor dat de standaard instelling voor het geselecteerde doel voor het **toevoegen van deze verbinding** aan de lijst met favoriete doelen blijft. Hierdoor wordt het apparaat automatisch opnieuw geprobeerd om de verbinding opnieuw te starten wanneer deze computer opnieuw wordt opgestart.
   2. Schakel het selectie vakje **meerdere paden inschakelen** in.
   3. Klik op **Geavanceerd**.
6. In het dialoog venster **Geavanceerde instellingen** :
   
   1. Selecteer **micro soft iSCSI-initiator** in de vervolg keuzelijst **lokale adapter** .
   2. Selecteer in de vervolg keuzelijst **INITIATOR IP** het IP-adres dat overeenkomt met de eerste interface op de host (iSCSI-interface).
   3. Selecteer in de vervolg keuzelijst **IP van doel Portal** het IP-adres voor de eerste gegevens interface die op het apparaat is ingeschakeld.
   4. Klik op **OK** om terug te keren naar het dialoog venster Eigenschappen van iSCSI-initiator.
7. Klik op **Eigenschappen** en klik in het dialoog venster **Eigenschappen** op **sessie toevoegen**.
8. Schakel in het dialoog venster **verbinding maken met doel** het selectie vakje **meerdere paden inschakelen** in en klik vervolgens op **Geavanceerd**.
9. In het dialoog venster **Geavanceerde instellingen** :
   
   1. Selecteer **micro soft iSCSI-initiator** in de vervolg keuzelijst **lokale adapter** .
   2. Selecteer in de vervolg keuzelijst **INITIATOR IP** het IP-adres dat overeenkomt met de tweede iSCSI-interface op de host.
   3. Selecteer in de vervolg keuzelijst **IP van doel Portal** het IP-adres voor de tweede gegevens interface die op het apparaat is ingeschakeld.
   4. Klik op **OK** om terug te keren naar het dialoog venster **Eigenschappen van iSCSI-initiator** . U hebt nu een tweede sessie aan het doel toegevoegd.
10. Herhaal stap 8-10 om extra sessies (paden) toe te voegen aan het doel. Met twee interfaces op de host en twee op het apparaat kunt u in totaal vier sessies toevoegen.
11. Nadat u de gewenste sessies (paden) hebt toegevoegd, selecteert u in het dialoog venster **Eigenschappen van iSCSI-initiator** het doel en klikt u op **Eigenschappen**. Bekijk op het tabblad sessies van het dialoog venster **Eigenschappen** de vier sessie-id's die overeenkomen met het mogelijke pad permutaties. Als u een sessie wilt annuleren, schakelt u het selectie vakje naast een sessie-id in en klikt u vervolgens op **verbinding verbreken**.
12. Als u apparaten wilt weer geven die in sessies worden aangeboden, selecteert u het tabblad **apparaten** . Als u het MPIO-beleid voor een geselecteerd apparaat wilt configureren, klikt u op **MPIO**. Het dialoog venster **Details van apparaat** wordt weer gegeven. Op het tabblad **MPIO** kunt u de juiste beleids instellingen voor de **taak** verdeling selecteren. U kunt ook het type **actief** of **stand-by** -pad weer geven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service om de configuratie van uw StorSimple-apparaat te wijzigen](storsimple-8000-modify-device-config.md).