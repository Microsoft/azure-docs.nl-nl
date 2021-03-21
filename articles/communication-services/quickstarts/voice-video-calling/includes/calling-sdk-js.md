---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: dee692dc6c82ae91272b39093398eba6ad908c1c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612428"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een geïmplementeerde Communication Services-resource. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een token voor gebruikers toegang om de aanroepende client in te scha kelen. Zie [toegangs tokens maken en beheren](../../access-tokens.md)voor meer informatie.
- Optioneel: Voltooi de Snelstartgids om [gesp roken oproep toe te voegen aan uw toepassing](../getting-started-with-calling.md).

## <a name="install-the-client-library"></a>De clientbibliotheek installeren

> [!NOTE]
> Dit document maakt gebruik van versie 1.0.0-Beta. 6 van de aanroepende client bibliotheek.

Gebruik de `npm install` opdracht om de Azure Communication Services-aanroep-en algemene client bibliotheken voor Java script te installeren.
In dit document wordt verwezen naar typen in versie 1.0.0-Beta. 5 van de aanroepende bibliotheek.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-client bibliotheek die aanroept:

| Naam                             | Beschrijving                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Het belangrijkste toegangs punt voor de aanroepende client bibliotheek.                                                                       |
| `CallAgent`                        | Wordt gebruikt om aanroepen te starten en te beheren.                                                                                            |
| `DeviceManager`                    | Gebruikt voor het beheren van media apparaten.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementeert de `CommunicationTokenCredential` Interface, die wordt gebruikt om een instantie te maken `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Initialiseer een CallClient-exemplaar, maak een CallAgent-exemplaar en toegang tot deviceManager

Maak een nieuw `CallClient` exemplaar. U kunt deze configureren met aangepaste opties, zoals een exemplaar van een logboek.

Wanneer u een `CallClient` exemplaar hebt, kunt u een `CallAgent` instantie maken door de `createCallAgent` methode aan te roepen voor het `CallClient` exemplaar. Hiermee wordt een `CallAgent` object Instance asynchroon geretourneerd.

De `createCallAgent` methode gebruikt `CommunicationTokenCredential` als een argument. Er wordt een [token voor gebruikers toegang](../../access-tokens.md)geaccepteerd.

Nadat u een instantie hebt gemaakt `callAgent` , kunt u de- `getDeviceManager` methode gebruiken `CallClient` om toegang te krijgen tot het exemplaar `deviceManager` .

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Een oproep doen

Als u een oproep wilt maken en starten, gebruikt u een van de Api's in `callAgent` en geeft u een gebruiker op die u hebt gemaakt via de client bibliotheek van de communicatie Services-identiteit.

Het maken van aanroepen en starten synchroon zijn. Met het gesprek exemplaar kunt u zich abonneren op het aanroepen van gebeurtenissen.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Een aanroep van 1: n naar een gebruiker of PSTN plaatsen

Als u een andere communicatie Services-gebruiker wilt aanroepen, gebruikt u de- `startCall` methode op `callAgent` en geeft u de geadresseerde door `CommunicationUserIdentifier` die u hebt [gemaakt met de beheer bibliotheek voor communicatie Services](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Als u een oproep wilt plaatsen naar een openbaar geschakeld telefoonnet werk (PSTN), gebruikt u de- `startCall` methode `callAgent` en geeft u de ontvanger door `PhoneNumberIdentifier` . Uw communicatie service-resource moet worden geconfigureerd om PSTN-aanroepen mogelijk te maken.

Wanneer u een PSTN-nummer aanroept, geeft u uw alternatieve beller-ID op. Een alternatieve beller-ID is een telefoon nummer (op basis van de E. 164-standaard) waarmee de beller in een PSTN-aanroep wordt geïdentificeerd. Het is het telefoon nummer dat de ontvanger van het gesprek ziet voor een inkomende oproep.

> [!NOTE]
> PSTN-aanroep bevindt zich momenteel in een persoonlijke preview. Voor toegang, van [toepassing op het early adopter programma](https://aka.ms/ACS-EarlyAdopter).

Voor een 1:1-aanroep gebruikt u de volgende code:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Voor een aanroep van 1: n gebruikt u de volgende code:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Een 1:1-oproep met video camera plaatsen

> [!IMPORTANT]
> Er kan momenteel niet meer dan één uitgaande lokale video stroom zijn.

Als u een video gesprek wilt plaatsen, moet u uw camera's opgeven met behulp `getCameras()` van de-methode in `deviceManager` .

Nadat u een camera hebt geselecteerd, gebruikt u deze om een instantie te maken `LocalVideoStream` . Geef deze door `videoOptions` als een item in de `localVideoStream` matrix met de `startCall` methode.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Wanneer uw gesprek verbinding maakt, begint het automatisch met het verzenden van een video stroom van de geselecteerde camera naar de andere deel nemer. Dit geldt ook voor de opties voor `Call.Accept()` video en `CallAgent.join()` video.

### <a name="join-a-group-call"></a>Deelnemen aan een groepsgesprek

Als u een nieuwe groeps oproep wilt starten of lid wilt worden van een doorlopende groep, gebruikt u de- `join` methode en geeft u een object door met een `groupId` eigenschap. De `groupId` waarde moet een GUID zijn.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Deel nemen aan een team vergadering

Als u wilt deel nemen aan een team vergadering, gebruikt u de- `join` methode en geeft u een koppeling of coördinaten voor de vergadering door.

Koppelen met behulp van een koppeling naar een vergadering:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Koppelen met behulp van Vergader coördinaten:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Een binnenkomend gesprek ontvangen

Het `callAgent` exemplaar verzendt een `incomingCall` gebeurtenis wanneer de aangemelde identiteit een inkomende oproep ontvangt. Als u wilt Luis teren naar deze gebeurtenis, kunt u zich aanmelden met een van de volgende opties:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo

    //Accept the call
    var call = await incomingCall.accept();

    //Reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

De `incomingCall` gebeurtenis bevat een `incomingCall` exemplaar dat u kunt accepteren of afwijzen.

## <a name="manage-calls"></a>Oproepen beheren

Tijdens een oproep kunt u toegang krijgen tot de oproep eigenschappen en video-en audio-instellingen beheren.

### <a name="check-call-properties"></a>Gespreks eigenschappen controleren

De unieke ID (teken reeks) voor een aanroep ophalen:

   ```js
    const callId: string = call.id;
   ```

Meer informatie over andere deel nemers aan de oproep door de verzameling te controleren `remoteParticipant` :

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

De aanroeper van een binnenkomende oproep identificeren:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` is een van de `CommunicationIdentifier` typen.

De status van een aanroep ophalen:

   ```js
   const callState = call.state;
   ```

   Hiermee wordt een teken reeks geretourneerd die de huidige status van een aanroep vertegenwoordigt:

  - `None`: Aanvankelijke oproep status.
  - `Incoming`: Hiermee wordt aangegeven dat een aanroep inkomend is. Deze moet ofwel geaccepteerd of afgekeurd zijn.
  - `Connecting`: Initiële overgangs status wanneer een aanroep wordt geplaatst of geaccepteerd.
  - `Ringing`: Voor een uitgaande oproep geeft aan dat een aanroep wordt opgeroepen voor externe deel nemers. `Incoming`Aan de zijkant.
  - `EarlyMedia`: Hiermee wordt een status aangegeven waarin een aankondiging wordt afgespeeld voordat de aanroep is verbonden.
  - `Connected`: Geeft aan dat de aanroep is verbonden.
  - `LocalHold`: Hiermee geeft u aan dat de oproep wordt geblokkeerd door een lokale deel nemer. Geen enkel medium loopt tussen het lokale eind punt en de externe deel nemers.
  - `RemoteHold`: Geeft aan dat de aanroep door een externe deel nemer is geblokkeerd. Geen enkel medium loopt tussen het lokale eind punt en de externe deel nemers.
  - `Disconnecting`: Transitie status voordat de aanroep naar een status wordt verplaatst `Disconnected` .
  - `Disconnected`: Laatste oproep status. Als de netwerk verbinding is verbroken, wordt de status gewijzigd in `Disconnected` na twee minuten.

Ontdek waarom een gesprek is beëindigd door de eigenschap te controleren `callEndReason` :

   ```js
   const callEndReason = call.callEndReason;
   // callEndReason.code (number) code associated with the reason
   // callEndReason.subCode (number) subCode associated with the reason
   ```

Meer informatie over de huidige oproep inkomend of uitgaand door de eigenschap te controleren `direction` . Wordt geretourneerd `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Controleer of de huidige microfoon is gedempt. Wordt geretourneerd `Boolean` .

   ```js
   const muted = call.isMicrophoneMuted;
   ```

Nagaan of de stroom voor het delen van het scherm wordt verzonden vanuit een bepaald eind punt door de eigenschap te controleren `isScreenSharingOn` . Wordt geretourneerd `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Inspecteer actieve video stromen door de `localVideoStreams` verzameling te controleren. Er worden `LocalVideoStream` objecten geretourneerd.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>Een callEnded-gebeurtenis controleren

Het `call` exemplaar verzendt een `callEnded` gebeurtenis wanneer de aanroep eindigt. Als u wilt Luis teren naar deze gebeurtenis, kunt u zich abonneren met de volgende code:

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

Als u een video wilt starten, moet u camera's opgeven met behulp van de `getCameras` methode voor het `deviceManager` object. Maak vervolgens een nieuw exemplaar van `LocalVideoStream` door de gewenste camera door te geven aan de `startVideo` methode als een argument:

```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);
```

Nadat u hebt begonnen `LocalVideoStream` met het verzenden van de video, wordt een exemplaar toegevoegd aan de `localVideoStreams` verzameling in een aanroep exemplaar.

```js
call.localVideoStreams[0] === localVideoStream;
```

Als u lokale video wilt stoppen, geeft u de `localVideoStream` instantie door die beschikbaar is in de `localVideoStreams` verzameling:

```js
await call.stopVideo(localVideoStream);
```

U kunt overschakelen naar een ander camera apparaat terwijl een video wordt verzonden door aan te roepen `switchSource` op een `localVideoStream` exemplaar:

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);
```

## <a name="manage-remote-participants"></a>Externe deel nemers beheren

Alle externe deel nemers worden vertegenwoordigd door `remoteParticipant` en zijn beschikbaar via de `remoteParticipants` verzameling op een aanroep exemplaar.

### <a name="list-the-participants-in-a-call"></a>De deel nemers in een gesprek weer geven

De `remoteParticipants` verzameling retourneert een lijst met externe deel nemers in een gesprek:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Eigenschappen van toegang tot externe deel nemer

Externe deel nemers hebben een set gekoppelde eigenschappen en verzamelingen:

- `CommunicationIdentifier`: De id voor een externe deel nemer ophalen. De identiteit is een van de `CommunicationIdentifier` volgende typen:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Dit kan een van de volgende `CommunicationIdentifier` typen zijn:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Object dat de ACS-gebruiker vertegenwoordigt.
  - `{ phoneNumber: '<E.164>' }`: Object dat het telefoon nummer in de indeling E. 164 vertegenwoordigt.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Object dat de gebruiker teams vertegenwoordigt.

- `state`: De status van een externe deel nemer ophalen.

  ```js
  const state = remoteParticipant.state;
  ```

  De status kan zijn:

  - `Idle`: Begin status.
  - `Connecting`: Transitie status terwijl een deel nemer verbinding maakt met de aanroep.
  - `Ringing`: Deel nemer wordt gebeld.
  - `Connected`: Deel nemer is verbonden met de aanroep.
  - `Hold`: Deel nemer is in de wacht stand.
  - `EarlyMedia`: Aankondiging die wordt afgespeeld voordat een deel nemer verbinding maakt met de aanroep.
  - `Disconnected`: Eind status. De deel nemer is losgekoppeld van de aanroep. Als de externe deel nemer de netwerk verbinding verliest, verandert de status in `Disconnected` na twee minuten.

- `callEndReason`: Als u wilt weten waarom een deel nemer de oproep heeft verlaten, controleert u de `callEndReason` eigenschap:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  // callEndReason.code (number) code associated with the reason
  // callEndReason.subCode (number) subCode associated with the reason
  ```

- `isMuted` status: als u wilt weten of een externe deel nemer is gedempt, controleert u de `isMuted` eigenschap. Wordt geretourneerd `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` status: als u wilt weten of een externe deel nemer spreekt, controleert u de `isSpeaking` eigenschap. Wordt geretourneerd `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Als u alle video stromen wilt controleren die een bepaalde deel nemer in deze aanroep verzendt, controleert u de `videoStreams` verzameling. Het bevat `RemoteVideoStream` objecten.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```

### <a name="add-a-participant-to-a-call"></a>Een deel nemer aan een gesprek toevoegen

Als u een deel nemer (een gebruiker of een telefoon nummer) aan een gesprek wilt toevoegen, kunt u gebruiken `addParticipant` . Geef een van de `Identifier` typen op. Hiermee wordt het `remoteParticipant` exemplaar geretourneerd.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Een deel nemer uit een gesprek verwijderen

Als u een deel nemer (een gebruiker of een telefoon nummer) van een gesprek wilt verwijderen, kunt u aanroepen `removeParticipant` . U moet een van de typen door geven `Identifier` . Dit wordt asynchroon opgelost nadat de deel nemer is verwijderd uit de aanroep. De deel nemer wordt ook verwijderd uit de `remoteParticipants` verzameling.

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

Als u wilt weer geven `RemoteVideoStream` , moet u zich abonneren op een `isAvailableChanged` gebeurtenis. Als de `isAvailable` eigenschap wordt gewijzigd in `true` , wordt een stroom door een externe deel nemer verzonden. Daarna maakt u een nieuw exemplaar van `Renderer` en maakt u een nieuw `RendererView` exemplaar met behulp van de asynchrone `createView` methode.  U kunt vervolgens koppelen `view.target` aan elk UI-element.

Wanneer de beschik baarheid van een externe stroom wordt gewijzigd, kunt u `Renderer` een specifiek exemplaar vernietigen, vernietigen `RendererView` of het allemaal blijven gebruiken. Renderers die zijn gekoppeld aan een niet-beschik bare stroom, resulteren in een leeg video frame.

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

- `id`: De ID van een externe video stroom.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `Stream.size`: De hoogte en breedte van een externe video stroom.

  ```js
  const size: {width: number; height: number} = remoteVideoStream.size;
  ```

- `mediaStreamType`: Kan `Video` of `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Of een stroom van een extern deel nemer-eind punt actief is.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="renderer-methods-and-properties"></a>Methoden en eigenschappen van renderer

Maak een `rendererView` exemplaar dat kan worden gekoppeld in de gebruikers interface van de toepassing om de externe video stroom te renderen:

  ```js
  renderer.createView()
  ```

Verwijdering van `renderer` en alle gekoppelde `rendererView` instanties:

  ```js
  renderer.dispose()
  ```

### <a name="rendererview-methods-and-properties"></a>RendererView methoden en eigenschappen

Wanneer u maakt `rendererView` , kunt u de `scalingMode` Eigenschappen en opgeven `isMirrored` . `scalingMode` kan `Stretch` , `Crop` of `Fit` . Als `isMirrored` is opgegeven, wordt de gerenderde stroom verticaal gespiegeld.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```

Elk `RendererView` exemplaar heeft een `target` eigenschap die het weergave oppervlak vertegenwoordigt. Voeg deze eigenschap toe aan de gebruikers interface van de toepassing:

```js
document.body.appendChild(rendererView.target);
```

U kunt bijwerken `scalingMode` door de methode aan te roepen `updateScalingMode` :

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Apparaatbeheer

In `deviceManager` kunt u lokale apparaten opgeven die uw audio-en video stromen in een gesprek kunnen verzenden. Het helpt u ook bij het aanvragen van machtigingen voor toegang tot de microfoon en camera van een andere gebruiker met behulp van de systeem eigen browser-API.

U kunt toegang krijgen `deviceManager` door de methode aan te roepen `callClient.getDeviceManager()` :

> [!IMPORTANT]
> U moet een `callAgent` object hebben voordat u toegang kunt krijgen `deviceManager` .

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Lokale apparaten ophalen

Voor toegang tot lokale apparaten kunt u inventarisatie methoden gebruiken `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>De standaard microfoon en-spreker instellen

In `deviceManager` kunt u een standaard apparaat instellen dat u gebruikt om een gesprek te starten. Als de standaard waarden van de client niet zijn ingesteld, gebruikt communicatie Services de standaard instellingen van het besturings systeem.

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

U kunt `deviceManager` en gebruiken `Renderer` om streams van uw lokale camera te renderen. Deze stroom wordt niet naar andere deel nemers verzonden. het is een lokale preview-feed.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Toestemming voor camera en microfoon aanvragen

Een gebruiker vragen om camera-en microfoon machtigingen te verlenen:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Dit leidt tot een object dat aangeeft of `audio` en `video` machtigingen zijn verleend:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Record aanroepen

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

Het opnemen van aanroepen is een uitgebreide functie van de core- `Call` API. U moet eerst het object voor de opname functie-API verkrijgen:

```js
const callRecordingApi = call.api(Features.Recording);
```

Controleer vervolgens de eigenschap van om te controleren of de oproep wordt vastgelegd `isRecordingActive` `callRecordingApi` . Wordt geretourneerd `Boolean` .

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

## <a name="transfer-calls"></a>Overdrachts aanroepen

De aanroep overdracht is een uitgebreide functie van de core- `Call` API. U moet eerst het object voor de overdrachts functie-API ophalen:

```js
const callTransferApi = call.api(Features.Transfer);
```

Bij gespreks overdrachten zijn drie partijen betrokken:

- *Cedent*: de persoon die de overdrachts aanvraag initieert.
- *Cessionaris*: de persoon die wordt overgedragen.
- *Doel* van de overdracht: de persoon naar wie wordt overgezet.

Transfers volgen de volgende stappen:

1. Er is al een verbonden oproep tussen de *cedent* en de *cessionaris*. De *cedent* besluit de oproep van de *cessionaris* over te dragen naar het doel van de *overdracht*.
1. De *cedent* roept de `transfer` API aan.
1. De *cessionaris* beslist of `accept` de overdrachts `reject` aanvraag moet worden verzonden naar het *doel* van de overdracht met behulp van een `transferRequested` gebeurtenis.
1. Het *overdrachts doel* ontvangt alleen een binnenkomende oproep als de *cessionaris* de overdrachts aanvraag accepteert.

U kunt de API gebruiken om een huidige oproep over te dragen `transfer` . `transfer``transferCallOptions`Hiermee wordt de optioneel, waarmee u een markering kunt instellen `disableForwardingAndUnanswered` :

- `disableForwardingAndUnanswered = false`: Als het *overdrachts doel* de overdrachts oproep niet beantwoordt, volgt de overdracht het door sturen van *overdrachts doelen* en de niet-geantwoorde instellingen.
- `disableForwardingAndUnanswered = true`: Als het *overdrachts doel* de overdrachts oproep niet beantwoordt, wordt de overdrachts poging beëindigd.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer`Met de API kunt u zich abonneren op `transferStateChanged` en `transferRequested` gebeurtenissen. Een `transferRequested` gebeurtenis is afkomstig van een `call` exemplaar; een `transferStateChanged` gebeurtenis en overdracht `state` en `error` afkomstig van een `transfer` exemplaar.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

De *cessionaris* kan de overdrachts aanvraag die wordt geïnitieerd door de *cedent* in het geval accepteren of afwijzen met `transferRequested` behulp van `accept()` of `reject()` in `transferRequestedEventArgs` . U kunt toegang krijgen tot `targetParticipant` informatie en `accept` of `reject` methoden in `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>Meer informatie over gebeurtenis modellen

Inspecteer de huidige waarden en Abonneer u op het bijwerken van gebeurtenissen voor toekomstige waarden.

### <a name="properties"></a>Eigenschappen

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Verzamelingen

```js
// Inspect the current collection
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
