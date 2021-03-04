---
title: Overzicht van runtime-SDK
description: Vertrouwd raken met de runtime-SDK voor object ankers.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 74663f05c5ff995a090c7cd35e4edf46a754da17
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034605"
---
# <a name="runtime-sdk-overview"></a>Overzicht van runtime-SDK

In deze sectie vindt u een overzicht van de runtime-SDK voor object ankerpunten, die wordt gebruikt voor het detecteren van objecten met behulp van een object ankers model. U krijgt inzicht in de manier waarop een object wordt weer gegeven en waarvoor de verschillende onderdelen worden gebruikt.

Alle typen die hieronder worden beschreven, kunt u vinden in de naam ruimte **micro soft. MixedReality. ObjectAnchors** .

## <a name="types"></a>Typen

### <a name="objectmodel"></a>ObjectModel

Een [objectmodel](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectmodel) vertegenwoordigt de geometrie van een fysiek object en codeert de benodigde para meters voor detectie en een schatting. Deze moet worden gemaakt met behulp van de [object ankers-service](../quickstarts/get-started-model-conversion.md). Vervolgens kan een toepassing het gegenereerde model bestand laden met behulp van de object-ankers-API en een query uitvoeren op de net embedded in dat model voor visualisatie.

### <a name="objectsearcharea"></a>ObjectSearchArea

Een [ObjectSearchArea](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) Hiermee geeft u de ruimte op die moet worden gezocht voor een of meer objecten. Het wordt gedefinieerd door een knoop punt-ID van ruimtelijk diagram en ruimtelijke grenzen in het coördinaten systeem vertegenwoordigd door de knoop punt-ID van ruimtelijke grafiek. De SDK voor object ankerpunten ondersteunt vier typen grenzen, namelijk het **veld met de weer gave**, **het begrenzingsvak**, de **bol** en de **locatie**.

### <a name="objectquery"></a>Object query

Een [object query](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery) vertelt een **object waarnemer** hoe objecten van een bepaald model moeten worden gevonden. Het biedt de volgende instel bare-para meters waarvan de standaard waarden kunnen worden opgehaald uit een object model.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

De eigenschap [MinSurfaceCoverage](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) geeft de waarde aan om een instantie zoals gedetecteerd te beschouwen.

Voor elke object kandidaat berekent een **waarnemer** de verhouding tussen de overlappende Opper vlakken tussen het getransformeerde object model en de scène. Vervolgens rapporteert dat kandidaat naar toepassing alleen wanneer de dekkings verhouding boven een bepaalde drempel waarde ligt.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

De eigenschap [IsExpectedToBeStandingOnGroundPlane](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) geeft aan of het doel object naar verwachting op het massa vlak is.

Een massa vlak is de laagste horizontale vloer in het zoek gebied. Het biedt een goede beperking voor het mogelijke object. Als u deze vlag inschakelt, wordt de **waarnemer** in staat gesteld om de pose te ramen in een beperkte ruimte en de nauw keurigheid te verbeteren. Deze para meter wordt genegeerd als het model niet op de massa plaat zou moeten worden opgebouwd.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

De eigenschap [ExpectedMaxVerticalOrientationInDegrees](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) geeft de verwachte maximum hoek in graden op tussen de richting van een object exemplaar en de ernst.

Deze para meter biedt een andere beperking voor de richting van een geschatte pose. Als een object bijvoorbeeld up-to-right is, kan deze para meter 0 zijn. Object ankerpunten moeten geen objecten detecteren die afwijken van het model. Als een model up-to-right is, detecteert het geen exemplaar dat naast elkaar wordt gedetecteerd. Er wordt een nieuw model gebruikt voor de indeling van de ondersteboven. Dezelfde regel is van toepassing op de aanscharniering.

#### <a name="maxscalechange"></a>MaxScaleChange

De eigenschap [MaxScaleChange](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) geeft de maximale wijziging van de object schaal aan (binnen 0 ~ 1) ten opzichte van ruimtelijke toewijzing. De geschatte schaal wordt toegepast op getransformeerde object hoekpunten gecentreerd op basis van de oorsprong en as-uitgelijnd. Geschatte schalen zijn mogelijk niet de werkelijke schaal tussen een CAD-model en de fysieke weer gave ervan, maar sommige waarden waarmee de app een object model dicht bij ruimtelijke toewijzing op het fysieke object kan weer geven.

#### <a name="searchareas"></a>SearchAreas

De eigenschap [SearchAreas](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) geeft een matrix van ruimtelijke grenzen aan waar object (en) moeten worden gevonden.

De **waarnemer** zoekt naar objecten in de samenvoeg ruimte van alle zoek gebieden die in een query zijn opgegeven. In deze release retour neren we Maxi maal één object met het hoogste vertrouwen om de latentie te verminderen.

### <a name="objectinstance"></a>ObjectInstance

Een [ObjectInstance](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectinstance) vertegenwoordigt een hypothetische positie waarbij een exemplaar van een bepaald model zich in het coördinaten systeem van HoloLens kan bevinden. Elk exemplaar wordt geleverd met een `SurfaceCoverage` eigenschap om aan te geven hoe goed de geschatte pose is.

Een exemplaar wordt gemaakt door de aanroepende `ObjectObserver.DetectAsync` methode en wordt automatisch bijgewerkt op de achtergrond wanneer dat actief is. Een toepassing kan worden geluisterd naar de gebeurtenis status gewijzigd in een bepaald exemplaar of door de tracerings modus te wijzigen om de update te onderbreken/hervatten. Er wordt automatisch een exemplaar van de **waarnemer** verwijderd wanneer het bijhouden is verbroken.

### <a name="objectobserver"></a>ObjectObserver

Een [ObjectObserver](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectobserver) laadt object modellen, detecteert hun instanties en rapporteert 6-DOF pose van elk exemplaar in het coördinaten systeem van HoloLens.

Hoewel elk object model of exemplaar wordt gemaakt op basis van een **waarnemer**, zijn hun levens duur onafhankelijk. Een toepassing kan een waarnemer verwijderen en het object model of exemplaar blijven gebruiken.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

De [ObjectDiagnosticSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) registreert diagnose en schrijft gegevens naar een archief.

Een diagnostische archief bevat de scène van de Cloud, de status van de waarneming en informatie over de modellen. Deze informatie is nuttig om mogelijke runtime problemen te identificeren. Raadpleeg de [Veelgestelde vragen](../faq.md)voor meer informatie.

## <a name="runtime-sdk-usage-and-details"></a>Gebruik en Details van runtime-SDK

Deze sectie bevat de basis beginselen van het gebruik van de runtime-SDK. U krijgt voldoende context om te bladeren door de voorbeeld toepassingen om te zien hoe object ankers op een holistische manier worden gebruikt.

### <a name="initialization"></a>Initialisatie

Toepassingen moeten de API aanroepen `ObjectObserver.IsSupported()` om te bepalen of object ankers worden ondersteund op het apparaat voordat het wordt gebruikt. Als de `ObjectObserver.IsSupported()` API wordt geretourneerd `false` , controleert u of de toepassing de **spatialPerception** -mogelijkheid heeft en/of upgrade naar het nieuwste HoloLens-besturings systeem is ingeschakeld.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Vervolgens maakt de toepassing een object waarnemer en worden de benodigde modellen geladen die zijn gegenereerd door de [object-ankers model conversie service](../quickstarts/get-started-model-conversion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

De toepassing maakt een query om exemplaren van dat model binnen een spatie te detecteren.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Elk gedetecteerd exemplaar wordt standaard automatisch bijgehouden door de **waarnemer**. We kunnen deze instanties eventueel bewerken door de tracerings modus te wijzigen of naar de gewijzigde gebeurtenis van de status te Luis teren.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

In de gebeurtenis status gewijzigd kunnen we de meest recente status opvragen of een exemplaar verwijderen als het bijhouden is mislukt.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Een toepassing kan ook eventueel een of meerdere diagnostische sessies registreren voor offline fout opsporing.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Ten slotte publiceren we resources door alle objecten te ontheffen.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```
