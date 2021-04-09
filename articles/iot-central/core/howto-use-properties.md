---
title: Eigenschappen in een Azure IoT Central-oplossing gebruiken
description: Meer informatie over het gebruik van alleen-lezen en beschrijf bare eigenschappen in een Azure IoT Central-oplossing.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 36329987e510372ff286a10584a115ea259afc60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119081"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Eigenschappen in een Azure IoT Central-oplossing gebruiken

In deze hand leiding wordt uitgelegd hoe u als een ontwikkel aars van een apparaat apparaateigenschappen kunt gebruiken die zijn gedefinieerd in een sjabloon in uw Azure IoT Central-toepassing.

Eigenschappen vertegenwoordigen waarden van het tijdstip. Een apparaat kan bijvoorbeeld een eigenschap gebruiken om de doel temperatuur te rapporteren die het probeert te bereiken. Standaard zijn de apparaateigenschappen alleen-lezen in IoT Central. Met Beschrijf bare eigenschappen kunt u de status synchroniseren tussen uw apparaat en uw Azure IoT Central-toepassing.

U kunt ook Cloud eigenschappen definiëren in een Azure IoT Central-toepassing. Waarden van Cloud-eigenschappen worden nooit uitgewisseld met een apparaat en vallen buiten het bereik van dit artikel.

## <a name="define-your-properties"></a>Uw eigenschappen definiëren

Eigenschappen zijn gegevens velden die de status van uw apparaat vertegenwoordigen. Eigenschappen gebruiken om de duurzame status van het apparaat weer te geven, zoals de aan/uit-status van een apparaat. Eigenschappen kunnen ook basis eigenschappen van apparaten vertegenwoordigen, zoals de software versie van het apparaat. U declareert eigenschappen als alleen-lezen of beschrijfbaar.

De volgende scherm afbeelding toont een eigenschaps definitie in een Azure IoT Central-toepassing.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Scherm afbeelding met een eigenschaps definitie in een Azure IoT Central-toepassing.":::

De volgende tabel bevat de configuratie-instellingen voor een eigenschaps mogelijkheid.

| Veld           | Beschrijving                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Weergavenaam    | De weergave naam voor de waarde van de eigenschap die wordt gebruikt in dash boards en formulieren.                                                                                                                                                              |
| Name            | De naam van de eigenschap. Azure IoT Central genereert een waarde voor dit veld van de weergave naam, maar u kunt indien nodig uw eigen waarde kiezen. Dit veld moet alfanumeriek zijn.  De apparaatcode gebruikt deze **naam** waarde.           |
| Type capaciteit | Eigenschap.                                                                                                                                                                                                                          |
| Semantisch type   | Het semantische type van de eigenschap, zoals de Tempe ratuur, de status of de gebeurtenis. De keuze van semantisch type bepaalt welke van de volgende velden beschikbaar zijn.                                                                       |
| Schema          | Het gegevens type van de eigenschap, zoals double, String of vector. Welke opties beschikbaar zijn, wordt bepaald door het semantische type. Schema is niet beschikbaar voor de semantische typen gebeurtenis en status.                                               |
| Beschrijfbaar       | Als de eigenschap niet schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan Azure IoT Central. Als de eigenschap schrijfbaar is, kan het apparaat eigenschaps waarden rapporteren aan Azure IoT Central. Vervolgens kan Azure IoT Central bijgewerkte eigenschappen verzenden naar het apparaat. |
| Severity        | Alleen beschikbaar voor het semantische gebeurtenis type. De ernst is **fout**, **informatie** of **waarschuwing**.                                                                                                                         |
| Status waarden    | Alleen beschikbaar voor het semantische type status. Definieer de mogelijke status waarden, die elk een weergave naam, naam, opsommings type en waarde hebben.                                                                                   |
| Eenheid            | Een eenheid voor de waarde van de eigenschap, zoals **mph**, **%** of **&deg; C**.                                                                                                                                                              |
| Eenheid weer geven    | Een weergave-eenheid voor gebruik in dash boards en formulieren.                                                                                                                                                                                    |
| Opmerking         | Eventuele opmerkingen over de eigenschaps mogelijkheid.                                                                                                                                                                                        |
| Description     | Een beschrijving van de eigenschaps mogelijkheid.                                                                                                                                                                                          |

De eigenschappen kunnen ook worden gedefinieerd in een interface in een sjabloon, zoals hier wordt weer gegeven:

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

In dit voor beeld worden twee eigenschappen weer gegeven. Deze eigenschappen hebben betrekking op de eigenschaps definitie in de gebruikers interface:

* `@type` Hiermee geeft u het type mogelijkheid op: `Property` . In het vorige voor beeld wordt ook het semantische type `Temperature` voor beide eigenschappen weer gegeven.
* `name` voor de eigenschap.
* `schema` Hiermee geeft u het gegevens type voor de eigenschap op. Deze waarde kan een primitief type zijn, zoals double, integer, Boolean of string. Complexe object typen en Maps worden ook ondersteund.
* `writable` Eigenschappen zijn standaard alleen-lezen. U kunt een eigenschap markeren als beschrijfbaar met behulp van dit veld.

Met optionele velden, zoals weergave naam en-beschrijving, kunt u meer details toevoegen aan de interface en de mogelijkheden.

Wanneer u een eigenschap maakt, kunt u complexe schema typen opgeven, zoals **object** en **Enum**.

![Scherm afbeelding die laat zien hoe u een mogelijkheid kunt toevoegen.](./media/howto-use-properties/property.png)

Wanneer u het complexe **schema** selecteert, zoals **object**, moet u het object ook definiëren.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="Scherm afbeelding die laat zien hoe u een object definieert":::

De volgende code toont de definitie van een eigenschaps type van een object. Dit object heeft twee velden met typen teken reeks en geheel getal.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Alleen-lezen eigenschappen implementeren

Eigenschappen zijn standaard alleen-lezen. Met alleen-lezen eigenschappen kan een apparaat eigenschapwaarde worden bijgewerkt naar uw Azure IoT Central-toepassing. Uw Azure IoT Central-toepassing kan de waarde van een alleen-lezen eigenschap niet instellen.

Azure IoT Central maakt gebruik van apparaatdubbels om eigenschaps waarden te synchroniseren tussen het apparaat en de Azure IoT Central-toepassing. Waarden van eigenschappen van een apparaat gebruiken door apparaatdubbels gerapporteerde eigenschappen. Zie [device apparaatdubbels](../../iot-hub/tutorial-device-twins.md)voor meer informatie.

In het volgende code fragment van een apparaatprofiel wordt de definitie van een alleen-lezen eigenschaps type weer gegeven:

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Eigenschaps updates worden door een apparaat als JSON-nettolading verzonden. Zie [payloads](./concepts-telemetry-properties-commands.md)voor meer informatie.

U kunt de Azure IoT Device SDK gebruiken om een eigenschaps update naar uw Azure IoT Central-toepassing te verzenden.

Dubbele eigenschappen van het apparaat kunnen worden verzonden naar uw Azure IoT Central-toepassing met behulp van de volgende functie:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

In dit artikel wordt Node.js gebruikt voor eenvoud. Zie voor voor beelden van andere talen de zelf studie [een client toepassing maken en verbinden met uw Azure IOT Central-toepassing](tutorial-connect-device.md) .

In de volgende weer gave in azure IoT Central-toepassing worden de eigenschappen weer gegeven die u kunt zien. De weer gave maakt de eigenschap **apparaat model** automatisch een _eigenschap met het kenmerk alleen-lezen_.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="Scherm opname van de weer gave van een alleen-lezen eigenschap":::

## <a name="implement-writable-properties"></a>Beschrijf bare eigenschappen implementeren

Beschrijf bare eigenschappen worden ingesteld door een operator in de Azure IoT Central-toepassing op een formulier. De eigenschap wordt door Azure IoT Central naar het apparaat verzonden. Azure IoT Central verwacht een bevestiging van het apparaat.

In het volgende code fragment van een apparaatprofiel wordt de definitie van een beschrijfbaar eigenschaps type weer gegeven:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Als u de Beschrijf bare eigenschappen wilt definiëren en verwerken waarop uw apparaat reageert, kunt u de volgende code gebruiken:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Het antwoord bericht moet de `ac` velden en bevatten `av` . Het veld `ad` is optioneel. Raadpleeg de volgende code fragmenten voor voor beelden:

* `ac` is een numeriek veld dat gebruikmaakt van de waarden in de volgende tabel.
* `av` is het versie nummer dat naar het apparaat wordt verzonden.
* `ad` is een beschrijving van een optie teken reeks.

| Waarde | Label | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Voltooid | De bewerking voor het wijzigen van de eigenschap is voltooid. |
| `'ac': 202` of `'ac': 201` | In behandeling | De bewerking voor het wijzigen van de eigenschap is in behandeling of wordt uitgevoerd. |
| `'ac': 4xx` | Fout | De aangevraagde eigenschaps wijziging is ongeldig of er is een fout opgetreden. |
| `'ac': 5xx` | Fout | Het apparaat heeft een onverwachte fout aangetroffen bij het verwerken van de aangevraagde wijziging. |

Zie [uw apparaten configureren vanuit een back-end-service](../../iot-hub/tutorial-device-twins.md)voor meer informatie over apparaatdubbels.

Wanneer de operator een Beschrijf bare eigenschap in de Azure IoT Central-toepassing instelt, gebruikt de toepassing een door het apparaat dubbele gewenste eigenschap om de waarde naar het apparaat te verzenden. Het apparaat reageert vervolgens met behulp van een dubbele, gerapporteerde eigenschap van het apparaat. Wanneer Azure IoT Central de gerapporteerde eigenschaps waarde ontvangt, wordt de eigenschappen weergave met de status **geaccepteerd** bijgewerkt.

In de volgende weer gave worden de Beschrijf bare eigenschappen weer gegeven. Wanneer u de waarde invoert en **Opslaan** selecteert, is de initiële status **in behandeling**. Wanneer het apparaat de wijziging accepteert, wordt de status gewijzigd in **geaccepteerd**.

![Scherm opname waarin de status in behandeling wordt weer gegeven.](./media/howto-use-properties/status-pending.png)

![Scherm opname van de geaccepteerde eigenschap.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u eigenschappen in uw Azure IoT Central-toepassing kunt gebruiken, raadpleegt u:

* [Nettoladingen](concepts-telemetry-properties-commands.md)
* [Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing](tutorial-connect-device.md)