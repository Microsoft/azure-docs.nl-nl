---
title: Beperkingen
description: Code beperkingen voor SDK-functies
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 68c0c04feba2779598a500c84b2ba4a9086b104d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593942"
---
# <a name="limitations"></a>Beperkingen

Een aantal functies hebben een grootte, aantal of andere beperkingen.

## <a name="azure-frontend"></a>Azure-front-end

De volgende beperkingen zijn van toepassing op de front-end-API (C++ en C#):
* Totaal aantal [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) -exemplaren per proces: 16.
* Totaal aantal [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession) -exemplaren per [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient): 16.

## <a name="objects"></a>Objecten

* Totaal aantal toegestane objecten van één type ([entiteit](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md), enzovoort): 16.777.215.
* Totaal aantal toegestane actieve knip abonnementen: 8.

## <a name="geometry"></a>Geometrie

* **Animatie:** Animaties zijn beperkt tot het animeren van afzonderlijke trans formaties van [Game-objecten](../concepts/entities.md). Skeletal-animaties met skins of vertex animaties worden niet ondersteund. Animatie tracks van het bron Asset-bestand blijven niet behouden. In plaats daarvan moeten animaties voor object transformatie worden aangedreven door client code.
* **Aangepaste shaders:** Het ontwerpen van aangepaste shaders wordt niet ondersteund. Alleen ingebouwde [kleur materialen](../overview/features/color-materials.md) of PBR- [materialen](../overview/features/pbr-materials.md) kunnen worden gebruikt.
* **Maximum aantal afzonderlijke materialen** in een asset: 65.535. Voor meer informatie over het automatisch verminderen van het aantal materialen raadpleegt u het hoofd stuk materiaal uit de [duplicatie](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* **Maximale dimensie van één textuur**: 16.384 x 16.384. Grotere bron structuren worden verkleind door het conversie proces.

### <a name="overall-number-of-polygons"></a>Totaal aantal veelhoeken

Het toegestane aantal veelhoeken voor alle geladen modellen is afhankelijk van de grootte van de virtuele machine die wordt door gegeven aan [de rest API voor sessie beheer](../how-tos/session-rest-api.md#create-a-session):

| Server grootte | Maximum aantal veelhoeken |
|:--------|:------------------|
|Standard| 20.000.000 |
|ultieme| geen limiet |

Zie het hoofd stuk [Server grootte](../reference/vm-sizes.md) voor gedetailleerde informatie over deze beperking.

## <a name="platform-limitations"></a>Platform beperkingen

**Windows 10 Desktop**

* Win32/x64 is het enige win32-platform dat wordt ondersteund. Win32/x86 wordt niet ondersteund.

**HoloLens 2**

* De [weer gave van de HW-camera](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) functie wordt niet ondersteund.