---
title: Info
description: Inleiding tot Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: 6001fff54b86b7c18684092aad6c7fdf64f81990
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729943"
---
# <a name="about-azure-remote-rendering"></a>Over Azure Remote Rendering

*Azure Remote Rendering* (ARR) is een service waarmee u kwalitatief hoogwaardige, interactieve 3D-inhoud in de cloud kunt weergeven en deze in realtime kunt streamen naar apparaten, zoals HoloLens 2.

![Diagram met een voorbeeld van een interactieve automotor in 3D, gerenderd in hoge kwaliteit.](../media/arr-engine.png)

Ongebonden apparaten hebben een beperkte rekenkracht voor het weergeven van complexe modellen. Voor veel toepassingen zou het echter onaanvaardbaar zijn om de visuele betrouwbaarheid te verminderen. De volgende schermopname vergelijkt het model tot in detail vergeleken met een model dat is gedecimeerd met een hulpprogramma voor het maken van inhoud:

![Voorbeeldmodel](./media/engine-model-decimated.png)

Het verkleinde model bestaat uit ongeveer 200.000 driehoeken (inclusief de gedetailleerde interne delen), vergeleken met meer dan 18.000.000 drie hoeken in het oorspronkelijke model.

*Remote rendering* lost dit probleem op door de rendering-workload naar geavanceerde GPU's in de cloud te verplaatsen. Met een in de Cloud gehoste grafische engine wordt de afbeelding weergegeven, gecodeerd als een videostroom en gestreamd naar het doelapparaat.

## <a name="hybrid-rendering"></a>Hybride rendering

In de meeste toepassingen is het niet voldoende om alleen een complex model weer te geven. U hebt ook een aangepaste gebruikersinterface nodig om functionaliteit aan de gebruiker toe te voegen. Met Azure Remote Rendering wordt u niet gedwongen een speciaal UI-framework te gebruiken, in plaats daarvan wordt *Hybrid Rendering* ondersteund. Dit betekent dat u elementen op het apparaat kunt weergeven met behulp van uw voorkeursmethode, zoals [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

Aan het einde van een frame combineert Azure Remote Rendering automatisch uw lokaal weergegeven inhoud met de externe afbeelding. Dit kan zelfs met de juiste occlusie.

## <a name="multi-gpu-rendering"></a>Multi-GPU rendering

Sommige modellen zijn zelfs voor een geavanceerde GPU te complex om te worden weergegeven met interactieve framesnelheden. Vooral bij industriële visualisatie is dit een veelvoorkomend probleem. Als u nog een stapje verder wilt gaan, kan Azure Remote Rendering de workload over meerdere GPU’s verdelen. De resultaten worden samengevoegd tot één afbeelding, waardoor het proces volledig transparant is voor de gebruiker.

## <a name="high-level-architecture"></a>Architectuur op hoog niveau

Dit diagram illustreert de architectuur voor remote rendering:

![Architectuur](./media/arr-high-level-architecture.png)

Een volledige cyclus voor het genereren van afbeeldingen omvat de volgende stappen:

1. Clientzijde: Frame instellen
    1. Uw code: Gebruikersinvoer wordt verwerkt, scènediagram wordt bijgewerkt
    1. ARR-code: Bijgewerkte scènediagrammen en voorspelde hoofdhouding worden naar de server verzonden
1. Serverzijde: Remote rendering
    1. Rendering-engine verdeelt de rendering over de beschikbare GPU’s
    1. De uitvoer van meerdere GPU's wordt samengesteld tot één afbeelding
    1. Afbeelding wordt gecodeerd als videostream en teruggestuurd naar de klant
1. Clientzijde: Voltooien
    1. Uw code: Optionele lokale inhoud (UI, markers,...) wordt weergegeven
    1. ARR-code: Bij 'Aanwezig' wordt lokaal gerenderde inhoud automatisch samengevoegd met videostream

Netwerklatentie is het belangrijkste probleem. De tijd tussen het verzenden van een aanvraag en het ontvangen van het resultaat is doorgaans te lang voor interactieve framesnelheden. Daarom kan het op elk moment zijn dat meer dan één frame wordt overgedragen.

## <a name="next-steps"></a>Volgende stappen

* [Systeemvereisten](system-requirements.md)
* [Snelstart: Een model weergeven met Unity](../quickstarts/render-model.md)
