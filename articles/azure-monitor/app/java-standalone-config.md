---
title: Configuratieopties - Azure Monitor Application Insights voor Java
description: Een Azure Monitor Application Insights voor Java configureren
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b78aaa659598e6eb58841c5cef0c209daaced5e0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811972"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Configuratieopties - Azure Monitor Application Insights voor Java

> [!WARNING]
> **Als u een upgrade van 3.0 Preview wilt uitvoeren**
>
> Bekijk alle onderstaande configuratieopties zorgvuldig, omdat de json-structuur volledig is gewijzigd, naast de bestandsnaam zelf, die allemaal kleine letters bevat.

## <a name="connection-string-and-role-name"></a>Verbindingsreeks en rolnaam

Verbindingsreeks en rolnaam zijn de meest voorkomende instellingen die nodig zijn om aan de slag te gaan:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

De connection string is vereist en de rolnaam is belangrijk wanneer u gegevens vanuit verschillende toepassingen naar dezelfde resource Application Insights verzenden.

Hieronder vindt u meer informatie en aanvullende configuratieopties.

## <a name="configuration-file-path"></a>Pad naar configuratiebestand

Standaard verwacht Application Insights Java 3.0 dat het configuratiebestand de naam heeft en zich in dezelfde map bevindt `applicationinsights.json` als `applicationinsights-agent-3.0.3.jar` .

U kunt uw eigen pad naar het configuratiebestand opgeven met behulp van een van beide

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` omgevingsvariabele, of
* `applicationinsights.configuration.file` Java-systeem-eigenschap

Als u een relatief pad opgeeft, wordt dit opgelost ten opzichte van de map waarin `applicationinsights-agent-3.0.3.jar` zich bevindt.

## <a name="connection-string"></a>Verbindingsreeks

Verbindingsreeks is vereist. U vindt uw connection string in uw Application Insights resource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights-verbindingsreeks":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

U kunt de connection string ook instellen met behulp van de omgevingsvariabele (die vervolgens voorrang heeft op connection string die zijn opgegeven in de `APPLICATIONINSIGHTS_CONNECTION_STRING` json-configuratie).

Als u de connection string wordt de Java-agent uitgeschakeld.

## <a name="cloud-role-name"></a>Naam van cloudrol

De naam van de cloudrol wordt gebruikt om het onderdeel op het toepassingskaart te labelen.

Als u de naam van de cloudrol wilt instellen:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Als de naam van de cloudrol niet is ingesteld, wordt Application Insights resource van de resource gebruikt om het onderdeel op het toepassingskaart te labelen.

U kunt ook de naam van de cloudrol instellen met behulp van de omgevingsvariabele (die vervolgens voorrang heeft op de naam van de cloudrol die `APPLICATIONINSIGHTS_ROLE_NAME` is opgegeven in de json-configuratie).

## <a name="cloud-role-instance"></a>Cloudrol-exemplaar

Het exemplaar van een cloudrol wordt standaard ingesteld op de computernaam.

Als u het exemplaar van de cloudrol wilt instellen op een andere naam dan de computernaam:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

U kunt het exemplaar van de cloudrol ook instellen met behulp van de omgevingsvariabele (die vervolgens voorrang krijgt op het exemplaar van de cloudrol dat `APPLICATIONINSIGHTS_ROLE_INSTANCE` is opgegeven in de json-configuratie).

## <a name="sampling"></a>Steekproeven

Steekproeven zijn handig als u de kosten wilt verlagen.
Steekproeven worden uitgevoerd als een functie op de bewerkings-id (ook wel trace-id genoemd), zodat dezelfde bewerkings-id altijd resulteert in dezelfde steekproefbeslissing. Dit zorgt ervoor dat er geen onderdelen van een gedistribueerde transactie worden opgenomen in terwijl andere onderdelen ervan worden bemonsterd.

Als u bijvoorbeeld steekproeven in stelt op 10%, ziet u slechts 10% van uw transacties, maar elk van deze 10% heeft volledige end-to-end transactiegegevens.

Hier ziet u een voorbeeld van het instellen van de steekproef om ongeveer **1/3** van alle transacties vast te leggen. Zorg ervoor dat u de juiste steekproeffrequentie in uw use-case in stelt:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

U kunt ook het steekproefpercentage instellen met behulp van de omgevingsvariabele (die vervolgens voorrang heeft op het samplingpercentage dat `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` is opgegeven in de json-configuratie).

> [!NOTE]
> Kies voor het steekproefpercentage een percentage dat dicht bij 100/N ligt, waarbij N een geheel getal is. Steekproeven bieden momenteel geen ondersteuning voor andere waarden.

## <a name="sampling-overrides-preview"></a>Sampling-overschrijvingen (preview)

Deze functie is beschikbaar als preview-versie vanaf 3.0.3.

Met sampling-overschrijvingen kunt u [](#sampling)het standaardsamplingspercentage overschrijven, bijvoorbeeld:
* Stel het steekproefpercentage in op 0 (of een kleine waarde) voor statuscontroles met ruis.
* Stel het steekproefpercentage in op 0 (of een kleine waarde) voor aanroepen van ruisafhankelijkheden.
* Stel het steekproefpercentage in op 100 voor een belangrijk aanvraagtype (bijvoorbeeld ) zelfs als u de standaardsampling hebt geconfigureerd `/login` op iets lager.

Raadpleeg de documentatie over [sampling-overschrijvingen voor meer](./java-standalone-sampling-overrides.md) informatie.

## <a name="jmx-metrics"></a>Metrische JMX-gegevens

Als u aanvullende JMX-metrische gegevens wilt verzamelen:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` is de metrische naam die wordt toegewezen aan deze JMX-metrische gegevens (kan van alles zijn).

`objectName` is de [objectnaam](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) van de JMX MBean die u wilt verzamelen.

`attribute` is de kenmerknaam in de JMX MBean die u wilt verzamelen.

Numerieke en Booleaanse JMX-metrische waarden worden ondersteund. Booleaanse JMX-metrische gegevens worden voor onwaar en waar `0` `1` aan toegevoegd.

## <a name="custom-dimensions"></a>Aangepaste dimensies

Als u aangepaste dimensies wilt toevoegen aan al uw telemetrie:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` kan worden gebruikt om de waarde van de opgegeven omgevingsvariabele te lezen bij het opstarten.

> [!NOTE]
> Als u vanaf versie 3.0.2 een aangepaste dimensie met de naam toevoegt, wordt de waarde opgeslagen in de kolom in de tabel Application Insights Logs in plaats van als een aangepaste `service.version` `application_Version` dimensie.

## <a name="telemetry-processors-preview"></a>Telemetrieprocessors (preview)

Deze functie is beschikbaar als preview-versie.

Hiermee kunt u regels configureren die worden toegepast op aanvraag-, afhankelijkheids- en traceer-telemetrie, bijvoorbeeld:
 * Gevoelige gegevens maskeren
 * Aangepaste dimensies voorwaardelijk toevoegen
 * Werk de spannaam bij, die wordt gebruikt om vergelijkbare telemetrie in de Azure Portal.
 * Specifieke span-kenmerken neerzetten om de opnamekosten te beheersen.

Raadpleeg de documentatie over [de telemetrieprocessor voor meer](./java-standalone-telemetry-processors.md) informatie.

> [!NOTE]
> Zie [Sampling-onderdrukkingen](./java-standalone-sampling-overrides.md)als u specifieke (hele) overspanningen wilt neerzetten om de opnamekosten te beheren.

## <a name="auto-collected-logging"></a>Automatisch verzamelde logboekregistratie

Log4j, Logback en java.util.logging worden automatisch geïns instrumenteerd en logboekregistratie die via deze frameworks voor logboekregistratie wordt uitgevoerd, wordt automatisch verzameld.

Logboekregistratie wordt alleen vastgelegd als deze voor het eerst voldoet aan het niveau dat is geconfigureerd voor het framework voor logboekregistratie en ten tweede ook voldoet aan het niveau dat is geconfigureerd voor Application Insights.

Als uw framework voor logboekregistratie bijvoorbeeld is geconfigureerd voor logboekregistratie (en hoger) vanuit pakket en Application Insights is geconfigureerd om vast te leggen (en hoger), wordt door Application Insights alleen `WARN` `com.example` `INFO` `WARN` (en hoger) vanuit pakket vast `com.example` leggen.

Het standaardniveau dat is geconfigureerd voor Application Insights is `INFO` . Als u dit niveau wilt wijzigen:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

U kunt het niveau ook instellen met behulp van de omgevingsvariabele (die vervolgens voorrang heeft op het niveau `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` dat is opgegeven in de json-configuratie).

Dit zijn de geldige waarden die u in het bestand kunt opgeven en hoe deze `level` overeenkomen met logboekregistratieniveaus in verschillende `applicationinsights.json` frameworks voor logboekregistratie:

| niveau             | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| UIT               | UIT    | UIT     | UIT     |
| Fatale             | Fatale  | ERROR   | Ernstige  |
| FOUT (OF ERNSTIG) | ERROR  | ERROR   | Ernstige  |
| WAARSCHUWEN (of WAARSCHUWING) | Waarschuwen   | Waarschuwen    | WAARSCHUWING |
| Info              | Info   | Info    | Info    |
| Config            | FOUTOPSPORING  | FOUTOPSPORING   | Config  |
| FOUTOPSPORING (of FINE)   | FOUTOPSPORING  | FOUTOPSPORING   | Fijn    |
| Fijnere             | FOUTOPSPORING  | FOUTOPSPORING   | Fijnere   |
| TRACEER (OF FIJNSTE) | Trace  | Trace   | Beste  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Als een uitzonderingsobject wordt doorgegeven aan de logger, wordt het logboekbericht (en details van het uitzonderingsobject) weergegeven in de Azure Portal onder de tabel in plaats van in de `exceptions` `traces` tabel.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatisch verzamelde metrische gegevens van Micrometer (inclusief Spring Boot Actuator- metrische gegevens)

Als uw toepassing [Micrometer gebruikt,](https://micrometer.io)worden automatisch metrische gegevens verzameld die naar het globale register van Micrometer worden verzonden.

Als uw toepassing gebruikmaakt [van Spring Boot Actuator,](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)worden ook automatisch metrische gegevens verzameld die zijn geconfigureerd door Spring Boot Actuator.

Automatische verzameling van metrische gegevens van Micrometer uitschakelen (inclusief Spring Boot Actuator-metrische gegevens):

> [!NOTE]
> Aangepaste metrische gegevens worden afzonderlijk gefactureerd en kunnen extra kosten genereren. Controleer de gedetailleerde [prijsinformatie.](https://azure.microsoft.com/pricing/details/monitor/) Als u de metrische gegevens van Micrometer en Spring Actuator wilt uitschakelen, voegt u de onderstaande configuratie toe aan uw configuratiebestand.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="auto-collected-azure-sdk-telemetry-preview"></a>Automatisch verzamelde Azure SDK-telemetrie (preview)

Veel van de nieuwste Azure SDK-bibliotheken zenden telemetrie (zie de [volledige lijst](./java-in-process-agent.md#azure-sdks-preview)).

Vanaf Application Insights Java 3.0.3 kunt u het vastleggen van deze telemetrie inschakelen.

Als u deze functie wilt inschakelen:

```json
{
  "preview": {
    "instrumentation": {
      "azureSdk": {
        "enabled": true
      }
    }
  }
}
```

U kunt deze functie ook inschakelen met behulp van de omgevingsvariabele `APPLICATIONINSIGHTS_PREVIEW_INSTRUMENTATION_AZURE_SDK_ENABLED`
(deze heeft dan voorrang op ingeschakeld zoals opgegeven in de json-configuratie).

## <a name="suppressing-specific-auto-collected-telemetry"></a>Specifieke automatisch verzamelde telemetrie onderdrukken

Vanaf versie 3.0.3 kunnen specifieke automatisch verzamelde telemetriegegevens worden onderdrukt met behulp van deze configuratieopties:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

U kunt deze instrumentaties ook onderdrukken met behulp van deze omgevingsvariabelen:

* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(deze heeft dan voorrang op ingeschakeld zoals opgegeven in de json-configuratie).

> OPMERKING Als u op zoek bent naar een meer fijnkeurig beheer, bijvoorbeeld om een aantal redis-aanroepen te onderdrukken, maar niet alle redis-aanroepen, bekijkt u [sampling-onderdrukkingen.](./java-standalone-sampling-overrides.md)

## <a name="heartbeat"></a>Hartslag

Standaard verzendt Application Insights Java 3.0 elke 15 minuten een heartbeat-metriek. Als u de heartbeat-metrische gegevens gebruikt om waarschuwingen te activeren, kunt u de frequentie van deze heartbeat verhogen:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> U kunt het interval niet langer dan 15 minuten verhogen, omdat de heartbeatgegevens ook worden gebruikt om het gebruik van Application Insights bijhouden.

## <a name="http-proxy"></a>HTTP-proxy

Als uw toepassing zich achter een firewall bevinden en niet rechtstreeks verbinding kan maken met Application Insights (zie [IP-adressen](./ip-addresses.md)die worden gebruikt door Application Insights ), kunt u Application Insights Java 3.0 configureren voor het gebruik van een HTTP-proxy:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.0 respecteert ook de globale en `-Dhttps.proxyHost` `-Dhttps.proxyPort` als deze zijn ingesteld.

## <a name="metric-interval"></a>Interval voor metrische gegevens

Deze functie is beschikbaar als preview-versie.

Standaard worden metrische gegevens elke 60 seconden vastgelegd.

Vanaf versie 3.0.3 kunt u dit interval wijzigen:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

De instelling is van toepassing op al deze metrische gegevens:

* Standaardprestatiemeters, zoals CPU en geheugen
* Standaard aangepaste metrische gegevens, bijvoorbeeld timing van garbagecontainers
* Geconfigureerde metrische JMX-gegevens ([zie hierboven](#jmx-metrics))
* Metrische gegevens van micrometer ([zie hierboven](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "OPMERKING OpenTelemetry-ondersteuning is in de privépreview totdat de OpenTelemetry-API 1.0 bereikt"

[//]: # "## Ondersteuning voor opentelemetry-API pre-1.0-releases"

[//]: # "Ondersteuning voor pre-1.0-versies van de OpenTelemetry-API is opt-in, omdat de OpenTelemetry-API nog niet stabiel is"
[//]: # "en dus ondersteunt elke versie van de agent alleen een specifieke pre-1.0-versie van de OpenTelemetry-API"
[//]: # "(Deze beperking is niet van toepassing zodra OpenTelemetry API 1.0 is uitgebracht)."

[//]: # "'''json"
[//]: # "{"
[//]: # "  \"preview: \" {"
[//]: # "    \"openTelemetryApiSupport: \" true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Zelfdiagnose

'Zelfdiagnose' verwijst naar interne logboekregistratie van Application Insights Java 3.0.

Deze functionaliteit kan handig zijn voor het herkennen en diagnosticeren van problemen met Application Insights zelf.

Standaard worden Application Insights Java 3.0-logboeken op niveau toegevoegd aan zowel het bestand als de console, die `INFO` overeenkomen met deze `applicationinsights.log` configuratie:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` kan een van of `file` `console` `file+console` zijn.

`level` kan een van `OFF` , , , , , of `ERROR` `WARN` `INFO` `DEBUG` `TRACE` zijn.

`path` kan een absoluut of relatief pad zijn. Relatieve paden worden opgelost op basis van de map waarin `applicationinsights-agent-3.0.3.jar` zich bevindt.

`maxSizeMb` is de maximale grootte van het logboekbestand voordat het wordt overgeslagen.

`maxHistory` is het aantal gerrolde logboekbestanden dat wordt bewaard (naast het huidige logboekbestand).

Vanaf versie 3.0.2 kunt u ook de zelfdiagnostiek instellen met behulp van de omgevingsvariabele (die vervolgens voorrang krijgt boven het zelfdiagnoseniveau dat is opgegeven in de `level` `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` json-configuratie).

## <a name="an-example"></a>Een voorbeeld

Dit is slechts een voorbeeld om te laten zien hoe een configuratiebestand eruitziet met meerdere onderdelen.
Configureer specifieke opties op basis van uw behoeften.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```
