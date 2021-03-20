---
title: OPC-Uitgever configureren-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u OPC Publisher kunt configureren voor het opgeven van gegevens wijzigingen van de OPC UA-knoop punten, OPC UA-gebeurtenissen die moeten worden gepubliceerd en ook de telemetrie-indeling.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 405fa04a3b6af7f0c87888aa48aba9bdffeaf282
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366952"
---
# <a name="configure-opc-publisher"></a>OPC Publisher configureren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

U kunt OPC Publisher configureren om het volgende op te geven:

- De gegevens van het OPC UA-knoop punt worden gewijzigd in publiceren.
- De OPC UA-gebeurtenissen die moeten worden gepubliceerd.
- De telemetrie-indeling.

U kunt OPC Publisher configureren met configuratie bestanden of met behulp van methode aanroepen.

## <a name="use-configuration-files"></a>Configuratiebestanden gebruiken

In deze sectie worden de opties beschreven voor het configureren van OPC UA-knoop punten publiceren met configuratie bestanden.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Een configuratie bestand gebruiken om wijzigingen in de publicatie gegevens te configureren

De eenvoudigste manier om de OPC UA-knoop punten te configureren om te publiceren is met een configuratie bestand. De indeling van het configuratie bestand wordt beschreven in [publishednodes.js](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) in de opslag plaats.

De syntaxis van het configuratie bestand is gewijzigd na verloop van tijd. OPC Publisher leest nog steeds oude indelingen, maar zet ze om in de meest recente indeling wanneer de configuratie persistent wordt gemaakt.

In het volgende voor beeld ziet u de indeling van het configuratie bestand:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Een configuratie bestand gebruiken voor het configureren van publicatie gebeurtenissen

Als u OPC UA-gebeurtenissen wilt publiceren, gebruikt u hetzelfde configuratie bestand als voor gegevens wijzigingen.

In het volgende voor beeld ziet u hoe u een publicatie configureert voor gebeurtenissen die worden gegenereerd door de [SimpleEvents-server](https://github.com/OPCFoundation/UA-.NETStandard-Samples/tree/master/Workshop/SimpleEvents/Server). De SimpleEvents-server kan worden gevonden in de [OPC Foundation-opslag plaats](https://github.com/OPCFoundation/UA-.NETStandard-Samples) :

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Methode aanroepen gebruiken

In deze sectie wordt beschreven hoe u de methode aanroepen kunt gebruiken om OPC Publisher te configureren.

### <a name="configure-using-opc-ua-method-calls"></a>Configureren met behulp van OPC UA-methode aanroepen

OPC Publisher bevat een OPC UA-server, die toegankelijk is via poort 62222. Als de hostnaam **Uitgever** is, is de URI van het eind punt: `opc.tcp://publisher:62222/UA/Publisher` .

Met dit eind punt worden de volgende vier methoden getoond:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT-HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Configureren met IoT Hub directe methode aanroepen

OPC Publisher implementeert de volgende IoT Hub directe methode aanroepen:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

De indeling van de JSON-payload van de methode-aanvraag en-antwoorden worden gedefinieerd in [opcpublisher/HubMethodModel. cs](https://github.com/Azure/iot-edge-opc-publisher/tree/master/opcpublisher).

Als u een onbekende methode aanroept in de module, reageert deze met een teken reeks die aangeeft dat de methode niet is geïmplementeerd. U kunt een onbekende methode aanroepen als een manier om de module te pingen.

### <a name="configure-username-and-password-for-authentication"></a>Gebruikers naam en wacht woord voor authenticatie configureren

De verificatie modus kan worden ingesteld via een IoT Hub directe methode aanroepen. De payload moet de eigenschap **OpcAuthenticationMode** en de gebruikers naam en het wacht woord bevatten:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Het wacht woord wordt versleuteld door de IoT Hub workload-client en opgeslagen in de configuratie van de uitgever. Als u de verificatie wilt wijzigen in anoniem, gebruikt u de methode met de volgende Payload:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Als de eigenschap **OpcAuthenticationMode** niet is ingesteld in de payload, blijven de verificatie-instellingen ongewijzigd in de configuratie.

## <a name="configure-telemetry-publishing"></a>Telemetrie-publicatie configureren

Als de OPC-uitgever een melding ontvangt van een waarde die in een gepubliceerd knoop punt wordt gewijzigd, wordt een bericht in JSON-indeling gegenereerd dat naar IoT Hub wordt verzonden.

U kunt de inhoud van dit bericht in JSON-indeling configureren met behulp van een configuratie bestand. Als er geen configuratie bestand met de optie is opgegeven `--tc` , wordt er een standaard configuratie gebruikt die compatibel is met de [Connected Factory Solution Accelerator](https://github.com/Azure/azure-iot-connected-factory).

Als OPC Publisher is geconfigureerd voor batch berichten, worden ze verzonden als een geldige JSON-matrix.

De telemetrie is afgeleid van de volgende bronnen:

- De OPC-knooppunt configuratie voor het knoop punt
- Het **MonitoredItem** -object van de OPC UA-stack waarvoor OPC-uitgever een melding heeft ontvangen.
- Het argument dat aan deze melding is door gegeven, waarmee details over de wijziging van de gegevens waarde worden verstrekt.

De telemetrie die in het bericht JSON-indeling wordt geplaatst, is een selectie van belang rijke eigenschappen van deze objecten. Als u meer eigenschappen nodig hebt, moet u de OPC Publisher code base wijzigen.

De syntaxis van het configuratie bestand is als volgt:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is omitted (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Publisher kunt configureren, kunt u het beste de volgende stap leren hoe u [OPC Publisher kunt uitvoeren](howto-opc-publisher-run.md).
