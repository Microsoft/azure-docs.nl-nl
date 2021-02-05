---
title: Rendering met één zijde
description: Beschrijft instellingen voor rendering met één zijde en use cases
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: fea9deae3948b36732b5ea5203fceea6bec07fb9
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594075"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: aanwijzer

De meeste renderers [maken gebruik van Back-face](https://en.wikipedia.org/wiki/Back-face_culling) om de prestaties te verbeteren. Wanneer de mazen zijn geopend met [knip abonnementen](cut-planes.md), zien de gebruikers er vaak uit dat er drie hoeken worden weer gegeven. Als deze drie hoeken zijn verwijderd, is het resultaat niet goed.

U kunt dit probleem op een betrouw bare manier voor komen door drie *hoeken dubbelzijdig te renderen.* Omdat het gebruik van back-ups niet wordt geruimen, worden de prestaties van het systeem standaard alleen overgeschakeld naar de weer gave met dubbelzijdige rendering voor mazen die elkaar kruisen met een knip vlak.

Met de instelling voor *:::no-loc text="single-sided"::: rendering* kunt u dit gedrag aanpassen.

> [!CAUTION]
> De :::no-loc text="single-sided"::: weer gave-instelling is een experimentele functie. Het kan in de toekomst opnieuw worden verwijderd. Wijzig de standaard instelling niet, tenzij er een kritiek probleem in uw toepassing is opgelost.

## <a name="prerequisites"></a>Vereisten

De :::no-loc text="single-sided"::: instelling voor rendering heeft alleen invloed op de netten die zijn [geconverteerd](../../how-tos/conversion/configure-model-conversion.md) met de `opaqueMaterialDefaultSidedness` optie ingesteld op `SingleSided` . Deze optie is standaard ingesteld op `DoubleSided` .

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: Rendering-instelling

Er zijn drie verschillende modi:

**Normaal:** In deze modus worden netten altijd weer gegeven wanneer ze worden geconverteerd. Dit betekent dat mazen die zijn geconverteerd met `opaqueMaterialDefaultSidedness` ingesteld op, `SingleSided` altijd worden gerenderd met behulp van de functie voor het afruimen van back-ups, zelfs wanneer ze een knip vlak overlappen.

**DynamicDoubleSiding:** Als in deze modus een knip vlak een net kruist, wordt dit automatisch overgeschakeld naar de rendering met dubbele weer gave. Deze modus is de standaard modus.

**AlwaysDoubleSided:** Hiermee wordt alle geometrie van één zijde geforceerd gerenderd op elk moment. Deze modus wordt meestal weer gegeven, zodat u eenvoudig de prestatie-impact kunt vergelijken tussen :::no-loc text="single-sided"::: en :::no-loc text="double-sided"::: weer gave.

Het wijzigen van de :::no-loc text="single-sided"::: instellingen voor rendering kan als volgt worden uitgevoerd:

```cs
void ChangeSingleSidedRendering(RenderingSession session)
{
    SingleSidedSettings settings = session.Connection.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<RenderingSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Connection()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>API-documentatie

* [C# RenderingConnection. SingleSidedSettings eigenschap](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.singlesidedsettings)
* [C++ RenderingConnection:: SingleSidedSettings ()](/cpp/api/remote-rendering/renderingconnection#singlesidedsettings)

## <a name="next-steps"></a>Volgende stappen

* [Vlakken knippen](cut-planes.md)
* [De model conversie configureren](../../how-tos/conversion/configure-model-conversion.md)