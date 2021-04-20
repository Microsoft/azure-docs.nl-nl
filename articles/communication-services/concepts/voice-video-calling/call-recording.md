---
title: Azure Communication Services-opname van aanroepen
titleSuffix: An Azure Communication Services concept document
description: Biedt een overzicht van de functie voor het opnemen van aanroepen en API's.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730580"
---
# <a name="calling-recording-overview"></a>Overzicht van het aanroepen van opname

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> Veel landen en staten hebben wetten en voorschriften die van toepassing zijn op het opnemen van PSTN-, spraak- en videooproepen, die vaak vereisen dat gebruikers toestemming geven voor de opname van hun communicatie. Het is uw verantwoordelijkheid om de mogelijkheden voor het opnemen van gesprekken te gebruiken in overeenstemming met de wet. U moet toestemming verkrijgen van de partijen van vastgelegde communicatie op een manier die voldoet aan de wetten die van toepassing zijn op elke deelnemer.

> [!NOTE]
> Regelgeving met het onderhoud van persoonsgegevens vereist de mogelijkheid om gebruikersgegevens te exporteren. Ter ondersteuning van deze vereisten bevat het vastleggen van metagegevensbestanden de participantId voor elke deelnemer aanroep in de `participants` matrix. U kunt kruislings verwijzen naar de MPI's in de matrix met uw interne gebruikersidentiteiten om `participants` deelnemers in een oproep te identificeren. Hieronder vindt u ter referentie een voorbeeld van een bestand met opnamemetagegevens.

Opname aanroepen biedt een set API's voor het starten, stoppen, onderbreken en hervatten van de opname. Deze API's zijn toegankelijk vanuit bedrijfslogica aan de serverzijde of via gebeurtenissen die worden geactiveerd door gebruikersacties. De uitvoer van opgenomen media heeft dezelfde indeling als Teams voor het `MP4 Audio+Video` opnemen van media. Meldingen met betrekking tot media en metagegevens worden via een Event Grid. Opnamen worden 48 uur opgeslagen in ingebouwde tijdelijke opslag voor het ophalen en verplaatsen naar een opslagoplossing naar keuze voor de lange termijn. 

## <a name="run-time-control-apis"></a>Run time Control-API's
Run-time controle-API's kunnen worden gebruikt voor het beheren van opname via interne bedrijfslogicatriggers, zoals een toepassing die een groepsoproep maakt en het gesprek vastneemt, of van een door de gebruiker geactiveerde actie die de servertoepassing vertelt dat de opname moet worden gestart. In beide `<conversation-id>` scenario's is vereist voor het opnemen van een specifieke vergadering of oproep. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Gespreks-id van een door de server geïnitieerde aanroep op te vragen

Een `ConversationId` wordt geretourneerd via de gebeurtenis `Microsoft.Communication.CallLegStateChanged` . Deze gebeurtenismelding wordt uitgezonden nadat een aanroep tot stand is gebracht. U vindt deze in het `data.ConversationId` veld . Deze waarde kan rechtstreeks worden gebruikt als `{conversationId}` de parameter in run time control-API's:
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>De gespreks-id van een door de gebruiker geactiveerde gebeurtenis op de client verkrijgen

Vanuit de JavaScript-bibliotheek roept u aan nadat u een aanroep hebt gemaakt om de op te halen. Vervolgens codeert Base64Url de om de op te halen voor gebruik in de `@azure/communication-calling` `let result = call.info.getConversationUrl()` `conversationUrl` **`conversationUrl` `{conversationId}` run-time besturingselement-API's.** Encoding kan worden uitgevoerd op de client voordat de gebeurtenis naar de server of serverzijde wordt verzonden.

Houd er rekening mee dat base64Url moet zijn gecodeerd, niet om te worden verward met alleen `conversationUrl`  Base64-codering (dat wil zeggen btoa).                                                            

### <a name="start-recording"></a>De opname starten

#### <a name="request"></a>Aanvraag

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Antwoord

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Opnametoestand van aanroepen krijgen

#### <a name="request"></a>Aanvraag

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Antwoord

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>De opname stoppen
#### <a name="request"></a>Aanvraag
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Antwoord
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Opname onderbreken
Als u de opname van aanroepen onderneemt en hervat, kunt u het opnemen van een deel van een gesprek of vergadering overslaan en de opname hervatten in één bestand. 
#### <a name="request"></a>Aanvraag
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Antwoord
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Opname hervatten
#### <a name="request"></a>Aanvraag
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Antwoord
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Media-uitvoertypen
Oproepopname ondersteunt momenteel mp4-uitvoerindeling voor gemengde audio en video. De uitvoermedia komen overeen met opnamen die zijn geproduceerd via Microsoft Teams-opname.

| Kanaaltype | Inhoudsindeling | Video | Audio |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | Video 1920x1080 8 FPS van alle deelnemers in standaardtegelindeling | 16 kHz mp4a gemengde audio van alle deelnemers |

## <a name="event-grid-notifications"></a>Event Grid meldingen
Een Event Grid wordt gepubliceerd wanneer een opname gereed is voor ophalen, meestal 1-2 minuten nadat het opnameproces is voltooid (bijvoorbeeld de vergadering is beëindigd, de opname is `Microsoft.Communication.RecordingFileStatusUpdated` gestopt). Opnamegebeurtenismeldingen bevatten een document-id, die kan worden gebruikt om zowel opgenomen media als een metagegevensbestand voor de opname op te halen:

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Voorbeeldcode voor het verwerken van Event Grid-meldingen en het downloaden van opname- en metagegevensbestanden vindt u [hier](../../quickstarts/voice-video-calling/download-recording-file-sample.md). 

### <a name="notification-schema"></a>Meldingsschema
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>Bestand downloaden

> Azure Communication Services korte termijn mediaopslag voor opnamen. **Exporteert opgenomen inhoud die u binnen 48 uur wilt behouden.** Na 48 uur zijn er geen opnamen meer beschikbaar.

### <a name="download-recording"></a>Opname downloaden
#### <a name="request"></a>Aanvraag
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Antwoord
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Metagegevens van opname downloaden
#### <a name="request"></a>Aanvraag
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Antwoord
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
