---
title: Faseruimte
description: Hierin worden de instellingen voor de fase ruimte en het gebruik beschreven
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594007"
---
# <a name="stage-space"></a>Faseruimte

Bij het uitvoeren van ARR op een apparaat dat de gegevens van de hoofd tracering levert, zoals HoloLens 2, wordt het hoofd pose verzonden naar de gebruikers toepassing en de server. De ruimte waarin de hoofd transformatie is gedefinieerd, wordt de *fase ruimte* genoemd.

Als u lokale en externe inhoud wilt uitlijnen, wordt ervan uitgegaan dat de stage ruimte en de wereld wijde ruimte identiek zijn op de client en server. Als de gebruiker besluit om boven op de camera een extra trans formatie toe te voegen, moet deze worden verzonden naar de server en moet de lokale en externe inhoud op de juiste manier worden uitgelijnd.

Typische redenen voor het verplaatsen van de stage ruimte zijn [wereld wijde vergrendelings hulpprogramma's](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) of andere echte verankerings technieken en het verplaatsen van een virtueel teken in vr.

## <a name="stage-space-settings"></a>Instellingen voor fase ruimte

> [!CAUTION]
> EXPERIMENT: deze functie is experimenteel en zal in de loop van de tijd veranderen. Voor het bijwerken van nieuwere client-SDK-versies is mogelijk extra werk vereist om de code bij te werken. De huidige implementatie verbreekt de uitlijning van lokale/externe inhoud voor een kort moment wanneer u de oorspronkelijke fase wijzigt.
Daarom is het momenteel alleen bedoeld om te worden gebruikt voor wereld wijde vergrendelings doeleinden, zoals ankers die gedurende een bepaalde periode slechts een zeer kleine wijziging vertonen.

Om de server op de hoogte te stellen dat een extra trans formatie wordt toegepast op de werk ruimte, moet de oorsprong die is gedefinieerd door een positie en een rotatie in de wereld wijde ruimte worden verzonden. U kunt toegang krijgen tot deze instelling via de *werk ruimte-instelling*.

> [!IMPORTANT]
> In de [simulatie van het bureau blad](../../concepts/graphics-bindings.md) wordt de locatie van de camera beschikbaar gesteld door de gebruikers toepassing. In dit geval moet het instellen van de oorsprong van de fase ruimte worden overgeslagen omdat deze al is vermenigvuldigd met de camera transformatie.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Ruimte script voor Unity-fase

Unit-integratie biedt een script met de naam **StageSpace** dat kan worden toegevoegd aan de bovenliggende GameObject van de camera. Zodra dit script aanwezig is, wordt het instellen van de oorsprong van de fase van de werk ruimte zorgvuldig toegepast.

## <a name="next-steps"></a>Volgende stappen

* [Afbeeldings binding](../../concepts/graphics-bindings.md)
* [Vertraagde fase van het project](late-stage-reprojection.md)
