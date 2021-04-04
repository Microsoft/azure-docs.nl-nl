---
title: Hiërarchische status overschrijven
description: Hierin wordt het concept van hiërarchische status onderdrukkings onderdelen uitgelegd.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94445150"
---
# <a name="hierarchical-state-override"></a>Hiërarchische status overschrijven

In veel gevallen is het nodig om de vormgeving van onderdelen van een [model](../../concepts/models.md)dynamisch te wijzigen, bijvoorbeeld om subgrafieken te verbergen of onderdelen te scha kelen naar transparante weer gave. Het wijzigen van de materialen van elk betrokken deel is niet praktisch omdat het nodig is om het hele scène diagram te herhalen en het klonen van materialen en de toewijzing op elk knoop punt te beheren.

Als u deze use-case wilt uitvoeren met de minst mogelijke overhead, gebruikt u de `HierarchicalStateOverrideComponent` . Dit onderdeel implementeert hiërarchische status updates op wille keurige vertakkingen van het scène diagram. Dit betekent dat een status kan worden gedefinieerd op een wille keurig niveau in het scène diagram en de hiërarchie trickles omlaag, totdat deze wordt overschreven door een nieuwe status of wordt toegepast op een leaf-object.

Denk bijvoorbeeld aan het model van een auto en u wilt de hele auto overschakelen op transparant, met uitzonde ring van het deel van de interne engine. Deze use-case omvat slechts twee exemplaren van het onderdeel:

* Het eerste onderdeel wordt toegewezen aan het hoofd knooppunt van het model en schakelt transparante rendering in voor de hele auto.
* Het tweede onderdeel wordt toegewezen aan het hoofd knooppunt van de engine en overschrijft de status opnieuw door de Lees modus expliciet uit te scha kelen.

## <a name="features"></a>Functies

De vaste set statussen die kunnen worden overschreven is:

* **`Hidden`**: Respectievelijke mazen in het scène diagram worden verborgen of weer gegeven.
* **`Tint color`**: Een gerenderd object kan worden voorzien van een kleur tint met de afzonderlijke tint kleur en het tinten-gewicht. De onderstaande afbeelding toont kleuren tinten van de velg van een wiel.
  
  ![Tint kleur die wordt gebruikt om een object groen in te scha kelen](./media/color-tint.png)

* **`See-through`**: De geometrie wordt semi-transparant gerenderd, bijvoorbeeld om de binnenste delen van een object zichtbaar te maken. In de volgende afbeelding ziet u dat de volledige auto wordt weer gegeven in de doorlees modus, met uitzonde ring van de rode rem Caliper:

  ![De Lees modus die wordt gebruikt om geselecteerde objecten transparant te maken](./media/see-through.png)

  > [!IMPORTANT]
  > Het effect doorkijk werkt alleen wanneer de *TileBasedComposition* - [rendering modus](../../concepts/rendering-modes.md) wordt gebruikt.

* **`Shell`**: De geometrie wordt weer gegeven als een transparante, verzadigde shell. In deze modus kunnen niet-belang rijke onderdelen van een scène worden uitgevaagd, terwijl de vorm en relatieve positionering behouden blijven. Als u de weer gave van de shell-rendering wilt wijzigen, gebruikt u de [ShellRenderingSettings](shell-effect.md) -status. Bekijk de volgende afbeelding voor het auto model dat volledig door de shell wordt weer gegeven, met uitzonde ring van de blauwe veren:

  ![Shell-modus die wordt gebruikt om specifieke objecten uit te faden](./media/shell.png)

  > [!IMPORTANT]
  > Het shell-effect werkt alleen wanneer de [rendermethode](../../concepts/rendering-modes.md) *TileBasedComposition* wordt gebruikt.

* **`Selected`**: De geometrie wordt weer gegeven met een [selectie overzicht](outlines.md).

  ![Contour optie die wordt gebruikt om een geselecteerd onderdeel te markeren](./media/selection-outline.png)

* **`DisableCollision`**: De geometrie wordt uitgesloten van [ruimtelijke query's](spatial-queries.md). De **`Hidden`** vlag heeft geen invloed op de status vlag voor conflicten, zodat deze twee vlaggen vaak samen worden ingesteld.

* **`UseCutPlaneFilterMask`**: Gebruik een afzonderlijk filter bitmasker om de selectie van het Knip vlak te beheren. Met deze markering wordt bepaald of het afzonderlijke filter masker moet worden gebruikt of overgenomen van het bovenliggende element. Het filter-bit masker zelf wordt ingesteld via de `CutPlaneFilterMask` eigenschap. Voor gedetailleerde informatie over hoe de filters werken, raadpleegt u de [alinea selectief knippen](cut-planes.md#selective-cut-planes). Zie het volgende voor beeld, waarbij alleen de banden en de velg worden geknipt terwijl de rest van de scène ongewijzigd blijft.
![Selectief knip schema's](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> Als alternatief voor het uitschakelen van de zicht baarheid en ruimtelijke query's voor een volledige subgrafiek, `enabled` kan de status van een spel object worden in-of uitgeschakeld. Als een hiërarchie is uitgeschakeld, heeft deze voor keur boven alle `HierarchicalStateOverrideComponent` .

## <a name="hierarchical-overrides"></a>Hiërarchische onderdrukkingen

De `HierarchicalStateOverrideComponent` kan worden gekoppeld op meerdere niveaus van een object hiërarchie. Aangezien er slechts één onderdeel van elk type op een entiteit kan zijn, `HierarchicalStateOverrideComponent` beheert de statussen voor verborgen, bekijken, geselecteerd, kleur tint en botsingen.

Elke status kan daarom worden ingesteld op een van de volgende opties:

* `ForceOn` -de status is ingeschakeld voor alle mazen op en onder dit knoop punt
* `ForceOff` -de status is uitgeschakeld voor alle netten op en onder dit knoop punt
* `InheritFromParent` -de status wordt niet beïnvloed door dit onderdrukkings onderdeel

U kunt de statussen rechtstreeks of via de `SetState` functie wijzigen:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Tint kleur

De `tint color` onderdrukking is iets speciaal omdat er sprake is van een aan/uit/overgenomen status en een tint kleur eigenschap. Het gedeelte alpha van de tint kleur definieert het gewicht van het effect van de tint: als deze is ingesteld op 0,0, is geen tint kleur zichtbaar en als deze is ingesteld op 1,0, wordt het object weer gegeven met de kleur puur tint. Voor in-tussen waarden wordt de uiteindelijke kleur gemengd met de tint kleur. De tint kleur kan per frame worden gewijzigd om een kleur animatie te bereiken.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Een exemplaar van `HierarchicalStateOverrideComponent` zichzelf voegt geen veel runtime overhead toe. Het is echter altijd een goed idee om het aantal actieve onderdelen laag te laten blijven. Bij het implementeren van een selectie systeem dat het verzamelde object markeert, is het raadzaam om het onderdeel te verwijderen wanneer de markering wordt verwijderd. Als u de onderdelen bijhoudt van neutrale functies, kunt u snel aan de slag.

Transparante rendering brengt meer werk belasting op de server Gpu's dan de standaard weergave. Als grote delen van de scène grafiek worden overgeschakeld om te worden *doorzocht*, terwijl veel lagen van de geometrie zichtbaar zijn, kan dit leiden tot een prestatie knelpunt. Hetzelfde geldt voor objecten met selectie- [Uitlijnen](../../overview/features/outlines.md#performance) en voor het [weer geven van shells](../../overview/features/shell-effect.md#performance) . 

## <a name="api-documentation"></a>API-documentatie

* [C# HierarchicalStateOverrideComponent-klasse](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [C++ HierarchicalStateOverrideComponent-klasse](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>Volgende stappen

* [Geeft een overzicht](../../overview/features/outlines.md)
* [Weergavemodellen](../../concepts/rendering-modes.md)
* [Ruimtelijke Query's](../../overview/features/spatial-queries.md)