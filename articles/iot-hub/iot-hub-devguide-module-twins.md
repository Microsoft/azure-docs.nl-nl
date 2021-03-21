---
title: Meer informatie over Azure IoT Hub module apparaatdubbels | Microsoft Docs
description: 'Ontwikkelaars handleiding: gebruik module apparaatdubbels om status-en configuratie gegevens te synchroniseren tussen IoT Hub en uw apparaten'
author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: nehsin
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6e728eaf8335a102e38a3b4b07ab5e504d452294
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91996464"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Module apparaatdubbels in IoT Hub begrijpen en gebruiken

In dit artikel wordt ervan uitgegaan dat u de [apparaatdubbels-apparaten in IOT hub](iot-hub-devguide-device-twins.md) eerst hebt gelezen en gebruikt. In IoT Hub kunt u onder elke apparaat-id Maxi maal 50 module-identiteiten maken. Elke module-id genereert impliciet een module dubbele. Net als bij Device apparaatdubbels zijn module apparaatdubbels JSON-documenten waarin de status informatie van de module wordt opgeslagen, inclusief meta gegevens, configuraties en voor waarden. Azure IoT Hub onderhoudt een module voor elke module die u aansluit op IoT Hub. 

Aan de kant van het apparaat kunt u met de Sdk's van het IoT Hub apparaat modules maken waarbij elke ene een onafhankelijke verbinding met IoT Hub opent. Met deze functie kunt u afzonderlijke naam ruimten voor verschillende onderdelen op het apparaat gebruiken. U hebt bijvoorbeeld een verkoop machine met drie verschillende Sens oren. Elke sensor wordt beheerd door verschillende afdelingen in uw bedrijf. U kunt voor elke sensor een module maken. Op deze manier kan elke afdeling alleen taken of directe methoden verzenden naar de sensor die ze beheren, conflicten en gebruikers fouten voor komen.

 Module-identiteit en module twee bieden dezelfde mogelijkheden als de apparaat-id en het apparaat, met een nauw keurigere granulariteit. Met deze nauw keurigheid kunnen apparaten, zoals apparaten op basis van een besturings systeem of firmware-apparaten meerdere onderdelen beheren, de configuratie en voor waarden voor elk van deze onderdelen worden geïsoleerd. Module identiteit en module apparaatdubbels bieden een beheer schei ding van problemen bij het werken met IoT-apparaten met modulaire software onderdelen. We streven naar het ondersteunen van de dubbele functionaliteit van het apparaat op het niveau van de module dubbele niveaus per module dubbele algemene Beschik baarheid. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In dit artikel wordt het volgende beschreven:

* De structuur van de module: *labels*, *gewenste* en *gerapporteerde eigenschappen*.
* De bewerkingen die de modules en back-ends kunnen uitvoeren op module apparaatdubbels.

Raadpleeg de [informatie over apparaat-naar-Cloud-communicatie](iot-hub-devguide-d2c-guidance.md) voor hulp bij het gebruik van gerapporteerde eigenschappen, apparaat-naar-Cloud-berichten of het uploaden van bestanden.

Raadpleeg de [communicatie richtlijnen van Cloud naar apparaat](iot-hub-devguide-c2d-guidance.md) voor hulp bij het gebruik van de gewenste eigenschappen, directe methoden of Cloud-naar-apparaat-berichten.

## <a name="module-twins"></a>Module apparaatdubbels

Module apparaatdubbels Store-informatie met betrekking tot de module:

* Modules op het apparaat en IoT Hub kunnen worden gebruikt voor het synchroniseren van module omstandigheden en configuratie.

* De back-end van de oplossing kan worden gebruikt om query's uit te voeren en langlopende bewerkingen te bereiken.

De levens cyclus van een module dubbele is gekoppeld aan de bijbehorende [module-identiteit](iot-hub-devguide-identity-registry.md). Modules apparaatdubbels worden impliciet gemaakt en verwijderd wanneer een module-id wordt gemaakt of verwijderd in IoT Hub.

Een module dubbele is een JSON-document dat het volgende bevat:

* **Tags**. Een sectie van het JSON-document waarnaar de back-end van de oplossing kan lezen en waarnaar kan worden geschreven. Tags zijn niet zichtbaar voor modules op het apparaat. Labels worden ingesteld voor het doel van query's.

* **Gewenste eigenschappen**. Wordt samen met de gerapporteerde eigenschappen gebruikt voor het synchroniseren van module configuratie of-voor waarden. De back-end van de oplossing kan gewenste eigenschappen instellen en de module-App kan deze lezen. De module-App kan ook meldingen ontvangen over wijzigingen in de gewenste eigenschappen.

* **Gerapporteerde eigenschappen**. Wordt samen met de gewenste eigenschappen gebruikt voor het synchroniseren van module configuratie of-voor waarden. De module-App kan gerapporteerde eigenschappen instellen en de back-end van de oplossing kan deze lezen en er query's op uitvoeren.

* **Eigenschappen van module-identiteit**. De hoofdmap van het document van de module dubbele JSON bevat de alleen-lezen eigenschappen van de bijbehorende module-id die is opgeslagen in het [identiteits register](iot-hub-devguide-identity-registry.md).

![Architectuur representatie van het apparaat dubbele](./media/iot-hub-devguide-device-twins/module-twin.jpg)

In het volgende voor beeld wordt een module-document met dubbele JSON weer gegeven:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
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

In het hoofd object bevinden zich de eigenschappen van de module-identiteit en container objecten voor `tags` en beide `reported` en `desired` Eigenschappen. De `properties` container bevat enkele alleen-lezen elementen ( `$metadata` , `$etag` en `$version` ) die in de [module dubbele meta gegevens](iot-hub-devguide-module-twins.md#module-twin-metadata) en [optimistische gelijktijdigheids](iot-hub-devguide-device-twins.md#optimistic-concurrency) secties worden beschreven.

### <a name="reported-property-example"></a>Voor beeld van een gerapporteerde eigenschap

In het vorige voor beeld bevat de module twee een `batteryLevel` eigenschap die wordt gerapporteerd door de module-app. Met deze eigenschap kunnen modules worden opgevraagd en uitgevoerd op basis van het laatst gerapporteerde accu niveau. Andere voor beelden zijn de module mogelijkheden van module App Reporting of de connectiviteits opties.

> [!NOTE]
> De gerapporteerde eigenschappen vereenvoudigen scenario's waarbij de back-end van de oplossing relevant is voor de laatst bekende waarde van een eigenschap. Gebruik [apparaat-naar-Cloud-berichten](iot-hub-devguide-messages-d2c.md) als de back-end van de oplossing module-telemetrie moet verwerken in de vorm van reeksen van tijds tempels, zoals een tijd reeks.

### <a name="desired-property-example"></a>Voor beeld van gewenste eigenschap

In het vorige voor beeld `telemetryConfig` worden de module dubbele gewenste en gerapporteerde eigenschappen gebruikt door de back-end van de oplossing en de module-app om de telemetrie-configuratie voor deze module te synchroniseren. Bijvoorbeeld:

1. Met de back-end van de oplossing stelt u de gewenste eigenschap in op de gewenste configuratie waarde. Hier is het gedeelte van het document met de gewenste eigenschap ingesteld:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. De module-app wordt onmiddellijk op de hoogte gesteld wanneer deze is verbonden, of bij de eerste keer opnieuw verbinding maken. De module-App rapporteert vervolgens de bijgewerkte configuratie (of een fout voorwaarde met behulp van de `status` eigenschap). Hier volgt het gedeelte van de gerapporteerde eigenschappen:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Met de back-end van de oplossing kunt u de resultaten van de configuratie bewerking in veel modules volgen door de module apparaatdubbels te [doorzoeken](iot-hub-devguide-query-language.md) .

> [!NOTE]
> De voor gaande fragmenten zijn voor beelden die zijn geoptimaliseerd voor de Lees baarheid, van een manier om een module configuratie en de status ervan te coderen. IoT Hub maakt geen specifiek schema voor de module dubbele gewenste en gerapporteerde eigenschappen in de module apparaatdubbels.
> 
> 

## <a name="back-end-operations"></a>Back-end-bewerkingen
De back-end van de oplossing werkt op de module, met behulp van de volgende atomische bewerkingen, beschikbaar via HTTPS:

* **Haal de module op met id**. Met deze bewerking wordt het module dubbele document geretourneerd, inclusief tags en gewenste en gerapporteerde systeem eigenschappen.

* **Module is gedeeltelijk bijgewerkt**. Met deze bewerking kan de back-end van de oplossing de labels gedeeltelijk bijwerken of de gewenste eigenschappen in een module dubbele. De gedeeltelijke update wordt uitgedrukt als een JSON-document waarmee elke eigenschap wordt toegevoegd of bijgewerkt. Eigenschappen die zijn ingesteld op `null` , worden verwijderd. In het volgende voor beeld wordt een nieuwe gewenste eigenschap gemaakt met `{"newProperty": "newValue"}` de waarde, wordt de bestaande waarde van `existingProperty` met vervangen `"otherNewValue"` en wordt deze verwijderd `otherOldProperty` . Er worden geen andere wijzigingen aangebracht in de bestaande gewenste eigenschappen of Tags:

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

* **Ontvang dubbele meldingen**. Met deze bewerking kan de back-end van de oplossing worden gewaarschuwd wanneer het dubbele wordt gewijzigd. Hiervoor moet uw IoT-oplossing een route maken en de gegevens bron instellen op *twinChangeEvents*. Standaard worden er geen dubbele meldingen verzonden, dat wil zeggen dat er geen dergelijke routes vooraf bestaan. Als de wijzigings ratio te hoog is of om andere redenen, zoals interne fouten, kan de IoT Hub slechts één melding verzenden die alle wijzigingen bevat. Als uw toepassing bijvoorbeeld betrouw bare controle en logboek registratie van alle tussenliggende statussen vereist, moet u apparaat-naar-Cloud-berichten gebruiken. Het dubbele meldings bericht bevat eigenschappen en hoofd tekst.

  - Eigenschappen

    | Name | Waarde |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Tijdstip waarop de melding is verzonden |
    $iothub-bericht bron | twinChangeEvents |
    $content-encoding | UTF-8 |
    deviceId | ID van het apparaat |
    moduleId | ID van de module |
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

Alle voor gaande bewerkingen ondersteunen [optimistische gelijktijdigheid](iot-hub-devguide-device-twins.md#optimistic-concurrency) en vereisen de **ServiceConnect** -machtiging, zoals gedefinieerd in de [toegang tot IOT hub](iot-hub-devguide-security.md) artikel.

Naast deze bewerkingen kan de back-end van de oplossing een query uitvoeren op de module apparaatdubbels met behulp van de SQL-achtige [IOT hub query taal](iot-hub-devguide-query-language.md).

## <a name="module-operations"></a>Module bewerkingen

De module-App werkt op de module met behulp van de volgende atomische bewerkingen:

* De **module twee ophalen** Met deze bewerking wordt het module dubbele document (inclusief tags en gewenste en gerapporteerde systeem eigenschappen) voor de momenteel verbonden module geretourneerd.

* De **gerapporteerde eigenschappen zijn gedeeltelijk bijgewerkt**. Met deze bewerking wordt de gedeeltelijke update van de gerapporteerde eigenschappen van de momenteel verbonden module ingeschakeld. Deze bewerking maakt gebruik van dezelfde JSON-update-indeling als de back-end van de oplossing gebruikt voor een gedeeltelijke update van de gewenste eigenschappen.

* **Bekijk de gewenste eigenschappen**. De momenteel verbonden module kan ervoor kiezen om op de hoogte te worden gesteld van updates voor de gewenste eigenschappen wanneer deze zich voordoen. De module ontvangt dezelfde vorm van update (gedeeltelijke of volledige vervanging) die wordt uitgevoerd door de back-end van de oplossing.

Voor alle voor gaande bewerkingen is de machtiging **ModuleConnect** vereist, zoals gedefinieerd in de [toegang tot IOT hub](iot-hub-devguide-security.md) artikel.

De [sdk's van het Azure IOT-apparaat](iot-hub-devguide-sdks.md) maken het eenvoudig om de voor gaande bewerkingen uit vele talen en platforms te gebruiken.

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

## <a name="module-twin-size"></a>Dubbele grootte van module

IoT Hub dwingt een maximale grootte van 8 KB af voor de waarde van en `tags` een maximale grootte van 32 KB voor elke waarde van `properties/desired` en `properties/reported` . Deze totalen zijn exclusief van alleen-lezen elementen als `$etag` , `$version` en `$metadata/$lastUpdated` .

Dubbele grootte wordt als volgt berekend:

* IoT Hub cumulatieve berekeningen voor elke eigenschap in het JSON-document en voegt de lengte van de sleutel en waarde van de eigenschap toe.

* Eigenschaps sleutels worden beschouwd als UTF8-gecodeerde teken reeksen.

* Eenvoudige eigenschaps waarden worden beschouwd als UTF8-gecodeerde teken reeksen, numerieke waarden (8 bytes) of Boole-waarden (4 bytes).

* De grootte van door UTF8 gecodeerde teken reeksen wordt berekend door alle tekens te tellen, met uitzonde ring van UNICODE-besturings tekens (segmenten C0 en C1).

* Complexe eigenschaps waarden (geneste objecten) worden berekend op basis van de cumulatieve grootte van de eigenschaps sleutels en eigenschaps waarden die ze bevatten.

IoT Hub weigert een fout bij alle bewerkingen die de grootte van deze documenten boven de limiet verg Roten.

## <a name="module-twin-metadata"></a>Dubbele meta gegevens van module

IoT Hub onderhoudt de tijds tempel van de laatste update voor elk JSON-object in de module dubbele gewenste en gerapporteerde eigenschappen. De tijds tempels zijn in UTC en worden gecodeerd in de [iso8601](https://en.wikipedia.org/wiki/ISO_8601) -indeling `YYYY-MM-DDTHH:MM:SS.mmmZ` .
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
                    "sendFrequency": "5m",
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

De gewenste module en gerapporteerde eigenschappen hebben geen ETags, maar hebben een `$version` waarde die gegarandeerd incrementeel is. Net als bij een ETag kan de versie worden gebruikt door de update partij om consistentie van updates af te dwingen. Bijvoorbeeld een module-App voor een gerapporteerde eigenschap of de back-end van de oplossing voor een gewenste eigenschap.

Versies zijn ook handig wanneer een waarneem bare agent (zoals de module-app die de gewenste eigenschappen waarneemt), races moet afstemmen tussen het resultaat van een ophalen-bewerking en een update-melding. De stroom voor het opnieuw verbinden van het [apparaat](iot-hub-devguide-device-twins.md#device-reconnection-flow) biedt meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende IoT Hub zelf studies voor het uitproberen van de concepten die in dit artikel worden beschreven:

* [Aan de slag met IoT Hub module-identiteit en-module, met behulp van .NET-back-end en .NET-apparaat](iot-hub-csharp-csharp-module-twin-getstarted.md)
