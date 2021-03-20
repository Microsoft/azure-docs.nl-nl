---
title: Diepte camera Azure Kinect DK
description: Inzicht in de principes van het besturings systeem en de belangrijkste functies van de diepte camera in uw Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, SDK, diepte camera, tof, principes, prestaties, ongeldigheid
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277298"
---
# <a name="azure-kinect-dk-depth-camera"></a>Diepte camera Azure Kinect DK

Op deze pagina wordt beschreven hoe u de diepte camera in uw Azure Kinect DK kunt gebruiken. De diepte camera is de seconde van de twee camera's. Zoals in de vorige secties wordt besproken, is de andere camera de RGB-camera.  

## <a name="operating-principles"></a>Bedrijfs principes

De diepte camera van Azure Kinect DK implementeert het principe van de amplitude gemoduleerd doorlopende Wave (AMCW) time-of-Flight (ToF). De camera giett gemoduleerd licht in het Near-IR (NIR)-spectrum op de scène. Vervolgens wordt een indirecte meting van de tijd vastgelegd waarmee het lampje van de camera naar de scène en terug kan rijden.

Deze metingen worden verwerkt voor het genereren van een diepte kaart. Een diepte toewijzing is een set Z-coördinaat waarden voor elke pixel van de afbeelding, gemeten in eenheden van millimeters.

In combi natie met een diepte kaart krijgt u ook een zogenaamde schone IR-Lees bewerking. De waarde van pixels in het gereinigde IR-lees proces is evenredig met de hoeveelheid licht die wordt geretourneerd door de scène. De afbeelding ziet er ongeveer uit als een normale IR-installatie kopie. In de afbeelding hieronder ziet u een voor beeld van een diepte kaart (links) en een bijbehorende schone IR-installatie kopie (rechts).

![Diepte en IR naast elkaar](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>Belangrijke functies

Technische kenmerken van de diepte camera zijn onder andere:

- ToF Imaging-chip van 1 Mega pixel met geavanceerde pixel technologie, waardoor hogere modulatie frequenties en nauw keurigheid kunnen worden uitgebreid.
- Twee NIR laser diodes waarmee de diep gaande en brede FoV-diepte modi (veld-of-View) worden ingeschakeld.
- De kleinste ToF pixel ter wereld, 3,5 μm met 3,5 μm.
- Automatische selectie van het aantal pixels voor het verkrijgen van een groot dynamisch bereik waardoor bijna-en Far-objecten schoon kunnen worden vastgelegd.
- Wereld wijde sluitingen waarmee de prestaties in zonlicht kunnen worden verbeterd.
- De berekenings methode met meerdere fasen waarmee robuuste nauw keurigheid kan worden uitgebreid, zelfs bij de aanwezigheid van chip-, laser-en energie toeleverings variaties.
- Weinig systematische en wille keurige fouten.

![Diepte module](./media/concepts/depth-camera-depth-module.jpg)

De diepte camera verzendt onbewerkte gemoduleerde IR-installatie kopieën naar de host-PC. Op de PC wordt het onbewerkte signaal door de GPU met de versnelde diepte-engine geconverteerd naar diepte kaarten.De diepte camera ondersteunt verschillende modi. Het **smalle veld weergave (FoV)** is ideaal voor scènes met kleinere gebieden in X-en Y-dimensies, maar ook voor grotere gebieden in de Z-dimensie. Als de scène grote X-en Y-gebieden heeft, maar kleinere Z-bereiken, zijn de **brede FoV-modi** beter geschikt.

De diepte camera ondersteunt **2x2 binning modi** voor het uitbreiden van het Z-bereik in vergelijking met de overeenkomstige **unbinned-modi**. Binning wordt uitgevoerd tegen de kosten van een lagere afbeeldings resolutie. Alle modi kunnen worden uitgevoerd met Maxi maal 30 frames per seconde (fps), met uitzonde ring van de modus van 1 Mega pixel (MP) die wordt uitgevoerd met een maximale frame snelheid van 15 fps. De diepte camera biedt ook een **passieve IR-modus**. In deze modus zijn de lampers op de camera niet actief en wordt alleen de omgevings verlichting in acht genomen.

## <a name="camera-performance"></a>Camera prestaties

De prestaties van de camera worden gemeten als systematisch en wille keurige fouten.

### <a name="systematic-error"></a>Systematische fout

Systematische fout wordt gedefinieerd als het verschil tussen de gemeten diepte na het verwijderen van de ruis en de juiste diepte (hoogte van de grond). We berekenen het tijdelijke gemiddelde over veel frames van een statische scène om diepte ruis zoveel mogelijk te elimineren. Nauw keuriger is de systematische fout gedefinieerd als:

![Uitgebreide, systematische fout](./media/concepts/depth-camera-systematic-error.png)

Waarbij *d <sub>t</sub>* de diepte van de meting op tijdstip *t* aanduidt, *N* is het aantal frames dat in de procedure voor het berekenen van het gemiddelde wordt gebruikt en *d <sub>gt</sub>* de diepte van de grond.

De systematische fout specificatie van de diepte camera is exclusief Multi-Path interferentie (MPI). MPI is wanneer een sensor pixel het licht integreert dat door meer dan één object wordt weer gegeven. MPI is deels verkleind in onze diepte camera met hogere modulatie frequenties, samen met de diep gaande validatie, die later wordt geïntroduceerd.

### <a name="random-error"></a>Wille keurige fout

We gaan ervan uitgaan dat er 100 installatie kopieën van hetzelfde object worden uitgevoerd zonder de camera te verplaatsen. De diepte van het object is enigszins anders in elk van de 100-installatie kopieën. Dit verschil wordt veroorzaakt door opname lawaai. Opname ruis is het aantal Photons dat de sensor op een wille keurige factor in de loop van de tijd varieert. We definiëren deze wille keurige fout op een statische scène als de standaard deviatie van de diepte in de loop van de tijd die wordt berekend als:

![Wille keurige fout](./media/concepts/depth-camera-random-error.png)

Waarbij *N* het aantal diepte metingen aangeeft, is *d <sub>t</sub>* de diepte meting op tijdstip *t* en *d* de gemiddelde waarde die wordt berekend over alle diepte metingen *d <sub>t</sub>*.

## <a name="invalidation"></a>Ongeldig maken

In bepaalde situaties biedt de diepte camera mogelijk niet de juiste waarden voor sommige pixels. In deze situaties zijn diepte pixels ongeldig. Ongeldige pixels worden aangegeven door de diepte waarde is gelijk aan 0. Redenen waarom de diepte-engine niet in staat is om de juiste waarden te produceren, zijn:

- Buiten het actieve IR-verlichtings masker
- Verzadigd IR-signaal
- Laag IR-signaal
- Uitschieter filteren
- Storing in meerdere paden

### <a name="illumination-mask"></a>Belichtings masker

Pixels worden ongeldig wanneer ze zich buiten het actieve IR-verlichtings masker bevinden. Het is niet raadzaam om het signaal van dergelijke pixels te gebruiken om diepte te berekenen. In de afbeelding hieronder ziet u het voor beeld van ongeldig door aflichtings masker. De niet-gevalideerde pixels zijn de zwarte-kleur pixels buiten de cirkel in de modus Wide FoV (links) en de zeshoek in de modus voor smalle FoV (rechts).

![Ongeldig aflichtings masker](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>Signaal sterkte

Pixels worden ongeldig wanneer ze een verzadigd IR-signaal bevatten. Wanneer pixels verzadigd zijn, gaan de gegevens van de fase verloren. In de onderstaande afbeelding ziet u het voor beeld van invalidatie met een verzadigd IR-signaal. Zie pijlen verwijzen naar de voorbeeld pixels in zowel de diepte-als de IR-installatie kopie.

![Verzadiging van invalidatie](./media/concepts/depth-camera-invalidation-saturation.png)

Validatie kan ook optreden als het IR-signaal niet sterk genoeg is om diepte te genereren. De onderstaande afbeelding toont het voor beeld van invalidatie met een laag IR-signaal. Bekijk de pijlen naar voor beeld van pixels in de diepte-en IR-installatie kopieën.

![Ongeldig laag signaal](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>Dubbel zinnige diepte

Pixels kunnen ook ongeldig worden gemaakt als ze signalen van meer dan één object in de scène ontvangen. Een veelvoorkomende situatie waarbij deze sortering van de ongeldigheid kan worden gezien, is in hoeken.  Vanwege de scène geometrie wordt het IR-lampje van de camera weer gegeven op één wand en op de andere. Deze weerspiegelde licht veroorzaakt dubbel zinnigheid in de gemeten diepte van de pixel. Filters in de diepte algoritme detecteren deze onduidelijke signalen en de pixels ongeldig te maken.

In de onderstaande afbeelding ziet u voor beelden van validatie van de detectie van meerdere paden. U kunt ook zien hoe dezelfde surface area die ongeldig was uit de weer gave van een camera (bovenste rij), opnieuw kan worden weer gegeven in een andere camera weergave (onderste rij). In deze afbeelding ziet u dat de Opper vlakken die van het ene perspectief ongeldig zijn, mogelijk zichtbaar zijn van een andere.

![Invalidatie: multipath-hoek](./media/concepts/depth-camera-invalidation-multipath.png)

Een ander normaal geval van meerdere paden is pixels die het gemengde signaal van de voor grond en de achtergrond bevatten (zoals rond object randen). Tijdens snelle bewegingen ziet u mogelijk meer niet-gevalideerde pixels rond de randen. De extra ongeldig gevalideerde pixels zijn vanwege het blootstellings interval van de opvangen van de ruwe diepte,

![Invalidatie: meerdere paden](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>Volgende stappen

[Systemen coördineren](coordinate-systems.md)
