---
title: Beperking van Z-bestrijding
description: Beschrijft technieken voor het oplossen van z-vecht artefacten
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: a399565d62b20f62b72257bcb9f3beb2c910ac98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594058"
---
# <a name="z-fighting-mitigation"></a>Beperking van Z-bestrijding

Wanneer twee Opper vlakken elkaar overlappen, is het niet duidelijk dat deze boven op de andere moeten worden weer gegeven. Het resultaat is zelfs per pixel afhankelijk, wat resulteert in een camera weergave-afhankelijke artefacten. Als de camera of het net wordt verplaatst, knipt u deze patronen daarom duidelijk. Dit artefact wordt *z-vechten* genoemd. Voor AR-en VR-toepassingen wordt het probleem geïntensiveerd omdat apparaten die zijn gekoppeld aan de vizier altijd worden verplaatst. Om te voor komen dat viewer met z-bestrij ding van de beperking van de beveiliging beschikbaar is in azure rendering op afstand.

## <a name="z-fighting-mitigation-modes"></a>Z-bestrij ding van risico modi

|Hiervan                        | Resultaat                               |
|---------------------------------|:-------------------------------------|
|Normaal z-vechten               |![Geen deterministische prioriteit tussen rood en groen quads](./media/zfighting-0.png)|
|Z-bestrij ding van beperking ingeschakeld    |![Rode Quad heeft prioriteit](./media/zfighting-1.png)|
|Markeren van dambord ingeschakeld|![Scha kelen tussen rood en groen vier wisselen voor keur in dambord patroon](./media/zfighting-2.png)|

Met de volgende code kunt u de z-bestrij ding beperken:

```cs
void EnableZFightingMitigation(RenderingSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Connection.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Connection()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> Z-bestrij ding van problemen is een algemene instelling die van invloed is op alle gerenderde netten.

## <a name="reasons-for-z-fighting"></a>Redenen voor z-vechten

Z-vechten gebeurt voornamelijk om twee redenen:

1. Wanneer de Opper vlakken ver weg van de camera zijn, is de nauw keurigheid van hun diepte waarden verslechterd en worden de waarden niet van elkaar onderscheiden
1. Wanneer Opper vlakken in een net fysiek overlappen

Het eerste probleem kan altijd optreden en is lastig te elimineren. Als dit gebeurt in uw toepassing, moet u ervoor zorgen dat de verhouding van de *bijna gelegen vlak* afstand tot de afstand tussen het *vlak* even laag is. Zo maakt een bijna-vlak op afstand van 0,01-en Far-1000 vlak het probleem veel eerder, dan met het vlak in de buurt van 0,1 en op afstand 20.

Het tweede probleem is een indicator voor onjuist geontwerpde inhoud. In de praktijk kunnen twee objecten op hetzelfde moment niet op dezelfde plaats staan. Afhankelijk van de toepassing willen gebruikers misschien weten of overlappende Opper vlakken bestaan en waar ze zich bevinden. Een voor beeld: een CAD-scène van een gebouw dat de basis vormt voor een echte wereld bouw, mag geen fysiek buitenste oppervlakte snij punten bevatten. Om visuele inspectie mogelijk te maken, is de markerings modus beschikbaar. deze geeft een mogelijke z-vechten weer als een animatie patroon.

## <a name="limitations"></a>Beperkingen

De beschik bare oplossing voor z-bestrij ding is een beste poging. Er is geen garantie dat alle z-vechten worden verwijderd. Er wordt ook automatisch een Opper vlak van de andere weer gegeven. Als u dus Opper vlakken hebt die te dicht bij elkaar staan, kan het gebeuren dat het ' onjuiste ' Opper vlak bovenaan staat. Een veelvoorkomend probleem doet zich voor wanneer tekst en andere Decals worden toegepast op een Opper vlak. Als u een z-aanval hebt ingeschakeld, kunnen deze details eenvoudig worden verdwenen.

## <a name="performance-considerations"></a>Prestatieoverwegingen

* Het inschakelen van z-bestrij ding van de beperking is niet alleen mogelijk als er geen prestatie overhead optreedt.
* Het inschakelen van de z-bedekking zorgt er ook voor dat er een niet-triviale prestatie overhead ontstaat, maar kan variëren, afhankelijk van de scène.

## <a name="api-documentation"></a>API-documentatie

* [C# RenderingConnection. ZFightingMitigationSettings eigenschap](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.zfightingmitigationsettings)
* [C++ RenderingConnection:: ZFightingMitigationSettings ()](/cpp/api/remote-rendering/renderingconnection#zfightingmitigationsettings)

## <a name="next-steps"></a>Volgende stappen

* [Weergavemodellen](../../concepts/rendering-modes.md)
* [Vertraagde fase van het project](late-stage-reprojection.md)