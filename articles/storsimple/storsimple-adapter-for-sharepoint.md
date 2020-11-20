---
title: StorSimple-adapter voor share point installeren | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple-adapter voor share point installeert en configureert of verwijdert in een share Point-server farm.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: 639efab3a9eaef400b3fbe3d0b134d02f458b865
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966289"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor share point installeren en configureren
## <a name="overview"></a>Overzicht
De StorSimple-adapter voor share point is een onderdeel waarmee u Microsoft Azure StorSimple flexibele opslag en gegevens beveiliging kunt bieden aan share Point-server farms. U kunt de adapter gebruiken om de inhoud van binaire Large Object (BLOB) te verplaatsen van de SQL Server-inhouds databases naar het Microsoft Azure StorSimple Hybrid Cloud Storage-apparaat.

De StorSimple-adapter voor share point fungeert als een resource voor externe BLOB-opslag (RBS) en maakt gebruik van de SQL Server externe BLOB Storage-functie voor het opslaan van ongestructureerde share point-inhoud (in de vorm van BLOBs) op een bestands server die wordt ondersteund door een StorSimple-apparaat.

> [!NOTE]
> De StorSimple-adapter voor share point ondersteunt externe BLOB-opslag van share Point Server 2010 (RBS). Het biedt geen ondersteuning voor externe BLOB Storage van share Point Server 2010 (EBS).


* Als u de StorSimple-adapter voor share point wilt downloaden, gaat u naar de [StorSimple-adapter voor share point][1] in het micro soft Download centrum.
* Ga voor informatie over planning voor de beperkingen van de RBS-en RBS-beslissing naar het [gebruik van RBS in share point 2013][2] of [plan voor resource structuur (share Point Server 2010)][3].

In de rest van dit overzicht vindt u een korte beschrijving van de rol van de StorSimple-adapter voor share point en de share point-capaciteit en prestatie limieten waarvan u op de hoogte moet zijn voordat u de adapter installeert en configureert. Nadat u deze informatie hebt bekeken, gaat u naar de [StorSimple-adapter voor share point-installatie](#storsimple-adapter-for-sharepoint-installation) om te beginnen met het instellen van de adapter.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple-adapter voor share point-voor delen
In een share point-site wordt inhoud opgeslagen als ongestructureerde BLOB-gegevens in een of meer inhouds databases. Deze data bases worden standaard gehost op computers met SQL Server en bevinden zich in de share Point-server farm. BLOBs kunnen snel groter worden, waardoor grote hoeveel heden on-premises opslag ruimte worden verbruikt. Daarom wilt u mogelijk een andere, goedkopere opslag oplossing vinden. SQL Server biedt een technologie met de naam externe Blob Storage (RBS) waarmee u BLOB-inhoud in het bestands systeem buiten de SQL Server-Data Base kunt opslaan. Met de RBS kunnen BLOBs zich bevinden in het bestands systeem op de computer met SQL Server, of kunnen ze worden opgeslagen in het bestands systeem op een andere Server computer.

RBS vereist dat u een RBS-provider gebruikt, zoals de StorSimple-adapter voor share point, om de resource structuur in share point in te scha kelen. De StorSimple-adapter voor share point werkt met de resource structuur, zodat u BLOBs kunt verplaatsen naar een server waarvan een back-up wordt gemaakt door het Microsoft Azure StorSimple systeem. Microsoft Azure StorSimple de BLOB-gegevens vervolgens lokaal of in de Cloud opslaat, op basis van het gebruik. BLOBs die zeer actief zijn (doorgaans aangeduid als Tier 1 of warme gegevens), zijn lokaal opgeslagen. Minder actieve gegevens-en archiverings gegevens bevinden zich in de Cloud. Nadat u RBS hebt ingeschakeld voor een inhouds database, worden nieuwe BLOB-inhoud die in share point is gemaakt, opgeslagen op het StorSimple-apparaat en niet in de inhouds database.

De Microsoft Azure StorSimple implementatie van de resource structuur biedt de volgende voor delen:

* Door BLOB-inhoud naar een afzonderlijke server te verplaatsen, kunt u de belasting van de query op SQL Server verminderen, waardoor de reactie snelheid van SQL Server kan worden verbeterd. 
* Azure StorSimple maakt gebruik van ontdubbeling en compressie om de gegevens grootte te reduceren.
* Azure StorSimple biedt gegevens beveiliging in de vorm van lokale en Cloud momentopnamen. Als u de data base zelf op het StorSimple-apparaat plaatst, kunt u ook een back-up maken van de inhouds database en BLOBs in een crash consistente manier. (Het verplaatsen van de inhouds database naar het apparaat wordt alleen ondersteund voor het apparaat uit de StorSimple 8000-serie. Deze functie wordt niet ondersteund voor de 5000-of 7000-serie.)
* Azure StorSimple bevat functies voor herstel na nood gevallen, waaronder failover, bestands-en volume herstel (met inbegrip van test herstel) en het snel herstellen van gegevens.
* U kunt gegevens herstel software, zoals Kroll Ontrack PowerControls, gebruiken met StorSimple moment opnamen van BLOB-gegevens voor het uitvoeren van herstel op item niveau van share point-inhoud. (Deze software voor gegevens herstel is een afzonderlijke aankoop.)
* De StorSimple-adapter voor share point wordt toegevoegd aan de share Point-Portal voor Centraal beheer, zodat u uw volledige share point-oplossing kunt beheren vanaf een centrale locatie.

Het verplaatsen van BLOB-inhoud naar het bestands systeem kan andere kosten besparingen en voor delen bieden. Als u bijvoorbeeld resource structuur gebruikt, kan de duur van de dure opslag op laag 1 worden verminderd en wordt het aantal data bases dat is vereist in de share Point-server farm verminderd door de resource structuur te verkleinen. Andere factoren, zoals de grootte limieten van de data base en de hoeveelheid niet-RBS-inhoud, kunnen ook van invloed zijn op de opslag vereisten. Voor meer informatie over de kosten en voor delen van het gebruik van de resource structuur, Zie [planning voor RBS (share point Foundation 2010)][4] en [het gebruik van de resource structuur in share point 2013][5].

### <a name="capacity-and-performance-limits"></a>Capaciteits-en prestatie limieten
Voordat u rekening houdt met het gebruik van de resource structuur in uw share point-oplossing, moet u rekening houden met de geteste prestaties en capaciteits limieten van share Point Server 2010 en share Point server 2013, en hoe deze limieten te maken hebben met acceptabele prestaties. Zie [Software grenzen en limieten voor share point 2013](/SharePoint/install/software-boundaries-and-limits-0)voor meer informatie.

Bekijk het volgende voordat u de resource structuur configureert:

* Zorg ervoor dat de totale grootte van de inhoud (de grootte van een inhouds database plus de grootte van alle gekoppelde externe BLOBs) de RBS-grootte limiet niet overschrijdt die door share point wordt ondersteund. Deze limiet is 200 GB. 
  
    **Inhouds database en BLOB-grootte meten**
  
  1. Voer deze query uit op de WFE van Centraal beheer. Start de share point-beheer shell en voer de volgende Windows Power shell-opdracht in om de grootte van de inhouds databases op te halen:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Met deze stap wordt de grootte van de inhouds database op de schijf opgehaald.
  2. Voer een van de volgende SQL-query's uit in SQL Management Studio in het vak SQL Server op elke inhouds database en voeg het resultaat toe aan het aantal dat u in stap 1 hebt verkregen.
     
     Voer in share point 2013-inhouds databases het volgende in:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Voer in share point 2010-inhouds databases het volgende in:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Met deze stap wordt de grootte van de BLOBs opgehaald die extern zijn ingesteld.
* U wordt aangeraden alle BLOB-en database inhoud lokaal op het StorSimple-apparaat op te slaan. Het StorSimple-apparaat is een cluster met twee knoop punten voor hoge Beschik baarheid. Het plaatsen van de inhouds databases en BLOBs op het StorSimple-apparaat biedt een hoge Beschik baarheid.
  
    Gebruik de traditionele SQL Server aanbevolen procedures voor de migratie om de inhouds database naar het StorSimple-apparaat te verplaatsen. Verplaats de data base pas nadat alle BLOB-inhoud van de data base is verplaatst naar de bestands share via de resource structuur. Als u ervoor kiest om de inhouds database naar het StorSimple-apparaat te verplaatsen, raden we u aan de opslag van inhouds databases op het apparaat als primair volume te configureren.
* Als in Microsoft Azure StorSimple gelaagde volumes worden gebruikt, is er geen manier om te garanderen dat inhoud die lokaal is opgeslagen op het StorSimple-apparaat niet wordt getierd naar Microsoft Azure-Cloud opslag. Daarom kunt u het beste StorSimple lokaal vastgemaakte volumes gebruiken in combi natie met share point-RBS. Dit zorgt ervoor dat alle BLOB-inhoud lokaal op het StorSimple-apparaat blijft en niet wordt verplaatst naar Microsoft Azure.
* Als u de inhouds databases niet opslaat op het StorSimple-apparaat, gebruikt u traditionele SQL Server aanbevolen procedures voor hoge Beschik baarheid die ondersteuning bieden voor RBS. SQL Server Clustering ondersteunt resource structuur, terwijl SQL Server mirroring niet. 

> [!WARNING]
> Als u de resource structuur niet hebt ingeschakeld, raden we u aan om de inhouds database niet naar het StorSimple-apparaat te verplaatsen. Dit is een niet-test configuratie.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple-adapter voor share point-installatie
Voordat u de StorSimple-adapter voor share point kunt installeren, moet u het StorSimple-apparaat configureren en ervoor zorgen dat de share Point server-farm en SQL Server-instantiëring voldoen aan alle vereisten. In deze zelf studie worden de configuratie vereisten beschreven, evenals de procedures voor het installeren en upgraden van de StorSimple-adapter voor share point.

## <a name="configure-prerequisites"></a>Vereisten configureren
Voordat u de StorSimple-adapter voor share point kunt installeren, moet u ervoor zorgen dat het StorSimple-apparaat, de share Point-server farm en SQL Server-instantiëring voldoen aan de volgende vereisten.

### <a name="system-requirements"></a>Systeemvereisten
De StorSimple-adapter voor share point werkt met de volgende hardware en software:

* Ondersteund besturings systeem: Windows Server 2008 R2 SP1, Windows Server 2012 of Windows Server 2012 R2
* Ondersteunde share point-versies: share Point Server 2010 of share Point server 2013
* Ondersteunde SQL Server versies: SQL Server 2008 Enter prise Edition, SQL Server 2008 R2 Enter prise Edition of SQL Server 2012 Enter prise Edition
* Ondersteunde StorSimple-apparaten – StorSimple 8000-reeks, StorSimple 7000-serie of StorSimple 5000-serie.

### <a name="storsimple-device-configuration-prerequisites"></a>Vereisten voor configuratie van StorSimple-apparaten
Het StorSimple-apparaat is een blok apparaat en hiervoor is een bestands server vereist waarop de gegevens kunnen worden gehost. U kunt het beste een afzonderlijke server gebruiken in plaats van een bestaande server uit de share point-farm. Deze bestands server moet zich op hetzelfde lokale netwerk (LAN) bevinden als de SQL Server computer die als host fungeert voor de inhouds databases.

> [!TIP]
> * Als u uw share point-Farm voor maximale Beschik baarheid configureert, moet u de bestands server ook implementeren voor maximale Beschik baarheid.
> * Als u de inhouds database niet opslaat op het StorSimple-apparaat, gebruik dan traditionele aanbevolen procedures voor hoge Beschik baarheid die ondersteuning bieden voor RBS. SQL Server Clustering ondersteunt resource structuur, terwijl SQL Server mirroring niet. 


Zorg ervoor dat uw StorSimple-apparaat correct is geconfigureerd en dat de juiste volumes ter ondersteuning van uw share point-implementatie zijn geconfigureerd en toegankelijk zijn vanaf uw SQL Server computer. Ga naar [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md) als u nog geen StorSimple-apparaat hebt geïmplementeerd en geconfigureerd. Noteer het IP-adres van het StorSimple-apparaat. u hebt deze nodig tijdens de installatie van de StorSimple-adapter voor share point.

Bovendien moet u ervoor zorgen dat het volume dat moet worden gebruikt voor BLOB externalization voldoet aan de volgende vereisten:

* Het volume moet zijn geformatteerd met een 64 KB-Allocation Unit Size.
* Uw web-front-end (WFE) en toepassings servers moeten toegang kunnen krijgen tot het volume via een UNC-pad (Universal Naming Convention).
* De share Point server-farm moet worden geconfigureerd om naar het volume te schrijven.

> [!NOTE]
> Nadat u de adapter hebt geïnstalleerd en geconfigureerd, moet alle BLOB-externalization via het StorSimple-apparaat worden weer gegeven (het apparaat geeft de volumes aan SQL Server en beheert de opslag lagen). U kunt geen andere doelen gebruiken voor BLOB-externalization.


Als u van plan bent Snapshot Manager StorSimple te gebruiken om moment opnamen van de BLOB-en database gegevens te maken, moet u StorSimple Snapshot Manager op de database server installeren, zodat deze de SQL Writer-service kan gebruiken voor het implementeren van de Windows Volume Shadow Copy Service (VSS).

> [!IMPORTANT]
> StorSimple Snapshot Manager biedt geen ondersteuning voor de share point VSS-schrijver en kan geen toepassings consistente moment opnamen van share point-gegevens maken. In een share point-scenario biedt StorSimple Snapshot Manager alleen crash consistente back-ups.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Vereisten voor share point-Farm configuratie
Controleer als volgt of de share Point-server farm correct is geconfigureerd:

* Controleer of de share Point-server farm in orde is en controleer het volgende:
* Alle share point WFE-en toepassings servers die in de farm zijn geregistreerd, worden uitgevoerd en kunnen worden gepingd vanaf de server waarop u de StorSimple-adapter voor share point gaat installeren.
* De share point-timer service (SPTimerV3 of SPTimerV4) wordt uitgevoerd op elke WFE-server en toepassings server.
* Zowel de share point-timer service als de IIS-toepassings groep waaronder de share point-centrale beheer site wordt uitgevoerd, heeft Administrator bevoegdheden.
* Zorg ervoor dat Internet Explorer Enhanced Security context (IE ESC) is uitgeschakeld. Volg deze stappen voor het uitschakelen van Internet Explorer ESC:
  
  1. Sluit alle exemplaren van Internet Explorer.
  2. Start de Serverbeheer.
  3. Klik in het linkerdeel venster op **lokale server**.
  4. Klik in het rechterdeel venster naast **Verbeterde beveiliging van Internet Explorer** op **ingeschakeld**.
  5. Klik onder **beheerders** op **uit**.
  6. Klik op **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Vereisten voor externe BLOB Storage (RBS)
Zorg ervoor dat u een ondersteunde versie van SQL Server gebruikt. Alleen de volgende versies worden ondersteund en kunnen gebruikmaken van RBS:

* SQL Server 2008 Enter prise Edition
* SQL Server 2008 R2 Enter prise Edition
* SQL Server 2012 Enter prise Edition

BLOBs kunnen worden geexternal op alleen de volumes die het StorSimple-apparaat aan SQL Server geeft. Er worden geen andere doelen voor BLOB-externalization ondersteund.

Wanneer u alle stappen voor de vereiste configuratie hebt voltooid, gaat u naar [de StorSimple-adapter installeren voor share point](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor share point installeren
Gebruik de volgende stappen om de StorSimple-adapter voor share point te installeren. Als u de software opnieuw wilt installeren, raadpleegt u [de StorSimple-adapter voor share point bijwerken of opnieuw](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)installeren. De tijd die nodig is voor de installatie is afhankelijk van het totale aantal share point-data bases in uw share Point-server farm.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Resource structuur configureren
Nadat u de StorSimple-adapter voor share point hebt geïnstalleerd, configureert u de resource structuur zoals beschreven in de volgende procedure.

> [!TIP]
> De StorSimple-adapter voor share point wordt op de pagina Centraal beheer van share point geplaatst, zodat de resource structuur kan worden in-of uitgeschakeld voor elke inhouds database in de share point-farm. Het in-of uitschakelen van de resource structuur voor de inhouds database leidt ertoe dat een IIS-reset, afhankelijk van de configuratie van uw farm, de beschik baarheid van de share point-Webfront-end (WFE) tijdelijk kan verstoren. (Factoren zoals het gebruik van een front-end load balancer, de huidige server workload, enzovoort, kunnen deze onderbreking beperken of elimineren.) Om gebruikers te beschermen tegen onderbrekingen, raden we u aan om RBS alleen in of uit te scha kelen tijdens een gepland onderhouds venster.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Garbagecollection configureren
Wanneer objecten worden verwijderd uit een share point-site, worden ze niet automatisch verwijderd uit het RBS-archief volume. In plaats daarvan worden zwevende BLOBs uit het bestands archief verwijderd met een asynchroon, onderhouds programma voor de achtergrond. Systeem beheerders kunnen dit proces plannen om periodiek uit te voeren, of ze kunnen het altijd starten als dat nodig is.

Dit onderhouds programma (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) wordt automatisch geïnstalleerd op alle share point WFE-servers en toepassings servers wanneer u RBS inschakelt. Het programma wordt geïnstalleerd op de volgende locatie: *opstart station*: \Program Files\Microsoft SQL Remote Blob Storage 10,50 \ maintainer \

Zie voor meer informatie over het configureren en gebruiken van het onderhouds programma [resource structuur onderhouden in share Point Server 2013][8].

> [!IMPORTANT]
> De RBS-onderhouds programma is resource-intensief. U moet de toepassing plannen om alleen te worden uitgevoerd tijdens peri Oden met een lichte activiteit op de share point-farm.


### <a name="delete-orphaned-blobs-immediately"></a>Zwevende BLOBs direct verwijderen
Als u zwevende BLOBs onmiddellijk wilt verwijderen, kunt u de volgende instructies gebruiken. Deze instructies zijn een voor beeld van hoe dit kan worden gedaan in een share point 2013-omgeving met de volgende onderdelen:

* De naam van de inhouds database is WSS_Content.
* De SQL Server naam is SHRPT13-SQL12\SHRPT13.
* De naam van de webtoepassing is share point – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>De StorSimple-adapter voor share point bijwerken of opnieuw installeren
Gebruik de volgende procedure om share Point server bij te werken en de StorSimple-adapter voor share point opnieuw te installeren of om eenvoudigweg de adapter in een bestaande share Point-server farm te upgraden of opnieuw te installeren.

> [!IMPORTANT]
> Controleer de volgende informatie voordat u een upgrade uitvoert van uw share point-software en/of de StorSimple-adapter voor share point moet bijwerken of opnieuw installeren:
> 
> * Bestanden die eerder zijn verplaatst naar externe opslag via de resource structuur, zijn pas beschikbaar als de installatie is voltooid en de resource structuur functie opnieuw is ingeschakeld. Als u de gevolgen van de gebruiker wilt beperken, voert u een upgrade of herinstallatie uit tijdens een gepland onderhouds venster.
> * De tijd die nodig is voor de upgrade/herinstallatie, kan variëren, afhankelijk van het totale aantal share point-data bases in de share Point-server farm.
> * Nadat de upgrade/herinstallatie is voltooid, moet u de resource structuur voor de inhouds databases inschakelen. Zie [resource structuur configureren](#configure-rbs) voor meer informatie.
> * Als u een resource structuur configureert voor een share point-farm met een zeer groot aantal data bases (groter dan 200), is er mogelijk een time-out opgetreden op de pagina **Centraal beheer van share point** . Als dat het geval is, vernieuwt u de pagina. Dit heeft geen invloed op het configuratie proces.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple-adapter voor share point verwijderen
In de volgende procedures wordt beschreven hoe u de BLOBs terugplaatst naar de SQL Server-inhouds databases en vervolgens de StorSimple-adapter voor share point kunt verwijderen. 

> [!IMPORTANT]
> U moet de BLOBs weer verplaatsen naar de inhouds databases voordat u de adapter software verwijdert.


### <a name="before-you-begin"></a>Voordat u begint
Verzamel de volgende informatie voordat u de gegevens weer naar de SQL Server-inhouds databases verplaatst en het verwijderen van de adapter start:

* De namen van alle data bases waarvoor de resource structuur is ingeschakeld
* Het UNC-pad van het geconfigureerde BLOB-archief

### <a name="move-the-blobs-back-to-the-content-databases"></a>Verplaats de BLOBs terug naar de inhouds databases
Voordat u de StorSimple-adapter voor share point-software verwijdert, moet u alle BLOBs migreren die extern zijn teruggestuurd naar de SQL Server-inhouds databases. Als u de StorSimple-adapter voor share point probeert te verwijderen voordat u alle BLOBs weer naar de inhouds databases verplaatst, wordt de volgende waarschuwing weer gegeven.

![Waarschuwings bericht](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>De BLOBs weer verplaatsen naar de inhouds databases
1. Down load elk van de externe objecten.
2. Open de pagina **Centraal beheer van share point** en blader naar **systeem instellingen**.
3. Klik onder **Azure StorSimple** op **StorSimple-adapter configureren**.
4. Op de pagina **StorSimple-adapter configureren** klikt u op de knop **uitschakelen** onder elk van de inhouds databases die u wilt verwijderen uit externe Blob-opslag. 
5. Verwijder de objecten uit share point en upload ze vervolgens opnieuw.

U kunt ook de micro soft `RBS Migrate()` Power shell-cmdlet gebruiken die deel uitmaakt van share point. Zie [inhoud migreren naar of uit een resource structuur](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14))voor meer informatie.

Nadat u de BLOBs weer naar de inhouds database hebt verplaatst, gaat u naar de volgende stap: [de adapter verwijderen](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>De adapter verwijderen
Nadat u de BLOBs weer naar de SQL Server-inhouds databases hebt verplaatst, gebruikt u een van de volgende opties om de StorSimple-adapter voor share point te verwijderen.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Het installatie programma gebruiken om de adapter te verwijderen
1. Gebruik een account met beheerders bevoegdheden om u aan te melden bij de server van de web-front-end (WFE).
2. Dubbel klik op de StorSimple-adapter voor share point Installer. De wizard Setup wordt gestart.
   
    ![Wizard Setup](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klik op **Volgende**. De volgende pagina wordt weer gegeven.
   
    ![Pagina installatie wizard verwijderen](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klik op **verwijderen** om het verwijderings proces te selecteren. De volgende pagina wordt weer gegeven.
   
    ![Pagina Bevestiging van installatie wizard](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Klik op **verwijderen** om de verwijdering te bevestigen. De volgende voortgangs pagina wordt weer gegeven.
   
    ![Voortgangs pagina van de installatie wizard](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Wanneer het verwijderen is voltooid, wordt de pagina volt ooien weer gegeven. Klik op **volt ooien** om de wizard Setup te sluiten.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Het configuratie scherm gebruiken om de adapter te verwijderen
1. Open het configuratie scherm en klik vervolgens op **Program ma's en onderdelen**.
2. Selecteer **StorSimple-adapter voor share point** en klik vervolgens op **verwijderen**.

## <a name="next-steps"></a>Volgende stappen
Meer [informatie over StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: /SharePoint/administration/rbs-planning
[3]: /previous-versions/office/sharepoint-server-2010/ff628583(v=office.14)
[4]: /previous-versions/office/sharepoint-foundation-2010/ff628569(v=office.14)
[5]: /SharePoint/administration/rbs-planning
[8]: /SharePoint/administration/maintain-rbs