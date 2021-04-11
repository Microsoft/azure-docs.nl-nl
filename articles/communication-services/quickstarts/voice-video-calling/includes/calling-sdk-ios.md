---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 479aa522462d14f295177e6b2d2fcc4707657760
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498830"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een geïmplementeerde Azure Communication Services-resource. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een token voor gebruikers toegang om de aanroep-client in te scha kelen. [Een token voor gebruikers toegang ophalen](../../access-tokens.md).
- Optioneel: Voltooi de Snelstartgids [spraak toevoegen aan uw app](../getting-started-with-calling.md) .

## <a name="set-up-your-system"></a>Uw systeem instellen

### <a name="create-the-xcode-project"></a>Het Xcode-project maken

Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View-app** (Toepassing met één weergave). In deze Quick Start wordt het [SwiftUI-Framework](https://developer.apple.com/xcode/swiftui/)gebruikt, dus u moet de **taal** instellen op **Swift** en de **gebruikers interface** van **SwiftUI**. 

U gaat tijdens deze Quick Start geen eenheids tests of UI-tests maken. U kunt de tekst vakken **testen van de include-eenheid** en de optie voor UI- **tests ook** uitschakelen.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Scherm afbeelding met het venster voor het maken van een project in Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installeer het pakket en de afhankelijkheden met CocoaPods

1. Maak een Podfile voor uw toepassing, zoals:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. Voer `pod install` uit.
3. Open `.xcworkspace` met Xcode.

### <a name="request-access-to-the-microphone"></a>Toegang tot de microfoon aanvragen

Als u toegang wilt krijgen tot de microfoon van het apparaat, moet u de lijst met gegevens van de app bijwerken met `NSMicrophoneUsageDescription` . U stelt de gekoppelde waarde in op een `string` die wordt opgenomen in het dialoog venster dat door het systeem wordt gebruikt om toegang aan te vragen bij de gebruiker.

Klik met de rechtermuisknop op de `Info.plist`-vermelding van de projectstructuur en selecteer **Open As** > **Source Code**. Voeg de volgende regels toe aan de sectie op het hoogste niveau `<dict>` en sla het bestand op.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Open het bestand *ContentView. Swift* van het project en voeg `import` boven aan het bestand een verklaring toe om de bibliotheek te importeren `AzureCommunicationCalling` . U kunt ook importeren `AVFoundation` . U hebt deze nodig voor audio-machtigings aanvragen in de code.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>Meer informatie over het object model

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services die SDK voor iOS aanroepen.

> [!NOTE]
> In deze Quick Start wordt versie 1.0.0-Beta. 8 van de aanroepende SDK gebruikt.


| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` is het belangrijkste ingangs punt voor de aanroepende SDK.|
| `CallAgent` | `CallAgent` wordt gebruikt om aanroepen te starten en te beheren. |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` wordt gebruikt als de token referentie die moet worden geïnstantieerd `CallAgent` .| 
| `CommunicationIdentifier` | `CommunicationIdentifier` wordt gebruikt om de identiteit van de gebruiker weer te geven. De identiteit kan `CommunicationUserIdentifier` , `PhoneNumberIdentifier` of zijn `CallingApplication` . |

> [!NOTE]
> Wanneer de toepassing gebeurtenis gemachtigden implementeert, moet deze een sterke verwijzing bevatten naar de objecten waarvoor gebeurtenis abonnementen zijn vereist. Als er bijvoorbeeld een `RemoteParticipant` object wordt geretourneerd bij het aanroepen van de- `call.addParticipant` methode en de toepassing de gemachtigde instelt op luistert `RemoteParticipantDelegate` , moet de toepassing een sterke verwijzing naar het `RemoteParticipant` object bevatten. Als dit object wordt verzameld, wordt door de gemachtigde een fatale uitzonde ring gegenereerd wanneer de aanroepende SDK het object probeert aan te roepen.

## <a name="initialize-callagent"></a>CallAgent initialiseren

Als u een `CallAgent` exemplaar van wilt maken `CallClient` , moet u een `callClient.createCallAgent` methode gebruiken die een object asynchroon retourneert `CallAgent` nadat het is geïnitialiseerd.

Als u een client voor aanroepen wilt maken, moet u een object door geven `CommunicationTokenCredential` .

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Geef het `CommunicationTokenCredential` object door dat u hebt gemaakt `CallClient` en stel de weergave naam in.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Een uitgaande oproep plaatsen

Als u een oproep wilt maken en starten, moet u een van de Api's aanroepen `CallAgent` en de communicatie Services-identiteit opgeven van een gebruiker die u hebt ingericht met de SDK voor communicatie Services-beheer.

Het maken van aanroepen en starten synchroon zijn. U ontvangt een gespreks exemplaar waarmee u zich kunt abonneren op alle gebeurtenissen in de oproep.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Plaats een 1:1-aanroep naar een gebruiker of een 1: n-gesprek met gebruikers en het PSTN

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Een aanroep van 1: n met gebruikers en PSTN plaatsen
U moet een telefoon nummer opgeven dat is verkregen met communicatie Services om de oproep naar het PSTN te kunnen plaatsen.

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-video"></a>Een 1:1-oproep met video plaatsen
Zie de sectie over het [beheren van apparaten](#manage-devices)als u een exemplaar van Apparaatbeheer wilt ophalen.

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Deelnemen aan een groepsgesprek
Als u aan een gesprek wilt toevoegen, moet u een van de Api's aanroepen op `CallAgent` .

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-to-an-incoming-call"></a>Abonneren op een binnenkomende oproep
Abonneren op een binnenkomende oproep gebeurtenis.

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Een binnenkomend gesprek accepteren
Als u een aanroep wilt accepteren, roept u de- `accept` methode aan op een aanroep object. Stel een gemachtigde in op `CallAgent` .

```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>Push meldingen instellen

Een mobiele push melding is de pop-upmelding die u op het mobiele apparaat ontvangt. Voor het aanroepen van worden we aandacht besteed aan push meldingen over VoIP (Voice-over Internet Protocol). 

In de volgende secties wordt beschreven hoe u push meldingen registreert voor, afhandelt en de registratie ongedaan maakt. Voer de volgende vereisten uit voordat u deze taken start:

1. Ga in Xcode naar **ondertekening & mogelijkheden**. Voeg een mogelijkheid toe door **+ mogelijkheid** te selecteren en vervolgens **Push meldingen** te selecteren.
2. Voeg nog een mogelijkheid toe door **+ mogelijkheid** te selecteren en vervolgens **achtergrond modi** te selecteren.
3. Schakel onder **achtergrond modi** het selectie vakje **Voice over IP** en **externe meldingen** in.

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Scherm afbeelding die laat zien hoe u mogelijkheden toevoegt in Xcode." lightbox="../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Registreren voor push meldingen

Als u zich wilt registreren voor push meldingen, roept u `registerPushNotification()` een `CallAgent` exemplaar aan met een apparaatregistratie-token.

De registratie van push meldingen moet plaatsvinden nadat de initialisatie is voltooid. Wanneer het `callAgent` object wordt vernietigd, `logout` wordt aangeroepen, waardoor de registratie van push meldingen automatisch ongedaan wordt gemaakt.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>Push meldingen verwerken
Als u push meldingen voor binnenkomende oproepen wilt ontvangen, roept u `handlePushNotification()` een `CallAgent` exemplaar aan met een woorden lijst-nettolading.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
### <a name="unregister-push-notifications"></a>Registratie van push meldingen ongedaan maken

Toepassingen kunnen de registratie van push meldingen op elk gewenst moment ongedaan maken. Roep de `unregisterPushNotification` methode aan `CallAgent` .

> [!NOTE]
> De registratie bij Push meldingen bij het afmelden wordt niet automatisch ongedaan gemaakt.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="perform-mid-call-operations"></a>Mid-Call-bewerkingen uitvoeren

U kunt verschillende bewerkingen uitvoeren tijdens een aanroep om instellingen te beheren die betrekking hebben op video en audio.

### <a name="mute-and-unmute"></a>Dempen en dempen opheffen

U kunt de `mute` en asynchrone api's gebruiken om het lokale eind punt te dempen of te dempen `unmute` .

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Gebruik de volgende code om het lokale eind punt asynchroon te ontheffen.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Verzenden van lokale video starten en stoppen

Als u wilt beginnen met het verzenden van lokale video naar andere deel nemers in een gesprek, gebruikt u de `startVideo` API en geeft u door `localVideoStream` `camera` .

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

Nadat u het verzenden van de video hebt gestart, `LocalVideoStream` wordt de `localVideoStreams` verzameling toegevoegd aan een exemplaar van de aanroep.

```swift

call.localVideoStreams[0]

```

Als u lokale video wilt stoppen, geeft u het exemplaar door dat is `localVideoStream` geretourneerd door het aanroepen van `call.startVideo` . Dit is een asynchrone actie.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>Externe deel nemers beheren

Alle externe deel nemers worden vertegenwoordigd door het `RemoteParticipant` type en zijn beschikbaar via de `remoteParticipants` verzameling in een aanroep exemplaar.

### <a name="list-participants-in-a-call"></a>Deel nemers in een gesprek weer geven

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>Eigenschappen van externe deel nemer ophalen

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Een deel nemer aan een gesprek toevoegen

Als u een deel nemer wilt toevoegen aan een aanroep (een gebruiker of een telefoon nummer), kunt u aanroepen `addParticipant` . Met deze opdracht wordt een extern deel nemer-exemplaar synchroon geretourneerd.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Een deel nemer uit een gesprek verwijderen
Als u een deel nemer van een gesprek (een gebruiker of een telefoon nummer) wilt verwijderen, kunt u de API aanroepen `removeParticipant` . Dit wordt asynchroon opgelost.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Video stromen voor externe deel nemers weer geven

Externe deel nemers kunnen video of scherm delen tijdens een aanroep starten.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Video delen of stromen voor het delen van schermen van externe deel nemers afhandelen

Als u de streams van externe deel nemers wilt weer geven, inspecteert u de `videoStreams` verzamelingen.

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="get-remote-video-stream-properties"></a>Eigenschappen van externe video-stream ophalen

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-streams"></a>Stromen van externe deel nemers weer geven

Gebruik de volgende code om te beginnen met het weer geven van externe streams van deel nemers.

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Methoden en eigenschappen van externe video renderer ophalen

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>Apparaten beheren

`DeviceManager` Hiermee kunt u lokale apparaten opsommen die kunnen worden gebruikt in een aanroep voor het verzenden van audio-of video-streams. U kunt hiermee ook machtigingen aanvragen van een gebruiker om toegang te krijgen tot een microfoon of camera. U kunt toegang krijgen tot `deviceManager` het `callClient` object.

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Lokale apparaten opsommen

Voor toegang tot lokale apparaten kunt u inventarisatie methoden gebruiken in Apparaatbeheer. Opsomming is een synchrone actie.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-the-default-microphone-or-speaker"></a>De standaard microfoon of-spreker instellen

U kunt Apparaatbeheer gebruiken om een standaard apparaat in te stellen dat wordt gebruikt wanneer een aanroep wordt gestart. Als de standaard waarden voor de stack niet worden ingesteld, worden de standaard instellingen van het besturings systeem hersteld door communicatie Services.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="get-a-local-camera-preview"></a>Een voor beeld van een lokale camera ophalen

U kunt gebruiken `Renderer` om te beginnen met het renderen van een stream van uw lokale camera. Deze stroom wordt niet naar andere deel nemers verzonden. het is een lokale preview-feed. Dit is een asynchrone actie.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="get-local-camera-preview-properties"></a>Eigenschappen van voor beeld van lokale camera ophalen

De renderer heeft eigenschappen en methoden die u in staat stellen de rendering te beheren.

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="subscribe-to-notifications"></a>Abonneren op meldingen

U kunt zich abonneren op de meeste eigenschappen en verzamelingen om te worden gewaarschuwd wanneer waarden worden gewijzigd.

### <a name="properties"></a>Eigenschappen
Gebruik de volgende code om u te abonneren op `property changed` gebeurtenissen.

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Verzamelingen
Gebruik de volgende code om u te abonneren op `collection updated` gebeurtenissen.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
