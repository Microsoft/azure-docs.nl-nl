---
title: Debug Rendering
description: Overzicht van het renderen van fouten aan de server zijde
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f6d79f41843069fe6cafe1fa1358ac6c1aab12e6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591732"
---
# <a name="debug-rendering"></a>Debug Rendering

De API voor het weer geven van fout opsporing biedt een aantal algemene opties voor het wijzigen van de server-side rendering met verschillende fout opsporingsgegevens.

## <a name="available-debug-rendering-effects"></a>Beschik bare rendering van fout opsporing

|Instelling                          | Effect                               |
|---------------------------------|:-------------------------------------|
|Frame teller                    | Hiermee wordt een tekst-overlay weer gegeven in de linkerbovenhoek van het kader. De tekst toont de huidige frame-ID aan de server zijde, die voortdurend wordt verhoogd als de weer gave wordt voortgezet. |
|Aantal veelhoeken                    | Hiermee wordt een tekst-overlay weer gegeven in de linkerbovenhoek van het kader. De tekst toont de momenteel gerenderde hoeveelheid veelhoeken, dezelfde waarde als de query op [prestatie query's aan de server zijde](performance-queries.md)| 
|Draad model                        | Als deze functie is ingeschakeld, wordt alle object geometrie die op de server is geladen, weer gegeven in de draad model modus. In deze modus worden alleen de randen van veelhoeken in pixels omgezet. |

Met de volgende code worden de volgende fout opsporings effecten ingeschakeld:

```cs
void EnableDebugRenderingEffects(RenderingSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Connection.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Connection()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
}
```

![Fout opsporing weer geven](./media/debug-rendering.png)

> [!NOTE]
> Alle rendering-effecten voor fout opsporing zijn algemene instellingen die van invloed zijn op het hele frame.

## <a name="use-cases"></a>Gebruiksvoorbeelden

De API voor het renderen van fout opsporing is bedoeld voor eenvoudige probleemoplossings taken, zoals controleren of de service verbinding daad werkelijk actief is en correct werkt. De opties voor tekst weergave beïnvloeden rechtstreeks de video frames die omlaag worden gestreamd. Als u deze functie inschakelt, wordt er gecontroleerd of er nieuwe frames zijn ontvangen en de video correct is gedecodeerd.

De gegeven effecten geven echter geen gedetailleerde introspectie in de status van de service. De [prestatie query's aan de server zijde](performance-queries.md) worden aanbevolen voor deze use-case.

## <a name="performance-considerations"></a>Prestatieoverwegingen

* Het inschakelen van de tekst-overlays is niet alleen mogelijk als er geen prestatie overhead optreedt.
* Het inschakelen van de draad model modus heeft een niet-trivial prestatie overhead, maar kan variëren afhankelijk van de scène. Voor complexe scènes kan deze modus ertoe leiden dat de frame frequentie onder het doel 60-Hz wordt geplaatst.

## <a name="api-documentation"></a>API-documentatie

* [C++ RenderingConnection::D ebugRenderingSettings ()](/cpp/api/remote-rendering/renderingconnection#debugrenderingsettings)

## <a name="next-steps"></a>Volgende stappen

* [Weergavemodellen](../../concepts/rendering-modes.md)
* [Prestatiequery's aan serverzijde](performance-queries.md)