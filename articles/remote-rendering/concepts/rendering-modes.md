---
title: Weergavemodellen
description: Beschrijft de verschillende rendermethoden van de server
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594498"
---
# <a name="rendering-modes"></a>Weergavemodellen

Externe rendering biedt twee belang rijke modi van de werking, de **TileBasedComposition** -modus en de **DepthBasedComposition** -modus. Deze modi bepalen hoe de werk belasting over meerdere Gpu's op de server wordt verdeeld. De modus moet worden opgegeven op verbindings tijd en kan niet worden gewijzigd tijdens runtime.

Beide modi worden geleverd met voor delen, maar ook met inherente functie beperkingen, dus het kiezen van de meest geschikte modus is specifiek use-case.

## <a name="modes"></a>Vormen

De twee modi worden nu uitvoerig besproken.

### <a name="tilebasedcomposition-mode"></a>Modus ' TileBasedComposition '

In de **TileBasedComposition** -modus worden met elke betrokken GPU specifieke subrechthoeken (tegels) op het scherm weer gegeven. De belangrijkste GPU maakt de uiteindelijke afbeelding van de tegels voordat deze wordt verzonden als een video frame naar de client. Daarom moeten alle Gpu's dezelfde set resources voor rendering hebben, zodat de geladen assets in één GPU-geheugen moeten passen.

De weergave kwaliteit in deze modus is iets beter dan in de **DepthBasedComposition** -modus, omdat MSAA kan werken aan de volledige set geometrie voor elke GPU. In de volgende scherm afbeelding ziet u dat antialiasing goed werkt voor beide randen:

![MSAA in TileBasedComposition](./media/service-render-mode-quality.png)

Bovendien kan in deze modus elk deel worden overgeschakeld naar een transparant materiaal of worden overgeschakeld naar de **weer gave-** modus via de [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Modus ' DepthBasedComposition '

In de **DepthBasedComposition** -modus wordt elke betrokken GPU weer gegeven met volledige scherm resolutie, maar slechts een subset van mazen. De uiteindelijke samen stelling van de installatie kopie op de belangrijkste GPU zorgt ervoor dat onderdelen goed worden samengevoegd op basis van hun diep gaande informatie. Natuurlijk wordt de geheugen lading verdeeld over de Gpu's, waardoor modellen die niet in één GPU-geheugen passen, kunnen worden weer gegeven.

Elke enkele GPU maakt gebruik van MSAA om lokale inhoud te autoaliasren. Er kan echter sprake zijn van inherente aliassen tussen de randen van afzonderlijke Gpu's. Dit effect wordt beperkt door de postprocessing van de uiteindelijke afbeelding, maar de MSAA-kwaliteit is nog steeds erger dan in de **TileBasedComposition** -modus.

MSAA-artefacten worden geïllustreerd in de volgende afbeelding: ![ MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

Antialiasing werkt goed tussen de Sculpture en het gordijn, omdat beide onderdelen op dezelfde GPU worden weer gegeven. Anderzijds toont de rand tussen gordijn en muur een aantal aliassen, omdat deze twee delen bestaan uit afzonderlijke Gpu's.

De grootste beperking van deze modus is dat geometrie onderdelen niet dynamisch kunnen worden overgeschakeld naar transparante materialen, en de **kijk** modus werkt niet voor de [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Andere functies voor het overschrijven van de status (overzicht, kleur tint,...) werken wel. Ook materiaal dat is gemarkeerd als transparant tijdens de conversie, werkt goed in deze modus.

### <a name="performance"></a>Prestaties

De prestatie kenmerken voor beide modi variëren op basis van de use-case en zijn moeilijk te reden of bieden algemene aanbevelingen. Als u niet beperkt bent door de hierboven genoemde beperkingen (geheugen of transparantie/Lees-through), wordt u aangeraden beide modi te proberen en de prestaties te bewaken met behulp van verschillende camera posities.

## <a name="setting-the-render-mode"></a>De weergave modus instellen

De weergave modus die wordt gebruikt op een externe rendering-server, wordt opgegeven tijdens `RenderingSession.ConnectAsync` via de `RendererInitOptions` .

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>API-documentatie

* [C# RenderingSession. ConnectAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [RendererInitOptions struct van C#](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [C++ RenderingSession:: ConnectToConnectAsyncRuntime ()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [Structuur van C++ RendererInitOptions](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>Volgende stappen

* [Sessies](../concepts/sessions.md)
* [Overschrijvings onderdeel hiërarchische status](../overview/features/override-hierarchical-state.md)