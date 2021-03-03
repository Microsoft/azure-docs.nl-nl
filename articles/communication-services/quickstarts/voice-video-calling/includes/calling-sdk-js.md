---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 3830025d761c94e2b0b0bc3e66389d66794b946c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661523"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een geïmplementeerde Communication Services-resource. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een `User Access Token` om de aanroepende client in te schakelen. Voor meer informatie over het [verkrijgen van een `User Access Token`](../../access-tokens.md)
- Optioneel: Voltooi de Snelstartgids om aan de [slag te gaan met het toevoegen van een oproep aan uw toepassing](../getting-started-with-calling.md)

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

> [!NOTE]
> Dit document maakt gebruik van versie 1.0.0-Beta. 6 van de aanroepende client bibliotheek.

Gebruik de `npm install` opdracht om de Azure Communication Services-aanroep-en algemene client bibliotheken voor Java script te installeren.
In dit document wordt verwezen naar de typen in versie 1.0.0-Beta. 5 van de aanroepende bibliotheek.

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
| DeviceManager                    | De DeviceManager wordt gebruikt voor het beheren van media apparaten                                                                                           |
| AzureCommunicationTokenCredential | De klasse AzureCommunicationTokenCredential implementeert de CommunicationTokenCredential-interface die wordt gebruikt om de CallAgent te instantiëren. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Initialiseer de CallClient, maak CallAgent en open DeviceManager

Exemplaar een nieuw `CallClient` exemplaar. U kunt deze configureren met aangepaste opties, zoals een exemplaar van een logboek.
Zodra een `CallClient` is geïnstantieerd, kunt u een instantie maken `CallAgent` door de `createCallAgent` methode aan te roepen voor het `CallClient` exemplaar. Hiermee wordt een `CallAgent` object Instance asynchroon geretourneerd.
De `createCallAgent` methode heeft een `CommunicationTokenCredential` als argument, waarmee een token voor [gebruikers toegang](../../access-tokens.md)wordt geaccepteerd.
Om toegang te krijgen tot het `DeviceManager` callAgent-exemplaar moet eerst worden gemaakt. U kunt de-methode vervolgens gebruiken voor het `getDeviceManager` `CallClient` exemplaar om de DeviceManager op te halen.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Een uitgaande oproep plaatsen

Als u een gesprek wilt maken en starten, moet u een van de Api's op CallAgent gebruiken en een gebruiker bieden die u hebt gemaakt via de client bibliotheek van de communicatie Services-identiteit.

Aanroepen maken en starten is synchroon. Met het gesprek exemplaar kunt u zich abonneren op het aanroepen van gebeurtenissen.

## <a name="place-a-call"></a>Een oproep doen

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Een 1:1-aanroep naar een gebruiker of PSTN plaatsen
Als u een aanroep naar een andere communicatie Services-gebruiker wilt plaatsen, roept u de- `startCall` methode aan `callAgent` en geeft u de CommunicationUserIdentifier van de oproep door die u hebt [gemaakt met de beheer bibliotheek voor communicatie Services](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Als u een aanroepen naar een PSTN wilt plaatsen, roept u de- `startCall` methode aan `callAgent` en geeft u de PhoneNumberIdentifier van de oproep door.
Uw communicatie service-resource moet worden geconfigureerd om PSTN-aanroepen mogelijk te maken.
Wanneer u een PSTN-nummer aanroept, moet u uw alternatieve beller-ID opgeven. Een alternatieve beller-ID verwijst naar een telefoon nummer (op basis van de standaard E. 164) waarmee de beller in een PSTN-aanroep wordt geïdentificeerd. Wanneer u bijvoorbeeld een alternatieve beller-ID voor de PSTN-aanroep opgeeft, wordt het telefoon nummer dat wordt weer gegeven aan de aanroepee wanneer de oproep binnenkomend is.

> [!WARNING]
> PSTN-aanroep bevindt zich momenteel in een persoonlijke preview. Voor toegang, van [toepassing op early adopter programma](https://aka.ms/ACS-EarlyAdopter).
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Een aanroep van 1: n met gebruikers en PSTN plaatsen
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Een 1:1-oproep met video camera plaatsen
> [!WARNING]
> Er kan momenteel niet meer dan één uitgaande lokale video stroom zijn.
U moet lokale camera's opsommen met behulp van de deviceManager-API als u een video gesprek wilt plaatsen `getCameras()` .
Wanneer u de gewenste camera hebt geselecteerd, gebruikt u deze om een exemplaar te maken `LocalVideoStream` en dit door te geven aan de hand van `videoOptions` een item in de `localVideoStream` matrix `startCall` .
Zodra uw gesprek verbinding maakt, begint automatisch met het verzenden van een video stroom van de geselecteerde camera naar de andere deel nemer (s). Dit geldt ook voor de video opties call. accept () en CallAgent. Add ().
```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Deelnemen aan een groepsgesprek
Als u een nieuwe groeps oproep wilt starten of lid wilt worden van een doorlopende groep, gebruikt u de methode ' samen voegen ' en geeft u een object door met een `groupId` eigenschap. De waarde moet een GUID zijn.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

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

## <a name="receiving-an-incoming-call"></a>Een binnenkomend gesprek ontvangen

Het `CallAgent` exemplaar verzendt een `incomingCall` gebeurtenis wanneer de geregistreerde identiteit een inkomende oproep ontvangt. Als u wilt Luis teren naar deze gebeurtenis, abonneert u zich op de volgende manier:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo
    
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

De `incomingCall` gebeurtenis wordt geleverd met een exemplaar van `IncomingCall` waarop u een aanroep kunt accepteren of afwijzen.


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

* De id van de oproepende functie als de aanroep inkomend is. Id is een van de `CommunicationIdentifier` typen
```js

const callerIdentity = call.callerInfo.identifier;

* Get the state of the Call.
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
* ' LocalHold ': de aanroep wordt door de lokale deel nemer in de wacht geplaatst, geen medium loopt tussen het lokale eind punt en de externe deel nemer (s)
* ' RemoteHold ': de oproep wordt door een externe deel nemer in de wacht geplaatst, geen medium loopt tussen het lokale eind punt en de externe deel nemer (s)
* ' Verbinding verbreken '-de status van de overdracht van de aanroep is verbroken
* ' Verbinding verbroken '-laatste gespreks status
  * Als de netwerk verbinding is verbroken, wordt de status na ongeveer 2 minuten verbroken.

* Inspecteer de eigenschap om te zien waarom een bepaalde aanroep is beëindigd `callEndReason` .
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Als u wilt weten of de huidige aanroep een binnenkomende of uitgaande oproep is, inspecteert u de `direction` eigenschap, wordt deze geretourneerd `CallDirection` .
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
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

### <a name="call-ended-event"></a>Gebeurtenis oproep beëindigd

Het `Call` exemplaar verzendt een `callEnded` gebeurtenis wanneer de aanroep eindigt. U kunt op de volgende manier abonneren op deze gebeurtenis:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
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


Als u een video wilt starten, moet u camera's opsommen met behulp `getCameras` van de methode voor het `deviceManager` object. Maak vervolgens een nieuw exemplaar van `LocalVideoStream` het door geven van de gewenste camera in de `startVideo` methode als een argument:


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
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);

```

## <a name="remote-participants-management"></a>Beheer van externe deel nemers

Alle externe deel nemers worden weer gegeven op `RemoteParticipant` type en beschikbaar via `remoteParticipants` verzameling op een aanroep exemplaar.

### <a name="list-participants-in-a-call"></a>Deel nemers in een gesprek weer geven
De `remoteParticipants` verzameling retourneert een lijst met externe deel nemers in de gegeven aanroep:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Eigenschappen van externe deel nemer
Aan de externe deel nemer is een set eigenschappen en verzamelingen gekoppeld
#### <a name="communicationidentifier"></a>CommunicationIdentifier
De id voor deze externe deel nemer ophalen.
De identiteit is een van de CommunicationIdentifier-typen:
```js
const identifier = remoteParticipant.identifier;
```
Dit kan een van de CommunicationIdentifier-typen zijn:
  * {communicationUserId: ' <ACS_USER_ID >}-object dat een ACS-gebruiker vertegenwoordigt
  * {phoneNumber: ' <E. 164> '}-object dat het telefoon nummer vertegenwoordigt in de indeling E. 164
  * {microsoftTeamsUserId: ' <TEAMS_USER_ID> ', isAnonymous?: Boolean; Cloud?: ' openbaar ' | "DoD" | "gcch"}-object dat teams gebruiker vertegenwoordigt

#### <a name="state"></a>Staat
De status van deze externe deel nemer ophalen.
```js

const state = remoteParticipant.state;
```
Status kan een van
* ' Inactief '-initiële status
* ' Verbinding maken '-transitie status terwijl deel nemer verbinding maakt met de aanroep
* ' Belsignaal ': de deel nemer wordt opgeroepen
* Verbonden: de deel nemer is verbonden met de oproep
* Hold-deel nemer is in de wacht stand
* ' EarlyMedia ': de aankondiging wordt afgespeeld voordat de deel nemer is verbonden met de oproep
* ' Verbinding verbroken ': eind status-de deel nemer is losgekoppeld van de aanroep
  * Als de externe deel nemer hun netwerk verbinding verliest, wordt de status van de externe deel nemer na ongeveer twee minuten niet meer verbonden.

#### <a name="call-end-reason"></a>Eind reden van aanroep
Als u wilt weten waarom de deel nemer de oproep heeft verlaten, inspecteert u de `callEndReason` eigenschap:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>Is gedempt
Als u wilt controleren of deze externe deel nemer is gedempt of niet, controleert `isMuted` u de eigenschap. `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Spreekt
Als u wilt controleren of deze externe deel nemer spreekt of niet, controleert `isSpeaking` u de eigenschap die wordt geretourneerd `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Video stromen
Als u alle video stromen wilt controleren die een bepaalde deel nemer in deze aanroep verzendt, controleert u of `videoStreams` deze `RemoteVideoStream` objecten bevat
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Een deel nemer aan een gesprek toevoegen

Als u een deel nemer wilt toevoegen aan een aanroep (een gebruiker of een telefoon nummer), kunt u deze aanroepen `addParticipant` .
Geef een van de typen ' identifier ' op.
Hiermee wordt het exemplaar van de externe deel nemer synchroon geretourneerd.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Deel nemer uit een gesprek verwijderen

Als u een deel nemer wilt verwijderen uit een aanroep (een gebruiker of een telefoon nummer), kunt u deze aanroepen `removeParticipant` .
U moet een van de typen ' identifier ' door geven deze wordt asynchroon opgelost zodra de deel nemer uit de aanroep is verwijderd.
De deel nemer wordt ook verwijderd uit de `remoteParticipants` verzameling.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Video stromen voor externe deel nemers weer geven

Als u de video stromen en de streams voor het delen van externe deel nemers wilt weer geven, inspecteert u de `videoStreams` verzamelingen:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Als u een wilt weer geven `RemoteVideoStream` , moet u zich abonneren op een `isAvailableChanged` gebeurtenis.
Als de `isAvailable` eigenschap wordt gewijzigd in `true` , wordt een stroom door een externe deel nemer verzonden.
Als dat gebeurt, maakt u een nieuw exemplaar van `Renderer` en maakt u vervolgens een nieuw `RendererView` exemplaar met behulp van de asynchrone `createView` methode.  U kunt vervolgens `view.target` aan elk UI-element worden gekoppeld.
Wanneer de beschik baarheid van een externe stroom verandert, kunt u ervoor kiezen om de volledige renderer te vernietigen, een specifieke `RendererView` of te blijven gebruiken, maar dit resulteert in het weer geven van een leeg video frame.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
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
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
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
Bij het maken van een `RendererView` kunt `scalingMode` u `isMirrored` Eigenschappen en opgeven.
De schaal modus kan ' Stretch ', ' gewas ' of ' fit ' zijn als `isMirrored` deze is opgegeven, de gerenderde stroom wordt verticaal gespiegeld.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```
Elk gegeven `RendererView` exemplaar heeft een `target` eigenschap die het weergave oppervlak vertegenwoordigt. Dit moet worden gekoppeld aan de gebruikers interface van de toepassing:
```js
document.body.appendChild(rendererView.target);
```

U kunt de schaal modus later bijwerken door de methode aan te roepen `updateScalingMode` .
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Apparaatbeheer

`DeviceManager` Hiermee kunt u lokale apparaten opsommen die kunnen worden gebruikt in een aanroep om uw audio/video-streams te verzenden. U kunt hiermee ook machtigingen aanvragen van een gebruiker om toegang te krijgen tot hun microfoon en camera met behulp van de systeem eigen browser-API.

U kunt de `deviceManager` methode by aanroepen gebruiken `callClient.getDeviceManager()` .
> [!WARNING]
> Momenteel `callAgent` moet een object eerst worden geïnstantieerd om toegang te krijgen tot DeviceManager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Lokale apparaten opsommen

Voor toegang tot lokale apparaten kunt u inventarisatie methoden gebruiken op de Apparaatbeheer. Opsomming is een asynchrone actie.

```js

//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Standaard microfoon/-spreker instellen

Met Apparaatbeheer kunt u een standaard apparaat instellen dat wordt gebruikt bij het starten van een aanroep.
Als de standaard waarden van de client niet zijn ingesteld, worden de standaard instellingen van het besturings systeem hersteld door communicatie Services.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Voor beeld van lokale camera

U kunt `DeviceManager` en gebruiken `Renderer` om streams van uw lokale camera te renderen. Deze stroom wordt niet naar andere deel nemers verzonden. het is een lokale preview-feed. Dit is een asynchrone actie.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Machtiging aanvragen voor camera/microfoon

Vraag een gebruiker om camera/microfoon machtigingen te verlenen met het volgende:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```
Dit wordt asynchroon opgelost met een object dat aangeeft of `audio` en `video` machtigingen zijn verleend:
```js
console.log(result.audio);
console.log(result.video);
```


## <a name="call-recording-management"></a>Beheer van oproep opname

Het opnemen van aanroepen is een uitgebreide functie van de core- `Call` API. U moet eerst het object voor de opname functie-API verkrijgen:

```js
const callRecordingApi = call.api(Features.Recording);
```

Als u vervolgens wilt controleren of de oproep wordt vastgelegd, inspecteert u de `isRecordingActive` eigenschap van, wordt deze `callRecordingApi` geretourneerd `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

U kunt zich ook abonneren op de opname wijzigingen:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="call-transfer-management"></a>Beheer van oproep overdracht

De aanroep overdracht is een uitgebreide functie van de core- `Call` API. U moet eerst het object voor de overdrachts functie-API verkrijgen:

```js
const callTransferApi = call.api(Features.Transfer);
```

Bij het overdragen van de oproep zijn drie betrokkenen *van derden,* *cessionaris* en *overdrachts doel* betrokken. Overdrachts stroom werkt als volgt:

1. Er is al een verbonden oproep tussen de *cedent* en de *overnemer*
2. *cedent* besluit de *oproep over te dragen (overdrachts*  ->  *doel*)
3. aanroep-API van de *cedent* `transfer`
4. *cessionaris* beslist of `accept` `reject` de overdrachts aanvraag moet worden *overgedragen* via een `transferRequested` gebeurtenis.
5. het *overdrachts doel* ontvangt alleen een binnenkomende aanroep *als de overdrachts* aanvraag is gelukt `accept`

### <a name="transfer-terminology"></a>Terminologie voor overdracht

- Cedent: degene die de overdrachts aanvraag initieert
- Cessionaris-het account dat wordt overgedragen door de cedent naar het doel van de overdracht
- Doel voor overdracht: het doel dat wordt overgedragen aan

U kunt synchrone API gebruiken om de huidige oproep over te dragen `transfer` . `transfer` accepteert optioneel, zodat `TransferCallOptions` u een vlag kunt instellen `disableForwardingAndUnanswered` :

- `disableForwardingAndUnanswered` = False: als het *overdrachts doel* de overdrachts oproep niet beantwoordt, wordt het door sturen van de *overdrachts doelen* en de niet-geantwoorde instellingen gevolgd
- `disableForwardingAndUnanswered` = True-als het *overdrachts doel* de overdrachts oproep niet beantwoordt, wordt de overdrachts poging beëindigd

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Met overdracht kunt u zich abonneren op `transferStateChanged` en `transferRequested` gebeurtenissen. `transferRequsted` de gebeurtenis is afkomstig van `call` instance, `transferStateChanged` Event en overdracht `state` en `error` is van `transfer` instantie

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

De overnemer kan de overdrachts aanvraag die wordt geïnitieerd door de cedent `transferRequested` via `accept()` of in accepteren of afwijzen `reject()` `transferRequestedEventArgs` . U kunt toegang krijgen tot `targetParticipant` informatie, `accept` , `reject` methoden in `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>Gebeurtenis model
U moet de huidige waarden controleren en u abonneren op het bijwerken van gebeurtenissen voor toekomstige waarden.

### <a name="properties"></a>Eigenschappen

```js
// Inspect current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Verzamelingen
```js
// Inspect current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});



// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
