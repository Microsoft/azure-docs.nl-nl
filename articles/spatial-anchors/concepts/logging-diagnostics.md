---
title: Logboekregistratie en diagnostische gegevens
description: Uitgebreide uitleg over het genereren en ophalen van logboek registratie en diagnostische gegevens in ruimtelijke ankers van Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95494638"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Logboek registratie en diagnose in ruimtelijke beankeringen van Azure

Ruimtelijke ankers van Azure bieden een standaard logboek registratie mechanisme dat nuttig is voor het ontwikkelen van apps. De logboek registratie modus ruimtelijk ankers is handig als u meer informatie nodig hebt voor fout opsporing. Met logboek registratie van diagnostische gegevens worden installatie kopieën van de omgeving opgeslagen.

## <a name="standard-logging"></a>Standaard logboek registratie
In de spatiale-ankers API kunt u zich abonneren op het logboek registratie mechanisme om nuttige logboeken te krijgen voor de ontwikkeling en fout opsporing van toepassingen. De standaard logboek registratie-Api's slaan geen afbeeldingen van de omgeving op de schijf van het apparaat op. De SDK biedt deze logboeken als gebeurtenis-Call Backs. U kunt deze logboeken integreren in het logboek registratie mechanisme van de toepassing.

### <a name="configuration-of-log-messages"></a>Configuratie van logboek berichten
Er zijn twee retour aanroepen die van belang zijn voor de gebruiker. In het volgende voor beeld ziet u hoe u de sessie kunt configureren.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Gebeurtenissen en eigenschappen

Deze gebeurtenis-Call backs worden geleverd om logboeken en fouten uit de sessie te verwerken:

- [LogLevel](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Hiermee geeft u het detail niveau op voor de gebeurtenissen die moeten worden ontvangen uit de runtime.
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): biedt standaard logboek gebeurtenissen voor fout opsporing.
- [Fout](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): bevat logboek gebeurtenissen die in de runtime worden beschouwd als fouten.

## <a name="diagnostics-logging"></a>Logboek registratie van diagnostische gegevens

Naast de standaard modus van voor logboek registratie, heeft ruimtelijke ankers ook een diagnostische modus. In de diagnostische modus worden installatie kopieën van de omgeving vastgelegd en op de schijf vastgelegd. U kunt deze modus gebruiken om bepaalde soorten problemen op te lossen, zoals het niet zoals verwacht een anker te vinden. Schakel logboek registratie van diagnostische gegevens alleen in om een specifiek probleem te reproduceren. Schakel deze vervolgens uit. Schakel diagnostische gegevens niet in wanneer u uw apps normaal uitvoert.

Tijdens een ondersteunings interactie met micro soft kan een micro soft-vertegenwoordiger vragen of u een diagnostische bundel moet indienen voor verder onderzoek. In dit geval kunt u ervoor kiezen om diagnostische gegevens in te scha kelen en het probleem te reproduceren zodat u de diagnostische bundel kunt indienen.

Als u een diagnostische logboek naar micro soft verzendt zonder voorafgaande bevestiging van een micro soft-vertegenwoordiger, wordt de inzending niet beantwoord.

In de volgende secties ziet u hoe u de diagnostische modus inschakelt en hoe u Diagnostische logboeken naar micro soft verzendt.

### <a name="enable-diagnostics-logging"></a>Diagnostische logboekregistratie inschakelen

Wanneer u een sessie voor diagnostische logboek registratie inschakelt, hebben alle bewerkingen in de sessie corresponderende diagnostische logboek registratie in het lokale bestands systeem. Tijdens het vastleggen worden installatie kopieën van de omgeving op de schijf opgeslagen.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>De diagnostische bundel verzenden

Het volgende code fragment laat zien hoe u een diagnostische bundel naar micro soft verzendt. Deze bundel bevat installatie kopieën van de omgeving die is vastgelegd door de sessie nadat u Diagnostische gegevens hebt ingeschakeld.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Onderdelen van een diagnose bundel
De diagnostische bundel kan de volgende informatie bevatten:

- **Keyframes**: installatie kopieën van de omgeving die tijdens de sessie is vastgelegd terwijl diagnostische gegevens zijn ingeschakeld.
- **Logboeken**: logboek gebeurtenissen vastgelegd door de runtime.
- **Meta gegevens van sessie**: meta gegevens die de sessie identificeren.