---
title: StorSimple implementeren Snapshot Manager | Microsoft Docs
description: Meer informatie over het downloaden en installeren van de StorSimple-Snapshot Manager, een MMC-module voor het beheren van StorSimple-gegevens bescherming en-back-upfuncties.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 5f94564c5e769d3cf0e0abbe92a309a1ee2117a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003868"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>De StorSimple Snapshot Manager MMC-module implementeren

## <a name="overview"></a>Overzicht
De StorSimple-Snapshot Manager is een MMC-module (micro soft Management Console) waarmee gegevens bescherming en het beheer van back-ups in een Microsoft Azure StorSimple omgeving worden vereenvoudigd. Met StorSimple Snapshot Manager kunt u Microsoft Azure StorSimple on-premises en Cloud opslag beheren alsof het een volledig geïntegreerd opslag systeem is, waardoor de back-up-en herstel processen worden vereenvoudigd en de kosten worden verminderd. 

In deze zelf studie worden de configuratie vereisten beschreven, evenals de procedures voor het installeren, verwijderen en bijwerken van StorSimple Snapshot Manager.

> [!NOTE]
> * U kunt StorSimple-Snapshot Manager niet gebruiken voor het beheren van Microsoft Azure StorSimple virtuele matrices (ook wel StorSimple on-premises virtuele apparaten genoemd).
> * Als u van plan bent StorSimple update 2 op uw StorSimple-apparaat te installeren, moet u de nieuwste versie van StorSimple Snapshot Manager downloaden en installeren **voordat u StorSimple update 2 installeert**. De nieuwste versie van StorSimple Snapshot Manager is achterwaarts compatibel en werkt met alle uitgebrachte versies van Microsoft Azure StorSimple. Als u de vorige versie van StorSimple Snapshot Manager gebruikt, moet u deze bijwerken (u hoeft de vorige versie niet te verwijderen voordat u de nieuwe versie installeert).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager-installatie
StorSimple Snapshot Manager kunnen worden geïnstalleerd op computers met het besturings systeem Windows Server 2008 R2 SP1, Windows Server 2012 of Windows Server 2012 R2. Op servers met Windows 2008 R2 moet u ook Windows Server 2008 SP1 en Windows Management Framework 3,0 installeren.

Voordat u de Snapshot Manager StorSimple-module voor de micro soft Management Console (MMC) installeert of bijwerkt, moet u ervoor zorgen dat het Microsoft Azure StorSimple-apparaat en de hostserver correct zijn geconfigureerd.

## <a name="configure-prerequisites"></a>Vereisten configureren
De volgende stappen bieden een globaal overzicht van de configuratie taken die u moet volt ooien voordat u de StorSimple-Snapshot Manager installeert. Zie [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor volledige Microsoft Azure StorSimple configuratie en installatie-informatie, inclusief systeem vereisten en stapsgewijze instructies.

> [!IMPORTANT]
> Controleer voordat u begint de [controle lijst voor implementatie configuratie](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) en [implementatie vereisten](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) in [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Voordat u StorSimple installeert Snapshot Manager
1. Het Microsoft Azure StorSimple apparaat uitpakken, koppelen en aansluiten zoals beschreven in [Installeer uw StorSimple 8100-apparaat](storsimple-8100-hardware-installation.md) of [installeer uw StorSimple 8600-apparaat](storsimple-8600-hardware-installation.md).
2. Zorg ervoor dat op de hostcomputer een van de volgende besturings systemen wordt uitgevoerd:
   
   * Windows Server 2008 R2 (op servers met Windows 2008 R2 moet u ook Windows Server 2008 SP1 en Windows Management Framework 3,0) installeren
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Voor een virtueel StorSimple-apparaat moet de host een Microsoft Azure virtuele machine zijn.
3. Zorg ervoor dat u voldoet aan de vereisten voor de Microsoft Azure StorSimple configuratie. Ga naar [implementatie vereisten](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)voor meer informatie.
4. Verbind het apparaat met de host en voer de eerste configuratie uit. Ga naar [implementatie stappen](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps)voor meer informatie.
5. Maak volumes op het apparaat, wijs ze toe aan de host en controleer of de host ze kan koppelen en gebruiken. StorSimple Snapshot Manager ondersteunt de volgende typen volumes:
   
   * Basis volumes
   * Eenvoudige volumes
   * Dynamische volumes
   * Gespiegelde dynamische volumes (RAID 1)
   * Gedeelde cluster volumes
     
     Voor informatie over het maken van volumes op het StorSimple-apparaat of het virtuele StorSimple-apparaat gaat u naar [stap 6: een volume maken](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)in [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Een nieuwe StorSimple-Snapshot Manager installeren
Voordat u StorSimple Snapshot Manager installeert, moet u ervoor zorgen dat de volumes die u hebt gemaakt op het StorSimple-apparaat of het virtuele StorSimple-apparaat zijn gekoppeld, geïnitialiseerd en ingedeeld zoals beschreven in [vereisten configureren](#configure-prerequisites).

> [!IMPORTANT]
> * Voor een virtueel StorSimple-apparaat moet de host een Microsoft Azure virtuele machine zijn. 
> * Op de host moet Windows 2008 R2, Windows Server 2012 of Windows Server 2012 R2 worden uitgevoerd. Als op uw server Windows Server 2008 R2 wordt uitgevoerd, moet u ook Windows Server 2008 SP1 en Windows Management Framework 3,0 installeren.
> * U moet een iSCSI-verbinding van de host naar het StorSimple-apparaat configureren voordat u het apparaat kunt verbinden met StorSimple Snapshot Manager.

Volg deze stappen om een nieuwe installatie van StorSimple Snapshot Manager te volt ooien. Als u een upgrade installeert, gaat u naar [upgrade of installeert u StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Stap 1: StorSimple installeren Snapshot Manager 
* Stap 2: verbinding maken tussen StorSimple Snapshot Manager en een apparaat 
* Stap 3: de verbinding met het apparaat controleren 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Stap 1: StorSimple installeren Snapshot Manager
Gebruik de volgende stappen om StorSimple Snapshot Manager te installeren.

#### <a name="to-install-storsimple-snapshot-manager"></a>StorSimple installeren Snapshot Manager
1. Down load de StorSimple Snapshot Manager-software (Ga naar [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) in het micro soft Download centrum) en sla deze lokaal op de host op.
2. Klik in Verkenner met de rechter muisknop op de gecomprimeerde map en klik vervolgens op **Alles uitpakken**.
3. Typ of blader in het venster **gecomprimeerde mappen uitpakken** in het vak **een bestemmings-en uitpakkende bestanden selecteren** het pad naar het bestand dat u wilt uitpakken.
   
    > [!IMPORTANT]
    > U moet StorSimple Snapshot Manager installeren op station C:.
    
4. Schakel het selectie vakje **uitgepakte bestanden weer geven wanneer voltooid** in en klik vervolgens op **uitpakken**.
   
    ![Het dialoog venster bestanden uitpakken](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Wanneer de extractie is voltooid, wordt de doelmap geopend. Dubbel klik op het installatie pictogram van de toepassing die wordt weer gegeven in de doelmap.
6. Wanneer het bericht **Setup is voltooid** wordt weer gegeven, klikt u op **sluiten**. U ziet het Snapshot Manager pictogram StorSimple op het bureau blad.
   
    ![bureaublad pictogram](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Stap 2: verbinding maken tussen StorSimple Snapshot Manager en een apparaat
Gebruik de volgende stappen om StorSimple Snapshot Manager te verbinden met een StorSimple-apparaat.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>StorSimple Snapshot Manager op een apparaat aansluiten
1. Klik op het Snapshot Manager pictogram StorSimple op het bureau blad. Het venster StorSimple-Snapshot Manager wordt weer gegeven. Het venster bevat een deel venster **bereik** , een **resultaten** venster en een **actie** deel venster. 
   
    ![StorSimple Snapshot Manager gebruikers interface](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Het deel venster **bereik** (het linkerdeel venster) bevat een lijst met knoop punten die zijn georganiseerd in een boom structuur. U kunt bepaalde knoop punten uitvouwen om een weer gave of specifieke gegevens met betrekking tot dat knoop punt te selecteren. Klik op het pijl pictogram om een knoop punt uit of samen te vouwen. Klik met de rechter muisknop op een item in het deel venster **bereik** om een lijst met beschik bare acties voor dat item weer te geven.
   * Het deel venster met **resultaten** (het middelste deel venster) bevat gedetailleerde status informatie over het knoop punt, de weer gave of de gegevens die u hebt geselecteerd in het deel venster **bereik** .
   * In het deel venster **acties** worden de bewerkingen weer gegeven die u kunt uitvoeren op het knoop punt, de weer gave of de gegevens die u hebt geselecteerd in het deel venster **bereik** .
     
     Zie [StorSimple Snapshot Manager gebruikers interface](storsimple-use-snapshot-manager.md)voor een volledige beschrijving van de StorSimple-Snapshot Manager gebruikers interface.
2. Klik met de rechter muisknop op het knoop punt **apparaten** in het deel venster **bereik** en klik vervolgens op **een apparaat configureren**. Het dialoog venster **een apparaat configureren** wordt weer gegeven.
   
    ![Een apparaat configureren](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Selecteer in de lijst **apparaat** het IP-adres van de Microsoft Azure StorSimple apparaat of virtueel apparaat. Typ in het tekstvak **wacht woord** het StorSimple Snapshot Manager wacht woord dat u hebt gemaakt voor het apparaat in de Azure Portal. Klik op **OK**.
4. StorSimple Snapshot Manager zoekt naar het apparaat dat u hebt geïdentificeerd. Als het apparaat beschikbaar is, wordt door StorSimple Snapshot Manager een verbinding toegevoegd. U kunt [de verbinding met het apparaat controleren](#to-verify-the-connection) om te bevestigen dat de verbinding is toegevoegd.
   
    Als het apparaat om de een of andere reden niet beschikbaar is, retourneert StorSimple Snapshot Manager een fout bericht. Klik op **OK** om het fout bericht te sluiten en klik vervolgens op **Annuleren** om het dialoog venster **een apparaat configureren** te sluiten.
5. Wanneer verbinding wordt gemaakt met een apparaat, wordt door StorSimple Snapshot Manager elke volume groep geïmporteerd die voor dat apparaat is geconfigureerd, op voor waarde dat aan de volume groep Back-ups zijn gekoppeld. Volume groepen waaraan geen back-ups zijn gekoppeld, worden niet geïmporteerd. Daarnaast worden back-upbeleiden die zijn gemaakt voor een volume groep niet geïmporteerd. Als u de geïmporteerde groepen wilt zien, klikt u met de rechter muisknop op het knoop punt bovenste **volume groepen** in het deel venster **bereik** en klikt u op **geïmporteerde groepen** in-/uitschakelen.

### <a name="step-3-verify-the-connection-to-the-device"></a>Stap 3: de verbinding met het apparaat controleren
Gebruik de volgende stappen om te controleren of StorSimple Snapshot Manager is aangesloten op het StorSimple-apparaat.

#### <a name="to-verify-the-connection"></a>De verbinding controleren
1. Klik in het deel venster **bereik** op het knoop punt **apparaten** .
   
    ![Apparaatstatus van StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Controleer het **resultaten** venster: 
   
   * Als er een groene indicator wordt weer gegeven op het pictogram van het apparaat en **beschikbaar** wordt weer gegeven in de kolom **status** , is het apparaat verbonden. 
   * Als er een rode indicator wordt weer gegeven op het pictogram apparaat en niet beschikbaar wordt weer gegeven in de kolom **status** , is het apparaat niet verbonden. 
   * Als **vernieuwen** wordt weer gegeven in de kolom **status** , wordt door StorSimple Snapshot Manager de volume groepen en de bijbehorende back-ups voor een verbonden apparaat opgehaald.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>StorSimple bijwerken of opnieuw installeren Snapshot Manager
U moet StorSimple volledig verwijderen Snapshot Manager voordat u de software bijwerkt of opnieuw installeert. 

Maak een back-up van de bestaande StorSimple Snapshot Manager-Data Base op de hostcomputer voordat u StorSimple Snapshot Manager opnieuw installeert. Hiermee worden de back-upbeleidgegevens en configuratie-informatie opgeslagen, zodat u deze gegevens gemakkelijk kunt herstellen vanuit een back-up.

Volg deze stappen als u een upgrade uitvoert of StorSimple Snapshot Manager opnieuw installeert:

* Stap 1: StorSimple verwijderen Snapshot Manager 
* Stap 2: een back-up maken van de StorSimple Snapshot Manager-Data Base 
* Stap 3: StorSimple Snapshot Manager opnieuw installeren en de data base herstellen 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Stap 1: StorSimple verwijderen Snapshot Manager
Gebruik de volgende stappen om StorSimple Snapshot Manager te verwijderen.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>StorSimple verwijderen Snapshot Manager
1. Open het **configuratie scherm** op de hostcomputer, klik op **Program ma's** en klik vervolgens op **Program ma's en onderdelen**.
2. Klik in het linkerdeel venster op **verwijderen of wijzigen van een programma**.
3. Klik met de rechter muisknop op **StorSimple Snapshot Manager** en klik vervolgens op **verwijderen**.
4. Hiermee start u het installatie programma voor StorSimple Snapshot Manager. Klik op **instellingen wijzigen** en klik vervolgens op **verwijderen**.
   
   > [!NOTE]
   > Als er MMC-processen worden uitgevoerd op de achtergrond, zoals StorSimple Snapshot Manager of schijf beheer, mislukt de verwijdering en ontvangt u een bericht dat alle exemplaren van MMC worden gesloten voordat u het programma probeert te verwijderen. Selecteer **toepassingen automatisch sluiten en probeer deze opnieuw op te starten nadat de installatie is voltooid** en klik vervolgens op **OK**.
   > 
   > 
5. Wanneer het verwijderings proces is voltooid, wordt het bericht **Setup** is voltooid weer gegeven. Klik op **Sluiten**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Stap 2: een back-up maken van de StorSimple Snapshot Manager-Data Base
Voer de volgende stappen uit om een kopie van de StorSimple-Snapshot Manager-Data Base te maken en op te slaan.

#### <a name="to-back-up-the-database"></a>Back-up maken van de data base
1. Stop de micro soft StorSimple Management-service:
   
   1. Start Serverbeheer.
   2. Selecteer op het Serverbeheer-dash board de optie **Services** in het menu **extra** .
   3. Selecteer **micro soft StorSimple Management service** op de pagina **Services** .
   4. Klik in het rechterdeel venster onder **micro soft StorSimple Management service** op **de service stoppen**.
      
        ![De StorSimple-Apparaatbeheer-service stoppen](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Bladeren naar C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData is een verborgen map.
  
3. Zoek het XML-bestand van de catalogus, kopieer het bestand en sla de kopie op in een veilige locatie of in de Cloud.
   
    ![StorSimple back-catalogus bestand](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Start de micro soft StorSimple Management-service opnieuw: 
   
   1. Selecteer op het Serverbeheer-dash board de optie **Services** in het menu **extra** .
   2. Selecteer op de pagina **Services** de **micro soft StorSimple Management-service**.
   3. Klik in het rechterdeel venster onder **micro soft StorSimple Management service** op **Start de service opnieuw**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Stap 3: StorSimple Snapshot Manager opnieuw installeren en de data base herstellen
Als u StorSimple opnieuw wilt installeren Snapshot Manager, volgt u de stappen in [een nieuwe StorSimple installeren Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Gebruik vervolgens de volgende procedure om de StorSimple-Snapshot Manager-Data Base te herstellen.

#### <a name="to-restore-the-database"></a>De data base herstellen
1. Stop de micro soft StorSimple Management-service:
   
   1. Start Serverbeheer.
   2. Selecteer op het Serverbeheer-dash board de optie **Services** in het menu **extra** .
   3. Selecteer **micro soft StorSimple Management service** op de pagina **Services** .
   4. Klik in het rechterdeel venster onder **micro soft StorSimple Management service** op **de service stoppen**.
2. Bladeren naar C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData is een verborgen map.
   > 
   > 
3. Verwijder het XML-bestand van de catalogus en vervang dit door de versie die u eerder hebt opgeslagen.
4. Start de micro soft StorSimple Management-service opnieuw: 
   
   1. Selecteer op het Serverbeheer-dash board de optie **Services** in het menu **extra** .
   2. Selecteer **micro soft StorSimple Management service** op de pagina **Services** .
   3. Klik in het rechterdeel venster onder **micro soft StorSimple Management service** op **Start de service opnieuw**.

## <a name="next-steps"></a>Volgende stappen
* Ga naar [Wat is StorSimple Snapshot Manager](storsimple-what-is-snapshot-manager.md)voor meer informatie over StorSimple-Snapshot Manager?.
* Ga voor meer informatie over de StorSimple-Snapshot Manager gebruikers interface naar [StorSimple Snapshot Manager gebruikers interface](storsimple-use-snapshot-manager.md).
* Ga voor meer informatie over het gebruik van StorSimple Snapshot Manager naar [StorSimple Snapshot Manager gebruiken om uw StorSimple-oplossing te beheren](storsimple-snapshot-manager-admin.md).

