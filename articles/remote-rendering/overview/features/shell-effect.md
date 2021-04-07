---
title: Shell-weergave
description: Uitleg over het gebruik van het weer geven van shell-effecten
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594160"
---
# <a name="shell-rendering"></a>Shell-weergave

De shell-status van het onderdeel voor het overschrijven van de [hiërarchische toestand](../../overview/features/override-hierarchical-state.md) is een transparantie-effect. In tegens [telling tot het weer geven van](../../overview/features/override-hierarchical-state.md) de weer gave, is alleen de front-grootste laag van objecten zichtbaar, Akin tot ondoorzichtige rendering. Daarnaast kan de normale vormgeving van de objecten worden gewijzigd wanneer ze worden gerenderd als shells. Het effect is bedoeld voor gebruik in gevallen waarbij de gebruiker visueel moet worden geleid van niet-belang rijke onderdelen, terwijl de ruimtelijke bewustmaking voor de hele scène behouden blijft.

U kunt de weer gave van door Shell gerenderde objecten configureren via de `ShellRenderingSettings` globale status. Alle objecten die gebruikmaken van shell-rendering, gebruiken dezelfde instelling. Er zijn geen object parameters.

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings-para meters

Class `ShellRenderingSettings` bevat de instellingen met betrekking tot de globale shell-rendering-eigenschappen:

| Parameter      | Type    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | De hoeveelheid verzadiging die moet worden toegepast op de gebruikelijke uiteindelijke object kleur, in het bereik 0 (geen verzadiging) op 1 (volledige verzadiging) |
| `Opacity`      | float   | De dekking van de door de shell gerenderde objecten, in bereik 0 (onzichtbaar) op 1 (volledig ondoorzichtig) |

Zie ook de volgende tabel voor voor beelden van de effecten van de para meters wanneer deze worden toegepast op een hele scène:

|                | 0 | 0,25 | 0,5 | 0,75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Verzadiging** | ![Deverzadiging-0,0](./media/shell-desaturation-00.png) | ![Deverzadiging-0,25](./media/shell-desaturation-025.png) | ![Deverzadiging-0,5](./media/shell-desaturation-05.png) | ![Deverzadiging-0,75](./media/shell-desaturation-075.png) | ![Deverzadiging-1,0](./media/shell-desaturation-10.png) |
| **Dekking**      | ![Dekking-0,0](./media/shell-opacity-00.png) | ![Dekking-0,25](./media/shell-opacity-025.png) | ![Dekking-0,5](./media/shell-opacity-05.png) | ![Dekking-0,75](./media/shell-opacity-075.png) | ![Dekking-1,0](./media/shell-opacity-10.png) |

Het shell-effect wordt toegepast op de uiteindelijke dekkende kleur waarbij de scène anders wordt weer gegeven. Dit omvat de tint voor het overschrijven van een [hiërarchische toestand](../../overview/features/override-hierarchical-state.md).

## <a name="example"></a>Voorbeeld

De volgende code toont een voor beeld van het gebruik van de `ShellRenderingSettings` status via de API:

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Prestaties

De functie voor het weer geven van shells heeft een kleine constante overhead in vergelijking met standaard transparante rendering. Het is aanzienlijk sneller dan het gebruik van transparante materialen op objecten of [door de weer](../../overview/features/override-hierarchical-state.md) gave. Prestaties kunnen sterk afnemen als alleen delen van de scène worden overgeschakeld naar de weer gave van de shell. Deze degradatie kan optreden als gevolg van het weer geven van objecten die moeten worden gerenderd. In dat opzicht gedraagt de prestaties zich op dezelfde manier als de functie voor het [knippen van abonnementen](../../overview/features/cut-planes.md) .

## <a name="next-steps"></a>Volgende stappen

* [Overschrijvings onderdeel hiërarchische status](../../overview/features/override-hierarchical-state.md)