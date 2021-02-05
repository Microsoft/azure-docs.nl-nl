---
title: Servergrootten
description: Beschrijft de verschillende server grootten die kunnen worden toegewezen
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594397"
---
# <a name="server-sizes"></a>Servergrootten

Externe rendering van Azure is beschikbaar in twee server configuraties: `Standard` en `Premium` .

## <a name="polygon-limits"></a>Polygoon limieten

Externe rendering met `Standard` grootte server heeft een maximale grootte van 20.000.000 veelhoeken voor de scène. Externe rendering met `Premium` grootte dwingt geen vaste maximum af, maar de prestaties kunnen gedegradeerd worden als uw inhoud de weergave mogelijkheden van de service overschrijdt.

Wanneer de renderer op een ' standaard ' server grootte op deze beperking van toepassing is, schakelt deze over naar een achtergrond met een dambord:

![In de scherm afbeelding ziet u een raster met zwarte en witte vier kanten met een extra menu.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>De server grootte opgeven

Het gewenste type server configuratie moet worden opgegeven bij het weer geven van de initialisatie tijd van de sessie. Het kan niet worden gewijzigd in een actieve sessie. De volgende code voorbeelden tonen de plaats waar de server grootte moet worden opgegeven:

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

Voor het [Power shell-voorbeeld script](../samples/powershell-example-scripts.md)moet de gewenste server grootte worden opgegeven in het `arrconfig.json` bestand:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Hoe de renderer het aantal veelhoeken evalueert

Het aantal veelhoeken dat wordt overwogen voor de beperkings test, is het aantal veelhoeken dat daad werkelijk wordt door gegeven aan de renderer. Deze geometrie is doorgaans de som van alle geïnstantieerd modellen, maar er zijn ook uitzonde ringen. De volgende geometrie is **niet opgenomen**:
* Geladen model instanties die volledig buiten de weer gave-frustum zijn.
* Modellen of model onderdelen die worden overgeschakeld naar onzichtbaar, met behulp van het [onderdeel hiërarchische status onderdrukking](../overview/features/override-hierarchical-state.md).

Daarom is het mogelijk om een toepassing te schrijven die de `standard` grootte verlaagt van meerdere modellen met een aantal veelhoeken dicht bij de limiet voor elk enkel model. Wanneer de toepassing slechts één model per keer weergeeft, wordt het dambord niet geactiveerd.

### <a name="how-to-determine-the-number-of-polygons"></a>Het aantal veelhoeken bepalen

Er zijn twee manieren om het aantal veelhoeken van een model of scène te bepalen die bijdragen aan de budget limiet van de `standard` configuratie grootte:
* Haal het JSON-bestand van de [conversie uitvoer](../how-tos/conversion/get-information.md)op in de sectie model conversie en controleer de `numFaces` vermelding in de [ *inputStatistics*](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Als uw toepassing een dynamische inhoud behandelt, kan het aantal gerenderde veelhoeken dynamisch worden opgevraagd tijdens runtime. Gebruik een [query voor prestatie analyse](../overview/features/performance-queries.md#performance-assessment-queries) en controleer het `polygonsRendered` lid in de `FrameStatistics` struct. Het `PolygonsRendered` veld wordt ingesteld op `bad` wanneer de renderer de grens van de veelhoek opraakt. De achtergrond van het dambord is altijd vervaagd met enige vertraging om ervoor te zorgen dat de gebruikers actie kan worden uitgevoerd na deze asynchrone query. Gebruikers actie kan bijvoorbeeld model instanties verbergen of verwijderen.

## <a name="pricing"></a>Prijzen

Raadpleeg de pagina met [prijzen voor externe rendering](https://azure.microsoft.com/pricing/details/remote-rendering) voor een gedetailleerde uitsplitsing van de prijzen voor elk type configuratie.

## <a name="next-steps"></a>Volgende stappen
* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
* [Modelconversie](../how-tos/conversion/model-conversion.md)

