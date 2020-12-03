---
title: Een upgrade uitvoeren van 2. x-Azure Monitor Application Insights java
description: Upgraden van Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: 9a0e8237d81428b1ecab95627fe106a563d2090c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532409"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Upgraden van Application Insights Java 2. x SDK

Als u Application Insights Java 2. x-SDK al gebruikt in uw toepassing, hoeft u deze niet te verwijderen.
De Java 3,0-agent detecteert deze en legt elke aangepaste telemetrie die u verzendt via de 2. x SDK, vast en onderdrukt alle automatische verzamelingen die worden uitgevoerd door de 2. x SDK om te voor komen dat de telemetrie wordt gedupliceerd.

Als u Application Insights 2. x-agent gebruikt, moet u het `-javaagent:` JVMe ARG verwijderen dat wijst naar de 2. x-agent.

In de rest van dit document worden de beperkingen en wijzigingen beschreven die u kunt tegen komen wanneer u een upgrade uitvoert van 2. x naar 3,0, evenals enkele tijdelijke oplossingen die u mogelijk kunt vinden.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers en TelemetryProcessors

De 2. x SDK TelemetryInitializers en TelemetryProcessors worden niet uitgevoerd wanneer de 3,0-agent wordt gebruikt.
Veel van de use-cases die eerder zijn vereist, kunnen worden opgelost in 3,0 door [aangepaste dimensies](./java-standalone-config.md#custom-dimensions) te configureren of [telemetrie-processors](./java-standalone-telemetry-processors.md)te configureren.

## <a name="multiple-applications-in-a-single-jvm"></a>Meerdere toepassingen in één JVM

Momenteel ondersteunt 3,0 slechts één [Connection String en rolnaam](./java-standalone-config.md#connection-string-and-role-name) per uitgevoerd proces. Met name u kunt niet meerdere Tomcat-web-apps in dezelfde Tomcat-implementatie gebruiken met andere verbindings reeksen of andere rolnaams.

## <a name="operation-names"></a>Bewerkings namen

Bewerkings namen in 3,0 zijn zodanig gewijzigd dat ze over het algemeen een betere samengevoegde weer gave hebben in de Application Insights portal U/X.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Bewerkings namen in 3,0":::

Voor sommige toepassingen kunt u echter nog steeds de voor keur geven aan de geaggregeerde weer gave in de U/X die is gegeven door de vorige bewerkings namen, in welk geval u de functie voor [telemetrie-processors](./java-standalone-telemetry-processors.md) (preview-versie) in 3,0 gebruikt om het vorige gedrag te repliceren.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Voor voegsel voor de naam van de bewerking met de HTTP-methode ( `GET` , `POST` enzovoort)

In de 2. x SDK werden de bewerkings namen voorafgegaan door de HTTP-methode ( `GET` , `POST` enzovoort), bijvoorbeeld

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Bewerkings namen, voorafgegaan door HTTP-methode":::

In het onderstaande fragment worden drie telemetrie-processors geconfigureerd die combi neren om het vorige gedrag te repliceren.
De telemetrie-processors voeren de volgende acties uit (op volg orde):

1. De eerste telemetrieprocessor is een bereik processor (is van het type `span` ), wat betekent dat deze van toepassing is op `requests` en `dependencies` .

   Dit komt overeen met een reeks die een kenmerk met de naam heeft `http.method` en een span naam heeft die begint met `/` .

   Vervolgens wordt deze span name geëxtraheerd naar een kenmerk met de naam `tempName` .

2. De tweede telemetrie-processor is ook een hoeveelheid processor.

   Dit komt overeen met een periode met een kenmerk met de naam `tempName` .

   Vervolgens wordt de bereik naam bijgewerkt door de twee kenmerken samen te voegen `http.method` `tempName` , gescheiden door een spatie.

3. De laatste telemetrieprocessor is een kenmerk processor (is van het type `attribute` ). Dit betekent dat dit van toepassing is op alle telemetrie die kenmerken hebben (momenteel `requests` `dependencies` en `traces` ).

   Dit komt overeen met een telemetrie met een kenmerk met de naam `tempName` .

   Vervolgens wordt het kenmerk met de naam verwijderd `tempName` zodat het niet wordt gerapporteerd als aangepaste dimensie.

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

### <a name="set-the-operation-name-to-the-full-path"></a>De naam van de bewerking instellen op het volledige pad

Daarnaast bevat de bewerkings namen in de 2. x SDK, in sommige gevallen, het volledige pad, bijvoorbeeld

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Bewerkings namen met volledig pad":::

In het onderstaande fragment worden 4 telemetriegegevens geconfigureerd die combi neren om het vorige gedrag te repliceren.
De telemetrie-processors voeren de volgende acties uit (op volg orde):

1. De eerste telemetrieprocessor is een bereik processor (is van het type `span` ), wat betekent dat deze van toepassing is op `requests` en `dependencies` .

   Dit komt overeen met een periode met een kenmerk met de naam `http.url` .

   Vervolgens wordt de naam van de span bijgewerkt met de `http.url` kenmerk waarde.

   Dit is het einde van het bestand, met uitzonde ring van `http.url` iets wat lijkt `http://host:port/path` op, en het is waarschijnlijk dat u alleen het `/path` onderdeel wilt.

2. De tweede telemetrie-processor is ook een hoeveelheid processor.

   Dit komt overeen met een wille keurige periode met een kenmerk met `http.url` de naam (met andere woorden, elke reeks waarmee de eerste processor overeenkomt).

   Vervolgens wordt het pad van de bereik naam geëxtraheerd naar een kenmerk met de naam `tempName` .

3. De derde telemetrie-processor is ook een hoeveelheid processor.

   Dit komt overeen met een periode met een kenmerk met de naam `tempPath` .

   Vervolgens wordt de bereik naam van het kenmerk bijgewerkt `tempPath` .

4. De laatste telemetrieprocessor is een kenmerk processor (is van het type `attribute` ). Dit betekent dat dit van toepassing is op alle telemetrie die kenmerken hebben (momenteel `requests` `dependencies` en `traces` ).

   Dit komt overeen met een telemetrie met een kenmerk met de naam `tempPath` .

   Vervolgens wordt het kenmerk met de naam verwijderd `tempPath` zodat het niet wordt gerapporteerd als aangepaste dimensie.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
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

## <a name="dependency-names"></a>Namen van afhankelijkheden

Afhankelijkheids namen in 3,0 zijn ook gewijzigd, zodat u over het algemeen een beter geaggregeerde weer gave kunt maken in de Application Insights portal U/X.

Het is ook mogelijk dat u voor sommige toepassingen de voor keur geeft aan de geaggregeerde weer gave in de U/X die werd verschaft door de vorige namen van afhankelijkheden, in welk geval u vergelijk bare technieken kunt gebruiken om het vorige gedrag te repliceren.

## <a name="operation-name-on-dependencies"></a>Bewerkings naam op afhankelijkheden

Eerder in de 2. x SDK werd de naam van de bewerking van de telemetrie van de aanvraag ook ingesteld op de telemetrie van de afhankelijkheid.
Application Insights Java 3,0 niet langer de bewerkings naam van de telemetrie van de afhankelijkheid invult.
Als u de naam van de bewerking wilt weer geven voor de aanvraag die het bovenliggende is van de telemetrie van de afhankelijkheid, kunt u een Kusto-query (logboek registratie) schrijven om een koppeling te maken tussen de afhankelijkheids tabel en de aanvraag tabel.
