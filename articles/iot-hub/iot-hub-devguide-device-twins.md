---
title: Meer informatie over Azure IoT Hub Device apparaatdubbels | Microsoft Docs
description: 'Ontwikkelaars handleiding: gebruik apparaat apparaatdubbels om status-en configuratie gegevens te synchroniseren tussen IoT Hub en uw apparaten'
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: b83faecb16ac09a47a0ade25474f7a5b3ecd4296
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400925"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Apparaat-apparaatdubbels in IoT Hub begrijpen en gebruiken

*Apparaatdubbels* zijn JSON-documenten die status informatie van een apparaat opslaan, inclusief meta gegevens, configuraties en voor waarden. Azure IoT Hub onderhoudt een apparaatdubbel voor elk apparaat dat u verbindt met IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In dit artikel wordt het volgende beschreven:

* De structuur van het apparaat: *labels*, *gewenste* en *gerapporteerde eigenschappen*.
* De bewerkingen die apps en back-ends van apparaten kunnen uitvoeren op apparaat apparaatdubbels.

Apparaat apparaatdubbels gebruiken voor het volgende:

* Sla apparaatspecifieke meta gegevens op in de Cloud. Bijvoorbeeld de locatie van de implementatie van een computer.

* Actuele status informatie rapporteren, zoals de beschik bare mogelijkheden en voor waarden van uw apparaat-app. Een apparaat is bijvoorbeeld verbonden met uw IoT-hub via mobiel of WiFi.

* Synchroniseer de status van langlopende werk stromen tussen de app van het apparaat en de back-end-app. Als de back-end van de oplossing bijvoorbeeld de nieuwe firmware versie aangeeft die moet worden geïnstalleerd, en de apparaat-app rapporteert de verschillende fasen van het update proces.

* Query's uitvoeren op de meta gegevens, configuratie of status van uw apparaat.

Raadpleeg de [informatie over apparaat-naar-Cloud-communicatie](iot-hub-devguide-d2c-guidance.md) voor hulp bij het gebruik van gerapporteerde eigenschappen, apparaat-naar-Cloud-berichten of het uploaden van bestanden.

Raadpleeg de [communicatie richtlijnen van Cloud naar apparaat](iot-hub-devguide-c2d-guidance.md) voor hulp bij het gebruik van de gewenste eigenschappen, directe methoden of Cloud-naar-apparaat-berichten.

## <a name="device-twins"></a>Apparaat apparaatdubbels

Apparaat-apparaatdubbels bevatten gerelateerde informatie die:

* Apparaat-en back-ends kunnen worden gebruikt voor het synchroniseren van de voor waarden en configuratie van apparaten.

* De back-end van de oplossing kan worden gebruikt om query's uit te voeren en langlopende bewerkingen te bereiken.

De levens cyclus van een apparaat moet worden gekoppeld aan de bijbehorende [apparaat-id](iot-hub-devguide-identity-registry.md). Apparaat-apparaatdubbels worden impliciet gemaakt en verwijderd wanneer een apparaat-id wordt gemaakt of verwijderd in IoT Hub.

Een apparaat dubbele is een JSON-document met de volgende opties:

* **Tags**. Een sectie van het JSON-document waarnaar de back-end van de oplossing kan lezen en waarnaar kan worden geschreven. Tags zijn niet zichtbaar voor apparaat-apps.

* **Gewenste eigenschappen**. Wordt samen met de gerapporteerde eigenschappen gebruikt voor het synchroniseren van apparaatconfiguratie of voor waarden. De back-end van de oplossing kan gewenste eigenschappen instellen en de apparaat-app kan deze lezen. De apparaat-app kan ook meldingen ontvangen over wijzigingen in de gewenste eigenschappen.

* **Gerapporteerde eigenschappen**. Wordt samen met de gewenste eigenschappen gebruikt voor het synchroniseren van apparaatconfiguratie of voor waarden. De apparaat-app kan gerapporteerde eigenschappen instellen en de back-end van de oplossing kan deze lezen en er query's op uitvoeren.

* **Eigenschappen** van de apparaat-id. De hoofdmap van het apparaatonafhankelijke JSON-document van het apparaat bevat de alleen-lezen eigenschappen van de overeenkomende apparaat-id die is opgeslagen in het [identiteits register](iot-hub-devguide-identity-registry.md). Eigenschappen `connectionStateUpdatedTime` en worden `generationId` niet opgenomen.

![Scherm afbeelding van dubbele eigenschappen van het apparaat](./media/iot-hub-devguide-device-twins/twin.png)

In het volgende voor beeld ziet u een dubbel JSON-document voor een apparaat:

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

In het hoofd object zijn de eigenschappen van de apparaat-id en container objecten voor `tags` en beide `reported` en `desired` Eigenschappen. De `properties` container bevat een aantal alleen-lezen elementen ( `$metadata` , `$etag` en `$version` ) die worden beschreven in de secties [Dubbele meta gegevens](iot-hub-devguide-device-twins.md#device-twin-metadata) en [optimistische gelijktijdigheid](iot-hub-devguide-device-twins.md#optimistic-concurrency) van het apparaat.

### <a name="reported-property-example"></a>Voor beeld van een gerapporteerde eigenschap

In het vorige voor beeld bevat het apparaat twee een `batteryLevel` eigenschap die wordt gerapporteerd door de apparaat-app. Met deze eigenschap kunnen apparaten op basis van het laatst gerapporteerde accu niveau worden opgevraagd en uitgevoerd. Andere voor beelden zijn de apparaat-en connectiviteits opties voor het rapporteren van Device-apps.

> [!NOTE]
> De gerapporteerde eigenschappen vereenvoudigen scenario's waarbij de back-end van de oplossing relevant is voor de laatst bekende waarde van een eigenschap. Gebruik [apparaat-naar-Cloud-berichten](iot-hub-devguide-messages-d2c.md) als de back-end van de oplossing apparaat-telemetrie moet verwerken in de vorm van reeksen van tijds tempels, zoals een tijd reeks.

### <a name="desired-property-example"></a>Voor beeld van gewenste eigenschap

In het vorige voor beeld `telemetryConfig` worden het dubbele gewenste en gerapporteerde eigenschappen gebruikt door de back-end van de oplossing en de apparaat-app om de telemetrie-configuratie voor dit apparaat te synchroniseren. Bijvoorbeeld:

1. Met de back-end van de oplossing stelt u de gewenste eigenschap in op de gewenste configuratie waarde. Hier is het gedeelte van het document met de gewenste eigenschap ingesteld:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. De apparaat-app wordt onmiddellijk op de hoogte gesteld wanneer deze is verbonden, of tijdens de eerste keer opnieuw verbinding maken. De app apparaat meldt vervolgens de bijgewerkte configuratie (of een fout voorwaarde met behulp van de `status` eigenschap). Hier volgt het gedeelte van de gerapporteerde eigenschappen:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Met de back-end van de oplossing kunt u de resultaten van de configuratie bewerking op veel apparaten volgen door een [query](iot-hub-devguide-query-language.md) uit te sturen naar de apparaatdubbels van het apparaat.

> [!NOTE]
> De voor gaande fragmenten zijn voor beelden die zijn geoptimaliseerd voor de Lees baarheid, van een manier om een apparaatconfiguratie en de status ervan te coderen. IoT Hub biedt geen specifiek schema voor het dubbele gewenste apparaat en de gerapporteerde eigenschappen in de apparaatdubbels van het apparaat.
> 

U kunt apparaatdubbels gebruiken voor het synchroniseren van langlopende bewerkingen, zoals firmware-updates. Zie [gewenste eigenschappen gebruiken om apparaten te configureren](tutorial-device-twins.md)voor meer informatie over het gebruik van eigenschappen voor het synchroniseren en volgen van een langlopende bewerking op alle apparaten.

## <a name="back-end-operations"></a>Back-end-bewerkingen

De back-end van de oplossing werkt op het apparaat tussen het gebruik van de volgende atomische bewerkingen, beschikbaar gesteld via HTTPS:

* Het **apparaat wordt opgehaald met de id**. Met deze bewerking wordt het dubbele document van het apparaat, inclusief tags en gewenste en gerapporteerde systeem eigenschappen, geretourneerd.

* **Apparaat is gedeeltelijk bijgewerkt**. Met deze bewerking kan de back-end van de oplossing de labels gedeeltelijk bijwerken of de gewenste eigenschappen van een apparaat dubbele. De gedeeltelijke update wordt uitgedrukt als een JSON-document waarmee elke eigenschap wordt toegevoegd of bijgewerkt. Eigenschappen die zijn ingesteld op `null` , worden verwijderd. In het volgende voor beeld wordt een nieuwe gewenste eigenschap gemaakt met `{"newProperty": "newValue"}` de waarde, wordt de bestaande waarde van `existingProperty` met vervangen `"otherNewValue"` en wordt deze verwijderd `otherOldProperty` . Er worden geen andere wijzigingen aangebracht in de bestaande gewenste eigenschappen of Tags:

   ```json
   {
       "properties": {
           "desired": {
               "newProperty": {
                   "nestedProperty": "newValue"
               },
               "existingProperty": "otherNewValue",
               "otherOldProperty": null
           }
       }
   }
   ```

* **Gewenste eigenschappen vervangen**. Met deze bewerking wordt de back-end van de oplossing in staat gesteld alle bestaande gewenste eigenschappen volledig te overschrijven en een nieuw JSON-document voor te vervangen `properties/desired` .

* **Tags vervangen**. Met deze bewerking kan de back-end van de oplossing alle bestaande Tags volledig overschrijven en vervangen door een nieuw JSON-document voor `tags` .

* **Ontvang dubbele meldingen**. Met deze bewerking kan de back-end van de oplossing worden gewaarschuwd wanneer het dubbele wordt gewijzigd. Hiervoor moet uw IoT-oplossing een route maken en de gegevens bron instellen op *twinChangeEvents*. Standaard zijn er geen routes vooraf aanwezig, waardoor er geen dubbele meldingen worden verzonden. Als de wijzigings ratio te hoog is of om andere redenen, zoals interne fouten, kan de IoT Hub slechts één melding verzenden die alle wijzigingen bevat. Als uw toepassing bijvoorbeeld betrouw bare controle en logboek registratie van alle tussenliggende statussen vereist, moet u apparaat-naar-Cloud-berichten gebruiken. Het dubbele meldings bericht bevat eigenschappen en hoofd tekst.

  - Eigenschappen

    | Naam | Waarde |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Tijdstip waarop de melding is verzonden |
    $iothub-bericht bron | twinChangeEvents |
    $content-encoding | UTF-8 |
    deviceId | ID van het apparaat |
    hubName | Naam van IoT Hub |
    operationTimestamp | [Iso8601](https://en.wikipedia.org/wiki/ISO_8601) tijds tempel van bewerking |
    iothub-Message-schema | twinChangeNotification |
    opType | "replaceTwin" of "updateTwin" |

    Eigenschappen van het berichten systeem worden voorafgegaan door het `$` symbool.

  - Hoofdtekst
        
    In deze sectie vindt u alle dubbele wijzigingen in een JSON-indeling. Er wordt gebruikgemaakt van dezelfde indeling als een patch, met het verschil dat deze alle dubbele secties kan bevatten: Tags, eigenschappen. gerapporteerd, eigenschappen. desired en de elementen $metadata bevatten. Bijvoorbeeld:

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Alle voor gaande bewerkingen ondersteunen [optimistische gelijktijdigheid](iot-hub-devguide-device-twins.md#optimistic-concurrency) en vereisen de **ServiceConnect** -machtiging, zoals is gedefinieerd in [toegang tot IOT hub beheren](iot-hub-devguide-security.md).

Naast deze bewerkingen kan de back-end van de oplossing:

* Een query uitvoeren op de apparaatdubbels van het apparaat met behulp van de SQL-achtige [IOT hub query taal](iot-hub-devguide-query-language.md).

* Bewerkingen uitvoeren op grote sets met apparaatdubbels met behulp van [taken](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Bewerkingen voor apparaten

De apparaat-app werkt op het apparaat tussen het gebruik van de volgende atomische bewerkingen:

* **Ophalen van apparaat dubbele**. Met deze bewerking wordt het dubbele document van het apparaat (inclusief gewenste systeem eigenschappen) geretourneerd voor het apparaat dat momenteel is verbonden. (Tags zijn niet zichtbaar voor apparaat-apps.)

* De **gerapporteerde eigenschappen zijn gedeeltelijk bijgewerkt**. Met deze bewerking wordt de gedeeltelijke update van de gerapporteerde eigenschappen van het momenteel verbonden apparaat ingeschakeld. Deze bewerking maakt gebruik van dezelfde JSON-update-indeling als de back-end van de oplossing gebruikt voor een gedeeltelijke update van de gewenste eigenschappen.

* **Bekijk de gewenste eigenschappen**. Het apparaat dat momenteel is verbonden, kan ervoor kiezen om op de hoogte te worden gesteld van updates voor de gewenste eigenschappen wanneer deze zich voordoen. Het apparaat ontvangt dezelfde vorm van update (gedeeltelijke of volledige vervanging) die wordt uitgevoerd door de back-end van de oplossing.

Voor alle voor gaande bewerkingen is de machtiging **DeviceConnect** vereist, zoals is gedefinieerd in [toegang tot IOT hub beheren](iot-hub-devguide-security.md).

De [sdk's van het Azure IOT-apparaat](iot-hub-devguide-sdks.md) maken het eenvoudig om de voor gaande bewerkingen uit vele talen en platforms te gebruiken. Zie voor meer informatie over de details van IoT Hub primitieven voor gewenste synchronisatie van het [apparaat de stroom](iot-hub-devguide-device-twins.md#device-reconnection-flow)voor het opnieuw verbinden van apparaten.

## <a name="tags-and-properties-format"></a>Indeling van tags en eigenschappen

Labels, gewenste eigenschappen en gerapporteerde eigenschappen zijn JSON-objecten met de volgende beperkingen:

* **Sleutels**: alle sleutels in JSON-objecten zijn UTF-8-code ring, hoofdletter gevoelig en Maxi maal 1 KB lang. Toegestane tekens bevatten UNICODE-besturings tekens (segmenten C0 en C1), en, en `.` `$` sp.

* **Waarden**: alle waarden in JSON-objecten kunnen van de volgende JSON-typen zijn: Boolean, Number, String, object. Matrices worden ook ondersteund.

    * Gehele getallen kunnen een minimum waarde van-4503599627370496 en een maximum waarde van 4503599627370495 hebben.

    * Teken reeks waarden zijn UTF-8-code ring en kunnen een maximum lengte hebben van 4 KB.

* **Diepte**: de maximale diepte van JSON-objecten in Tags, gewenste eigenschappen en gerapporteerde eigenschappen is 10. Het volgende object is bijvoorbeeld geldig:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Dubbele grootte van apparaat

IoT Hub dwingt een maximale grootte van 8 KB af voor de waarde van en `tags` een maximale grootte van 32 KB voor elke waarde van `properties/desired` en `properties/reported` . Deze totalen zijn exclusief van alleen-lezen elementen als `$etag` , `$version` en `$metadata/$lastUpdated` .

Dubbele grootte wordt als volgt berekend:

* IoT Hub cumulatieve berekeningen voor elke eigenschap in het JSON-document en voegt de lengte van de sleutel en waarde van de eigenschap toe.

* Eigenschaps sleutels worden beschouwd als UTF8-gecodeerde teken reeksen.

* Eenvoudige eigenschaps waarden worden beschouwd als UTF8-gecodeerde teken reeksen, numerieke waarden (8 bytes) of Boole-waarden (4 bytes).

* De grootte van door UTF8 gecodeerde teken reeksen wordt berekend door alle tekens te tellen, met uitzonde ring van UNICODE-besturings tekens (segmenten C0 en C1).

* Complexe eigenschaps waarden (geneste objecten) worden berekend op basis van de cumulatieve grootte van de eigenschaps sleutels en eigenschaps waarden die ze bevatten.

IoT Hub weigert een fout bij alle bewerkingen die de grootte van de `tags` , `properties/desired` , of de `properties/reported` documenten boven de limiet verg Roten.

## <a name="device-twin-metadata"></a>Dubbele meta gegevens van het apparaat

IoT Hub onderhoudt de tijds tempel van de laatste update voor elk JSON-object in de dubbele gewenste en gerapporteerde eigenschappen van het apparaat. De tijds tempels zijn in UTC en worden gecodeerd in de [iso8601](https://en.wikipedia.org/wiki/ISO_8601) -indeling `YYYY-MM-DDTHH:MM:SS.mmmZ` .

Bijvoorbeeld:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Deze informatie wordt op elk niveau (niet alleen de bladeren van de JSON-structuur) bewaard om updates die object sleutels verwijderen te behouden.

## <a name="optimistic-concurrency"></a>Optimistische gelijktijdigheid

De labels, gewenste en gerapporteerde eigenschappen bieden ondersteuning voor optimistische gelijktijdigheid.
Labels hebben een ETag, zoals per [RFC7232](https://tools.ietf.org/html/rfc7232), die de JSON-weer gave van de tag vertegenwoordigt. U kunt ETags gebruiken in bewerkingen voor voorwaardelijke updates van de back-end van de oplossing om consistentie te garanderen.

Het dubbele gewenste apparaat en de gerapporteerde eigenschappen hebben geen ETags, maar hebben een `$version` waarde die gegarandeerd incrementeel is. Net als bij een ETag kan de versie worden gebruikt door de update partij om consistentie van updates af te dwingen. Bijvoorbeeld een apparaat-app voor een gerapporteerde eigenschap of de back-end van de oplossing voor een gewenste eigenschap.

Versies zijn ook handig wanneer een waarneem bare agent (zoals de apparaat-app die de gewenste eigenschappen nadenkt), races moet afstemmen tussen het resultaat van een ophalen-bewerking en een update melding. De [sectie stroom](iot-hub-devguide-device-twins.md#device-reconnection-flow) voor het opnieuw verbinden van apparaten biedt meer informatie.

## <a name="device-reconnection-flow"></a>Stroom voor opnieuw verbinden van apparaat

De gewenste eigenschappen van updates voor niet-verbonden apparaten worden niet door IoT Hub bewaard. Het volgt dat een apparaat dat verbinding maakt, het volledige gewenste eigenschappen document moet ophalen naast het abonneren op update meldingen. Gezien de mogelijkheid van races tussen update meldingen en het volledig ophalen, moet de volgende stroom worden gegarandeerd:

1. Apparaat-app maakt verbinding met een IoT-hub.
2. Apparaat-app wordt geabonneerd voor gewenste eigenschappen van meldingen bijwerken.
3. Met Device App wordt het volledige document opgehaald voor gewenste eigenschappen.

De apparaat-app kan alle meldingen negeren met `$version` minder of gelijk zijn aan de versie van het volledig opgehaalde document. Deze methode is mogelijk omdat IoT Hub garandeert dat de versies altijd worden verhoogd.

> [!NOTE]
> Deze logica is al geïmplementeerd in de [Azure IOT-apparaat-sdk's](iot-hub-devguide-sdks.md). Deze beschrijving is alleen nuttig als de apparaat-app geen van de Azure IoT-apparaat-Sdk's kan gebruiken en de MQTT-interface rechtstreeks moet Program meren.
> 

## <a name="additional-reference-material"></a>Extra referentie materiaal

Andere naslag onderwerpen in de IoT Hub ontwikkelaars handleiding zijn:

* In het artikel [IOT hub-eind](iot-hub-devguide-endpoints.md) punten worden de verschillende eind punten beschreven die elke IOT-hub beschikbaar maakt voor runtime-en beheer bewerkingen.

* In het artikel [beperking en quota's](iot-hub-devguide-quotas-throttling.md) worden de quota's beschreven die van toepassing zijn op de IOT hub-service en het beperkings gedrag dat u kunt verwachten wanneer u de service gebruikt.

* Het artikel [sdk's van Azure IOT-apparaat en-service](iot-hub-devguide-sdks.md) bevat een lijst met de diverse taal-sdk's die u kunt gebruiken bij het ontwikkelen van zowel apparaat-als service-apps die communiceren met IOT hub.

* In de [IOT hub query taal voor apparaatdubbels, Jobs en bericht routering](iot-hub-devguide-query-language.md) wordt de IOT hub query taal beschreven die u kunt gebruiken om informatie op te halen van IOT hub over de apparaatdubbels en taken van uw apparaat.

* Het [MQTT-ondersteunings](iot-hub-mqtt-support.md) artikel voor IOT hub biedt meer informatie over IOT hub ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over device apparaatdubbels, bent u mogelijk geïnteresseerd in de volgende IoT Hub onderwerpen over de ontwikkelaars handleiding:

* [Module apparaatdubbels in IoT Hub begrijpen en gebruiken](iot-hub-devguide-module-twins.md)
* [Een rechtstreekse methode aanroepen op een apparaat](iot-hub-devguide-direct-methods.md)
* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Zie de volgende IoT Hub zelf studies voor het uitproberen van de concepten die in dit artikel worden beschreven:

* [Het dubbele apparaat gebruiken](iot-hub-node-node-twin-getstarted.md)
* [De dubbele eigenschappen van een apparaat gebruiken](tutorial-device-twins.md)
* [Apparaatbeheer met Azure IoT Tools voor VS Code](iot-hub-device-management-iot-toolkit.md)
