---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 2c894ea4bcb9701b8b65bcb9cd0b4b82c1898448
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500475"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een geïmplementeerde Communication Services-resource. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een `User Access Token` om de aanroepende client in te schakelen. Voor meer informatie over het [verkrijgen van een `User Access Token`](../../access-tokens.md)
- Optioneel: Voltooi de Snelstartgids om aan de [slag te gaan met het toevoegen van een oproep aan uw toepassing](../getting-started-with-calling.md)

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Gebruik de `npm install` opdracht om de Azure Communication Services-aanroep-en algemene client bibliotheken voor Java script te installeren.

```console
npm install @azure/communication-common --save

npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-clientbibliotheek voor aanroepen:

| Naam                             | Beschrijving                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | De CallClient is het belangrijkste ingangspunt voor de clientbibliotheek voor oproepen.                                                                       |
| CallAgent                        | De CallAgent wordt gebruikt om oproepen te starten en te beheren.                                                                                            |
| AzureCommunicationUserCredential | De klasse AzureCommunicationUserCredential implementeert de CommunicationUserCredential-interface die wordt gebruikt om de CallAgent te instantiëren. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Initialiseer de CallClient, maak CallAgent en open DeviceManager

Exemplaar een nieuw `CallClient` exemplaar. U kunt deze configureren met aangepaste opties, zoals een exemplaar van een logboek.
Zodra een `CallClient` is geïnstantieerd, kunt u een instantie maken `CallAgent` door de `createCallAgent` methode aan te roepen voor het `CallClient` exemplaar. Hiermee wordt een `CallAgent` object Instance asynchroon geretourneerd.
De `createCallAgent` methode heeft een `CommunicationUserCredential` als argument, waarmee een token voor [gebruikers toegang](../../access-tokens.md)wordt geaccepteerd.
Om toegang te krijgen tot het `DeviceManager` callAgent-exemplaar moet eerst worden gemaakt. U kunt de-methode vervolgens gebruiken voor het `getDeviceManager` `CallClient` exemplaar om de DeviceManager op te halen.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationUserCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Een uitgaande oproep plaatsen

Als u een gesprek wilt maken en starten, moet u een van de Api's op CallAgent gebruiken en een gebruiker bieden die u hebt gemaakt via de client bibliotheek voor communicatie Services-beheer.

Aanroepen maken en starten is synchroon. Met het gesprek exemplaar kunt u zich abonneren op het aanroepen van gebeurtenissen.

## <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Plaats een 1:1-aanroep naar een gebruiker of een 1: n-gesprek met gebruikers en het PSTN

Als u een aanroep naar een andere communicatie Services-gebruiker wilt plaatsen, roept u de- `call` methode aan `callAgent` en geeft u de CommunicationUser door die u hebt [gemaakt met de beheer bibliotheek voor communicatie Services](../../access-tokens.md).

```js
const oneToOneCall = callAgent.call([CommunicationUser]);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Een aanroep van 1: n met gebruikers en PSTN plaatsen

Als u een aanroepen van 1: n wilt plaatsen voor een gebruiker en een PSTN-nummer, moet u een CommunicationUser en een telefoon nummer opgeven voor beide callees.

Uw communicatie service-resource moet worden geconfigureerd om PSTN-aanroepen mogelijk te maken.
```js

const userCallee = { communicationUserId: <ACS_USER_ID> };
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const groupCall = callAgent.call([userCallee, pstnCallee], placeCallOptions);

```

### <a name="place-a-11-call-with-video-camera"></a>Een 1:1-oproep met video camera plaatsen
> [!WARNING]
> Er kan momenteel niet meer dan één uitgaande lokale video stroom zijn.
U moet lokale camera's opsommen met behulp van de deviceManager-API als u een video gesprek wilt plaatsen `getCameraList` .
Wanneer u de gewenste camera hebt geselecteerd, gebruikt u deze om een exemplaar te maken `LocalVideoStream` en dit door te geven aan de hand van `videoOptions` een item in de `localVideoStream` matrix `call` .
Zodra uw gesprek verbinding maakt, begint automatisch met het verzenden van een video stroom van de geselecteerde camera naar de andere deel nemer (s).

Dit geldt ook voor de video opties call. accept () en CallAgent. Add ().
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="receiving-an-incoming-call"></a>Een binnenkomend gesprek ontvangen
```js
callAgent.on('callsUpdated', e => {
    e.added.forEach(addedCall => {
        if(addedCall.isIncoming) {
        addedCall.accept();
    }
    });
})
```

### <a name="join-a-group-call"></a>Deelnemen aan een groepsgesprek
Als u een nieuwe groeps oproep wilt starten of lid wilt worden van een doorlopende groep, gebruikt u de methode ' samen voegen ' en geeft u een object door met een `groupId` eigenschap. De waarde moet een GUID zijn.
```js

const locator = { groupId: <GUID>}
const call = callAgent.join(locator);

```

### <a name="join-a-teams-meeting"></a>Deel nemen aan een team vergadering
Als u lid wilt worden van een vergadering, gebruikt u de methode ' samen voegen ' en geeft u een koppeling naar de vergadering of de coördinaten van een vergadering door
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="call-management"></a>Oproep beheer

U hebt toegang tot de oproep eigenschappen en kunt verschillende bewerkingen uitvoeren tijdens een aanroep om instellingen te beheren die betrekking hebben op video en audio.

### <a name="call-properties"></a>Eigenschappen van oproep
* Haal de unieke ID (teken reeks) op voor deze aanroep.
```js

const callId: string = call.id;

```

* Inspecteer de `remoteParticipant` verzameling op het exemplaar voor meer informatie over andere deel nemers aan de aanroep `call` . Matrix bevat lijst `RemoteParticipant` objecten
```js
const remoteParticipants = call.remoteParticipants;
```

* De identiteit van de aanroeper als de aanroep inkomend is. De identiteit is een van de `Identifier` typen
```js

const callerIdentity = call.callerIdentity;

```

* De status van de aanroep ophalen.
```js

const callState = call.state;

```
Hiermee wordt een teken reeks geretourneerd die de huidige status van een aanroep vertegenwoordigt:
* Geen '-initiële gespreks status
* ' Binnenkomend ': geeft aan dat een aanroep inkomend moet worden geaccepteerd of geweigerd
* ' Verbinding maken '-initiële overgangs status zodra de aanroep is geplaatst of geaccepteerd
* ' Rinkelend ': voor een uitgaande oproep wordt aangegeven dat de aanroep wordt opgeroepen voor externe deel nemers, het is ' binnenkomend ' aan hun zijde
* ' EarlyMedia ': geeft een status aan waarin een aankondiging wordt afgespeeld voordat de aanroep is verbonden
* Verbonden: de oproep is verbonden
* Hold-aanroep is in de wacht stand gezet, geen medium loopt tussen het lokale eind punt en de externe deel nemer (s)
* ' Verbinding verbreken '-de status van de overdracht van de aanroep is verbroken
* ' Verbinding verbroken '-laatste aanroep status.
   * Als de netwerk verbinding is verbroken, wordt de status na ongeveer 2 minuten verbroken.


* Inspecteer de eigenschap om te zien waarom een bepaalde aanroep is beëindigd `callEndReason` .
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Als u wilt weten of de huidige aanroep een binnenkomende oproep is, inspecteert u de `isIncoming` eigenschap, wordt deze geretourneerd `Boolean` .
```js
const isIncoming = call.isIncoming;
```

* Als u wilt controleren of de oproep wordt vastgelegd, inspecteert u de `isRecordingActive` eigenschap, wordt deze geretourneerd `Boolean` .
```js
const isResordingActive = call.isRecordingActive;
```

*  Als u wilt controleren of de huidige microfoon is gedempt, inspecteert u de `muted` eigenschap `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Als u wilt zien of de stroom voor het delen van het scherm wordt verzonden vanuit een bepaald eind punt, controleert `isScreenSharingOn` u de eigenschap `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Als u actieve videostreams wilt controleren, controleert `localVideoStreams` u de verzameling. Deze bevat `LocalVideoStream` objecten
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="mute-and-unmute"></a>Dempen en dempen opheffen

U kunt de `mute` en asynchrone api's gebruiken om het lokale eind punt te dempen of te dempen `unmute` :

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Verzenden van lokale video starten en stoppen


Als u een video wilt starten, moet u camera's opsommen met behulp `getCameraList` van de methode voor het `deviceManager` object. Maak vervolgens een nieuw exemplaar van `LocalVideoStream` het door geven van de gewenste camera in de `startVideo` methode als een argument:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Zodra u de video hebt verzonden, `LocalVideoStream` wordt een exemplaar toegevoegd aan de `localVideoStreams` verzameling in een aanroep exemplaar.

```js

call.localVideoStreams[0] === localVideoStream;

```

Als u lokale video wilt stoppen, geeft u de instantie door die `localVideoStream` beschikbaar is in de `localVideoStreams` verzameling:

```js

await call.stopVideo(localVideoStream);

```

U kunt overschakelen naar een ander camera apparaat terwijl de video wordt verzonden door aan te roepen `switchSource` op een `localVideoStream` exemplaar:

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```
### <a name="faq"></a>Veelgestelde vragen
 * Als de netwerk verbinding is verbroken, wordt de status van de aanroep gewijzigd in ' losgekoppeld '?
    * Ja, als de netwerk verbinding langer dan twee minuten verloren is gegaan, wordt de aanroep overgezet naar de niet-verbonden status en wordt de aanroep beëindigd.

## <a name="remote-participants-management"></a>Beheer van externe deel nemers

Alle externe deel nemers worden weer gegeven op `RemoteParticipant` type en beschikbaar via `remoteParticipants` verzameling op een aanroep exemplaar.

### <a name="list-participants-in-a-call"></a>Deel nemers in een gesprek weer geven
De `remoteParticipants` verzameling retourneert een lijst met externe deel nemers in de gegeven aanroep:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Eigenschappen van externe deel nemer
Aan de externe deel nemer is een set eigenschappen en verzamelingen gekoppeld

* De id voor deze externe deel nemer ophalen.
De identiteit is een van de typen ' identifier ':
```js
const identifier = remoteParticipant.identifier;
//It can be one of:
// { communicationUserId: '<ACS_USER_ID'> } - object representing ACS User
// { phoneNumber: '<E.164>' } - object representing phone number in E.164 format
```

* De status van deze externe deel nemer ophalen.
```js

const state = remoteParticipant.state;
```
Status kan een van
* ' Inactief '-initiële status
* ' Verbinding maken '-transitie status terwijl deel nemer verbinding maakt met de aanroep
* Verbonden: de deel nemer is verbonden met de oproep
* Hold-deel nemer is in de wacht stand
* ' EarlyMedia ': de aankondiging wordt afgespeeld voordat de deel nemer is verbonden met de oproep
* ' Verbinding verbroken ': eind status: de deel nemer is losgekoppeld van de aanroep.
   * Als de externe deel nemer hun netwerk verbinding verliest, wordt de status van de externe deel nemer na ongeveer twee minuten niet meer verbonden.

Als u wilt weten waarom de deel nemer de oproep heeft verlaten, inspecteert u de `callEndReason` eigenschap:
```js

const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Als u wilt controleren of deze externe deel nemer is gedempt of niet, controleert `isMuted` u de eigenschap. `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```

* Als u wilt controleren of deze externe deel nemer spreekt of niet, controleert `isSpeaking` u de eigenschap die wordt geretourneerd `Boolean`
```js

const isSpeaking = remoteParticipant.isSpeaking;

```

* Als u alle video stromen wilt controleren die een bepaalde deel nemer in deze aanroep verzendt, controleert u of `videoStreams` deze `RemoteVideoStream` objecten bevat
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Een deel nemer aan een gesprek toevoegen

Als u een deel nemer wilt toevoegen aan een aanroep (een gebruiker of een telefoon nummer), kunt u deze aanroepen `addParticipant` .
Geef een van de typen ' identifier ' op.
Hiermee wordt het exemplaar van de externe deel nemer synchroon geretourneerd.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>};
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier);
```

### <a name="remove-participant-from-a-call"></a>Deel nemer uit een gesprek verwijderen

Als u een deel nemer wilt verwijderen uit een aanroep (een gebruiker of een telefoon nummer), kunt u deze aanroepen `removeParticipant` .
U moet een van de typen ' identifier ' door geven deze wordt asynchroon opgelost zodra de deel nemer uit de aanroep is verwijderd.
De deel nemer wordt ook verwijderd uit de `remoteParticipants` verzameling.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>};
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Video stromen voor externe deel nemers weer geven

Als u de video stromen en de streams voor het delen van externe deel nemers wilt weer geven, inspecteert u de `videoStreams` verzamelingen:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
Als u een wilt weer geven `RemoteVideoStream` , moet u zich abonneren op een `isAvailableChanged` gebeurtenis.
Als de `isAvailable` eigenschap wordt gewijzigd in `true` , wordt een stroom door een externe deel nemer verzonden.
Als dat gebeurt, maakt u een nieuw exemplaar van `Renderer` en maakt u vervolgens een nieuw `RendererView` exemplaar met behulp van de asynchrone `createView` methode.  U kunt vervolgens `view.target` aan elk UI-element worden gekoppeld.
Wanneer de beschik baarheid van een externe stroom verandert, kunt u ervoor kiezen om de volledige renderer te vernietigen, een specifieke `RendererView` of te blijven gebruiken, maar dit resulteert in het weer geven van een leeg video frame.

```js
let renderer: Renderer;
const displayVideo = () => {
    renderer = new Renderer(remoteParticipantStream);
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Eigenschappen van externe video-stream
Externe video stromen hebben de volgende eigenschappen:

* `Id` -ID van een externe video stroom
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -grootte (breedte/hoogte) van een externe video stroom
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -kan ' video ' of ' ScreenSharing ' zijn
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -Geeft aan of het eind punt van de externe deel nemer actief stroom verzendt
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Methoden en eigenschappen van renderer

* Maak een `RendererView` exemplaar dat later kan worden gekoppeld in de gebruikers interface van de toepassing om de externe video stroom weer te geven.
```js
renderer.createView()
```

* De renderer en alle bijbehorende instanties verwijderen `RendererView` .
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView methoden en eigenschappen
Bij het maken van een `RendererView` kunt `scalingMode` u `mirrored` Eigenschappen en opgeven.
De schaal modus kan ' Stretch ', ' gewas ' of ' fit ' zijn als `Mirrored` deze is opgegeven, de gerenderde stroom wordt verticaal gespiegeld.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Elk gegeven `RendererView` exemplaar heeft een `target` eigenschap die het weergave oppervlak vertegenwoordigt. Dit moet worden gekoppeld aan de gebruikers interface van de toepassing:
```js
document.body.appendChild(rendererView.target);
```

U kunt de schaal modus later bijwerken door de methode aan te roepen `updateScalingMode` .
```js
view.updateScalingMode('Crop')
```
### <a name="faq"></a>Veelgestelde vragen
* Als een deel nemer de netwerk verbinding verliest, wordt de status ervan gewijzigd in ' losgekoppeld '?
    * Ja, als een externe deel nemer hun netwerk verbinding langer dan twee minuten kwijtraakt, wordt de status ervan overgezet naar de verbinding verbroken en worden ze verwijderd uit de aanroep.
## <a name="device-management"></a>Apparaatbeheer

`DeviceManager` Hiermee kunt u lokale apparaten opsommen die kunnen worden gebruikt in een aanroep om uw audio/video-streams te verzenden. U kunt hiermee ook machtigingen aanvragen van een gebruiker om toegang te krijgen tot hun microfoon en camera met behulp van de systeem eigen browser-API.

U kunt de `deviceManager` methode by aanroepen gebruiken `callClient.getDeviceManager()` .
> [!WARNING]
> Momenteel `callAgent` moet een object eerst worden geïnstantieerd om toegang te krijgen tot DeviceManager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Lokale apparaten opsommen

Voor toegang tot lokale apparaten kunt u inventarisatie methoden gebruiken op de Apparaatbeheer. Opsomming is een synchrone actie.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Standaard microfoon/-spreker instellen

Met Apparaatbeheer kunt u een standaard apparaat instellen dat wordt gebruikt bij het starten van een aanroep.
Als de standaard waarden van de client niet zijn ingesteld, worden de standaard instellingen van het besturings systeem hersteld door communicatie Services.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceMicrophone.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Voor beeld van lokale camera

U kunt `DeviceManager` en gebruiken `Renderer` om streams van uw lokale camera te renderen. Deze stroom wordt niet naar andere deel nemers verzonden. het is een lokale preview-feed. Dit is een asynchrone actie.

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Machtiging aanvragen voor camera/microfoon

Vraag een gebruiker om camera/microfoon machtigingen te verlenen met het volgende:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Dit wordt asynchroon opgelost met een object dat aangeeft of `audio` en `video` machtigingen zijn verleend:
```js
console.log(result.audio);
console.log(result.video);
```

U kunt de huidige machtigings status voor een bepaald type controleren door het volgende aan te roepen `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="eventing-model"></a>Gebeurtenis model

U kunt zich abonneren op de meeste eigenschappen en verzamelingen om te worden gewaarschuwd wanneer waarden worden gewijzigd.

### <a name="properties"></a>Eigenschappen
Abonneren op `property changed` gebeurtenissen:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Verzamelingen
Abonneren op `collection updated` gebeurtenissen:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
