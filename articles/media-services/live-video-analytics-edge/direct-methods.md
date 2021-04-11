---
title: Gebruik directe methoden in live video Analytics op IoT Edge-Azure
description: Met live video Analytics op IoT Edge worden verschillende directe methoden getoond. De directe methoden zijn gebaseerd op de conventies die in dit onderwerp worden beschreven.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56fbf565af45a9b3877ff8d6c48a56713ddc3da6
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076986"
---
# <a name="direct-methods"></a>Directe methoden

Live video Analytics op IoT Edge biedt verschillende directe methoden die kunnen worden aangeroepen vanuit IoT Hub. Directe methoden vertegenwoordigen een aanvraag/antwoord-interactie met een apparaat dat vergelijkbaar is met een HTTP-aanroep wanneer deze slagen of direct mislukken (na een door de gebruiker opgegeven time-out). Deze aanpak is nuttig voor scenario's waarbij het verloop van de onmiddellijke actie verschillend is, afhankelijk van het feit of het apparaat in staat was om te reageren. Zie voor meer informatie [direct methoden begrijpen en aanroepen vanuit IOT hub](../../iot-hub/iot-hub-devguide-direct-methods.md).

In dit onderwerp worden deze methoden en conventies beschreven.

## <a name="conventions"></a>Conventies

De directe methoden zijn gebaseerd op de volgende conventies:

1. Voor directe methoden is een versie die primair is opgegeven. SECUNDAIRE indeling (zoals weer gegeven in het volgende voor beeld):

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. Een bepaalde versie van live video Analytics op IoT Edge module biedt ondersteuning voor alle directe methoden die de huidige versie hebben. Module versie 1,3 biedt bijvoorbeeld ondersteuning voor directe methoden met versies 1,3, 1,2, 1,1 en 1,0.
3. Alle directe methoden zijn synchroon.
4. Fout resultaten volgen OData-fout schema.
5. Namen moeten de volgende beperkingen naleven:
    
    * Alleen alfanumerieke tekens en streepjes als deze niet beginnen en eindigen met een streepje
    * Geen spaties
    * Maxi maal 32 tekens

### <a name="example"></a>Voorbeeld

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Fout codes op het hoogste niveau     

|Status |Code   |Bericht|
|---|---|---|
|400|   BadRequest| Aanvraag is niet geldig|
|400|   InvalidResource|    De resource is niet geldig|
|400|   InvalidVersion| De API-versie is ongeldig|
|402|   ConnectivityRequired    |De Edge-module vereist dat de Cloud connectiviteit goed werkt.|
|404|   NotFound    |Kan de resource niet vinden|
|409|   Conflict|   Bewerkings conflict|
|500|   InternalServerError|    Interne serverfout|
|503|   ServerBusy| Server bezet|

### <a name="detailed-error-codes"></a>Gedetailleerde fout codes

Gedetailleerde validatie fout, zoals validaties van de module Graph, worden toegevoegd als fout Details:

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Status|    Gedetailleerde code   |Description|
|---|---|---|
|400|   GraphValidationError|   Algemene grafiek fouten, zoals cycli of partitioneren, enzovoort.|
|400|   ModuleValidationError|  Module-specifieke validatie fouten.|
|409|   GraphTopologyInUse| Er wordt nog steeds naar de grafiek topologie verwezen door een of meer exemplaren van de grafiek.|
|409|   OperationNotAllowedInState| De aangevraagde bewerking kan niet worden uitgevoerd in de huidige status.|
|409|   ResourceValidationError|    Resource waarnaar wordt verwezen (voor beeld: activa) heeft geen geldige status.|

## <a name="details"></a>Details  

### <a name="graphtopologyget"></a>GraphTopologyGet

Deze directe methode haalt een topologie met één grafiek op.

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Statuscodes

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| Entiteit gevonden | 200 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| Kan de entiteit niet vinden | 404 |  |
| Algemene serverfouten | 500-bereik |  |

### <a name="graphtopologyset"></a>GraphTopologySet

Hiermee wordt één topologie gemaakt als er geen bestaande is met de opgegeven naam of updates en een bestaande met dezelfde naam.

Belangrijkste aspecten:

* De topologie kan vrij worden bijgewerkt, maar er is geen grafiek die ernaar verwijst.
* De topologie kan gratis worden bijgewerkt als alle verwijzende grafieken zo lang worden gedeactiveerd:

    * Nieuwe toegevoegde para meters hebben standaard waarden
    * Er wordt in geen enkele grafiek verwezen naar verwijderde para meters
* Topologie-updates zijn niet toegestaan als er actieve grafieken zijn

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Statuscodes

|Voorwaarde  |Statuscode    |Gedetailleerde fout code|
|---|---|---|
Bestaande entiteit bijgewerkt |200|   N.v.t.|
Nieuwe entiteit gemaakt  |201|   N.v.t.|
Algemene gebruikersfouten |400-bereik  ||
Grafiek validatie fouten |400    |GraphValidationError|
Module validatie fouten|   400 |ModuleValidationError|
Algemene serverfouten   |500-bereik  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

Hiermee verwijdert u een topologie met één grafiek.

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Statuscodes

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| Entiteit verwijderd | 200 | N.v.t. |
| Kan de entiteit niet vinden | 204 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| Er wordt naar de grafiek topologie verwezen door een of meer grafiek exemplaren | 409 | GraphTopologyInUse |
| Algemene serverfouten | 500-bereik |  |

### <a name="graphtopologylist"></a>GraphTopologyList

Hiermee wordt een lijst opgehaald met alle grafiek-topologieën die overeenkomen met de filter criteria.

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Ondersteuning voor filters

|Bewerking |Veld (en)    |Operators|
|---|---|---|
|$orderby|naam  |ASC|


#### <a name="status-codes"></a>Statuscodes

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| Geslaagd | 200 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| Algemene serverfouten | 500-bereik |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

Hiermee wordt één exemplaar van een grafiek opgehaald:

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Statuscodes

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| Entiteit gevonden | 200 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| Kan de entiteit niet vinden | 404 |  |
| Algemene serverfouten | 500-bereik |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

Hiermee maakt u een enkel Graph-exemplaar als er geen bestaat met de opgegeven naam of updates, en een bestaande met dezelfde naam.

Belangrijkste aspecten:

* Graph-exemplaar kan vrij worden bijgewerkt terwijl de status ' gedeactiveerd ' is.

    * De grafiek wordt opnieuw gevalideerd bij elke bijgewerkte.
* Updates voor Graph-exemplaren zijn deels beperkt terwijl de grafiek niet de status ' inactive ' heeft.
* Updates voor Graph-instanties zijn niet toegestaan voor actieve grafieken.

#### <a name="request"></a>Aanvraag

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Antwoord

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Statuscodes

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| Bestaande entiteit bijgewerkt | 200 | N.v.t. |
| Nieuwe entiteit gemaakt | 201 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| Grafiek validatie fouten | 400 | GraphValidationError |
| Module validatie fouten | 400 | ModuleValidationError |
| Bron validatie fouten | 409 | ResourceValidationError |
| Algemene serverfouten | 500-bereik |  | 

### <a name="graphinstancedelete"></a>GraphInstanceDelete

Hiermee verwijdert u één exemplaar van de grafiek.

Belangrijkste aspecten:

* Alleen gedeactiveerde grafieken kunnen worden verwijderd.

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Statuscodes

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| De grafiek is verwijderd | 200 | N.v.t. |
| De grafiek is niet gevonden | 204 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| De grafiek heeft niet de status ' Stopped ' | 409 | OperationNotAllowedInState |
| Algemene serverfouten | 500-bereik |  |

### <a name="graphinstancelist"></a>GraphInstanceList

Dit is vergelijkbaar met GraphTopologyList. Hiermee kunt u de grafiek instanties opsommen.
Hiermee wordt een lijst opgehaald met alle grafiek exemplaren die overeenkomen met de filter criteria.

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Ondersteuning voor filters

|Bewerking  |   Veld (en)|   Operators|
|---|---|---|
|$orderby|  naam|   ASC|

#### <a name="status-codes"></a>Statuscodes

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| Geslaagd | 200 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| Algemene serverfouten | 500-bereik |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

Hiermee wordt één exemplaar van een grafiek geactiveerd. 

Belang rijke aspecten

* Methode retourneert alleen wanneer grafiek wordt geactiveerd 
* Graph gaat ervan uit dat de status wordt geactiveerd tijdens het activeren.

    * Een lijst/Get-bewerking in de grafiek zou de grafiek met de juiste status retour neren.
* Idempotentie

    * Het starten van een grafiek met de status ' activeren ' gedraagt zich op dezelfde manier als wanneer de grafiek is gedeactiveerd (dat wil zeggen: aanroep blokken totdat de grafiek is geactiveerd)
    * Het activeren van een grafiek op status actief retourneert direct.

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Statuscodes

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| De grafiek is geactiveerd | 200 | N.v.t. |
| Nieuwe entiteit gemaakt | 201 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| Module validatie fouten | 400 | ModuleValidationError |
| Bron validatie fouten | 409 | ResourceValidationError |
| De grafiek wordt gedeactiveerd | 409 | OperationNotAllowedInState |
| Algemene serverfouten | 500-bereik |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

Hiermee wordt één exemplaar van een grafiek gedeactiveerd. Als u een grafiek deactiveert, deactiveert u de media verwerking zonder problemen en zorgt u ervoor dat alle gebeurtenissen en media die op de rand worden opgeslagen, worden doorgevoerd in de Cloud, indien van toepassing.

Belangrijkste aspecten:

* Methode retourneert alleen wanneer grafiek wordt gedeactiveerd
* In de grafiek wordt uitgegaan van de status ' deactiveren ' tijdens het deactiveren.

    * Een lijst/Get-bewerking in de grafiek zou de grafiek met de juiste status retour neren.
    * Stop alleen als alle media zijn geüpload naar de Cloud.
* Idempotentie

    * Het deactiveren van een grafiek bij de status deactiveren gedraagt zich op dezelfde manier als wanneer de grafiek is gedeactiveerd (dat wil zeggen: aanroep blokken totdat de grafiek is gedeactiveerd)
    * Het deactiveren van een grafiek op de status niet actief retourneert direct.

#### <a name="request"></a>Aanvraag

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Antwoord

```
{
    "status": 200,
    "payload": null
}
```

| Voorwaarde | Statuscode | Gedetailleerde fout code |
|--|--|--|
| De grafiek is geactiveerd | 200 | N.v.t. |
| Nieuwe entiteit gemaakt | 201 | N.v.t. |
| Algemene gebruikersfouten | 400-bereik |  |
| De status van de grafiek wordt geactiveerd | 409 | OperationNotAllowedInState |
| Algemene serverfouten | 500-bereik |  |

## <a name="next-steps"></a>Volgende stappen

[Configuratieschema van moduledubbel](module-twin-configuration-schema.md)
