---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 40d9f03526e5232c0a7b33f64ff35a8501702609
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107719"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een geïmplementeerde Communication Services-resource. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een `User Access Token` om de aanroepende client in te schakelen. Voor meer informatie over het [verkrijgen van een `User Access Token`](../../access-tokens.md)
- Optioneel: Voltooi de Snelstartgids om aan de [slag te gaan met het toevoegen van een oproep aan uw toepassing](../getting-started-with-calling.md)

## <a name="setting-up"></a>Instellen

### <a name="install-the-package"></a>Het pakket installeren

> [!NOTE]
> In dit document wordt versie 1.0.0-Beta. 8 van de aanroepende SDK gebruikt.

Zoek de build.gradle op projectniveau op en vergeet niet `mavenCentral()` toe te voegen aan de lijst met opslagplaatsen onder `buildscript` en `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Voeg vervolgens in het module niveau build. gradle de volgende regels toe aan de sectie Dependencies

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-aanroepende SDK:

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| De CallClient is het belangrijkste ingangs punt voor de aanroepende SDK.|
| CallAgent | De CallAgent wordt gebruikt om oproepen te starten en te beheren. |
| CommunicationTokenCredential | De CommunicationTokenCredential wordt gebruikt als de token referentie voor het instantiëren van de CallAgent.|
| CommunicationIdentifier | De CommunicationIdentifier wordt gebruikt als een ander type deel nemer dat zou kunnen deel nemen aan een aanroep.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Initialiseer de CallClient, maak een CallAgent en open de DeviceManager

Als u een `CallAgent` exemplaar wilt maken, moet u de methode aanroepen `createCallAgent` voor een `CallClient` exemplaar. Hiermee wordt een `CallAgent` object Instance asynchroon geretourneerd.
De `createCallAgent` methode heeft een `CommunicationUserCredential` als argument, waarmee een [toegangs token](../../access-tokens.md)wordt ingekapseld.
Als u toegang wilt krijgen tot de `DeviceManager` , moet u eerst een callAgent-exemplaar maken. vervolgens kunt u de `CallClient.getDeviceManager` methode gebruiken om de DeviceManager op te halen.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```
Als u een weergave naam voor de oproepende functie wilt instellen, gebruikt u deze alternatieve methode:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Een uitgaande oproep plaatsen en toevoegen aan een groeps oproep

Als u een gesprek wilt maken en starten, moet u de-methode aanroepen `CallAgent.startCall()` en het van de genodigden opgeven `Identifier` .
Als u lid wilt worden van een groeps oproep, moet u de methode aanroepen `CallAgent.join()` en de groupid opgeven. Groeps-Id's moeten de GUID-of UUID-indeling hebben.

Het maken van aanroepen en starten synchroon zijn. Met het gesprek exemplaar kunt u zich abonneren op alle gebeurtenissen in de aanroep.

### <a name="place-a-11-call-to-a-user"></a>Een 1:1-aanroep naar een gebruiker plaatsen
Als u een aanroep naar een andere communicatie Services-gebruiker wilt plaatsen, roept u de- `call` methode aan `callAgent` en geeft u een object met de `communicationUserId` sleutel door.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Een aanroep van 1: n met gebruikers en PSTN plaatsen
> [!WARNING]
> De aanroepen op dit moment is niet beschikbaar

Als u wilt een aanroep van 1: n naar een gebruiker en een PSTN-nummer, moet u het telefoon nummer van de genodigde opgeven.
Uw communicatie service-resource moet worden geconfigureerd om PSTN-aanroepen mogelijk te maken:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Een 1:1-oproep met video camera plaatsen
> [!WARNING]
> Momenteel wordt slechts één uitgaande lokale video stroom ondersteund om een gesprek met video te plaatsen die u nodig hebt om lokale camera's op te sommen met behulp van de `deviceManager` `getCameras` API.
Wanneer u een gewenste camera hebt geselecteerd, gebruikt u deze om een instantie te maken `LocalVideoStream` en door `videoOptions` te geven als een item in de `localVideoStream` matrix naar een `call` methode.
Zodra de oproep verbinding maakt, begint automatisch met het verzenden van een video stroom van de geselecteerde camera naar een andere deel nemer (s).

> [!NOTE]
> Vanwege privacy-problemen wordt video niet gedeeld met de aanroep als deze niet lokaal wordt weer gegeven.
Zie de [voor beeld van een lokale camera](#local-camera-preview) voor meer informatie.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Deelnemen aan een groepsgesprek
Als u een nieuwe groeps oproep wilt starten of lid wilt worden van een doorlopende groeps oproep, moet u de methode ' samen voegen ' aanroepen en een object door geven aan een `groupId` eigenschap. De waarde moet een GUID zijn.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>Een gesprek accepteren
Als u een aanroep wilt accepteren, roept u de methode accept aan voor een aanroep object.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Een gesprek met video camera accepteren op:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

De inkomende oproep kan worden verkregen door zich te abonneren op de `onIncomingCall` gebeurtenis op het `callAgent` object:

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>Pushmeldingen

### <a name="overview"></a>Overzicht
Mobiele push meldingen zijn de pop-upmeldingen die u op mobiele apparaten ziet. We richten zich op de push meldingen van VoIP (Voice-over Internet Protocol). U kunt registreren voor push meldingen, Push meldingen verwerken en vervolgens de registratie van push meldingen ongedaan maken.

### <a name="prerequisites"></a>Vereisten

Een Firebase-account dat is ingesteld met Cloud Messa ging (FCM) ingeschakeld en met uw Firebase Cloud Messa ging-service verbonden met een Azure notification hub-exemplaar. Zie [communicatie Services-meldingen](../../../concepts/notifications.md) voor meer informatie.
Daarnaast wordt door de zelf studie aangenomen dat u Android Studio versie 3,6 of hoger gebruikt om uw toepassing te bouwen.

Er is een set machtigingen vereist voor de Android-toepassing om meldings berichten te kunnen ontvangen van Firebase Cloud Messa ging. Voeg in uw `AndroidManifest.xml` bestand de volgende machtigingen toe na het *manifest<... >* of onder het *</application>* label

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registreren voor push meldingen

Als u zich wilt registreren voor push meldingen, moet de toepassing `registerPushNotification()` een *CallAgent* -exemplaar aanroepen met een apparaat registratie token.

Om het token voor apparaatregistratie te verkrijgen, voegt u de Firebase-SDK toe aan het bestand *Build. gradle* van uw toepassings module door de volgende regels toe te voegen in de `dependencies` sectie als deze nog niet bestaat:

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

Voeg in het bestand *Build. gradle* van het project het volgende toe in de `dependencies` sectie als dit nog niet is gebeurd:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Voeg de volgende invoeg toepassing toe aan het begin van het bestand als dit nog niet is gebeurd:

```
apply plugin: 'com.google.gms.google-services'
```

Selecteer *Nu synchroniseren* in de werk balk. Voeg het volgende code fragment toe om het token voor apparaatregistratie op te halen dat is gegenereerd door de Firebase Cloud Messa ging SDK voor het exemplaar van de client toepassing. Voeg de onderstaande invoer toe aan de koptekst van de hoofd activiteit voor het exemplaar. Ze zijn vereist voor het fragment om het token op te halen:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Voeg dit fragment toe om het token op te halen:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Registreer het token voor apparaatregistratie met de Call Services SDK voor inkomende gesprek push meldingen:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Afhandeling van push meldingen

Als u push meldingen voor inkomende oproepen wilt ontvangen, roept u *handlePushNotification ()* aan voor een *CallAgent* -exemplaar met een payload.

Als u de payload van Firebase Cloud Messa ging wilt verkrijgen, begint u met het maken van een nieuwe service (bestand > nieuwe > service > service) die de *FirebaseMessagingService* Firebase SDK-klasse uitbreidt en de methode overschrijft `onMessageReceived` . Deze methode is de gebeurtenis-handler die wordt aangeroepen wanneer Firebase Cloud Messa ging de push melding naar de toepassing levert.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Voeg de volgende service definitie toe aan het `AndroidManifest.xml` bestand in de- <application> Tag:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- Zodra de payload is opgehaald, kan deze worden door gegeven aan de SDK van de *communicatie Services* om te worden geparseerd naar een intern *IncomingCallInformation* -object dat wordt verwerkt door de *handlePushNotification* -methode aan te roepen voor een *CallAgent* -exemplaar. Er `CallAgent` wordt een exemplaar gemaakt door de `createCallAgent(...)` methode aan te roepen voor de `CallClient` klasse.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Wanneer de verwerking van het push meldings bericht is geslaagd en de handlers voor alle gebeurtenissen correct zijn geregistreerd, wordt de toepassing geringt.

### <a name="unregister-push-notifications"></a>Registratie van push meldingen ongedaan maken

Toepassingen kunnen de registratie van push meldingen op elk gewenst moment ongedaan maken. Roep de `unregisterPushNotification()` methode op callAgent aan om de registratie ongedaan te maken.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Oproep beheer
U hebt toegang tot de oproep eigenschappen en kunt verschillende bewerkingen uitvoeren tijdens een aanroep om instellingen te beheren die betrekking hebben op video en audio.

### <a name="call-properties"></a>Eigenschappen van oproep

De unieke ID voor deze aanroep ophalen:

```java
String callId = call.getId();
```

Voor meer informatie over andere deel nemers in de verzameling aanroep inspectie `remoteParticipant` op het `call` exemplaar:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

De identiteit van de aanroeper als de aanroep inkomend is:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

De status van de aanroep ophalen: 

```java
CallState callState = call.getState();
```

Er wordt een teken reeks geretourneerd die de huidige status van een aanroep vertegenwoordigt:
* Geen '-initiële gespreks status
* ' Verbinding maken '-initiële overgangs status zodra de aanroep is geplaatst of geaccepteerd
* ' Rinkelend ': voor een uitgaande oproep wordt aangegeven dat de aanroep wordt opgeroepen voor externe deel nemers
* ' EarlyMedia ': geeft een status aan waarin een aankondiging wordt afgespeeld voordat de aanroep is verbonden
* Verbonden: de oproep is verbonden
* ' LocalHold ': de aanroep wordt door de lokale deel nemer in de wacht geplaatst, geen medium loopt tussen het lokale eind punt en de externe deel nemer (s)
* ' RemoteHold ': de aanroep wordt door een externe deel nemer in de wacht geplaatst. er wordt geen medium stromen tussen het lokale eind punt en de externe deel nemer (s)
* ' Verbinding verbreken van '-de status van de overgang voor de aanroep is verbroken
* ' Verbinding verbroken '-laatste gespreks status


Inspecteer de eigenschap om te zien waarom een aanroep is beëindigd `callEndReason` . Het bevat code/subcode: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Inspecteer de eigenschap om te zien of de huidige aanroep een binnenkomende of uitgaande oproep is `callDirection` :

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.Incoming for incoming call
// callDirection == CallDirection.Outgoing for outgoing call
```

Als u wilt zien of de huidige microfoon is gedempt, inspecteert u de `muted` eigenschap:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Als u wilt zien of de huidige aanroep wordt vastgelegd, inspecteert u de `isRecordingActive` eigenschap:

```java
boolean recordinggActive = call.getIsRecordingActive();
```

Als u actieve video stromen wilt controleren, controleert u de `localVideoStreams` verzameling:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Dempen en dempen opheffen

U kunt de `mute` en asynchrone api's gebruiken om het lokale eind punt te dempen of te dempen `unmute` :

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Verzenden van lokale video starten en stoppen

Als u een video wilt starten, moet u camera's opsommen met behulp `getCameraList` van de API voor het `deviceManager` object. Maak vervolgens een nieuw exemplaar van `LocalVideoStream` het door geven van de gewenste camera en geef deze door `startVideo` als een argument in de API:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(currentLocalVideoStream);
startVideoFuture.get();
```

Zodra u de video hebt verzonden, `LocalVideoStream` wordt een exemplaar toegevoegd aan de `localVideoStreams` verzameling op het aanroep exemplaar.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

Als u lokale video wilt stoppen, geeft u het `LocalVideoStream` exemplaar dat beschikbaar is in de `localVideoStreams` verzameling door.

```java
call.stopVideo(currentLocalVideoStream).get();
```

U kunt overschakelen naar een ander camera apparaat terwijl de video wordt verzonden door aan te roepen `switchSource` op een `LocalVideoStream` exemplaar:
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Beheer van externe deel nemers

Alle externe deel nemers worden weer gegeven op `RemoteParticipant` type en zijn beschikbaar via de `remoteParticipants` verzameling op een aanroep exemplaar.

### <a name="list-participants-in-a-call"></a>Deel nemers in een gesprek weer geven
De `remoteParticipants` verzameling retourneert een lijst met externe deel nemers in de gegeven aanroep:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Eigenschappen van externe deel nemer
Aan alle gegeven externe deel nemers is een set eigenschappen en verzamelingen gekoppeld:

* De id voor deze externe deel nemer ophalen.
Identiteit is een van de typen ' identifier '
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* De status van deze externe deel nemer ophalen.
```java
ParticipantState state = remoteParticipant.getState();
```
Status kan een van
* ' Inactief '-initiële status
* ' EarlyMedia ': de aankondiging wordt afgespeeld voordat de deel nemer is verbonden met de oproep
* ' Belsignaal ': deel nemer oproept ring
* ' Verbinding maken '-transitie status terwijl deel nemer verbinding maakt met de aanroep
* Verbonden: de deel nemer is verbonden met de oproep
* Hold-deel nemer is in de wacht stand
* ' Inlobby ': de deel nemer wacht op de lobby om te worden toegelaten. Momenteel alleen gebruikt in teams Interop-scenario
* ' Verbinding verbroken ': eind status-de deel nemer is losgekoppeld van de aanroep


* Als u wilt weten waarom een deel nemer de oproep heeft verlaten, inspecteert u de `callEndReason` eigenschap:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Als u wilt controleren of deze externe deel nemer is gedempt of niet, inspecteert u de `isMuted` eigenschap:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Als u wilt controleren of deze externe deel nemer spreekt of niet, inspecteert u de `isSpeaking` eigenschap:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Als u alle video stromen wilt controleren die een bepaalde deel nemer in deze oproep verzendt, controleert u de `videoStreams` verzameling:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Een deel nemer aan een gesprek toevoegen

Als u een deel nemer wilt toevoegen aan een aanroep (een gebruiker of een telefoon nummer), kunt u deze aanroepen `addParticipant` . Hiermee wordt het exemplaar van de externe deel nemer synchroon geretourneerd.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Deel nemer uit een gesprek verwijderen
Als u een deel nemer wilt verwijderen uit een aanroep (een gebruiker of een telefoon nummer), kunt u deze aanroepen `removeParticipant` .
Dit wordt asynchroon opgelost zodra de deel nemer is verwijderd uit de aanroep.
De deel nemer wordt ook uit de `remoteParticipants` verzameling verwijderd.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>Video stromen voor externe deel nemers weer geven
Als u de video stromen en de streams voor het delen van externe deel nemers wilt weer geven, inspecteert u de `videoStreams` verzamelingen:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Als u een `RemoteVideoStream` van een externe deel nemer wilt renderen, moet u zich abonneren op een `OnVideoStreamsUpdated` gebeurtenis.

In het geval geeft de eigenschap wijzigen `isAvailable` in waar aan dat de deel nemer momenteel een stroom verzendt. Als dit het geval is, maakt u een nieuw exemplaar van a en `Renderer` vervolgens maakt u een nieuwe sessie `RendererView` met asynchrone `createView` API en koppelt u deze `view.target` overal in de gebruikers interface van uw toepassing.

Wanneer de beschik baarheid van een externe stroom verandert, kunt u ervoor kiezen om de volledige renderer te vernietigen, een specifieke `RendererView` of te blijven gebruiken, maar dit resulteert in het weer geven van een leeg video frame.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Eigenschappen van externe video-stream
De externe video stroom heeft een aantal eigenschappen

* `Id` -ID van een externe video stroom
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -Kan ' video ' of ' ScreenSharing ' zijn
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -Geeft aan of het eind punt van de externe deel nemer actief stroom verzendt
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Methoden en eigenschappen van renderer
Renderer-object na Api's

* Maak een `RendererView` exemplaar dat later kan worden gekoppeld in de gebruikers interface van de toepassing om externe video stroom weer te geven.
```java
// Create a view for a video stream
renderer.createView()
```
* De renderer en alle `RendererView` aan deze renderer verwijderen. Moet worden aangeroepen wanneer u alle gekoppelde weer gaven uit de gebruikers interface hebt verwijderd.
```java
renderer.dispose()
```

* `StreamSize` -grootte (breedte/hoogte) van een externe video stroom
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererView methoden en eigenschappen
Bij het maken van een `RendererView` kunt u `scalingMode` de `mirrored` Eigenschappen en opgeven die van toepassing zijn op deze weer gave: de schaal modus kan de waarde stretch hebben | ' Bijsnijden ' | ' Fit ' als `mirrored` is ingesteld op `true` , wordt de gerenderde stroom verticaal gespiegeld.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

De gemaakte RendererView kan vervolgens worden gekoppeld aan de gebruikers interface van de toepassing met behulp van het volgende code fragment:
```java
layout.addView(rendererView);
```

U kunt de schaal modus later bijwerken door de API aan te roepen `updateScalingMode` voor het object RendererView met een van ScalingMode. Stretch | ScalingMode. gewas | ScalingMode. fit als een argument.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Apparaatbeheer

`DeviceManager` Hiermee kunt u lokale apparaten opsommen die kunnen worden gebruikt in een aanroep om uw audio/video-streams te verzenden. U kunt hiermee ook machtigingen aanvragen van een gebruiker om toegang te krijgen tot hun microfoon en camera met behulp van de systeem eigen browser-API.

U kunt toegang krijgen `deviceManager` door middel van een aanroep `callClient.getDeviceManager()` methode.
> [!WARNING]
> Momenteel `callAgent` moet een object eerst worden geïnstantieerd om toegang te krijgen tot DeviceManager

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Lokale apparaten opsommen

Voor toegang tot lokale apparaten kunt u inventarisatie methoden gebruiken op de Apparaatbeheer. Opsomming is een synchrone actie.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Standaard microfoon/-spreker instellen

Met Apparaatbeheer kunt u een standaard apparaat instellen dat wordt gebruikt bij het starten van een aanroep.
Als de standaard waarden van de client niet zijn ingesteld, worden de standaard instellingen van het besturings systeem hersteld door communicatie Services.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophones().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakers().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Voor beeld van lokale camera

U kunt `DeviceManager` en gebruiken `Renderer` om streams van uw lokale camera te renderen. Deze stroom wordt niet naar andere deel nemers verzonden. het is een lokale preview-feed. Dit is een asynchrone actie.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Gebeurtenis model
U kunt zich abonneren op de meeste eigenschappen en verzamelingen om te worden gewaarschuwd wanneer waarden worden gewijzigd.

### <a name="properties"></a>Eigenschappen
Abonneren op `property changed` gebeurtenissen:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Verzamelingen
Abonneren op `collection updated` gebeurtenissen:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
