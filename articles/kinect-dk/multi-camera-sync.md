---
title: Meerdere Azure Kinect DK-apparaten synchroniseren
description: In dit artikel worden de voor delen van synchronisatie met meerdere apparaten besproken en wordt uitgelegd hoe u de apparaten kunt instellen om te synchroniseren.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, kinect, specs, hardware, DK, mogelijkheden, diepte, kleur, RGB, IMU, matrix, diepte, multi, synchronisatie
ms.openlocfilehash: 30961152b31a659cb27e91a99d6806490998d18d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592276"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Meerdere Azure Kinect DK-apparaten synchroniseren

Elk Azure Kinect DK-apparaat bevat 3,5-mm synchronisatie poorten (**synchroniseren** en **synchroniseren**) die u kunt gebruiken om meerdere apparaten tegelijk te koppelen. Nadat u de apparaten hebt verbonden, kan uw software de trigger tijd tussen deze modems coördineren. 

In dit artikel wordt beschreven hoe u verbinding maakt met de apparaten en hoe u deze synchroniseert.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Voor delen van het gebruik van meerdere Azure Kinect DK-apparaten

Er zijn veel redenen om meerdere Azure Kinect DK-apparaten te gebruiken, waaronder de volgende:

- Vul occlusions in. Hoewel de Azure Kinect DK-gegevens transformaties één installatie kopie produceren, zijn de twee camera's (diepte en RGB) in feite een kleine afstand van elkaar. De offset maakt occlusions mogelijk. Een bedekking treedt op wanneer een voorgrond object de weer gave van een deel van een achtergrond object voor een van de twee camera's op een apparaat blokkeert. In de resulterende kleuren afbeelding lijkt het voorgrond object een schaduw op het achtergrond object te casten.  
   In het volgende diagram ziet de camera aan de linkerkant bijvoorbeeld de grijze pixel P2. Het witte object voorgrond blokkeert echter de IR-straal van de camera aan de rechter kant. De camera aan de rechter kant heeft geen gegevens voor P2.  
   ![Diagram toont twee camera's die zijn gericht op hetzelfde punt als een van deze die zijn geblokkeerd.](./media/occlusion.png)  
   Aanvullende gesynchroniseerde apparaten kunnen de occluded-gegevens leveren.
- Objecten in drie dimensies scannen.
- Verhoog de effectiefste frame frequentie tot een waarde die groter is dan 30 frames per seconde (FPS).
- Leg meerdere afbeeldingen van 4.000 kleuren van dezelfde scène vast, die allemaal binnen 100 micro seconden ( &mu; en) van het midden van de bloot stelling zijn uitgelijnd.
- Verg root de camera dekking binnen de ruimte.

## <a name="plan-your-multi-device-configuration"></a>De configuratie van meerdere apparaten plannen

Voordat u begint, moet u ervoor zorgen dat u de [Azure KINECT DK-hardwarespecificaties](hardware-specification.md) en de [diepte camera van Azure Kinect DK](depth-camera.md)bekijkt.

### <a name="select-a-device-configuration"></a>Configuratie van een apparaat selecteren

U kunt een van de volgende benaderingen gebruiken voor de apparaatconfiguratie:

- **Configuratie** van een ringnet werk. Eén Master apparaat synchroniseren en Maxi maal acht onderliggende apparaten.  
   ![Diagram dat laat zien hoe u met Azure Kinect DK-apparaten verbinding maakt in een configuratie met een keten van een ringnet werk.](./media/multicam-sync-daisychain.png)
- **Ster configuratie**. Eén Master apparaat synchroniseren en Maxi maal twee onderliggende apparaten.  
   ![Diagram dat laat zien hoe u meerdere Azure DK-apparaten kunt instellen in een ster configuratie.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Een externe synchronisatie trigger gebruiken

In beide configuraties levert het Master-apparaat het trigger signaal voor de onderliggende apparaten. U kunt echter een aangepaste externe bron voor de synchronisatie trigger gebruiken. U kunt deze optie bijvoorbeeld gebruiken om afbeeldings opnames te synchroniseren met andere apparatuur. In de keten configuratie of de ster configuratie wordt de externe trigger bron verbonden met het Master-apparaat.

Uw externe trigger bron moet op dezelfde manier functioneren als het Master-apparaat. Er moet een synchronisatie signaal worden geleverd met de volgende kenmerken:

- Actief hoog
- Pulse-breedte: groter dan 8 &mu; s
- 5V TTL/CMOS
- Maximale aandrijf capaciteit: Maxi maal 8 milliamps (mA)
- Frequentie ondersteuning: precies 30 FPS, 15 FPS en 5 FPS (de frequentie van het VSYNC sein van de kleuren camera)

De trigger bron moet het signaal leveren aan de **synchronisatie van** het Master apparaat in de poort door een audio kabel van 3,5 mm te gebruiken. U kunt een stereo-of mono-kabel gebruiken. In azure Kinect DK worden alle hoesjes en ringen van de audio-kabel connector met elkaar verkort en worden deze door gronden. Zoals in het volgende diagram wordt weer gegeven, ontvangt het apparaat alleen het synchronisatie signaal van de tip van de connector.

![Kabel configuraties voor een extern trigger signaal](./media/resources/camera-trigger-signal.jpg)

Zie [Azure Kinect-recorder gebruiken met externe gesynchroniseerde apparaten](record-external-synchronized-units.md) voor meer informatie over het werken met externe apparaten

### <a name="plan-your-camera-settings-and-software-configuration"></a>Uw camera-instellingen en software configuratie plannen

Zie de [Azure Kinect sensor SDK](about-sensor-sdk.md)voor informatie over het instellen van uw software voor het beheren van de camera's en het gebruik van de afbeeldings gegevens.

In deze sectie worden verschillende factoren besproken die van invloed zijn op gesynchroniseerde apparaten (maar niet op één apparaat). De software moet rekening houden met deze factoren.

#### <a name="exposure-considerations"></a>Belichtings overwegingen
Als u de exacte timing van elk apparaat wilt bepalen, raden we u aan een hand matige belichtings instelling te gebruiken. Met de automatische belichtings instelling kan elke kleur camera de werkelijke belichting dynamisch wijzigen. Omdat de bloot stelling van invloed is op de timing, worden deze wijzigingen snel door de camera's gepusht.

Vermijd herhaaldelijk de instelling voor het vastleggen van de installatie kopie. Roep de API slechts één keer aan wanneer deze nodig is.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Voorkom storingen tussen meerdere diepte camera's

Wanneer meerdere diepte camera's afbeeldingen overlappende velden van weer gave zijn, moet elke camera een eigen gekoppelde Laser hebben. Om te voor komen dat de lasers met elkaar conflicteren, moeten de camera beelden van elkaar worden gecompenseerd door 160 μs of meer.

Voor elke diepte van de camera wordt er negen keer op de laser gezet en is er slechts 125 &mu; s actief. De laser wordt vervolgens voor 14505 &mu; s of 23905 s inactief &mu; , afhankelijk van de bewerkings modus. Dit gedrag houdt in dat het start punt voor de berekening van de offset 125 &mu; s.

Daarnaast verhogen verschillen tussen de camera klok en de firmware klok van het apparaat de minimale offset tot 160 &mu; s. Als u een nauw keurigere offset voor uw configuratie wilt berekenen, noteert u de diepte modus die u gebruikt en raadpleegt u de [tabel diepte sensor RAW time](hardware-specification.md#depth-sensor-raw-timing)-out. Met behulp van de gegevens uit deze tabel kunt u de minimale offset (de belichtings tijd van elke camera) berekenen met behulp van de volgende vergelijking:

> *Blootstellings tijd* = (*IR Pulses* &times; *pulsische breedte*) + (tijd niet-actieve *Peri Oden* &times; )

Wanneer u een offset van 160 &mu; s gebruikt, kunt u Maxi maal negen extra diepte camera's configureren zodat elke laser wordt ingeschakeld terwijl de andere lasers inactief zijn.

Gebruik in uw software ```depth_delay_off_color_usec``` of ```subordinate_delay_off_master_usec``` om ervoor te zorgen dat elke IR-laser wordt geactiveerd in het eigen &mu; venster van 160 s of een ander veld van de weer gave heeft.

> [!NOTE]  
> De werkelijke Pulse-breedte is 125us maar de 160us voor sommige Leeway. Wanneer u NFOV UNBINNED als voor beeld neemt, wordt elke 125us puls gevolgd door 1450us inactief. Totaal van deze up-(9 x 125) + (8 x 1450): levert de blootstellings tijd van 12,8 ms. De behuizing waarmee twee apparaten kunnen worden blootgesteld, is de eerste puls van de tweede camera in de eerste niet-actieve periode van de eerste camera. De vertraging tussen de eerste en tweede camera's kan net zo weinig zijn als 125us (de breedte van een puls), maar we raden u aan een aantal Leeway mogelijk te maken. Op basis van 160us kunt u de blootstellings perioden van Maxi maal 10 camera's Interleaved.

## <a name="prepare-your-devices-and-other-hardware"></a>Uw apparaten en andere hardware voorbereiden

Naast meerdere Azure Kinect DK-apparaten moet u mogelijk extra hostcomputers en andere hardware aanschaffen ter ondersteuning van de configuratie die u wilt bouwen. Gebruik de informatie in deze sectie om ervoor te zorgen dat alle apparaten en hardware gereed zijn voordat u begint met het instellen van.

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK-apparaten

Ga als volgt te werk voor elk Azure Kinect DK-apparaat dat u wilt synchroniseren:

- Zorg ervoor dat de meest recente firmware op het apparaat is geïnstalleerd. Ga voor meer informatie over het bijwerken van uw apparaten naar [Azure KINECT DK-firmware bijwerken](update-device-firmware.md). 
- Verwijder de apparaat-cover om de synchronisatie poorten zichtbaar te laten worden.
- Noteer het serie nummer voor elk apparaat. U zult dit nummer later in het installatie proces gebruiken.

### <a name="host-computers"></a>Hostcomputers

Normaal gesp roken gebruikt elke Azure Kinect DK een eigen hostcomputer. U kunt een speciale host-controller gebruiken, afhankelijk van hoe u het apparaat gebruikt en hoeveel gegevens worden overgedragen via de USB-verbinding. 

Zorg ervoor dat de Azure Kinect sensor SDK is geïnstalleerd op elke hostcomputer. Ga voor meer informatie over het installeren van de sensor SDK naar [Quick Start: uw Azure KINECT DK instellen](set-up-azure-kinect-dk.md). 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux-computers: USB-geheugen op Ubuntu

Op Linux gebaseerde hostcomputers wijzen standaard de USB-controller slechts 16 MB kernelgeheugen toe om USB-overdrachten af te handelen. Deze hoeveelheid is doorgaans voldoende voor de ondersteuning van één Azure Kinect DK. Ter ondersteuning van meerdere apparaten heeft de USB-controller echter meer geheugen nodig. Voer de volgende stappen uit om het geheugen te verg Roten:

1. Bewerken/**etc/standaard/grub**.
1. Zoek naar de volgende regel:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Vervang deze door deze regel te gebruiken:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Met deze opdrachten wordt het USB-geheugen ingesteld op 32 MB. Dit is een voor beeld van een instelling op twee keer de standaard waarde. U kunt een veel grotere waarde instellen, afhankelijk van wat u nodig hebt voor uw oplossing.
1. Voer **sudo-update-grub** uit.
1. Start de computer opnieuw op.

### <a name="cables"></a>Kabels

Als u de apparaten op elkaar en op de hostcomputers wilt aansluiten, moet u gebruikmaken van een van de 3,5-mm man-to-man-kabels (ook wel bekend als een 3,5-mm audio kabel). De kabels moeten minder dan 10 meter lang zijn en kunnen stereo of mono zijn.

Het aantal kabels dat u nodig hebt, is afhankelijk van het aantal apparaten dat u gebruikt en ook de specifieke apparaatconfiguratie. Het vak Azure Kinect DK bevat geen kabels. U moet deze afzonderlijk aanschaffen.

Als u de apparaten in de ster configuratie verbindt, moet u ook één hoofdtelefoon splitter hebben.

## <a name="connect-your-devices"></a>Uw apparaten verbinden

**Verbinding maken met Azure Kinect DK-apparaten in een configuratie met een ringnet werk**

1. Verbind elk Azure Kinect DK met Power.
1. Verbind elk apparaat met de host-PC. 
1. Selecteer een apparaat om het hoofd apparaat te zijn en sluit een 3,5-mm audio kabel aan op de **synchronisatie** poort.
1. Sluit het andere uiteinde van de kabel aan **op de synchronisatie** poort van het eerste onderliggende apparaat.
1. Als u verbinding wilt maken met een ander apparaat, sluit u een andere kabel aan op de poort voor **synchroniseren** van het eerste onderliggende apparaat en in de **synchronisatie** poort van het volgende apparaat.
1. Herhaal de vorige stap totdat alle apparaten zijn verbonden. Het laatste apparaat mag slechts één kabel verbinding hebben. De **synchronisatie** poort moet leeg zijn.

**Verbinding maken met Azure Kinect DK-apparaten in een ster configuratie**

1. Verbind elk Azure Kinect DK met Power.
1. Verbind elk apparaat met de host-PC. 
1. Selecteer een apparaat als Master apparaat en sluit het ene uiteinde van de hoofdtelefoon splitter aan op de **synchronisatie** poort.
1. Verbind 3,5-mm audio kabels met de uiteinden van de hoofd telefoon splitter.
1. Sluit het andere uiteinde van elke kabel aan **op de synchronisatie** poort van een van de onderliggende apparaten.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Controleren of de apparaten zijn verbonden en communiceren

Gebruik [Azure Kinect Viewer](azure-kinect-viewer.md)om te controleren of de apparaten goed zijn verbonden. Herhaal deze procedure als dat nodig is voor het testen van elk onderliggend apparaat in combi natie met het hoofd apparaat

> [!IMPORTANT]  
> Voor deze procedure moet u het serie nummer van elk Azure Kinect DK weten.

1. Open twee exemplaren van Azure Kinect viewer.
1. Onder **apparaat openen** selecteert u het serie nummer van het onderliggende apparaat dat u wilt testen.  
   ![Apparaat openen](./media/open-devices.png)
   > [!IMPORTANT]  
   > Als u een nauw keurige vastleg uitlijning wilt maken tussen alle apparaten, moet u het hoofd apparaat het laatst starten.  
1. Onder **externe synchronisatie** selecteert u **Sub**.  
   ![Start van onderliggende camera](./media/sub-device-start.png)
1.  Selecteer **Starten**.  
    > [!NOTE]  
    > Omdat dit een onderliggend apparaat is, wordt in azure Kinect Viewer geen installatie kopie weer gegeven nadat het apparaat is gestart. Er wordt geen afbeelding weer gegeven totdat het onderliggende apparaat een synchronisatie signaal van het hoofd apparaat ontvangt.
1. Nadat het onderliggende apparaat is gestart, gebruikt u het andere exemplaar van Azure Kinect viewer om het hoofd apparaat te openen.
1. Onder **externe synchronisatie** selecteert u **Master**.
1. Selecteer **Starten**.

Wanneer het hoofd-Azure Kinect-apparaat wordt gestart, moeten beide exemplaren van de Azure Kinect-Viewer installatie kopieën weer geven.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>De apparaten als een gesynchroniseerde set kalibreren

Nadat u hebt gecontroleerd of de apparaten correct communiceren, bent u klaar om ze te kalibreren om installatie kopieën in één domein te maken.

In één apparaat zijn de diep gaande en RGB-camera's in de fabriek gekalibreerd om samen te werken. Als meerdere apparaten echter moeten samen werken, moeten ze worden gekalibreerd om te bepalen hoe een installatie kopie moet worden getransformeerd van het domein van de camera die deze heeft vastgelegd in het domein van de camera die u wilt gebruiken voor het verwerken van installatie kopieën.

Er zijn meerdere opties voor het kruislings kalibreren van apparaten. Micro soft biedt het [github-code voorbeeld van het groene scherm](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), dat gebruikmaakt van de methode OpenCV. Het Leesmij-bestand voor dit code voorbeeld bevat meer informatie en instructies voor het kalibreren van de apparaten.

Zie voor meer informatie over kalibratie [Azure Kinect-kalibratie functies gebruiken](use-calibration-functions.md).

## <a name="next-steps"></a>Volgende stappen

Nadat u gesynchroniseerde apparaten hebt ingesteld, kunt u ook meer informatie over het gebruik van de
> [!div class="nextstepaction"]
> [Azure Kinect sensor SDK record and Play-API](record-playback-api.md)

## <a name="related-topics"></a>Verwante onderwerpen

- [Over de Azure Kinect sensor SDK](about-sensor-sdk.md)
- [Hardwarespecificaties voor Azure Kinect DK](hardware-specification.md) 
- [Snelstartgids: uw Azure Kinect DK instellen](set-up-azure-kinect-dk.md) 
- [Azure Kinect DK-firmware bijwerken](update-device-firmware.md) 
- [Azure Kinect DK opnieuw instellen](reset-azure-kinect-dk.md) 
- [Azure Kinect viewer](azure-kinect-viewer.md) 
