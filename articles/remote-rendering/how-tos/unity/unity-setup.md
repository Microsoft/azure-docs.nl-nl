---
title: Remote Rendering voor Unity instellen
description: Externe rendering van Azure initialiseren in een Unity-project
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594211"
---
# <a name="set-up-remote-rendering-for-unity"></a>Remote Rendering voor Unity instellen

Voor het inschakelen van de externe rendering van Azure (ARR) in eenheid, bieden we speciale methoden die de specifieke aspecten van de maat eenheid verzorgen.

## <a name="startup-and-shutdown"></a>Opstarten en afsluiten

Gebruik om de externe rendering te initialiseren `RemoteManagerUnity` . Deze klasse maakt deel uit van het generieke `RenderingConnection` , maar implementeert al unit-specifieke Details voor u. Unit maakt bijvoorbeeld gebruik van een specifiek coördinaten systeem. Wanneer u aanroept `RemoteManagerUnity.Initialize` , wordt de juiste Conventie ingesteld. Voor de aanroep moet u ook de eenheids camera opgeven die moet worden gebruikt voor het weer geven van de extern gerenderde inhoud.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Voor het afsluiten van externe rendering, roept u aan `RemoteManagerStatic.ShutdownRemoteRendering()` .

Nadat een `RenderingSession` is gemaakt en als primaire rendering-sessie is gekozen, moet deze zijn geregistreerd bij `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Volledige voorbeeld code

In de volgende code ziet u alle stappen die nodig zijn voor het initialiseren van Azure remote rendering in Unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Gebruiks gemak functies

### <a name="session-state-events"></a>Sessie status gebeurtenissen

`RemoteManagerUnity.OnSessionUpdate` Raadpleeg de code documentatie voor meer informatie over het meebrengen van gebeurtenissen voor de sessie status.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` is een optioneel onderdeel voor het stroom lijnen van het instellen en beheren van sessies. Het bevat opties voor het automatisch stoppen van de sessie wanneer de toepassing wordt afgesloten of de afspeel modus wordt afgesloten in de editor, en de sessie lease automatisch vernieuwen wanneer dat nodig is. De gegevens worden in de cache opgeslagen, zoals de sessie-eigenschappen (Zie de `LastProperties` variabele), en er worden gebeurtenissen weer gegeven voor sessie status wijzigingen en sessie fouten.

Er mag niet meer dan één exemplaar van `ARRServiceUnity` tegelijkertijd zijn. Het is bedoeld om sneller aan de slag te gaan met het implementeren van enkele algemene functionaliteit. Voor een grotere toepassing is het mogelijk beter om deze dingen zelf te doen, maar ook.

`ARRServiceUnity`Zie [zelf studie: externe gerenderde modellen weer geven](../../tutorials/unity/view-remote-models/view-remote-models.md)voor een voor beeld van het instellen en gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Het Remote Rendering-pakket voor Unity installeren](install-remote-rendering-unity-package.md)
* [Zelfstudie: Extern gegenereerde modellen bekijken](../../tutorials/unity/view-remote-models/view-remote-models.md)
