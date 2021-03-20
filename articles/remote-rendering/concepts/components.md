---
title: Onderdelen
description: Definitie van onderdelen in het bereik van Azure remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592191"
---
# <a name="components"></a>Onderdelen

Azure remote rendering maakt gebruik van het [systeem patroon entity component](https://en.wikipedia.org/wiki/Entity_component_system) . Hoewel [entiteiten](entities.md) de positie en de hiërarchische samen stelling van objecten vertegenwoordigen, zijn onderdelen verantwoordelijk voor het implementeren van het gedrag.

De meest gebruikte typen onderdelen zijn [:::no-loc text="mesh components":::](meshes.md) , waarmee u netten kunt toevoegen aan de rendering-pijp lijn. Op dezelfde manier worden [lichte onderdelen](../overview/features/lights.md) gebruikt voor het toevoegen van de onderdelen verlichting en [knip vlak](../overview/features/cut-planes.md) , waarmee open mazen worden geknipt.

Al deze onderdelen gebruiken de trans formatie (positie, rotatie, schaal) van de entiteit waaraan ze zijn gekoppeld, als referentie punt.

## <a name="working-with-components"></a>Werken met onderdelen

U kunt eenvoudig componenten programmatisch toevoegen, verwijderen en bewerken:

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

Een onderdeel wordt tijdens de aanmaak gekoppeld aan een entiteit. U kunt deze later niet naar een andere entiteit verplaatsen. Onderdelen worden expliciet verwijderd met `Component.Destroy()` of automatisch wanneer de entiteit eigenaar van het onderdeel wordt vernietigd.

Er kan slechts één exemplaar van elk onderdeel type tegelijk aan een entiteit worden toegevoegd.

## <a name="unity-specific"></a>Eenheids specifiek

De unit-integratie heeft aanvullende uitbreidings functies voor interactie met-onderdelen. Zie [Unit Game-objecten en-onderdelen](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>API-documentatie

* [C#-ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection. CreateComponent ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [C#-entiteit. FindComponentOfType ()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++ ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection:: CreateComponent ()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [C++-entiteit:: FindComponentOfType ()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Volgende stappen

* [Objectgrenzen](object-bounds.md)
* [Meshes](meshes.md)