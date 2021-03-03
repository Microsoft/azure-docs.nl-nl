---
title: IoT Edge-logboeken ophalen-Azure IoT Edge
description: IoT Edge het ophalen van de module Logboeken en uploaden naar Azure Blob Storage.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 65d95533e4cff02866111881f036225f9f544852
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719012"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Logboeken ophalen uit IoT Edge-implementaties

U kunt Logboeken ophalen uit uw IoT Edge-implementaties zonder dat u fysieke of SSH-toegang tot het apparaat nodig hebt met behulp van de directe methoden die zijn opgenomen in de module IoT Edge agent. Directe methoden worden geïmplementeerd op het apparaat en kunnen vervolgens vanuit de cloud worden aangeroepen. De IoT Edge-agent bevat directe methoden waarmee u uw IoT Edge apparaten op afstand kunt bewaken en beheren. De directe methoden die in dit artikel worden beschreven, zijn in het algemeen beschikbaar in de 1.0.10-release.

Zie voor meer informatie over directe methoden, hoe u deze kunt gebruiken en hoe u deze in uw eigen modules implementeert, [direct methoden begrijpen en aanroepen vanuit IOT hub](../iot-hub/iot-hub-devguide-direct-methods.md).

De namen van deze directe methoden worden hoofdletter gevoelig afgehandeld.

## <a name="recommended-logging-format"></a>Aanbevolen logboek registratie-indeling

Hoewel dit niet vereist is, voor de beste compatibiliteit met deze functie is de aanbevolen logboek indeling:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Timestamp}` moet worden opgemaakt als `yyyy-MM-dd hh:mm:ss.fff zzz` en `{Log Level}` moet volgen op de onderstaande tabel, die de ernst niveaus van de [Ernst code in de syslog-standaard](https://wikipedia.org/wiki/Syslog#Severity_level)afleidt.

| Waarde | Ernst |
|-|-|
| 0 | Urgentie |
| 1 | Waarschuwing |
| 2 | Kritiek |
| 3 | Fout |
| 4 | Waarschuwing |
| 5 | Kennisgeving |
| 6 | Informatief |
| 7 | Fouten opsporen |

De [logger-klasse in IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) fungeert als een canonieke implementatie.

## <a name="retrieve-module-logs"></a>Module Logboeken ophalen

Gebruik de **GetModuleLogs** directe methode om de logboeken van een IOT Edge module op te halen.

Met deze methode wordt een JSON-nettolading met het volgende schema geaccepteerd:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Naam | Type | Beschrijving |
|-|-|-|
| schemaVersion | tekenreeks | Ingesteld op `1.0` |
| vermeldingen | JSON-matrix | Een matrix met `id` en `filter` Tuples. |
| Id | tekenreeks | Een reguliere expressie die de module naam levert. Dit kan overeenkomen met meerdere modules op een edge-apparaat. Indeling van [reguliere .net-expressies](/dotnet/standard/base-types/regular-expressions) wordt verwacht. |
| filter | JSON-sectie | Logboek filters die moeten worden toegepast op de modules die overeenkomen met de `id` reguliere expressie in de tuple. |
| 13,Arabische | geheel getal | Het aantal logboek regels in het verleden dat moet worden opgehaald vanaf de laatste. Beschrijving. |
| moment | tekenreeks | Als duur (1 d, 90 m, 2 dagen 3 uur 2 minuten), rfc3339-tijds tempel of UNIX-Time Stamp, worden er sinds dit tijdstip alleen logboeken geretourneerd.  Als beide `tail` en `since` worden opgegeven, worden de logboeken opgehaald met de `since` waarde eerst. Vervolgens wordt de `tail` waarde toegepast op het resultaat en wordt het uiteindelijke resultaat geretourneerd. Beschrijving. |
| totdat | tekenreeks | Alleen logboeken retour neren vóór de opgegeven tijd, als rfc3339 Time Stamp, UNIX time stamp of duration (1 d, 90 m, 2 dagen 3 uur 2 minuten). Beschrijving. |
| logboek niveau | geheel getal | Filter logboek regels die kleiner zijn dan of gelijk zijn aan het opgegeven logboek niveau. Logboek regels moeten de aanbevolen logboek registratie-indeling volgen en standaard [syslog-urgentie niveau](https://en.wikipedia.org/wiki/Syslog#Severity_level) gebruiken. Beschrijving. |
| reguliere | tekenreeks | Filter logboek regels die inhoud hebben die overeenkomt met de opgegeven reguliere expressie met behulp van de notatie voor [reguliere expressies van .net](/dotnet/standard/base-types/regular-expressions) . Beschrijving. |
| gecodeerd | tekenreeks | `gzip` of `none`. De standaardinstelling is `none`. |
| Invoer | tekenreeks | `json` of `text`. De standaardinstelling is `text`. |

> [!NOTE]
> Als de logboek inhoud de limiet voor de reactie grootte van directe methoden overschrijdt, die momenteel 128 KB is, retourneert het antwoord een fout.

Een geslaagde logboek registratie retourneert een **' status ': 200** gevolgd door een Payload die de logboeken bevat die zijn opgehaald uit de module, gefilterd op de instellingen die u in uw aanvraag hebt opgegeven.

Bijvoorbeeld:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

Roep in de Azure Portal de methode met de naam van de methode `GetModuleLogs` en de volgende JSON-nettolading:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Directe methode ' GetModuleLogs ' aanroepen in Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

U kunt de CLI-uitvoer ook door sluizen naar Linux-hulpprogram ma's, zoals [gzip](https://en.wikipedia.org/wiki/Gzip), om een gecomprimeerd antwoord te verwerken. Bijvoorbeeld:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Module Logboeken uploaden

Gebruik de **UploadModuleLogs** direct-methode om de aangevraagde logboeken te verzenden naar een opgegeven Azure Blob Storage-container.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Als u logboeken wilt uploaden vanaf een apparaat achter een gateway-apparaat, moet u de [modules API-proxy en Blob Storage](how-to-configure-api-proxy-module.md) hebben geconfigureerd op het apparaat voor de bovenste laag. Deze modules sturen de logboeken van uw lagere laag-apparaat via uw gateway apparaat naar uw opslag in de Cloud.

::: moniker-end

Deze methode accepteert een JSON-nettolading vergelijkbaar met **GetModuleLogs**, met toevoeging van de sleutel ' sasUrl ':

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Naam | Type | Beschrijving |
|-|-|-|
| sasURL | teken reeks (URI) | [Shared Access Signature URL met schrijf toegang tot de Azure Blob Storage-container](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer). |

Een geslaagde aanvraag voor het uploaden van Logboeken retourneert een **' status ': 200** gevolgd door een Payload met het volgende schema:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Naam | Type | Beschrijving |
|-|-|-|
| status | tekenreeks | Een van `NotStarted` ,,, `Running` `Completed` `Failed` of `Unknown` . |
| message | tekenreeks | Bericht als fout, lege teken reeks. |
| correlationId | tekenreeks   | ID voor het opvragen van de status van de upload aanvraag. |

Bijvoorbeeld:

Met de volgende aanroep worden de laatste 100 logboek regels van alle modules geüpload, in de gecomprimeerde JSON-indeling:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

Met de volgende aanroep worden de laatste 100 logboek regels van edgeAgent en edgeHub met de laatste 1000 logboek regels uit de Temp sensor-module in niet-gecomprimeerde tekst indeling geüpload:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

In de Azure Portal roept u de methode met de naam van de methode `UploadModuleLogs` en de volgende JSON-nettolading aan nadat u de sasURL hebt gevuld met uw gegevens:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Directe methode ' UploadModuleLogs ' aanroepen in Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Diagnostische gegevens over ondersteunings bundel uploaden

Gebruik de **UploadSupportBundle** directe methode om een zip-bestand van IOT Edge module Logboeken te bundelen en te uploaden naar een beschik bare Azure Blob Storage-container. Met deze directe methode voert [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) u de opdracht op uw IOT edge-apparaat uit om de logboeken op te halen.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Als u logboeken wilt uploaden vanaf een apparaat achter een gateway-apparaat, moet u de [modules API-proxy en Blob Storage](how-to-configure-api-proxy-module.md) hebben geconfigureerd op het apparaat voor de bovenste laag. Deze modules sturen de logboeken van uw lagere laag-apparaat via uw gateway apparaat naar uw opslag in de Cloud.

::: moniker-end

Met deze methode wordt een JSON-nettolading met het volgende schema geaccepteerd:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Naam | Type | Beschrijving |
|-|-|-|
| schemaVersion | tekenreeks | Ingesteld op `1.0` |
| sasURL | teken reeks (URI) | [Shared Access Signature URL met schrijf toegang tot de Azure Blob Storage-container](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| moment | tekenreeks | Als duur (1 d, 90 m, 2 dagen 3 uur 2 minuten), rfc3339-tijds tempel of UNIX-Time Stamp, worden er sinds dit tijdstip alleen logboeken geretourneerd. Beschrijving. |
| totdat | tekenreeks | Alleen logboeken retour neren vóór de opgegeven tijd, als rfc3339 Time Stamp, UNIX time stamp of duration (1 d, 90 m, 2 dagen 3 uur 2 minuten). Beschrijving. |
| edgeRuntimeOnly | booleaans | Indien waar, worden alleen logboeken geretourneerd van de Edge-agent, Edge-hub en de Edge Security daemon. Standaard: onwaar.  Beschrijving. |

> [!IMPORTANT]
> IoT Edge-ondersteunings bundel kan persoons gegevens bevatten.

Een geslaagde aanvraag voor het uploaden van Logboeken retourneert een **' status ': 200** gevolgd door een Payload met hetzelfde schema als het **UploadModuleLogs** -antwoord:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Naam | Type | Beschrijving |
|-|-|-|
| status | tekenreeks | Een van `NotStarted` ,,, `Running` `Completed` `Failed` of `Unknown` . |
| message | tekenreeks | Bericht als fout, lege teken reeks. |
| correlationId | tekenreeks   | ID voor het opvragen van de status van de upload aanvraag. |

Bijvoorbeeld:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

In de Azure Portal roept u de methode met de naam van de methode `UploadSupportBundle` en de volgende JSON-nettolading aan nadat u de sasURL hebt gevuld met uw gegevens:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Directe methode ' UploadSupportBundle ' aanroepen in Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Status van upload aanvragen ophalen

Gebruik de **GetTaskStatus** directe methode om de status van een aanvraag voor het uploaden van Logboeken op te vragen. De **GetTaskStatus** -aanvraag lading maakt gebruik `correlationId` van de aanvraag van de upload Logboeken om de status van de taak op te halen. De `correlationId` wordt geretourneerd als reactie op de aanroep van de **UploadModuleLogs** direct-methode.

Met deze methode wordt een JSON-nettolading met het volgende schema geaccepteerd:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

Een geslaagde aanvraag voor het uploaden van Logboeken retourneert een **' status ': 200** gevolgd door een Payload met hetzelfde schema als het **UploadModuleLogs** -antwoord:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Naam | Type | Beschrijving |
|-|-|-|
| status | tekenreeks | Een van `NotStarted` ,,, `Running` `Completed` `Failed` of `Unknown` . |
| message | tekenreeks | Bericht als fout, lege teken reeks. |
| correlationId | tekenreeks   | ID voor het opvragen van de status van de upload aanvraag. |

Bijvoorbeeld:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

In de Azure Portal roept u de methode met de naam van de methode `GetTaskStatus` en de volgende JSON-nettolading aan nadat u de GUID hebt gevuld met uw gegevens:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Directe methode ' GetTaskStatus ' aanroepen in Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Volgende stappen

[Eigenschappen van de IoT Edge agent en IoT Edge hub-module apparaatdubbels](module-edgeagent-edgehub.md)
