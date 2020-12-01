---
title: Een upgrade uitvoeren van 2. x-Azure Monitor Application Insights java
description: Upgraden van Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355046"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgraden van Application Insights Java SDK 2. x

Als u Application Insights Java SDK 2. x al gebruikt in uw toepassing, hoeft u deze niet te verwijderen.
De Java 3,0-agent detecteert deze en legt elke aangepaste telemetrie die u verzendt via de Java SDK 2. x, vast en zorgt ervoor dat alle automatische verzamelingen die door de Java SDK 2. x worden uitgevoerd, worden onderdrukt om dubbele telemetrie te voor komen.

Als u Application Insights 2. x-agent gebruikt, moet u het `-javaagent:` JVMe ARG verwijderen dat wijst naar de 2. x-agent.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers en TelemetryProcessors

Java SDK 2. x TelemetryInitializers en TelemetryProcessors worden niet uitgevoerd wanneer de 3,0-agent wordt gebruikt.
Veel van de use-cases die eerder zijn vereist, kunnen worden opgelost in 3,0 door [aangepaste dimensies](./java-standalone-config.md#custom-dimensions) te configureren of [telemetrie-processors](./java-standalone-telemetry-processors.md)te configureren.

## <a name="multiple-applications-in-a-single-jvm"></a>Meerdere toepassingen in één JVM

Momenteel ondersteunt 3,0 slechts één [Connection String en rolnaam](./java-standalone-config.md#connection-string-and-role-name) per uitgevoerd proces. Met name u kunt niet meerdere Tomcat-web-apps in dezelfde Tomcat-implementatie gebruiken met andere verbindings reeksen of andere rolnaams.

## <a name="http-request-telemetry-names"></a>Telemetrie-namen van HTTP-aanvragen

De telemetrie-namen van HTTP-aanvragen in 3,0 zijn gewijzigd in het algemeen een betere samengevoegde weer gave in de Application Insights portal U/X.

Voor sommige toepassingen kunt u echter nog steeds de voor keur geven aan de geaggregeerde weer gave in de U/X die werd verschaft door de vorige telemetrie-namen, in welk geval u de preview-functie voor telemetrie-processors in 3,0 gebruikt om terug te keren naar de vorige namen.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>Als voor voegsel van de telemetrie-naam met de HTTP-methode ( `GET` , `POST` enzovoort):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>De naam van de telemetrie instellen op het volledige URL-pad

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
