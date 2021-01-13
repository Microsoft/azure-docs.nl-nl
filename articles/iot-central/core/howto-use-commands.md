---
title: Opdrachten voor apparaten gebruiken in een Azure IoT Central-oplossing
description: Instructies voor het gebruik van opdrachten in azure IoT Central-oplossing. In deze zelf studie wordt uitgelegd hoe u als een ontwikkelaar van een apparaat opdrachten in de client-app kunt gebruiken voor uw Azure IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 52872175eb799785674c331ad4d687ff8ef427a4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134276"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Opdrachten gebruiken in een Azure IoT Central-oplossing

In deze hand leiding wordt uitgelegd hoe u als een ontwikkelaar van een apparaat opdrachten kunt gebruiken die zijn gedefinieerd in een sjabloon.

Een operator kan de IoT Central gebruikers interface gebruiken om een opdracht op een apparaat aan te roepen. Opdrachten bepalen het gedrag van een apparaat. Een operator kan bijvoorbeeld een opdracht aanroepen om een apparaat opnieuw op te starten of diagnostische gegevens te verzamelen.

Een apparaat kan:

* Reageer onmiddellijk op een opdracht.
* Reageer op IoT Central wanneer de opdracht wordt ontvangen en geef IoT Central later op de hoogte wanneer de *langlopende opdracht* is voltooid.

Standaard verwachten opdrachten dat een apparaat is verbonden en mislukt het als het apparaat niet kan worden bereikt. Als u de optie **wachtrij als offline** in de gebruikers interface van de sjabloon voor het apparaat selecteert, kan de opdracht in de wachtrij worden geplaatst totdat een apparaat online is. Deze *offline opdrachten* worden beschreven in een afzonderlijke sectie verderop in dit artikel.

## <a name="define-your-commands"></a>Uw opdrachten definiëren

Standaard opdrachten worden verzonden naar een apparaat om het apparaat te instrueren om iets te doen. Een opdracht kan para meters met aanvullende informatie bevatten. Een opdracht voor het openen van een klep op een apparaat kan bijvoorbeeld een para meter hebben die aangeeft hoeveel de klep moet worden geopend. Opdrachten kunnen ook een retour waarde ontvangen wanneer het apparaat de opdracht voltooit. Een opdracht die een apparaat vraagt om een diagnose uit te voeren, kan bijvoorbeeld een diagnostische rapport als retour waarde ontvangen.

Opdrachten worden gedefinieerd als onderdeel van een apparaatprofiel. De volgende scherm afbeelding toont de definitie van het **Max-Min rapport ophalen** in de sjabloon van het **Thermo** -apparaat. Deze opdracht heeft zowel de aanvraag-als de antwoord-para meters: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Scherm opname van de opdracht maximum aantal min-rapport in een thermo-Device sjabloon":::

De volgende tabel bevat de configuratie-instellingen voor een opdracht mogelijkheid:

| Veld             |Beschrijving|
|-------------------|-----------|
|Weergavenaam       |De opdracht waarde die wordt gebruikt voor dash boards en formulieren.|
| Naam            | De naam van de opdracht. IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. Dit veld moet alfanumeriek zijn. De apparaatcode gebruikt deze **naam** waarde.|
| Type mogelijkheid | Cmd.|
| Wachtrij indien offline | Hiermee wordt aangegeven of deze opdracht een *offline* opdracht moet zijn. |
| Beschrijving     | Een beschrijving van de opdracht mogelijkheid.|
| Opmerking     | Eventuele opmerkingen over de opdracht mogelijkheid.|
| Aanvraag     | De payload voor de opdracht apparaat.|
| Antwoord     | De nettolading van het antwoord op de opdracht van het apparaat.|

Het volgende code fragment toont de JSON-weer gave van de opdracht in het model van het apparaat. In dit voor beeld is de reactie waarde een complex **object** type met meerdere velden:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> U kunt een model van een apparaat exporteren via de pagina met de sjabloon.

U kunt deze opdracht definitie koppelen aan de scherm opname van de gebruikers interface met behulp van de volgende velden:

* `@type` het type mogelijkheid opgeven: `Command`
* `name` voor de opdracht waarde.

Met optionele velden, zoals weergave naam en-beschrijving, kunt u meer details toevoegen aan de interface en de mogelijkheden.

## <a name="standard-commands"></a>Standaard opdrachten

In deze sectie ziet u hoe een apparaat een reactie waarde verzendt zodra de opdracht wordt ontvangen.

Het volgende code fragment laat zien hoe een apparaat kan reageren op een opdracht die onmiddellijk een succes code verzendt:

> [!NOTE]
> In dit artikel wordt Node.js gebruikt voor eenvoud. Zie voor voor beelden van andere talen de zelf studie [een client toepassing maken en verbinden met uw Azure IOT Central-toepassing](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

De aanroep voor `onDeviceMethod` het instellen van de `commandHandler` methode. Deze opdracht-handler:

1. Hiermee wordt de naam van de opdracht gecontroleerd.
1. Voor de `getMaxMinReport` opdracht roept het de `getMaxMinReportObject` waarden op die moeten worden opgehaald in het retour object.
1. Roept `sendCommandResponse` om het antwoord terug te sturen naar IOT Central. Dit antwoord bevat de `200` reactie code om aan te geven dat dit is geslaagd.

In de volgende scherm afbeelding ziet u hoe de geslaagde opdracht respons wordt weer gegeven in de IoT Central UI:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Scherm afbeelding die laat zien hoe de opdracht Payload voor een standaard opdracht wordt weer gegeven":::

## <a name="long-running-commands"></a>Langlopende opdrachten

In deze sectie ziet u hoe een apparaat kan vertragen met het verzenden van een bevestiging dat de opdracht voltooid.

Het volgende code fragment laat zien hoe een apparaat een langlopende opdracht kan implementeren:

> [!NOTE]
> In dit artikel wordt Node.js gebruikt voor eenvoud. Zie voor voor beelden van andere talen de zelf studie [een client toepassing maken en verbinden met uw Azure IOT Central-toepassing](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

De aanroep voor `onDeviceMethod` het instellen van de `commandHandler` methode. Deze opdracht-handler:

1. Hiermee wordt de naam van de opdracht gecontroleerd.
1. Roept `sendCommandResponse` om het antwoord terug te sturen naar IOT Central. Dit antwoord bevat de `202` antwoord code om de in behandeling zijnde resultaten aan te geven.
1. Hiermee wordt de langlopende bewerking voltooid.
1. Gebruikt een gerapporteerde eigenschap met dezelfde naam als de opdracht om IoT Central te geven dat de opdracht is voltooid.

De volgende scherm afbeelding laat zien hoe de opdracht antwoord wordt weer gegeven in de IoT Central gebruikers interface wanneer het de respons code 202 ontvangt:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Scherm opname die de onmiddellijke reactie van het apparaat weergeeft":::

In de volgende scherm afbeelding ziet u de IoT Central-gebruikers interface wanneer deze de eigenschaps update ontvangt die aangeeft dat de opdracht is voltooid:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Scherm opname van de langlopende opdracht die wordt uitgevoerd":::

## <a name="offline-commands"></a>Offline opdrachten

In deze sectie wordt beschreven hoe een apparaat een offline opdracht verwerkt. Als een apparaat online is, kan het de offline opdracht afhandelen zodra het wordt ontvangen. Als een apparaat offline is, wordt de offline opdracht verwerkt wanneer de volgende verbinding maakt met IoT Central. Apparaten kunnen geen retour waarde verzenden als reactie op een offline opdracht.

> [!NOTE]
> In dit artikel wordt Node.js gebruikt voor eenvoud.

De volgende scherm afbeelding toont een offline opdracht met de naam **GenerateDiagnostics**. De aanvraag parameter is een object met de eigenschap datetime met de naam **StartTime** en een opsommings eigenschap met de naam **Bank**:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Scherm opname van de gebruikers interface voor een offline opdracht":::

Het volgende code fragment laat zien hoe een client kan Luis teren naar offline opdrachten en de inhoud van het bericht weer geven:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

De uitvoer van het vorige code fragment toont de payload met de waarden **StartTime** en **Bank** . De eigenschappen lijst bevat de naam van de opdracht in het lijst item met de naam van de **methode** :

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u opdrachten in uw Azure IoT Central-toepassing gebruikt, raadpleegt u [payloads](concepts-telemetry-properties-commands.md) voor meer informatie over opdracht parameters en [maakt en verbindt u een client toepassing met uw Azure IOT Central-toepassing](tutorial-connect-device.md) om volledige code voorbeelden in verschillende talen te bekijken.
