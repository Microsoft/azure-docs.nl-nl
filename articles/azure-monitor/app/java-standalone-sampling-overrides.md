---
title: Steek proeven van onderdrukkingen (preview)-Azure Monitor Application Insights voor Java
description: Meer informatie over het configureren van steekproef onderdrukkingen in Azure Monitor Application Insights voor Java.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 17979bd548ca0d7b704ebdeb4d060bf35973b319
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024143"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Steek proeven van onderdrukkingen (preview)-Azure Monitor Application Insights voor Java

> [!NOTE]
> De functie voor het controleren van de steek proeven is in preview, vanaf 3.0.3-BETA. 2.

Met bemonsterings onderdrukkingen kunt u het [standaard sampling percentage](./java-standalone-config.md#sampling)overschrijven, bijvoorbeeld:
 * Stel het steekproef percentage in op 0 (of een kleine waarde) voor slechte status controles.
 * Stel het steekproef percentage in op 0 (of een kleine waarde) voor ruis afhankelijke afhankelijkheids aanroepen.
 * Stel het steekproef percentage in op 100 voor een belang rijk aanvraag type (bijvoorbeeld `/login` ), hoewel u de standaard steekproef hebt geconfigureerd voor iets lager.

## <a name="terminology"></a>Terminologie

Voordat u meer informatie krijgt over steek proeven, moet u de term *span* begrijpen. Een reeks is een algemene term voor:

* Een binnenkomende aanvraag.
* Een uitgaande afhankelijkheid (bijvoorbeeld een externe aanroep naar een andere service).
* Een in-process afhankelijkheid (werk dat wordt gedaan door subonderdelen van de service).

Voor het bemonsteren van onderdrukkingen zijn deze onderdelen van het bereik belang rijk:

* Kenmerken

De span-kenmerken vertegenwoordigen zowel de standaard eigenschappen als de aangepaste eigenschap van een bepaalde aanvraag of afhankelijkheid.

## <a name="getting-started"></a>Aan de slag

Maak een configuratie bestand met de naam *applicationinsights.jsop* om te beginnen. Sla het bestand op in dezelfde map als *applicationinsights-agent- \* . jar*. Gebruik de volgende sjabloon.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Uitleg

Wanneer een reeks is gestart, worden de kenmerken die op die tijd aanwezig zijn, gebruikt om te controleren of een van de steek proeven wordt vervangen.

Als een van de steek proeven wordt vervangen, wordt het steekproef percentage gebruikt om te bepalen of de reeks moet worden gesampled of niet.

Alleen de eerste vervangende steek proef die overeenkomt, wordt gebruikt.

Als er geen steek proeven worden gevonden:

* Als dit de eerste reeks in de tracering is, wordt het [standaard sampling-percentage](./java-standalone-config.md#sampling) gebruikt.
* Als dit niet de eerste reeks in de tracering is, wordt de bovenliggende steekproef beslissing gebruikt.

> [!IMPORTANT]
> Als er een beslissing is genomen om geen bereik te verzamelen, worden ook alle downstream-reeksen niet verzameld, zelfs niet als er steek proeven worden overschreven die overeenkomen met de downstream-reeks.
> Dit gedrag is nood zakelijk omdat andere verbroken traceringen resulteren in het verzamelen van downstream-reeksen, maar die worden geparenteerd tot niet-verzamelde reeksen.

> [!NOTE]
> De beoordelings beslissing is gebaseerd op het hashen van de traceId (ook wel bekend als operationId) naar een getal tussen 0 en 100, en die hash wordt vergeleken met het steekproef percentage.
> Aangezien alle interacties in een bepaalde tracering dezelfde traceId hebben, hebben ze dezelfde hash, waardoor de steekproef beslissing consistent is voor het hele spoor.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Voor beeld: het verzamelen van telemetrie onderdrukken voor status controles

Hiermee wordt het verzamelen van telemetrie voor alle aanvragen onderdrukt `/health-checks` .

Hiermee wordt ook het verzamelen van downstream-reeksen (afhankelijkheden) die normaal gesp roken worden verzameld, onderdrukt `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Voor beeld: onderdrukt het verzamelen van telemetrie voor een afhankelijkheids oproep voor een enkele ruis

Hiermee wordt het verzamelen van telemetrie voor alle `GET my-noisy-key` redis-aanroepen onderdrukt.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Voor beeld: 100% van de telemetrie voor een belang rijk aanvraag type verzamelen

Hiermee wordt 100% van de telemetrie verzameld voor `/login` .

Aangezien downstream-reeksen (afhankelijkheden) de beoordelings beslissing van het bovenliggende element eerbiedigen (waarbij elke steek proef voor het downstream-bereik wordt overschreven), worden deze ook verzameld voor alle '/login-aanvragen.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Algemene span kenmerken

In deze sectie vindt u enkele algemene span kenmerken die door het gebruik van onderdrukkingen kunnen worden gebruikt.

### <a name="http-spans"></a>HTTP-reeksen

| Kenmerk  | Type | Description | 
|---|---|---|
| `http.method` | tekenreeks | HTTP-aanvraag methode.|
| `http.url` | tekenreeks | Volledige URL van de HTTP-aanvraag in het formulier `scheme://host[:port]/path?query[#fragment]` . Het fragment wordt meestal niet via HTTP verzonden. Maar als het fragment bekend is, moet het worden opgenomen.|
| `http.status_code` | getal | [Status code van http-antwoord](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | tekenreeks | Type HTTP-protocol. |
| `http.user_agent` | tekenreeks | Waarde van de [http-](https://tools.ietf.org/html/rfc7231#section-5.5.3) header van de agent die door de client is verzonden. |

### <a name="jdbc-spans"></a>JDBC-reeksen

| Kenmerk  | Type | Description  |
|---|---|---|
| `db.system` | tekenreeks | Id voor het Database Management System DBMS-product dat wordt gebruikt. |
| `db.connection_string` | tekenreeks | Verbindings reeks die wordt gebruikt om verbinding te maken met de data base. Het is raadzaam om Inge sloten referenties te verwijderen.|
| `db.user` | tekenreeks | Gebruikers naam voor toegang tot de data base. |
| `db.name` | tekenreeks | De teken reeks die wordt gebruikt voor het rapporteren van de naam van de data base die wordt geopend. Voor opdrachten waarmee de data base wordt overgeschakeld, moet deze teken reeks worden ingesteld op de doel database, zelfs als de opdracht mislukt.|
| `db.statement` | tekenreeks | De data base-instructie die wordt uitgevoerd.|
