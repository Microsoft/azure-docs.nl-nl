---
title: Vlakken knippen
description: Legt uit wat knip abonnementen zijn en hoe u deze kunt gebruiken
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594369"
---
# <a name="cut-planes"></a>Vlakken knippen

Een *knip vlak* is een visuele functie die pixels aan één zijde van een virtueel vlak knipt, waardoor de binnenkant van de [netten](../../concepts/meshes.md)zichtbaar wordt.
In de onderstaande afbeelding ziet u het effect. Links ziet u de oorspronkelijke mesh, aan de rechter kant kan in het net kijken:

![Vlak knippen](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

U voegt een knip vlak aan de scène toe door een *CutPlaneComponent* te maken. De locatie en richting van het vlak worden bepaald door de [entiteit](../../concepts/entities.md)van de eigenaar van het onderdeel.

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent-eigenschappen

De volgende eigenschappen worden weer gegeven op een knip vlak onderdeel:

* `Enabled`: U kunt het knippen van abonnementen tijdelijk uitschakelen door het onderdeel uit te scha kelen. Uitgeschakelde knip abonnementen zijn geen rendering-overhead en worden ook niet meegeteld op basis van de algemene grens voor het snij vlak.

* `Normal`: Hiermee geeft u op welke richting (+ X,-X, + Y,-Y, + Z,-Z) wordt gebruikt als het vlak normaal. Deze richting is relatief ten opzichte van de richting van de eigenaar van de entiteit. Verplaats en roteer de entiteit eigenaar voor een exacte plaatsing.

* `FadeColor` en `FadeLength`:

  Als de Alfa waarde van *FadeColor* niet gelijk is aan nul, wordt pixels dicht bij het Knip vlak overvloeien naar het RGB-onderdeel van FadeColor. De sterkte van het Alfa kanaal bepaalt of deze volledig naar de vervagings kleur of slechts gedeeltelijk vervaagt. *FadeLength* definieert de afstand die de vervaging zal plaatsvinden.

* `ObjectFilterMask`: Een filter-bit masker dat bepaalt welke geometrie wordt beïnvloed door het Knip vlak. Zie de volgende alinea voor gedetailleerde informatie.

### <a name="selective-cut-planes"></a>Selectief knip schema's

Het is mogelijk om afzonderlijke knip plannen te configureren zodat ze alleen van invloed zijn op specifieke geometrie. In de volgende afbeelding ziet u hoe deze instellingen in de praktijk kunnen worden weer geven:

![Selectief knip schema's](./media/selective-cut-planes.png)

De filtering werkt via een **logische-bits masker vergelijking** tussen een bitmasker op de knip vlak en een tweede-bits masker dat is ingesteld op de geometrie. Als het resultaat van een logische `AND` bewerking tussen de maskers niet nul is, is het snij vlak van invloed op de geometrie.

* Het bitmasker op het Knip vlak onderdeel wordt ingesteld via de bijbehorende `ObjectFilterMask` eigenschap
* Het bitmasker op een subhiërarchie van geometrie wordt ingesteld via de [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features)

Voorbeelden:

| Filter masker voor knip vlak | Masker voor geometrie filter  | Resultaat van logische `AND` | Vlak knippen is van invloed op geometrie?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) = = 1   | (0000 0001) = = 1  | (0000 0001) = = 1  | Yes |
| (1111 0000) = = 240 | (0001 0001) = = 17 | (0001 0000) = = 16 | Yes |
| (0000 0001) = = 1   | (0000 0010) = = 2  | (0000 0000) = = 0  | No |
| (0000 0011) = = 3   | (0000 1000) = = 8  | (0000 0000) = = 0  | No |

>[!TIP]
> Als u een cut instelt `ObjectFilterMask` op 0, heeft dit geen invloed op een geometrie omdat het resultaat van logische waarde `AND` nooit niet-null mag zijn. In het rendering-systeem worden deze schema's in de eerste plaats niet in overweging gegeven. Dit is dus een lichte methode om afzonderlijke knip abonnementen uit te scha kelen. Deze knip abonnementen tellen ook niet mee op basis van de limiet van 8 actieve abonnementen.

## <a name="limitations"></a>Beperkingen

* De externe rendering van Azure biedt ondersteuning **voor Maxi maal acht actieve knip abonnementen**. U kunt meer knip vlak onderdelen maken, maar als u meerdere tegelijk probeert in te scha kelen, wordt de activering genegeerd. Schakel eerst andere abonnementen uit als u wilt overschakelen welke onderdelen van invloed op de scène moeten zijn.
* Knip abonnementen zijn een louter visuele functie. ze hebben geen invloed op het resultaat van [ruimtelijke query's](spatial-queries.md). Als u een straal wilt omzetten in een cut, kunt u het begin punt van de Ray aanpassen zodat deze zich op het Knip vlak bevindt. Op deze manier kan de Ray alleen zicht bare onderdelen aanraken.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Elk actief knip vlak maakt een kleine kosten tijdens de rendering. Knip abonnementen uitschakelen of verwijderen wanneer ze niet nodig zijn.

## <a name="api-documentation"></a>API-documentatie

* [C# CutPlaneComponent-klasse](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C++ CutPlaneComponent-klasse](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Volgende stappen

* [Rendering met één zijde](single-sided-rendering.md)
* [Ruimtelijke query's](spatial-queries.md)