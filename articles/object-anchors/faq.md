---
title: Veelgestelde vragen
description: Veelgestelde vragen over de Azure object ankers-service.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: aebc1013dcead6c32dab55512ce915e25f60f94a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047572"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Veelgestelde vragen over Azure object-ankers

Met Azure object-ankers kan een toepassing een object in de fysieke wereld detecteren met behulp van een 3D-model en een schatting maken van de 6 DoFe pose.

Zie overzicht van Azure- [object ankerpunten](overview.md)voor meer informatie.

## <a name="product-faq"></a>Veelgestelde vragen over product
**V: welke aanbevelingen hebt u voor de objecten die moeten worden gebruikt?**

**A:** We raden de volgende eigenschappen aan voor objecten:

* 1-10 meters voor elke dimensie
* Niet-symmetrisch, met voldoende variaties in de geometrie
* Laag reflectivity (matte Opper vlakken) met heldere kleur
* Stationaire objecten
* Geen of kleine hoeveel heden van de inscharniering
* Achtergronden wissen zonder of minimale wirwar
* Het gescande object moet 1:1 overeenkomen met het model dat u hebt getraind

**V: wat zijn de maximale object dimensies die kunnen worden verwerkt voor model opname?**

**A:** Elke dimensie van een CAD-model moet kleiner zijn dan 10 meters.

**V: wat is de maximale grootte van het CAD-model dat kan worden verwerkt voor opname?**

**A:** De grootte van het model bestand moet kleiner zijn dan 150 MB.

**V: wat zijn de ondersteunde CAD-indelingen?**

**A:** Momenteel worden `fbx` ,, `ply` , `obj` en `glb` `gltf` Bestands typen ondersteund.

**V: wat is de ZWAARTE richting en de eenheid die is vereist voor de model opname service? Hoe kunnen we deze afbeelding afnemen?**

**A:** De ZWAARTE richting is de vector die naar de aarde wijst. Voor CAD-modellen is de ZWAARTE richting doorgaans het tegenovergestelde van een richting omhoog. In veel gevallen is dat bijvoorbeeld in het geval-Z of `Vector3(0.0, 0.0, -1.0)` de richting van de ZWAARTE punt aangeeft. Bij het bepalen van de ernst moet u het model niet alleen overwegen, maar ook de afdruk stand waarin het model tijdens runtime wordt weer gegeven. Als u een stoel in de echte wereld probeert te detecteren op een plat vlak, is de ernst mogelijk `Vector3(0.0, 0.0, -1.0)` . Als de stoel zich echter op een 45-diploma helling bevindt, kan de ZWAARTE kracht zijn `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` .

De ZWAARTE richting kan worden gemotiveerd met een 3D-rendering-hulp programma, zoals [MeshLab](http://www.meshlab.net/).

De eenheid vertegenwoordigt de maat eenheid van het model. Ondersteunde eenheden vindt u met de inventarisatie van **micro soft. Azure. ObjectAnchors. opname. unit** .

**V: hoe lang duurt het om een CAD-model op te nemen?**

**A:** Voor een `ply` model, meestal 3-15 minuten. Als u modellen in andere indelingen indient, moet u 15-60 minuten wachten, afhankelijk van de grootte van het bestand.

**V: welke apparaten ondersteunen object ankerpunten?**

**A:** HoloLens 2 

**V: welke besturingssysteem versie moet mijn HoloLens uitvoeren?**

**A:** OS Build 18363,720 of nieuwer, uitgebracht na 12 maart 2020.

  Meer informatie over [Windows 10 maart 12, 2020 update](https://support.microsoft.com/help/4551762).

**V: hoe lang duurt het om een object op HoloLens te detecteren?**

**A:** Dit is afhankelijk van de object grootte en het scan proces. Volg de aanbevolen procedures voor een grondige scan om snellere detectie te verkrijgen. Voor kleinere objecten binnen twee meters in elke dimensie kan de detectie binnen een paar seconden plaatsvinden. Voor grotere objecten, zoals een auto, moet de gebruiker een volledige lus rond het object door lopen om een betrouw bare detectie te krijgen, wat betekent dat detectie tien tallen seconden kan duren.

**V: wat zijn de aanbevolen procedures voor het gebruik van object ankerpunten in een HoloLens-toepassing?**

**Één**

 1. Voer de ogen kalibreren uit om nauw keurige rendering te krijgen.
 2. Zorg ervoor dat de ruimte een uitgebreid visueel patroon en een goede belichting heeft.
 3. Bewaar het object niet, indien mogelijk, weg van wirwar.
 4. U kunt eventueel ook de cache voor [ruimtelijke toewijzing](/windows/mixed-reality/spatial-mapping) wissen op uw HoloLens-apparaat.
 5. Scan het object door dit rond te zetten. Zorg ervoor dat het grootste deel van het object wordt waargenomen.
 6. Stel een zoek gebied voldoende groot in om het object te kunnen behandelen.
 7. Het object moet stil blijven tijdens de detectie.
 8. Object detectie starten en de weer gave visualiseren op basis van de geschatte pose.
 9. Vergrendel het gedetecteerde object of stop het volgen zodra de pose stabiel is en nauw keurig is om de levens duur van de accu te hand haven.

**V: hoe nauw keurig is een geschatte pose?**

**A:** Dit is afhankelijk van de grootte van een object, materiaal, omgeving enz. Voor kleine objecten kan de geschatte pose zich in 2 cm fout bevindt. Voor grote objecten, zoals een auto, kan de fout Maxi maal 2-8 cm zijn.

**V: kunnen object ankerpunten objecten verplaatsen verwerken?**

**A:** We ondersteunen **voortdurend bewegende** of **dynamische** objecten niet.

**V: kunnen object ankerpunten vervormingen of digitizers verwerken?**

**A:** Gedeeltelijk, afhankelijk van de manier waarop de object vorm of geometrie wordt gewijzigd als gevolg van de vervorming of de afbakening. Als de geometrie van het object een partij wijzigt, kan de gebruiker een ander model maken voor die configuratie en deze gebruiken voor detectie.

**V: hoeveel verschillende objecten kunnen object ankers op hetzelfde moment detecteren?**

**A:** Momenteel wordt het detecteren van één object model per keer ondersteund. 

**V: kunnen object ankerpunten meerdere exemplaren van hetzelfde object model detecteren?**

**A:** Ja, u kunt Maxi maal drie objecten van hetzelfde model type detecteren. De toepassing kan `ObjectObserver.DetectAsync` meerdere keren aanroepen met verschillende query's om meerdere exemplaren van hetzelfde model te detecteren.

**V: wat moet ik doen als het object ankers runtime mijn object niet kan detecteren?**

**Één**

* Zorg ervoor dat de ruimte voldoende bitmappatronen heeft door een paar posters toe te voegen.
* Scan het object vollediger.
* Pas de para meters van het model aan zoals hierboven is beschreven.
* Geef een strak selectie kader op als zoek gebied dat alle of het grootste deel van het object bevat.
* Wis de cache voor ruimtelijke toewijzing en scan het object opnieuw.
* Leg diagnose vast en verzend de gegevens naar ons.

**V: objecten query parameters kiezen?**

**Één**

* Zorg ervoor dat het volledige object optimaal wordt bedekt door nauw keurige Zoek gebieden om de detectie snelheid en nauw keurigheid te verbeteren.
* Standaard `ObjectQuery.MinSurfaceCoverage` van object model is goed, anders wordt een kleinere waarde gebruikt om een snellere detectie te verkrijgen.
* Gebruik een kleine waarde voor `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` als het object naar verwachting up-to-right moet worden.
* Een app moet altijd een `1:1` object model gebruiken voor detectie. De geschatte schaal moet dicht bij 1% in het ideale geval worden weer gegeven in een fout van 1%. Een app kan `ObjectQuery.MaxScaleChange` worden ingesteld op `0` of uit te scha kelen `0.1` , en kan de geschaalde schatting kwalitatief evalueren.

**V: Hoe kan ik worden object ankers diagnostische gegevens van de HoloLens ophalen?**

**A:** De toepassing kan de locatie van de diagnostische archieven opgeven. De voor beeld-App object ankers schrijft diagnostische gegevens naar de map **TempState** .

**V: Waarom wordt het bron model niet uitgelijnd met het fysieke object wanneer de pose wordt gebruikt die wordt geretourneerd door de object ankerpunten unit-SDK?**

**A:** Unit-eenheid kan het coördinaten systeem wijzigen bij het importeren van een object model. De object ankerpunt eenheid SDK keert bijvoorbeeld de Z-as om van rechts naar links gecooerdineerd coördinaten systeem, maar unit kan een extra draaiing over de X-of Y-as Toep assen. Een ontwikkelaar kan deze extra draaiing bepalen door de coördinaten systemen te visualiseren en te vergelijken.

**V: wordt 2D ondersteund?**

**A:** Omdat we op geometrie zijn gebaseerd, wordt alleen 3D ondersteund.

**V: kan ik onderscheid maken tussen hetzelfde model in verschillende kleuren?**

**A:** Aangezien de algoritmen zijn gebaseerd op geometrie, gedragen verschillende kleuren van hetzelfde model zich niet anders tijdens de detectie.

**V: kan ik object ankerpunten gebruiken zonder verbinding met Internet?**

**Één** 
* Voor het opnemen en trainen van een model is connectiviteit vereist omdat dit in de Cloud plaatsvindt.
* Runtime-sessies zijn volledig op het apparaat en vereisen geen verbinding omdat alle berekeningen worden uitgevoerd op de HoloLens 2.

## <a name="privacy-faq"></a>Veelgestelde vragen over privacy
**V: hoe worden gegevens in azure object-ankers opgeslagen?**

**A:** Er worden alleen systeemmetagegevens opgeslagen, die zijn versleuteld met een door micro soft beheerde gegevens versleutelings sleutel.