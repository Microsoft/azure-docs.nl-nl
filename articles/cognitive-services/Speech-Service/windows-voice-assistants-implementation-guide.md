---
title: Spraak assistenten in Windows-hoger vergrendelings richtlijnen
titleSuffix: Azure Cognitive Services
description: De instructies voor het implementeren van spraak activering en de mogelijkheden voor het vergren delen van een spraak agent-toepassing.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939856"
---
# <a name="implementing-voice-assistants-on-windows"></a>Spraak assistenten implementeren in Windows

In deze hand leiding vindt u meer informatie over de implementatie voor het maken van een telefoon assistent in Windows.

## <a name="implementing-voice-activation"></a>Spraak activering implementeren

Nadat u [uw omgeving hebt ingesteld](how-to-windows-voice-assistants-get-started.md) en [hoe Voice-activering werkt](windows-voice-assistants-overview.md#how-does-voice-activation-work), kunt u beginnen met het implementeren van spraak activering voor uw eigen toepassing voor spraak assistentie.

### <a name="registration"></a>Registratie

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Zorg ervoor dat de microfoon beschikbaar en toegankelijk is en controleer vervolgens de status

MVA moet een microfoon hebben zodat deze toegankelijk is om een spraak activering te kunnen detecteren. Gebruik de klassen [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher)en [MediaCapture](/uwp/api/windows.media.capture.mediacapture) om te controleren op de privacy van de microfoon, de aanwezigheid van apparaten en de apparaatstatus (zoals volume en dempen).

### <a name="register-the-application-with-the-background-service"></a>De toepassing registreren bij de achtergrond service

De toepassing moet worden geregistreerd bij de achtergrond service om MVA de toepassing op de achtergrond te starten. Bekijk [hier](/windows/uwp/launch-resume/register-a-background-task)een volledige hand leiding voor de registratie van de achtergrond service.

### <a name="unlock-the-limited-access-feature"></a>De functie beperkte toegang ontgrendelen

Gebruik de functie sleutel voor beperkte toegang van micro soft om de functie Voice Assistant te ontgrendelen. Gebruik de [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) -klasse van de Windows SDK om dit te doen.

### <a name="register-the-keyword-for-the-application"></a>Het tref woord voor de toepassing registreren

De toepassing moet zichzelf, het trefwoord model en de taal van Windows registreren.

Begin met het ophalen van de trefwoord detectie. In deze voorbeeld code haalt u de eerste detectie op, maar u kunt een bepaalde detector selecteren door deze te selecteren in `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

Nadat u het ActivationSignalDetector-object hebt opgehaald, roept `ActivationSignalDetector.CreateConfigurationAsync` u de methode aan met de signaal-id, model-id en weergave naam om uw tref woord te registreren en de toepassing op te halen `ActivationSignalDetectionConfiguration` . Het signaal en de model-Id's moeten guid's zijn die door de ontwikkelaar worden bepaald en blijven consistent voor hetzelfde sleutel woord.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Controleer of de instelling voor spraak activering is ingeschakeld

Als u spraak activering wilt gebruiken, moet een gebruiker spraak activering voor hun systeem inschakelen en spraak activering inschakelen voor hun toepassing. De instelling onder privacy-instellingen voor spraak activering vindt u in de Windows-instellingen. Als u de status van de instelling voor spraak activering in uw toepassing wilt controleren, gebruikt u het exemplaar van het `ActivationSignalDetectionConfiguration` sleutel woord registreren. Het veld [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) op de `ActivationSignalDetectionConfiguration` bevat een Enum-waarde die de status van de instelling voor spraak activering beschrijft.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Een ConversationalAgentSession ophalen om de app te registreren bij het MVA-systeem

De `ConversationalAgentSession` is een klasse in de Windows SDK waarmee uw app Windows kan bijwerken met de status van de app (niet actief, detecteren, Luis teren, werken, praten) en gebeurtenissen ontvangen, zoals activerings detectie en systeem status wijzigingen, zoals de scherm vergrendeling. Bij het ophalen van een exemplaar van de AgentSession moet u de toepassing ook registreren bij Windows als activatable per stem. Het is best practice om één verwijzing naar de te behouden `ConversationalAgentSession` . Als u de sessie wilt ophalen, gebruikt u de `ConversationalAgentSession.GetCurrentSessionAsync` API.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Luister naar de twee activerings signalen: OnBackgroundActivated en OnSignalDetected

Uw app wordt door Windows gesignaleerd wanneer een sleutel woord op een van de volgende twee manieren wordt gedetecteerd. Als de app niet actief is (dat wil zeggen: u hebt geen verwijzing naar een niet-afgestoten exemplaar van `ConversationalAgentSession` ), wordt uw app gestart en wordt de methode OnBackgroundActivated in het app.xaml.CS-bestand van uw toepassing aangeroepen. Als het veld gebeurtenis argumenten `BackgroundActivatedEventArgs.TaskInstance.Task.Name` overeenkomt met de teken reeks ' AgentBackgroundTrigger ', wordt het opstarten van de toepassing geactiveerd door de spraak activering. De toepassing moet deze methode overschrijven en een exemplaar van ConversationalAgentSession ophalen om te Signa leren op Windows die nu actief is. Wanneer de toepassing actief is, geeft Windows een waarschuwing over het optreden van een spraak activering met behulp van de `ConversationalAgentSession.OnSignalDetected` gebeurtenis. Voeg een gebeurtenis-handler toe aan deze gebeurtenis zodra u de hebt opgehaald `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Trefwoord verificatie

Zodra een spraak agent toepassing is geactiveerd door de stem, is de volgende stap te controleren of de detectie van het tref woord geldig is. Windows biedt geen oplossing voor trefwoord verificatie, maar biedt spraak assistenten toegang tot de audio van de hypothetische activering en voert de verificatie zelf uit.

### <a name="retrieve-activation-audio"></a>Activerings audio ophalen

Maak een [AudioGraph](/uwp/api/windows.media.audio.audiograph) en geef deze door aan de `CreateAudioDeviceInputNodeAsync` van `ConversationalAgentSession` . Hiermee wordt de audio buffer van de grafiek geladen met de audio, te *beginnen ongeveer drie seconden voordat het sleutel woord is gedetecteerd*. Deze extra toonaangevende audio is opgenomen om een breed scala aan sleutel woorden en luidspreker snelheden te bieden. Vervolgens verwerkt u de gebeurtenis [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) uit het audio diagram om de audio gegevens op te halen.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Opmerking: de eerste audio die is opgenomen in de audio buffer, kan de verificatie van het tref woord veroorzaken. U kunt dit probleem oplossen door het begin van de audio buffer te verkleinen voordat u de audio verzendt voor trefwoord verificatie. Dit eerste bijsnijden moet worden aangepast aan elke assistent.

### <a name="launch-in-the-foreground"></a>Start op de voor grond

Wanneer de verificatie van het tref woord is geslaagd, moet de toepassing naar de voor grond worden verplaatst om de gebruikers interface weer te geven. Roep de `ConversationalAgentSession.RequestForegroundActivationAsync` API aan om uw toepassing naar de voor grond te verplaatsen.

### <a name="transition-from-compact-view-to-full-view"></a>Overgang van compacte weer gave naar volledige weer gave

Wanneer uw toepassing voor het eerst wordt geactiveerd door een spraak, wordt deze in een compacte weer gave gestart. Lees de [ontwerp richtlijnen voor de preview-versie van spraak activering](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) voor meer informatie over de verschillende weer gaven en overgangen ertussen voor spraak assistenten in Windows.

Als u de overgang van de compacte weer gave naar de volledige app-weer gave wilt maken, gebruikt u de ApplicationView-API `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementatie boven activering van vergren deling

De volgende stappen zijn van toepassing op de vereisten om een spraak-assistent in te scha kelen voor het uitvoeren van een vergren deling, inclusief verwijzingen naar voorbeeld code en richt lijnen voor het beheren van de levens cyclus van de toepassing.

Ga naar de [hand leiding best practices](windows-voice-assistants-best-practices.md)voor hulp bij het ontwerpen van de bovenstaande vergrendelings ervaring.

Wanneer een app een weer gave boven slot toont, wordt deze als ' kiosk modus ' beschouwd. Zie de documentatie voor de [kiosk modus](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)voor meer informatie over het implementeren van een app die gebruikmaakt van de kiosk modus.

### <a name="transitioning-above-lock"></a>Overgangen boven slot

Een activering boven slot is vergelijkbaar met een activering onder vergren deling. Als er geen actieve instanties van de toepassing zijn, wordt een nieuw exemplaar gestart op de achtergrond en `OnBackgroundActivated` wordt de app.xaml.cs aangeroepen. Als er een exemplaar van de toepassing is, ontvangt die instantie een melding via de `ConversationalAgentSession.SignalDetected` gebeurtenis.

Als de toepassing nog niet wordt weer gegeven boven de vergren deling, moet deze worden aangeroepen `ConversationalAgentSession.RequestForegroundActivationAsync` . Hiermee wordt de `OnLaunched` methode in app.xaml.cs geactiveerd die moet worden gebruikt om naar de weer gave te navigeren die boven de vergren deling wordt weer gegeven.

### <a name="detecting-lock-screen-transitions"></a>Overgangen van vergrendelings scherm detecteren

De ConversationalAgent-bibliotheek in de Windows SDK biedt een API om de status van het vergrendelings scherm te maken en de status van de vergrendelings scherm te wijzigen. Controleer het veld om de status van het huidige vergrendelings scherm te detecteren `ConversationalAgentSession.IsUserAuthenticated` . Als u wijzigingen in de vergrendelings status wilt detecteren, moet u een gebeurtenis-handler toevoegen aan de `ConversationalAgentSession` gebeurtenis van het object `SystemStateChanged` . Het wordt geactiveerd wanneer het scherm wordt gewijzigd van ontgrendeld naar vergrendeld of andersom. Als de waarde van de gebeurtenis argumenten is `ConversationalAgentSystemStateChangeType.UserAuthentication` , is de status van het vergrendelings scherm gewijzigd.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Detectie van voor gaande gebruikers voorkeur voor vergren deling

De vermelding van de toepassing op de pagina privacy-instellingen voor spraak activering bevat een wissel knop voor de vergrendelings functionaliteit. Als u wilt dat uw app boven slot kan worden gestart, moet deze instelling worden ingeschakeld voor de gebruiker. De status van de bovenstaande vergrendelings machtigingen wordt ook opgeslagen in het ActivationSignalDetectionConfiguration-object. De status AvailabilityInfo. HasLockScreenPermission geeft aan of de gebruiker de bovenstaande vergrendelings machtiging heeft opgegeven. Als deze instelling is uitgeschakeld, kan een spraak toepassing de gebruiker vragen naar de juiste instellingen pagina op de koppeling ' MS-settings: privacy-voiceactivation ' te navigeren met instructies over het inschakelen van de voor waarde voor het activeren van de app.

## <a name="closing-the-application"></a>De toepassing sluiten

Gebruik de API om de toepassing op een programmatische manier te sluiten wanneer deze boven of onder de vergren deling wordt gebruikt `WindowService.CloseWindow()` . Hiermee worden alle UWP levenscyclus methoden geactiveerd, inclusief OnSuspend, waardoor de toepassing het exemplaar kan verwijderen voordat deze wordt `ConversationalAgentSession` gesloten.

> [!NOTE]
> De toepassing kan worden gesloten zonder het [onderstaande vergrendelings exemplaar](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)te sluiten. In dit geval moet de bovenstaande vergrendelings weergave ' opschonen ', zodat u zeker weet dat er geen gebeurtenis-handlers of taken worden uitgevoerd die de bovenstaande vergrendelings weergave proberen te bewerken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ga naar de voor beeld-app UWP Voice Assistant voor voor beelden en code-instructies](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
