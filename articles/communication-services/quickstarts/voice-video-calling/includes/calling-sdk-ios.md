---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: d36bf92a1b1bdef4e45b22b934728b3e8c46c3da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107717"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een geïmplementeerde Communication Services-resource. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een `User Access Token` om de aanroepende client in te schakelen. Voor meer informatie over het [verkrijgen van een `User Access Token`](../../access-tokens.md)
- Optioneel: Voltooi de Snelstartgids om aan de [slag te gaan met het toevoegen van een oproep aan uw toepassing](../getting-started-with-calling.md)

## <a name="setting-up"></a>Instellen

### <a name="creating-the-xcode-project"></a>Het Xcode-project maken

> [!NOTE]
> In dit document wordt versie 1.0.0-Beta. 8 van de aanroepende SDK gebruikt.

Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View-app** (Toepassing met één weergave). Deze Snelstartgids maakt gebruik van het [SwiftUI-Framework](https://developer.apple.com/xcode/swiftui/), dus u moet de **taal** instellen op **Swift** en de **gebruikers interface** op **SwiftUI**. U gaat tijdens deze Quick Start geen eenheids tests of UI-tests maken. Schakel het selectie vakje **include-tests toevoegen** uit en schakel ook **ui-tests** uit.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Schermafbeelding dat het nieuw aangemaakte venster Nieuw Project in Xcode aantoont.":::

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
3. Open de `.xcworkspace` with-Xcode.

### <a name="request-access-to-the-microphone"></a>Toegang tot de microfoon aanvragen

Als u toegang wilt krijgen tot de microfoon van het apparaat, moet u de eigenschappenlijst van de app bijwerken met een `NSMicrophoneUsageDescription`. U stelt de bijbehorende waarde in op een `string` die wordt opgenomen in het dialoogvenster dat het systeem gebruikt om toegang te vragen aan de gebruiker.

Klik met de rechtermuisknop op de `Info.plist`-vermelding van de projectstructuur en selecteer **Open As** > **Source Code**. Voeg de volgende regels toe in de bovenste sectie `<dict>` en sla het bestand op.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Open het bestand **ContentView.swift** van het project en voeg boven aan het bestand een `import`-declaratie toe om de `AzureCommunicationCalling library`te importeren. Daarnaast `AVFoundation` moet u dit voor de code van de audio-machtiging in het programma importeren.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services die SDK voor iOS aanroepen.


| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | De CallClient is het belangrijkste ingangs punt voor de aanroepende SDK.|
| CallAgent | De CallAgent wordt gebruikt om oproepen te starten en te beheren. |
| CommunicationTokenCredential | De CommunicationTokenCredential wordt gebruikt als de token referentie voor het instantiëren van de CallAgent.| 
| CommunicationIdentifier | De CommunicationIdentifier wordt gebruikt om de identiteit van de gebruiker te vertegenwoordigen. Dit kan een van de volgende zijn: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

> [!NOTE]
> Bij het implementeren van gebeurtenis gemachtigden moet de toepassing een sterke verwijzing bevatten naar de objecten waarvoor gebeurtenis abonnementen zijn vereist. Als er bijvoorbeeld een `RemoteParticipant` object wordt geretourneerd bij het aanroepen van de- `call.addParticipant` methode en de toepassing de gemachtigde instelt op luistert `RemoteParticipantDelegate` , moet de toepassing een sterke verwijzing naar het `RemoteParticipant` object bevatten. Als dit object wordt verzameld, wordt door de gemachtigde een fatale uitzonde ring gegenereerd wanneer de aanroepende SDK het object probeert aan te roepen.

## <a name="initialize-the-callagent"></a>De CallAgent initialiseren

Als u een `CallAgent` exemplaar van wilt maken `CallClient` , moet u `callClient.createCallAgent` een methode gebruiken die een `CallAgent` object asynchroon retourneert nadat het is geïnitialiseerd

Als u een aanroep-client wilt maken, moet u een object door geven `CommunicationTokenCredential` .

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

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Geef `CommunicationTokenCredential` het object dat hierboven is gemaakt door `CallClient` en stel de weergave naam in.

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

Als u een gesprek wilt maken en starten, moet u een van de Api's aanroepen `CallAgent` en de communicatie Services-identiteit opgeven van een gebruiker die u hebt ingericht met de SDK voor communicatie Services-beheer.

Aanroepen maken en starten is synchroon. U ontvangt een gespreks exemplaar waarmee u zich kunt abonneren op alle gebeurtenissen in de aanroep.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Plaats een 1:1-aanroep naar een gebruiker of een 1: n-gesprek met gebruikers en het PSTN

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Een aanroep van 1: n met gebruikers en PSTN plaatsen
Als u de oproep naar het PSTN wilt plaatsen, moet u het telefoon nummer opgeven dat u hebt verkregen met communicatie Services
```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Plaats een 1:1-aanroep met met video
Meer informatie over een instantie van Apparaatbeheer vindt u [hier](#device-management)

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
Als u wilt samen voegen met een gesprek, moet u een van de Api's aanroepen op *CallAgent*

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-for-incoming-call"></a>Abonneren op binnenkomende oproep
Abonneren op een binnenkomende oproep gebeurtenis

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
Als u een aanroep wilt accepteren, roept u de methode accept aan voor een aanroep object.
Een gemachtigde instellen op de CallAgent 
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

## <a name="push-notification"></a>Pushmelding

Mobiele push meldingen is de pop-upmelding die u op het mobiele apparaat ontvangt. Voor aanroepen worden we geadviseerd op VoIP (Voice-over Internet Protocol) Push meldingen. We bieden u de mogelijkheid om u te registreren voor push meldingen, om Push meldingen te verwerken en om de registratie van push meldingen ongedaan te maken.

### <a name="prerequisite"></a>Vereiste

- Stap 1: Xcode->-ondertekening & mogelijkheden: > invoeg toepassingen toevoegen-> "push meldingen"
- Stap 2: Xcode & mogelijkheden voor het ondertekenen van >-> mogelijkheden toevoegen-> "achtergrond modi"
- Stap 3: "achtergrond modi"-> Selecteer "Voice over IP" en "externe meldingen"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Scherm afbeelding die laat zien hoe u mogelijkheden toevoegt in Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registreren voor push meldingen

Als u zich wilt registreren voor push meldingen, roept u registerPushNotification () aan op een *CallAgent* -exemplaar met een apparaat registratie token.

Registreren voor push melding moet worden aangeroepen na een geslaagde initialisatie. Wanneer het `callAgent` object wordt vernietigd, `logout` wordt aangeroepen, waarmee push meldingen automatisch worden verwijderd.


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

#### <a name="push-notification-handling"></a>Afhandeling van push meldingen
Als u push meldingen voor binnenkomende oproepen wilt ontvangen, roept u *handlePushNotification ()* aan op een *CallAgent* -exemplaar met een woorden lijst-nettolading.

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
#### <a name="unregister-push-notification"></a>Registratie van push melding opheffen

Toepassingen kunnen de registratie van push meldingen op elk gewenst moment ongedaan maken. Roep de `unregisterPushNotification` methode aan op *CallAgent*.
> [!NOTE]
> De registratie van toepassingen bij een push melding bij afmelden wordt niet automatisch ongedaan gemaakt.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="mid-call-operations"></a>Mid-Call-bewerkingen

U kunt verschillende bewerkingen uitvoeren tijdens een aanroep om instellingen te beheren die betrekking hebben op video en audio.

### <a name="mute-and-unmute"></a>Dempen en dempen opheffen

U kunt de `mute` en asynchrone api's gebruiken om het lokale eind punt te dempen of te dempen `unmute` :

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Asynchrone Lokaal uitschakelen

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

Als u wilt beginnen met het verzenden van lokale video naar andere deel nemers in de oproep, gebruikt u de `startVideo` API en Pass Through `localVideoStream``camera`

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

Zodra u begint met het verzenden van de video, `LocalVideoStream` wordt de `localVideoStreams` verzameling toegevoegd aan een instantie van een aanroep:

```swift

call.localVideoStreams[0]

```

Asynchrone Als u lokale video wilt stoppen, geeft u het `localVideoStream` resultaat van het aanroepen van `call.startVideo` :

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Beheer van externe deel nemers

Alle externe deel nemers worden vertegenwoordigd door het `RemoteParticipant` type en zijn beschikbaar via de `remoteParticipants` verzameling op een instantie van een aanroep:

### <a name="list-participants-in-a-call"></a>Deel nemers in een gesprek weer geven

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Eigenschappen van externe deel nemer

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
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

Als u een deel nemer wilt toevoegen aan een aanroep (een gebruiker of een telefoon nummer), kunt u deze aanroepen `addParticipant` . Hiermee wordt synchroon een exemplaar van een externe deel nemer geretourneerd.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Een deel nemer uit een gesprek verwijderen
Als u een deel nemer wilt verwijderen uit een gesprek (een gebruiker of een telefoon nummer), kunt u de API aanroepen  `removeParticipant` . Dit wordt asynchroon opgelost.

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

Externe deel nemers kunnen tijdens een aanroep video of scherm delen starten.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Externe deel nemers video/scherm delen streams afhandelen

Als u de streams van externe deel nemers wilt weer geven, inspecteert u de `videoStreams` verzamelingen:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Eigenschappen van externe video-stream

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Stroom van externe deel nemers weer geven

Rendering van externe deel nemers streams starten:

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Methoden en eigenschappen van externe video weergave

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Apparaatbeheer

`DeviceManager` Hiermee kunt u lokale apparaten opsommen die kunnen worden gebruikt in een aanroep voor het verzenden van audio/video-streams. U kunt hiermee ook machtigingen van een gebruiker aanvragen voor toegang tot de microfoon/camera. U kunt toegang krijgen tot `deviceManager` het `callClient` object:

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

Voor toegang tot lokale apparaten kunt u inventarisatie methoden gebruiken op de Apparaatbeheer. Opsomming is een synchrone actie.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Standaard microfoon/-spreker instellen

Met Apparaatbeheer kunt u een standaard apparaat instellen dat wordt gebruikt bij het starten van een aanroep. Als de stack-standaard waarden niet zijn ingesteld, worden de standaard instellingen van het besturings systeem hersteld door communicatie Services.

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

### <a name="local-camera-preview"></a>Voor beeld van lokale camera

U kunt gebruiken `Renderer` om te beginnen met het renderen van een stream van uw lokale camera. Deze stroom wordt niet naar andere deel nemers verzonden. het is een lokale preview-feed. Dit is een asynchrone actie.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Eigenschappen van voor beeld van lokale camera

De renderer heeft eigenschappen en methoden die u in staat stellen om de rendering te beheren:

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

## <a name="eventing-model"></a>Gebeurtenis model

U kunt zich abonneren op de meeste eigenschappen en verzamelingen om te worden gewaarschuwd wanneer waarden worden gewijzigd.

### <a name="properties"></a>Eigenschappen
Abonneren op `property changed` gebeurtenissen:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Verzamelingen
Abonneren op `collection updated` gebeurtenissen:

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
