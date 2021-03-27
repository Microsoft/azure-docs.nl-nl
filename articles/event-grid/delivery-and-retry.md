---
title: Azure Event Grid levering en probeer het opnieuw
description: Hierin wordt beschreven hoe Azure Event Grid gebeurtenissen levert en hoe er niet-bezorgde berichten worden verwerkt.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e7fa627464ddb85ebded3ae99229b7fe8dd3fde3
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629271"
---
# <a name="event-grid-message-delivery-and-retry"></a>Bericht bezorging Event Grid en probeer het opnieuw

In dit artikel wordt beschreven hoe Azure Event Grid gebeurtenissen afhandelt wanneer de levering niet wordt bevestigd.

Event Grid biedt een duurzame levering. Het levert elk bericht **ten minste één keer** per abonnement. Gebeurtenissen worden direct naar het geregistreerde eind punt van elk abonnement verzonden. Als een eind punt de ontvangst van een gebeurtenis niet bevestigt, Event Grid nieuwe pogingen van de gebeurtenis.

> [!NOTE]
> Event Grid garandeert geen bestelling voor gebeurtenis levering, zodat de abonnee deze niet in de juiste volg orde kan ontvangen. 

## <a name="batched-event-delivery"></a>Levering van batch gebeurtenissen

Event Grid standaard elke gebeurtenis afzonderlijk naar abonnees verzenden. De abonnee ontvangt een matrix met één gebeurtenis. U kunt Event Grid voor de levering van batch gebeurtenissen configureren voor betere HTTP-prestaties in scenario's met hoge door voer.

De batch levering heeft twee instellingen:

* **Max. gebeurtenissen per batch** -maximum aantal gebeurtenissen Event grid per batch worden geleverd. Dit aantal wordt nooit overschreden. er kunnen echter minder gebeurtenissen worden geleverd als er geen andere gebeurtenissen beschikbaar zijn op het moment van publiceren. Event Grid vertraagt geen gebeurtenissen om een batch te maken als er minder gebeurtenissen beschikbaar zijn. Moet tussen 1 en 5.000.
* De **voor Keurs-Batch grootte in kilo bytes** -doel plafond voor Batch grootte in kB. Net als bij de maximale gebeurtenissen kan de Batch grootte kleiner zijn als er meer gebeurtenissen niet beschikbaar zijn op het moment van publiceren. Het is mogelijk dat een batch groter is dan de voorkeurs Batch grootte *als* één gebeurtenis groter is dan de voorkeurs grootte. Als de voorkeurs grootte bijvoorbeeld 4 KB is en een gebeurtenis van 10 KB naar Event Grid wordt gepusht, wordt de gebeurtenis met 10 KB nog steeds in een eigen batch opgenomen, in plaats van dat deze wordt verwijderd.

Batch levering is geconfigureerd op basis van per gebeurtenis abonnement via de portal, CLI, Power shell of Sdk's.

### <a name="azure-portal"></a>Azure Portal: 
![Instellingen voor batch levering](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
Gebruik de volgende para meters bij het maken van een gebeurtenis abonnement: 

- **Max-gebeurtenissen-per batch** -maximum aantal gebeurtenissen in een batch. Moet een getal tussen 1 en 5000 zijn.
- **voor keur-Batch-grootte-in-kilo bytes** -de gewenste Batch grootte in kB. Moet een getal tussen 1 en 1024 zijn.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Voor meer informatie over het gebruik van Azure CLI met Event Grid raadpleegt [u opslag gebeurtenissen naar een webeindpunt routeren met Azure cli](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Schema en duur van opnieuw proberen

Wanneer EventGrid een fout ontvangt voor een gebeurtenis bezorgings poging, bepaalt EventGrid of het een nieuwe levering of onbestelbare letter moet doen of de gebeurtenis wilt verwijderen op basis van het type fout. 

Als de fout die door het geabonneerde eind punt wordt geretourneerd, een configuratie fout is die niet kan worden opgelost met nieuwe pogingen (bijvoorbeeld als het eind punt is verwijderd), voert EventGrid de gebeurtenis onbestelbare berichten uit of verwijdert de gebeurtenis als de onbestelbare letter niet is geconfigureerd.

Hier volgen de typen eind punten waarvoor het nieuwe pogingen niet gebeurt:

| Eindpunttype | Foutcodes |
| --------------| -----------|
| Azure-bronnen | 400 ongeldige aanvraag, 413 aanvraag entiteit te groot, 403 verboden | 
| Webhook | 400 ongeldige aanvraag, 413 aanvraag entiteit te groot, 403 verboden, 404 niet gevonden, 401 niet toegestaan |
 
> [!NOTE]
> Als Dead-Letter niet is geconfigureerd voor het eind punt, worden gebeurtenissen verwijderd wanneer de bovenstaande fouten optreden. Overweeg het configureren van onbestelbare berichten als u niet wilt dat dit soort gebeurtenissen wordt verwijderd.

Als de fout die wordt geretourneerd door het geabonneerde eind punt niet voor komt in de bovenstaande lijst, voert EventGrid de volgende stappen uit die hieronder worden beschreven:

Event Grid 30 seconden wachten op een reactie na het afleveren van een bericht. Als het eind punt 30 seconden niet heeft gereageerd, wordt het bericht in de wachtrij geplaatst voor opnieuw proberen. Event Grid gebruikt een exponentiële uitstel beleid voor opnieuw proberen voor gebeurtenis levering. Event Grid nieuwe pogingen op het volgende schema worden uitgevoerd op basis van de beste inspanningen:

- 10 seconden
- 30 seconden
- 1 minuut
- 5 minuten
- 10 minuten
- 30 minuten
- 1 uur
- 3 uur
- 6 uur
- Elke 12 uur tot 24 uur


Als het eind punt binnen drie minuten reageert, probeert Event Grid de gebeurtenis te verwijderen uit de wachtrij voor nieuwe pogingen, maar zijn er dubbele items mogelijk wel ontvangen.

Event Grid voegt een kleine wille keurige stap toe aan alle stappen voor opnieuw proberen en kan eventueel bepaalde nieuwe pogingen overs Laan als een eind punt zich in de consistente status bevindt, gedurende een lange periode niet actief is of niet wordt overbelast.

Stel voor deterministisch gedrag de gebeurtenis tijd in op Live en Maxi maal bezorgings pogingen in het [beleid voor opnieuw proberen](manage-event-delivery.md)van het abonnement.

Event Grid verloopt standaard alle gebeurtenissen die binnen 24 uur niet worden geleverd. U kunt [het beleid voor opnieuw proberen](manage-event-delivery.md) aan te passen bij het maken van een gebeurtenis abonnement. U geeft het maximum aantal bezorgings pogingen op (standaard 30) en de gebeurtenis time-to-Live (de standaard waarde is 1440 minuten).

## <a name="delayed-delivery"></a>Vertraagde levering

Als een eind punt uitvalt, worden de levering en het opnieuw proberen van gebeurtenissen naar dat eind punt door Event Grid uitgesteld. Als bijvoorbeeld de eerste 10 gebeurtenissen die naar een eind punt zijn gepubliceerd, mislukt, Event Grid ervan uitgaan dat het eind punt problemen ondervindt en alle volgende pogingen *en nieuwe* leveringen gedurende een bepaalde periode tot enkele uren in enkele gevallen vertragen.

Het functionele doel van een vertraagde levering is het beveiligen van beschadigde eind punten en het Event Grid systeem. Als er geen back-up wordt uitgevoerd en er wordt uitgegaan van de levering aan de beschadigde eind punten, kan het beleid voor opnieuw proberen van Event Grid en kunnen de volume mogelijkheden eenvoudig een systeem overbelasten.

## <a name="dead-letter-events"></a>Onbestelbare gebeurtenissen
Wanneer Event Grid een gebeurtenis binnen een bepaalde periode niet kunt afleveren of nadat de gebeurtenis een bepaald aantal keer is geprobeerd, kan de gebeurtenis worden verzonden naar een opslag account. Dit proces wordt **onbestelbare berichten** genoemd. Event Grid onbestelbare berichten een gebeurtenis wanneer aan **een van de volgende** voor waarden wordt voldaan. 

- De gebeurtenis wordt niet binnen de **time-to-Live-** periode bezorgd. 
- Het **aantal pogingen** om de gebeurtenis te leveren heeft de limiet overschreden.

Als aan een van de voor waarden wordt voldaan, wordt de gebeurtenis verwijderd of onbestelbaar.  Standaard wordt door Event Grid geen onbestelbare berichten ingeschakeld. Als u deze functie wilt inschakelen, moet u een opslag account opgeven om niet-bezorgde gebeurtenissen te bewaren tijdens het maken van het gebeurtenis abonnement. U haalt gebeurtenissen uit dit opslag account op om leveringen op te lossen.

Event Grid verzendt een gebeurtenis naar de locatie van de onbestelbare berichten wanneer deze alle nieuwe pogingen heeft geprobeerd uit te voeren. Als Event Grid een respons code van 400 (ongeldige aanvraag) of 413 (te grote aanvraag entiteit) ontvangt, wordt de gebeurtenis direct gepland voor onbestelbare berichten. Met deze antwoord codes wordt aangegeven dat de levering van de gebeurtenis nooit slaagt.

De time-to-Live-verval datum wordt alleen gecontroleerd bij de volgende geplande leverings poging. , Zelfs als time-to-Live verloopt vóór de volgende geplande leverings poging, wordt gebeurtenis verloop alleen gecontroleerd op het tijdstip van de volgende levering en vervolgens onbestelbare berichten. 

Er is een vertraging van vijf minuten tussen de laatste poging om een gebeurtenis te leveren en wanneer deze wordt geleverd aan de locatie van de onbestelbare berichten. Deze vertraging is bedoeld om het aantal Blob Storage-bewerkingen te verminderen. Als de locatie voor onbestelbare berichten vier uur niet beschikbaar is, wordt de gebeurtenis verwijderd.

Voordat u de locatie van de onbestelbare letter instelt, moet u een opslag account hebben met een container. U geeft het eind punt voor deze container op bij het maken van het gebeurtenis abonnement. Het eind punt heeft de volgende indeling: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Mogelijk wilt u een melding ontvangen wanneer een gebeurtenis naar de locatie van de onbestelbare brief is verzonden. Als u Event Grid wilt gebruiken om te reageren op niet-bezorgde gebeurtenissen, [maakt u een gebeurtenis abonnement](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) voor de onbestelbare Blob-opslag. Telkens wanneer uw dead-letter-Blob-opslag een niet-bezorgd gebeurtenis ontvangt, wordt Event Grid een melding verzonden naar uw handler. De handler reageert met de acties die u wilt uitvoeren voor het afstemmen van niet-bezorgde gebeurtenissen. Zie [Dead-letter en beleid voor opnieuw proberen](manage-event-delivery.md)voor een voor beeld van het instellen van een niet-actieve locatie en het beleid voor opnieuw proberen.

## <a name="delivery-event-formats"></a>Bezorgings gebeurtenis indelingen
In deze sectie vindt u voor beelden van gebeurtenissen en gebeurtenissen met onbestelbare berichten in verschillende indelingen voor afleverings schema's (Event Grid schema, CloudEvents 1,0-schema en aangepast schema). Zie [Event grid](event-schema.md) schema-en [Cloud evenementen 1,0-schema](cloud-event-schema.md) artikelen voor meer informatie over deze indelingen. 

### <a name="event-grid-schema"></a>Event Grid-schema

#### <a name="event"></a>Gebeurtenis 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Onbestelbare gebeurtenis

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1,0-schema

#### <a name="event"></a>Gebeurtenis

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Onbestelbare gebeurtenis

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Aangepast schema

#### <a name="event"></a>Gebeurtenis

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Onbestelbare gebeurtenis
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Leverings status van bericht

Event Grid maakt gebruik van HTTP-antwoord codes om de ontvangst van gebeurtenissen te bevestigen. 

### <a name="success-codes"></a>Geslaagde codes

Event Grid beschouwt **alleen** de volgende HTTP-antwoord codes als geslaagde leveringen. Alle andere status codes worden beschouwd als mislukte leveringen en worden indien nodig opnieuw geprobeerd of deadlettered. Wanneer de status code is ontvangen, wordt de levering van Event Grid beschouwd als voltooid.

- 200 OK
- 201 gemaakt
- 202 geaccepteerd
- 203 niet-bindende informatie
- 204 geen inhoud

### <a name="failure-codes"></a>Fout codes

Alle andere codes die zich niet in de bovenstaande set (200-204) bevinden, worden beschouwd als storingen en worden opnieuw geprobeerd (indien nodig). Enkele van de specifieke beleids regels voor opnieuw proberen die hieronder worden beschreven, volgen alle andere voor beelden van het standaard exponentiële uitstel model. Het is belang rijk dat u er rekening mee houdt dat het gedrag van de nieuwe poging niet-deterministisch is als gevolg van de zeer parallelle aard van de architectuur van Event Grid. 

| Statuscode | Gedrag voor opnieuw proberen |
| ------------|----------------|
| 400 Ongeldige aanvraag | Niet opnieuw geprobeerd |
| 401 Onbevoegd | Opnieuw proberen na 5 minuten of langer voor Azure-resources-eind punten |
| 403 Verboden | Niet opnieuw geprobeerd |
| 404 Niet gevonden | Opnieuw proberen na 5 minuten of langer voor Azure-resources-eind punten |
| 408 Time-out van aanvraag | Opnieuw proberen na twee minuten of langer |
| de 413-aanvraag entiteit is te groot | Niet opnieuw geprobeerd |
| 503 Service niet beschikbaar | Opnieuw proberen na 30 seconden of langer |
| Alle andere | Opnieuw proberen na 10 seconden of langer |

## <a name="delivery-with-custom-headers"></a>Levering met aangepaste kopteksten
Met gebeurtenis abonnementen kunt u HTTP-headers instellen die in de geleverde gebeurtenissen zijn opgenomen. Met deze mogelijkheid kunt u aangepaste headers instellen die vereist zijn voor een doel. U kunt Maxi maal 10 kopteksten instellen bij het maken van een gebeurtenis abonnement. Elke header waarde mag niet groter zijn dan 4.096 bytes (4.000). U kunt aangepaste kopteksten instellen voor de gebeurtenissen die worden geleverd aan de volgende bestemmingen:

- Webhooks
- Azure Service Bus onderwerpen en wacht rijen
- Azure Event Hubs
- Hybride verbindingen doorgeven

Zie [levering met aangepaste kopteksten](delivery-properties.md)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)als u de status van de gebeurtenis bezorgingen wilt weer geven.
* Zie [Dead-letter en beleid voor opnieuw proberen](manage-event-delivery.md)om de bezorgings opties voor gebeurtenissen aan te passen.
