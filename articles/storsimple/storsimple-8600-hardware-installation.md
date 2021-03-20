---
title: Microsoft Azure StorSimple 8600-apparaat installeren
description: Hierin wordt beschreven hoe u het StorSimple 8600-apparaat uitpakt, koppelt en kabelt voordat u de software implementeert en configureert.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91871810"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Uw StorSimple 8600-apparaat uitpakken, aan het rek koppelen en bekabelen

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Uw Microsoft Azure StorSimple 8600 is een apparaat met twee behuizingen en bestaat uit een primaire en een EBOD behuizing. In deze zelf studie wordt uitgelegd hoe u de StorSimple 8600-computerhardware uitpakt, koppelt en bekabelt voordat u de StorSimple-software configureert.

## <a name="unpack-your-storsimple-8600-device"></a>Uw StorSimple 8600-apparaat uitpakken
De volgende stappen bevatten duidelijke, gedetailleerde instructies voor het uitpakken van uw StorSimple 8600-opslag apparaat. Dit apparaat wordt in twee dozen verzonden, een voor de primaire behuizing en een andere voor de EBOD-behuizing. Deze twee vakken worden vervolgens in één vak geplaatst.

### <a name="prepare-to-unpack-your-device"></a>Voorbereiden op het uitpakken van uw apparaat
Lees de volgende informatie voordat u uw apparaat uitpakt.

![Waarschuwing pictogram pictogram voor ](./media/storsimple-safety/IC740879.png)![ zwaar gewicht ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **.**

1. Zorg ervoor dat er twee mensen beschikbaar zijn om het gewicht van het apparaat te beheren als u het hand matig verwerkt. Een volledig geconfigureerde behuizing kan tot 32 kg (70 lbs) wegen.
2. Leg de doos op een vlak, horizontaal oppervlak.

Voer vervolgens de volgende stappen uit om uw apparaat uit te pakken.

#### <a name="to-unpack-your-device"></a>Uw apparaat uitpakken
1. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om u te helpen bepalen of het apparaat in goede staat werkt.
2. Open het buitenste vak en voer de twee selectie vakjes uit die overeenkomen met primaire en EBOD behuizingen. U kunt nu de primaire en EBOD-behuizingen uitpakken. In de volgende afbeelding ziet u de uitgepakte weer gave van een van de bijlagen.
   
    ![Uw opslag apparaat uitpakken](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Uitgepakte weer gave van uw opslag apparaat**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Vak voor verpakking |
   |   2 |SAS-kabels (in accessoires en kabels lade) |
   |   3 |Onderste schuim |
   |   4 |Apparaat |
   |   5 |Bovenste schuim |
   |   6 |Vak voor accessoire |
3. Nadat u de twee selectie vakjes hebt uitgepakt, moet u het volgende hebben:
   
   * 1 primaire behuizing (de primaire behuizing en EBOD Enclosure bevinden zich in twee afzonderlijke vakken)
   * 1 EBOD-behuizing
   * 4 stroom kabels, 2 in elk vak
   * 2 SAS-kabels (om de primaire behuizing te verbinden met EBOD Enclosure)
   * 1 Crossover Ethernet-kabel
   * 2 kabels van seriële console
   * 1 serieel USB-conversie programma voor seriële toegang
   * 4 QSFP-naar-SFP +-adapters voor gebruik met 10 GbE-netwerk interfaces
   * 2 rack montagekit (4 rails met gekoppelde hardware, 2 voor de primaire behuizing en EBOD behuizing), 1 in elk vak
   * Documentatie voor aan de slag
     
     Als u geen van de hierboven vermelde items hebt ontvangen, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).  

De volgende stap is het in het rek monteren van uw apparaat.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rack: uw StorSimple 8600-apparaat koppelen
Volg de volgende stappen om uw StorSimple 8600-opslag apparaat te installeren in een standaard 19-inch rack met front-en post berichten. Dit apparaat wordt geleverd met twee behuizingen: een primaire behuizing en een EBOD-behuizing. Beide moeten zijn gekoppeld aan een rek.

De installatie bestaat uit meerdere stappen, die allemaal worden besproken in de volgende procedures.

> [!IMPORTANT]
> StorSimple-apparaten moeten zijn gekoppeld aan een rek voor een goede werking.
> 
> 

### <a name="site-preparation"></a>Site voorbereiding
De behuizingen moeten worden geïnstalleerd in een standaard 19-inch rack met zowel voor-als achteruitkijk berichten. Gebruik de volgende procedure om de rack installatie voor te bereiden.

#### <a name="to-prepare-the-site-for-rack-installation"></a>De site voorbereiden voor de installatie van een rek
1. Zorg ervoor dat de primaire en EBOD-behuizing veilig zijn op een plat, stabiel en niveau werk vlak (of vergelijkbaar).
2. Controleer of de site waar u wilt instellen, een standaard AC van een onafhankelijke bron of een PDU (netstroom distributie-eenheid) met een nood voeding (UPS) heeft.
3. Zorg ervoor dat er één 4U (2 X 2U)-sleuf beschikbaar is in het rek waarin u de bijlagen wilt koppelen.

![Waarschuwing pictogram pictogram voor ](./media/storsimple-safety/IC740879.png)![ zwaar gewicht ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **.**

 Zorg ervoor dat er twee mensen beschikbaar zijn voor het beheren van het gewicht als u de apparaat-installatie hand matig uitvoert. Een volledig geconfigureerde behuizing kan tot 32 kg (70 lbs) wegen.

### <a name="rack-prerequisites"></a>Rack vereisten
De behuizingen zijn ontworpen voor installatie in een standaard 19-inch rack behuizing met:

* Minimale diepte van 27,84 cm van het rack post-bericht
* Maximum gewicht van 32 kg voor het apparaat
* Maximale druk van 5 Pascal (0,5 mm vocht meter)

### <a name="rack-mounting-rail-kit"></a>Rail Kit voor rack montage
Er wordt een reeks montage rails meegeleverd voor gebruik met de 19-inch rack behuizing. De rails zijn getest om het gewicht van de maximale behuizing te verwerken. Deze rails bieden ook de mogelijkheid om meerdere behuizingen te installeren zonder verlies van ruimte binnen het rek. Installeer eerst de EBOD-behuizing.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>De EBOD Enclosure op de rails installeren
1. Voer deze stap alleen uit als de interne rails niet op uw apparaat zijn geïnstalleerd. Normaal gesp roken worden de interne rails in de fabriek geïnstalleerd. Als er geen rails zijn geïnstalleerd, installeert u de dia's uit de linker-en rechter rails aan de zijkanten van het behuizing-chassis. Ze worden aan elke zijde gekoppeld met zes metrische schroeven. Om u te helpen bij het bezorgen van de afdruk stand, worden de Rail dia's als **LH-front** en **RH-front** en wordt het uiteinde dat zich op de achterkant van de behuizing bevindt, verzegeld.
   
    ![Rail dia's koppelen aan het behuizing-chassis](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Rail dia's koppelen aan de zijkanten van de behuizing**
   
   | Label | Beschrijving |
   | --- | --- |
   |  1 |M 3x4 knop-Head schroefs |
   |  2 |Chassis dia's |
2. Koppel de rails van de linker-en rechter Rail aan de verticale leden van het rek-Cabinet. De vier Kante haken zijn gemarkeerd **LH**, **RH** en **deze kant** aan de hand van de juiste stand.
3. Zoek de pennen van de rails aan voor- en achterzijde van de rail. Breid de rails uit voor de rack berichten en plaats de pincodes in de voor-en achterliggende leden van het buiten rack. Zorg ervoor dat de Rail-assembly niveau is.
4. Beveilig de Rail-assembly met de verticale leden van het rek door gebruik te maken van twee van de gegeven metrische schroeven. Gebruik één schroef aan de voor zijde en een aan de achterzijde.
5. Herhaal deze stappen voor de andere Rail-assembly.
   
     ![Rail dia's koppelen aan een rack behuizing](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Rail-assembly's aan het rek koppelen**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Schroef bout |
   |   2 |Vier Kante opening voor rack-schroef |
   |   3 |Locatie pincodes van het voorste spoor |
   |   4 |Schroef bout |
   |   5 |Locatie pincodes achterblijvend spoor |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>De EBOD behuizing in het rek koppelen
Voer de volgende stappen uit om de EBOD-behuizing in het rek te koppelen met behulp van de rack rails die zojuist zijn geïnstalleerd.

#### <a name="to-mount-the-ebod-enclosure"></a>De EBOD-behuizing koppelen
1. Met een assistent, til u de behuizing op en lijnt u deze uit met de Rack Rails.
2. Plaats de behuizing nauw keurig in de rails en push deze vervolgens volledig naar de rack behuizing.
   
    ![Apparaat in het rek invoegen](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **De behuizing in het rek koppelen**
3. Verwijder de linker-en rechter flens van de uiteinden door de kapitalen vrij te halen. De flens uiteinden worden simpelweg op de flens uitgelijnd.
4. Beveilig de behuizing in het rek door één van de meegeleverde Phillips via elke flens, links en rechts, te installeren.
5. Installeer de flens uiteinden door ze naar de gewenste positie te verplaatsen.
   
     ![Flens cap's installeren](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Flens cap's installeren**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Schroef bout behuizing |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>De primaire behuizing in het rek koppelen
Nadat u klaar bent met het koppelen van de EBOD-behuizing, moet u de primaire behuizing koppelen volgens dezelfde stappen.

> [!NOTE]
> * Het kan zijn dat er een paar lege sleuven in het rek zijn tussen de primaire behuizing en de EBOD-behuizing.
> * Gebruik de meegeleverde SAS-kabel van de 2 min. om de primaire behuizing aan de EBOD-behuizing te koppelen.
> * Er zijn geen beperkingen voor de relatieve plaatsing van de Head-eenheid naar de EBOD-eenheid. De primaire behuizing kan daarom in de bovenste sleuf en de onderstaande EBOD-behuizing worden geplaatst, of andersom.
> 
> 

De volgende stap is het bekabelen van uw apparaat voor energie, netwerk en seriële toegang.

## <a name="cable-your-storsimple-8600-device"></a>Uw StorSimple 8600-apparaat bekabelen
In de volgende procedures wordt uitgelegd hoe u het StorSimple 8600-apparaat kunt bekabelen voor energie-, netwerk-en seriële verbindingen.

### <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat met de kabel van uw apparaat, hebt u het volgende nodig:

* Uw primaire behuizing en de EBOD-behuizing, volledig uitgepakt
* 4 stroom kabels (2 voor de primaire en EBOD behuizing) die bij uw apparaat zijn geleverd
* 2 SAS-kabels die zijn meegeleverd met het apparaat om de EBOD-behuizing aan de primaire behuizing te koppelen
* Toegang tot 2 energie distributie-eenheden (Pdu's) (aanbevolen)
* Netwerk kabels
* Beschik bare seriële kabels
* Serieel USB-converter met het juiste stuur programma dat is geïnstalleerd op uw PC (indien nodig)
* Voorzien in 4 QSFP-naar-SFP +-adapters voor gebruik met 10 GbE-netwerk interfaces
* [Ondersteunde hardware voor de 10 GbE-netwerk interfaces op uw StorSimple-apparaat](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS en stroom kabels
Uw apparaat heeft zowel een primaire behuizing als een EBOD-behuizing. Hiervoor moeten de eenheden worden verbonden met een SAS-verbinding (Serial Attached SCSI) en kracht.

Wanneer u dit apparaat voor de eerste keer instelt, voert u de stappen voor SAS-bekabeling eerst uit en voert u de stappen voor energie bekabeling uit.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Netwerk kabels
Uw apparaat bevindt zich in een configuratie met actief/stand-by: op elk gewenst moment is één controller module actief en verwerkt alle schijf-en netwerk bewerkingen terwijl de andere controller module op stand-by staat. Als er een storing optreedt in de controller, activeert de standby-controller de schijf-en netwerk bewerkingen onmiddellijk en gaat deze verder.

Ter ondersteuning van deze redundante Controller-failover moet u het netwerk van het apparaat bekabelen, zoals wordt weer gegeven in de volgende stappen.

#### <a name="to-cable-for-network-connection"></a>Naar kabel voor netwerk verbinding
1. Uw apparaat heeft zes netwerk interfaces op elke controller: 4 1 Gbps en 2 10 Gbps Ethernet-poorten. Raadpleeg de volgende afbeelding om de gegevens poorten op de backplane van uw apparaat te identificeren.
   
     ![Backplane van 8600-apparaat](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Terug op het apparaat met de gegevens poorten**
   
   | Label | Beschrijving |
   | --- | --- |
   |   0, 1, 4, 5 |1 GbE-netwerk interfaces |
   |   2, 3 |10 GbE-netwerk interfaces |
   |   6 |Seriële poorten |
2. Zie het volgende diagram voor netwerk kabels. (De minimale netwerk configuratie wordt weer gegeven door middel van ononderbroken blauwe lijnen. Voor hoge Beschik baarheid en prestaties wordt aanvullende configuratie vereist weer gegeven door stippel lijnen.)

![Uw 4U-apparaat bekabelen voor netwerk](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Netwerk kabels voor uw apparaat**

| Label | Beschrijving |
| --- | --- |
| A |LAN met Internet toegang |
| B |Controller 0 |
| C |PCM 0 |
| D |Controller 1 |
| E |PCM 1 |
| F |EBOD-controller 0 |
| G |EBOD-controller 1 |
| H, IK |Hosts (bijvoorbeeld bestands servers) |
| 0-5 |Netwerkinterfaces |
| 6 |Primaire behuizing |
| 7 |EBOD-behuizing |

Bij het bekabelen van het apparaat vereist de minimale configuratie:

* Ten minste twee netwerk interfaces die zijn verbonden op elke controller met één voor Cloud toegang en één voor iSCSI. De poort DATA 0 wordt automatisch ingeschakeld en geconfigureerd via de seriële console van het apparaat. Naast gegevens 0 moet ook een andere gegevens poort worden geconfigureerd via de klassieke Azure-Portal. In dit geval verbindt u DATA 0-poort met het primaire LAN (netwerk met Internet toegang). De andere gegevens poorten kunnen worden aangesloten op het SAN/iSCSI LAN (VLAN)-segment van het netwerk, afhankelijk van de beoogde rol.
* Identieke interfaces op elke controller die zijn verbonden met hetzelfde netwerk om te zorgen voor Beschik baarheid als er een failover voor de controller plaatsvindt. Als u bijvoorbeeld gegevens 0 en DATA 3 voor een van de controllers wilt verbinden, moet u de overeenkomende gegevens 0 en DATA 3 op de andere controller verbinden.

Denk eraan voor hoge Beschik baarheid en prestaties:

* Configureer, indien mogelijk, een paar netwerk interface voor Cloud toegang (1 GbE) en een ander paar voor iSCSI (10 GbE aanbevolen) op elke controller.
* Als dat mogelijk is, kunt u netwerk interfaces van elke controller verbinden met twee verschillende switches om de beschik baarheid van een switch-fout te garanderen. De afbeelding illustreert de 2 10 GbE-netwerk interfaces, DATA 2 en DATA 3, van elke controller die is verbonden met twee verschillende switches. Raadpleeg de **netwerk interfaces** onder de [vereisten voor hoge Beschik baarheid voor uw StorSimple-apparaat](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)voor meer informatie.

> [!NOTE]
> Als u SFP +-transceivers met uw 10 GbE-netwerk interfaces gebruikt, gebruikt u de meegeleverde QSFP-SFP +-adapters. Voor meer informatie gaat u naar [ondersteunde hardware voor de 10 GbE-netwerk interfaces op uw StorSimple-apparaat](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Bekabeling van seriële poort
Voer de volgende stappen uit om uw seriële poort te bekabelen.

#### <a name="to-cable-for-serial-connection"></a>Naar kabel voor seriële verbinding
1. Uw apparaat heeft een seriële poort op elke controller die wordt geïdentificeerd door een moersleutel-pictogram. Als u de seriële poorten wilt vinden, raadpleegt u de afbeelding waarin de gegevens poorten op de achterkant van het apparaat worden weer gegeven.
2. Identificeer de actieve controller op de backplane van uw apparaat. Een knipperende blauwe LED geeft aan dat de controller actief is.
3. Gebruik de meegeleverde seriële kabel (indien nodig, het USB-seriële conversie programma voor uw laptop) en verbind uw console of computer (met terminal emulatie op het apparaat) met de seriële poort van de actieve controller.
4. Installeer de seriële USB-Stuur programma's (geleverd bij het apparaat) op uw computer.
5. Stel de seriële verbinding als volgt in:
   
   * 115.200 baud
   * 8 databits
   * 1 stopbit
   * Geen pariteit
   * Datatransport besturing is ingesteld op **geen**
6. Controleer of de verbinding werkt door op ENTER op de console te drukken. Er moet een seriële console worden weer gegeven.

> [!NOTE]
> **Licht beheer:** Wanneer het apparaat is geïnstalleerd in een extern Data Center of in een computer ruimte met beperkte toegang, moet u ervoor zorgen dat de seriële verbindingen met beide controllers altijd zijn verbonden met een seriële console-switch of vergelijk bare apparatuur. Dit maakt buiten-band beheer op afstand en ondersteunings bewerkingen mogelijk in het geval van storingen in het netwerk of onverwachte storingen.
> 
> 

U hebt de bekabeling van uw apparaat voltooid voor stroom, netwerk toegang en seriële verbinding. De volgende stap is het configureren van de software op uw apparaat.

## <a name="next-steps"></a>Volgende stappen
U bent nu klaar om [uw on-premises StorSimple-apparaat te implementeren en te configureren](storsimple-8000-deployment-walkthrough-u2.md).

