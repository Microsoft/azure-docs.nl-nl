---
title: 'Quick Start: video toevoegen aan uw app (Java script)'
titleSuffix: An Azure Communication Services quickstart
description: In deze Quick Start leert u hoe u video gesprekken kunt toevoegen aan uw app met behulp van Azure Communication Services.
author: xumo-95
ms.author: mikben
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 1d5bd8179d07477d8ae0cf60d4de291ed0e00201
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201081"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>Snelstartgids: 1:1 video toevoegen aan uw app (Java script)

## <a name="download-code"></a>Download code

De voltooide code voor deze Quick Start vinden op [github](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling)

## <a name="prerequisites"></a>Vereisten
- Een Azure-account verkrijgen met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/en/) Actieve LTS en onderhoud LTS-versies (8.11.1 en 10.14.1)
- Maak een actieve communicatie Services-resource. [Een Communication Services-resource maken](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp).
- Maak een token voor gebruikers toegang om de aanroep-client te instantiëren. [Meer informatie over het maken en beheren van tokens voor gebruikers toegang](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-csharp).

## <a name="setting-up"></a>Instellen
### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken
Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>Het pakket installeren
Gebruik de opdracht `npm install` voor het installeren van de clientbibliotheek voor oproepen voor Javascript in Azure Communication Services.

Deze Quick Start gebruikte Azure-communicatie die client bibliotheek aanroept `1.0.0.beta-6` . 

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In deze snelstart wordt webpack gebruikt om de toepassingsassets te bundelen. Voer de volgende opdracht uit om de `webpack` NPM-pakketten te installeren `webpack-cli` `webpack-dev-server` en deze weer te geven als ontwikkelings afhankelijkheden in uw `package.json` :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Maak een `index.html` bestand in de hoofdmap van uw project. Dit bestand wordt gebruikt voor het configureren van een basis indeling waarmee de gebruiker een 1:1-video oproep kan plaatsen.

Hier volgt de code:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Maak een bestand in de hoofdmap van uw project met `client.js` de naam zodat de toepassings logica voor deze Quick Start wordt opgenomen. Voeg de volgende code toe om de aanroepende client te importeren en om verwijzingen naar de DOM-elementen op te halen.

```JavaScript
import { CallClient, CallAgent, Renderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-clientbibliotheek voor aanroepen:

| Naam      | Beschrijving | 
| :---        |    :----   |
| CallClient  | De CallClient is het belangrijkste ingangspunt voor de clientbibliotheek voor oproepen.      |
| CallAgent  | De CallAgent wordt gebruikt om oproepen te starten en te beheren.        |
| DeviceManager | De DeviceManager wordt gebruikt voor het beheren van media apparaten.    |
| AzureCommunicationTokenCredential | De klasse AzureCommunicationTokenCredential implementeert de CommunicationTokenCredential-interface die wordt gebruikt om de CallAgent te instantiëren.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>De client-en toegangs DeviceManager verifiëren

U moet <USER_ACCESS_TOKEN> vervangen door een geldig token voor gebruikers toegang voor uw resource. Raadpleeg de documentatie inzake Token voor gebruikerstoegang als u nog geen token hebt. Met behulp van de CallClient initialiseert u een CallAgent-exemplaar met een CommunicationUserCredential, waarmee we aanroepen kunnen maken en ontvangen. Om toegang te krijgen tot de DeviceManager moet u eerst een callAgent-exemplaar maken. U kunt de-methode vervolgens gebruiken voor het `getDeviceManager` `CallClient` exemplaar om de te verkrijgen `DeviceManager` .

Voeg de volgende code toe aan `client.js`:

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Een uitgaande video oproep van 1:1 naar een gebruiker plaatsen

Een gebeurtenislistener toevoegen om een aanroep te initiëren wanneer `callButton` erop wordt geklikt:

Eerst moet u lokale camera's opsommen met behulp van de deviceManager getCameraList-API. In deze Snelstartgids gebruiken we de eerste camera in de verzameling. Zodra de gewenste camera is geselecteerd, wordt een LocalVideoStream-exemplaar geconstrueerd en door gegeven binnen videoOptions als een item binnen de localVideoStream-matrix met de aanroep methode. Zodra uw gesprek verbinding maakt, wordt automatisch een video stroom naar de andere deel nemer verzonden. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
Als u een wilt weer geven `LocalVideoStream` , moet u een nieuw exemplaar van maken `Renderer` en vervolgens een nieuw RendererView-exemplaar maken met behulp van de asynchrone `createView` methode. U kunt vervolgens `view.target` aan elk UI-element worden gekoppeld. 

```JavaScript
async function localVideoView() {
    rendererLocal = new Renderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Alle externe deel nemers zijn beschikbaar via de `remoteParticipants` verzameling op een aanroep exemplaar. U moet zich abonneren op de externe deel nemers van de huidige aanroep en Luis teren naar de gebeurtenis om u te `remoteParticipantsUpdated` Abonneren op toegevoegde externe deel nemers.

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.remoteParticipants.forEach( p => {
        subscribeToRemoteParticipant(p);
    })
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToRemoteParticipant(p);
        })
    });   
}
```
U kunt zich abonneren op de `remoteParticipants` verzameling van de huidige oproep en de `videoStreams` verzamelingen controleren om de streams van elke deel nemer weer te geven. U moet zich ook abonneren op de remoteParticipantsUpdated-gebeurtenis voor het afhandelen van toegevoegde externe deel nemers. 

```JavaScript
function subscribeToRemoteParticipant(remoteParticipant) {
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
}
```
U moet zich abonneren op een `isAvailableChanged` gebeurtenis om de weer te geven `remoteVideoStream` . Als de `isAvailable` eigenschap wordt gewijzigd in `true` , wordt een stroom door een externe deel nemer verzonden. Wanneer de beschik baarheid van een externe stroom verandert, kunt u ervoor kiezen om het geheel te vernietigen `Renderer` , een specifiek `RendererView` of te blijven, maar dit resulteert in het weer geven van een leeg video frame.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
Als u een wilt weer geven `RemoteVideoStream` , moet u een nieuw exemplaar van maken `Renderer` en vervolgens een nieuw `RendererView` exemplaar maken met behulp van de asynchrone `createView` methode. U kunt vervolgens `view.target` aan elk UI-element worden gekoppeld. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new Renderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Een binnenkomend gesprek ontvangen
Als u binnenkomende oproepen wilt afhandelen, moet u Luis teren naar de `incomingCall` gebeurtenis van `callAgent` . Zodra er een inkomende oproep is, moet u lokale camera's opsommen en een instantie maken `LocalVideoStream` om een video stroom naar de andere deel nemer te verzenden. U moet zich ook abonneren op `remoteParticipants` om externe video-streams af te handelen. U kunt de aanroep via het exemplaar accepteren of afwijzen `incomingCall` . 

Plaats de implementatie in `init()` voor het afhandelen van binnenkomende oproepen. 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);   
});
```
## <a name="end-the-current-call"></a>De huidige oproep beëindigen
Voeg een gebeurtenis-listener toe om de huidige oproep te beëindigen wanneer op de `hangUpButton` wordt geklikt:
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>Abonneren op het aanroepen van updates
U moet zich abonneren op de gebeurtenis wanneer de deel nemer op afstand de aanroep voor het verwijderen van video renderers en wissel knop status beëindigt. 

Plaats de implementatie in init () om u te abonneren op de `callsUpdated` gebeurtenis. 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Video starten en beëindigen tijdens de aanroep
U kunt de video tijdens de huidige oproep stoppen door een gebeurtenislistener toe te voegen aan de knop video stoppen om de renderer van te verwijderen `localVideoStream` . 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
U kunt een gebeurtenislistener toevoegen aan de knop video starten om de video weer in te scha kelen wanneer deze tijdens de huidige oproep werd gestopt. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>De code uitvoeren
Gebruik de `webpack-dev-server` om uw app te bouwen en uit te voeren. Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Open uw browser en ga naar http://localhost:8080/. U ziet nu het volgende:

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1 op een pagina met een video-oproep":::

U kunt een uitgaande video oproep van 1:1 maken door een gebruikers-ID op te geven in het tekst veld en te klikken op de knop oproep starten. 

## <a name="sample-code"></a>Voorbeeldcode
U kunt de voor beeld-app downloaden van [github](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).

## <a name="clean-up-resources"></a>Resources opschonen
Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen
Raadpleeg voor meer informatie de volgende artikelen:
- Bekijk ons [webaanroep voor beeld](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Meer informatie over de [mogelijkheden van de clientbibliotheek voor aanroepen](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Meer informatie over [de werking van aanroepen](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
