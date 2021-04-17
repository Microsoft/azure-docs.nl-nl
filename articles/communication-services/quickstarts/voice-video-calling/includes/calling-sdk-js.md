---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590080"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een geïmplementeerde Communication Services-resource. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een token voor gebruikerstoegang om de aanroepende client in teschakelen. Zie Toegangstokens maken [en beheren voor meer informatie.](../../access-tokens.md)
- Optioneel: Voltooi de quickstart om [spraak aan uw toepassing toe te voegen.](../getting-started-with-calling.md)

## <a name="install-the-sdk"></a>De SDK installeren

> [!NOTE]
> In dit document wordt gebruikgemaakt van ACS Calling Web SDK.

Gebruik de opdracht om de Azure Communication Services en algemene `npm install` SDK's voor JavaScript te installeren.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services Calling SDK:

| Naam                             | Beschrijving                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Het belangrijkste toegangspunt voor de aanroepende SDK.                                                                       |
| `CallAgent`                        | Wordt gebruikt om aanroepen te starten en te beheren.                                                                                            |
| `DeviceManager`                    | Wordt gebruikt voor het beheren van mediaapparaten.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementeert de `CommunicationTokenCredential` -interface, die wordt gebruikt om te `callAgent` instanteren. |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Initialiseer een CallClient-exemplaar, maak een CallAgent-exemplaar en krijg toegang tot deviceManager

Maak een nieuw `CallClient` exemplaar. U kunt deze configureren met aangepaste opties, zoals een Logger-exemplaar.

Wanneer u een exemplaar `CallClient` hebt, kunt u een exemplaar `CallAgent` maken door de methode op het exemplaar aan te `createCallAgent` `CallClient` roepen. Hiermee wordt asynchroon een `CallAgent` exemplaarobject retourneert.

De `createCallAgent` methode gebruikt als `CommunicationTokenCredential` argument. Het accepteert een [token voor gebruikerstoegang.](../../access-tokens.md)

U kunt de methode `getDeviceManager` op het exemplaar gebruiken om toegang te krijgen tot `CallClient` `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Een oproep doen

Als u een aanroep wilt maken en starten, gebruikt u een van de API's in en geeft u een gebruiker op die u hebt gemaakt via `callAgent` de Communication Services identity SDK.

Het maken en starten van aanroepen is synchroon. Met het aanroep-exemplaar kunt u zich abonneren op aanroepgebeurtenissen.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Een 1:n-aanroep naar een gebruiker of PSTN plaatsen

Als u een andere Communication Services wilt aanroepen, gebruikt u de methode on en geef u de van de ontvanger door die u hebt gemaakt met `startCall` `callAgent` de `CommunicationUserIdentifier` [Communication Services-beheerbibliotheek](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Als u een aanroep naar een openbaar telefoonnetwerk (PSTN) wilt plaatsen, gebruikt u de methode on en door te geven aan de `startCall` `callAgent` van de `PhoneNumberIdentifier` ontvanger. Uw Communication Services resource moet worden geconfigureerd om PSTN-aanroepen toe te staan.

Wanneer u een PSTN-nummer aanroept, geeft u uw alternatieve beller-id op. Een alternatieve beller-id is een telefoonnummer (op basis van de E.164-standaard) dat de aanroeper in een PSTN-oproep identificeert. Het is het telefoonnummer dat de ontvanger van de oproep ziet voor een binnenkomende oproep.

> [!NOTE]
> PSTN-aanroepen is momenteel beschikbaar als privépreview. Voor toegang moet [u van toepassing zijn op early adopter programma](https://aka.ms/ACS-EarlyAdopter).

Gebruik de volgende code voor een 1:1-aanroep:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Gebruik de volgende code voor een 1:n-aanroep:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Een 1:1-oproep plaatsen met de videocamera

> [!IMPORTANT]
> Er kan momenteel niet meer dan één uitgaande lokale videostream zijn.

Als u een videooproep wilt plaatsen, moet u lokale camera's opsnoemen met behulp van `getCameras()` de methode in `deviceManager` .

Nadat u een camera hebt geselecteerd, gebruikt u deze om een exemplaar te `LocalVideoStream` maken. Geef deze binnen `videoOptions` als een item in de matrix door aan de methode `localVideoStream` `startCall` .


```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Wanneer uw aanroep verbinding maakt, wordt automatisch een videostream van de geselecteerde camera naar de andere deelnemer verzonden. Dit geldt ook voor de `Call.Accept()` videoopties en `CallAgent.join()` videoopties.

### <a name="join-a-group-call"></a>Deelnemen aan een groepsgesprek

> [!NOTE]
> De `groupId` parameter wordt beschouwd als systeemmetagegevens en kan door Microsoft worden gebruikt voor bewerkingen die nodig zijn om het systeem uit te voeren. Neem geen persoonsgegevens op in de `groupId` waarde. Microsoft behandelt deze parameter niet als persoonlijke gegevens en de inhoud ervan kan zichtbaar zijn voor Microsoft-werknemers of op de lange termijn worden opgeslagen.
>
> Voor `groupId` de parameter moeten gegevens een GUID-indeling hebben. We raden u aan willekeurig gegenereerde GUID's te gebruiken die niet worden beschouwd als persoonsgegevens in uw systemen.
>

Als u een nieuwe groepsoproep wilt starten of wilt deelnemen aan een lopende groepsoproep, gebruikt u de methode en gebruikt u een `join` -object met een `groupId` eigenschap. De `groupId` waarde moet een GUID zijn.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Deelnemen aan een teamsvergadering
> [!NOTE]
> Deze API is beschikbaar als preview-versie voor ontwikkelaars en wordt mogelijk gewijzigd op basis van de feedback die we ontvangen. Gebruik deze API niet in een productie-omgeving. Als u deze API wilt gebruiken, gebruikt u de bètaversie van ACS Calling Web SDK

Als u deel wilt nemen aan een Teams-vergadering, gebruikt u de methode en passeert u een koppeling naar een vergadering `join` of de coördinaten van een vergadering.

Deelnemen met behulp van een koppeling naar een vergadering:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Deelnemen met behulp van coördinaten van vergadering:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Een binnenkomende oproep ontvangen

Het exemplaar stuurt een gebeurtenis wanneer de aangemelde identiteit een `callAgent` `incomingCall` binnenkomende aanroep ontvangt. Als u wilt luisteren naar deze gebeurtenis, abonneert u zich met een van de volgende opties:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 
    // Get incoming call ID
    var incomingCallId = incomingCall.id
    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

De `incomingCall` gebeurtenis bevat een exemplaar dat u kunt accepteren of `incomingCall` afwijzen.

## <a name="manage-calls"></a>Aanroepen beheren

Tijdens een aanroep hebt u toegang tot oproepeigenschappen en kunt u video- en audio-instellingen beheren.

### <a name="check-call-properties"></a>Aanroepeigenschappen controleren

Haal de unieke id (tekenreeks) voor een aanroep op:

   ```js
    const callId: string = call.id;
   ```
Informatie over de aanroep:
> [!NOTE]
> Deze API is beschikbaar als preview-versie voor ontwikkelaars en wordt mogelijk gewijzigd op basis van de feedback die we ontvangen. Gebruik deze API niet in een productie-omgeving. Als u deze API wilt gebruiken, gebruikt u de bètaversie van ACS Calling Web SDK
   ```js
   const callInfo = call.info;
   ```

Meer informatie over andere deelnemers aan de oproep door de verzameling `remoteParticipants` te controleren op het exemplaar 'aanroepen':

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identificeer de aanroeper van een binnenkomende aanroep:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` is een van de `CommunicationIdentifier` typen.

De status van een aanroep op te halen:

   ```js
   const callState = call.state;
   ```

   Hiermee wordt een tekenreeks retourneert die de huidige status van een aanroep vertegenwoordigt:

  - `None`: Initiële aanroeptoestand.
  - `Connecting`: Initiële overgangstoestand wanneer een aanroep wordt geplaatst of geaccepteerd.
  - `Ringing`: Voor een uitgaande aanroep geeft aan dat er een aanroep voor externe deelnemers binnenkomende. Het staat `Incoming` aan hun kant.
  - `EarlyMedia`: Geeft een status aan waarin een aankondiging wordt afgespeeld voordat de aanroep is verbonden.
  - `Connected`: geeft aan dat de aanroep is verbonden.
  - `LocalHold`: geeft aan dat de aanroep door een lokale deelnemer in de wacht is gezet. Er stroomt geen media tussen het lokale eindpunt en externe deelnemers.
  - `RemoteHold`: geeft aan dat de aanroep door een externe deelnemer in de wacht is gezet. Er stroomt geen media tussen het lokale eindpunt en externe deelnemers.
  - `InLobby`: Geeft aan dat de gebruiker zich in de hoofdgroep van de computer heeft.
  - `Disconnecting`: Overgangstoestand voordat de aanroep naar een status `Disconnected` gaat.
  - `Disconnected`: Status van laatste aanroep. Als de netwerkverbinding is verloren, verandert de status `Disconnected` na twee minuten in .

Ontdek waarom een aanroep is beëindigd door de eigenschap te `callEndReason` controleren:

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Ontdek of de huidige aanroep binnenkomend of uitgaand is door de eigenschap te `direction` inspecteren. Deze retourneert `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Controleer of de huidige microfoon is gedempt. Deze retourneert `Boolean` .

   ```js
   const muted = call.isMuted;
   ```

Als u wilt weten of de stroom voor het delen van schermen vanaf een bepaald eindpunt wordt verzonden, controleert u de `isScreenSharingOn` eigenschap . Deze retourneert `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Controleer actieve videostreams door de verzameling te `localVideoStreams` controleren. Het `LocalVideoStream` retourneert objecten.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```




### <a name="mute-and-unmute"></a>Dempen en dempen dempen

Als u het lokale eindpunt wilt dempen of dempen, kunt u de `mute` `unmute` asynchrone API's en gebruiken:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Lokale video starten en stoppen

Als u een video wilt starten, moet u camera's opsnoemen met behulp van `getCameras` de methode voor het `deviceManager` -object. Maak vervolgens een nieuw exemplaar van `LocalVideoStream` met de gewenste camera en geef het `LocalVideoStream` object door aan de methode `startVideo` :

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Nadat u video hebt verzonden, wordt `LocalVideoStream` er een exemplaar toegevoegd aan de verzameling op een `localVideoStreams` aanroepin exemplaar.

```js
call.localVideoStreams[0] === localVideoStream;
```

Als u de lokale video wilt stoppen, geef dan het `localVideoStream` exemplaar door dat beschikbaar is in de `localVideoStreams` verzameling:

```js
await call.stopVideo(localVideoStream);
```

U kunt overschakelen naar een ander cameraapparaat terwijl een video wordt verzonden door een exemplaar `switchSource` aan `localVideoStream` teroepen:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Externe deelnemers beheren

Alle externe deelnemers worden vertegenwoordigd door `RemoteParticipant` type en beschikbaar via verzameling op een `remoteParticipants` oproepin exemplaar.

### <a name="list-the-participants-in-a-call"></a>De deelnemers in een oproep op een lijst zetten

De `remoteParticipants` verzameling retourneert een lijst met externe deelnemers in een oproep:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Eigenschappen van externe deelnemer openen

Externe deelnemers hebben een set gekoppelde eigenschappen en verzamelingen:

- `CommunicationIdentifier`: Haal de id voor een externe deelnemer op. Identiteit is een van de `CommunicationIdentifier` typen:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Dit kan een van de volgende typen `CommunicationIdentifier` zijn:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Object dat de ACS-gebruiker vertegenwoordigt.
  - `{ phoneNumber: '<E.164>' }`: Object dat het telefoonnummer in E.164-indeling vertegenwoordigt.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Object dat de Teams-gebruiker vertegenwoordigt.
  - `{ id: string }`: object repredenting identifier that doesn't fit any of the other identifier types

- `state`: De status van een externe deelnemer op te halen.

  ```js
  const state = remoteParticipant.state;
  ```

  De status kan zijn:

  - `Idle`: Initiële status.
  - `Connecting`: Overgangstoestand terwijl een deelnemer verbinding maakt met de aanroep.
  - `Ringing`: Deelnemer is aan het belken.
  - `Connected`: Deelnemer is verbonden met de aanroep.
  - `Hold`: Deelnemer is in de wacht.
  - `EarlyMedia`: Aankondiging die wordt afgespeeld voordat een deelnemer verbinding maakt met de oproep.
  - `InLobby`: Geeft aan dat de externe deelnemer zich in de vs.
  - `Disconnected`: Eindtoestand. De verbinding van de deelnemer met de aanroep is verbroken. Als de externe deelnemer de netwerkverbinding verliest, verandert de status `Disconnected` na twee minuten in .

- `callEndReason`: Als u wilt weten waarom een deelnemer de aanroep heeft verlaten, controleert u de `callEndReason` eigenschap :

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` status: Als u wilt weten of een externe deelnemer gedempt is, controleert u de `isMuted` eigenschap . Deze retourneert `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` status: Als u wilt weten of een externe deelnemer spreekt, controleert u de `isSpeaking` eigenschap . Deze retourneert `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Als u alle videostreams wilt inspecteren die een bepaalde deelnemer in deze aanroep verstuurt, controleert u de `videoStreams` verzameling. Het bevat `RemoteVideoStream` objecten.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: Als u de weergavenaam voor deze externe deelnemer wilt op halen, inspecteert u `displayName` de eigenschap die de tekenreeks retourneert. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Een deelnemer toevoegen aan een oproep

Als u een deelnemer (een gebruiker of een telefoonnummer) wilt toevoegen aan een oproep, kunt u `addParticipant` gebruiken. Geef een van de typen `Identifier` op. Het retourneert het exemplaar `remoteParticipant` synchroon. De `remoteParticipantsUpdated` gebeurtenis van Aanroep wordt verhoogd wanneer een deelnemer is toegevoegd aan de aanroep.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Een deelnemer uit een oproep verwijderen

Als u een deelnemer (een gebruiker of een telefoonnummer) uit een gesprek wilt verwijderen, kunt u `removeParticipant` aanroepen. U moet een van de typen `Identifier` doorgeven. Dit wordt asynchroon opgelost nadat de deelnemer uit de aanroep is verwijderd. De deelnemer wordt ook verwijderd uit de `remoteParticipants` verzameling.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Externe deelnemer-videostreams renderen

Als u de videostreams en stromen voor het delen van schermen van externe deelnemers wilt bekijken, inspecteert u de `videoStreams` verzamelingen:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Als u `RemoteVideoStream` wilt renderen, moet u zich abonneren op de gebeurtenis van de `isAvailableChanged` gebeurtenis. Als de `isAvailable` eigenschap wordt gewijzigd in , stuurt een externe deelnemer een `true` stroom. Daarna maakt u een nieuw exemplaar van en maakt u vervolgens een nieuw exemplaar `VideoStreamRenderer` met behulp van de `VideoStreamRendererView` asynchrone `createView` methode.  U kunt vervolgens koppelen `view.target` aan elk UI-element.

Wanneer de beschikbaarheid van een externe stroom verandert, kunt u ervoor kiezen om de hele , een specifieke stroom te vernietigen of te behouden, maar dit resulteert in het weergeven `VideoStreamRenderer` `VideoStreamRendererView` van een leeg videoframe.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Eigenschappen van externe videostream

Externe videostreams hebben de volgende eigenschappen:

- `id`: De id van een externe videostream.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: Kan of `Video` `ScreenSharing` zijn.

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Of een eindpunt van een externe deelnemer actief een stroom verstuurt.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Methoden en eigenschappen van VideoStreamRenderer
Maak een exemplaar dat kan worden gekoppeld in de gebruikersinterface van de toepassing om de externe videostream weer te geven, gebruik `VideoStreamRendererView` een asynchrone methode. Deze wordt opgelost wanneer de stroom gereed is om weer te geven en retourneert een object met een eigenschap die het element vertegenwoordigt dat overal in de `createView()` `target` `video` DOM-structuur kan worden toegevoegd

  ```js
  videoStreamRenderer.createView()
  ```

Verwijdering van `videoStreamRenderer` en alle `VideoStreamRendererView` bijbehorende exemplaren:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>Methoden en eigenschappen van VideoStreamRendererView

Wanneer u een `VideoStreamRendererView` maakt, kunt u de eigenschappen `scalingMode` en `isMirrored` opgeven. `scalingMode` kan `Stretch` , `Crop` of `Fit` zijn. Als `isMirrored` is opgegeven, wordt de weergegeven stroom verticaal gespiegeld.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Elk `VideoStreamRendererView` exemplaar heeft een eigenschap die het `target` renderingoppervlak vertegenwoordigt. Voeg deze eigenschap toe in de gebruikersinterface van de toepassing:

```js
htmlElement.appendChild(view.target);
```

U kunt bijwerken `scalingMode` door de methode aan `updateScalingMode` teroepen:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Apparaatbeheer

In `deviceManager` kunt u lokale apparaten opsnoemen die uw audio- en videostreams in een aanroep kunnen verzenden. U kunt deze ook gebruiken om machtigingen aan te vragen voor toegang tot de microfoons en camera's van het lokale apparaat.

U kunt toegang krijgen `deviceManager` door de methode aan te `callClient.getDeviceManager()` roepen:

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Lokale apparaten op halen

Voor toegang tot lokale apparaten kunt u op - enumeratiemethoden `deviceManager` gebruiken. Enumeratie is een asynchrone actie

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>De standaardmicrofoon en -spreker instellen

In kunt u een standaardapparaat instellen dat u gebruikt om `deviceManager` een aanroep te starten. Als de standaardinstellingen van de client niet zijn ingesteld, Communication Services standaardinstellingen van het besturingssysteem gebruikt.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Voorbeeld van lokale camera

U kunt en `deviceManager` gebruiken om stromen te `VideoStreamRenderer` renderen vanaf uw lokale camera. Deze stroom wordt niet verzonden naar andere deelnemers; het is een lokale preview-feed.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Machtiging voor camera en microfoon aanvragen

Vraag een gebruiker om camera- en microfoonmachtigingen te verlenen:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Dit wordt opgelost met een -object dat aangeeft of `audio` en `video` machtigingen zijn verleend:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Aanroepen opnemen
> [!NOTE]
> Deze API is beschikbaar als preview-versie voor ontwikkelaars en wordt mogelijk gewijzigd op basis van de feedback die we ontvangen. Gebruik deze API niet in een productie-omgeving. Als u deze API wilt gebruiken, gebruikt u de bètaversie van ACS Calling Web SDK

Oproepopname is een uitgebreide functie van de `Call` kern-API. U moet eerst het API-object voor de opnamefunctie verkrijgen:

```js
const callRecordingApi = call.api(Features.Recording);
```

Controleer vervolgens of de aanroep wordt vastgelegd door de eigenschap `isRecordingActive` van te `callRecordingApi` controleren. Deze retourneert `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

U kunt zich ook abonneren op het vastleggen van wijzigingen:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="transfer-calls"></a>Aanroepen overdragen
> [!NOTE]
> Deze API is beschikbaar als preview-versie voor ontwikkelaars en wordt mogelijk gewijzigd op basis van de feedback die we ontvangen. Gebruik deze API niet in een productie-omgeving. Als u deze API wilt gebruiken, gebruikt u de bètaversie van ACS Calling Web SDK

Overdracht van aanroepen is een uitgebreide functie van de `Call` kern-API. U moet eerst het API-object voor de overdrachtsfunctie op halen:

```js
const callTransferApi = call.api(Features.Transfer);
```

Bij aanroepoverdrachten zijn drie partijen betrokken:

- *Transferor:* de persoon die de overdrachtsaanvraag initieert.
- *Overdracht:* de persoon die wordt overgedragen.
- *Overdrachtsdoel:* de persoon naar wie wordt overgedragen.

Volg deze stappen voor overdrachten:

1. Er is al een verbonden aanroep tussen de *overdrachtsor* en *de overdrachts-*. De *overdrachtsor* besluit de aanroep van de *overdrachtser over te* dragen naar het *overdrachtsdoel*.
1. De *overdrachtsor* roept de `transfer` API aan.
1. De *overdrachtsinschrijvingsinschrijving* bepaalt met behulp van een gebeurtenis of de overdrachtsaanvraag naar het `accept` `reject` *overdrachtsdoel.* `transferRequested`
1. Het *overdrachtsdoel* ontvangt alleen een binnenkomende aanroep *als de overdrachtsinschrijving* de overdrachtsaanvraag accepteert.

Als u een huidige aanroep wilt overdragen, kunt u de `transfer` API gebruiken. `transfer` neemt de `transferCallOptions` optionele , waarmee u een vlag kunt `disableForwardingAndUnanswered` instellen:

- `disableForwardingAndUnanswered = false`: Als het *overdrachtsdoel* de overdrachtsaanroep niet  beantwoordt, volgt de overdracht de doorsturen van het overdrachtsdoel en niet-beantwoorde instellingen.
- `disableForwardingAndUnanswered = true`: Als het *overdrachtsdoel* de overdrachtsoproep niet beantwoordt, wordt de overdrachtspoging beëindigd.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Met `transfer` de API kunt u zich abonneren op gebeurtenissen en `transferStateChanged` `transferRequested` . Een `transferRequested` gebeurtenis is afkomstig van een `call` instantie; een gebeurtenis en overdracht `transferStateChanged` en afkomstig van een `state` `error` `transfer` instantie.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

De *overdrachtshouder* kan de overdrachtsaanvraag accepteren of afwijzen die in de gebeurtenis door de *overdrachtsor* is geïnitieerd met `transferRequested` of in `accept()` `reject()` `transferRequestedEventArgs` . U kunt toegang `targetParticipant` krijgen tot informatie en of methoden in `accept` `reject` `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>Meer informatie over gebeurtenismodellen

Inspecteer huidige waarden en abonneer u op het bijwerken van gebeurtenissen voor toekomstige waarden.

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
