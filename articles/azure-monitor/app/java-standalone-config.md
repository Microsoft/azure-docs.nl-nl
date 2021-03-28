---
title: Configuratie opties-Azure Monitor Application Insights voor Java
description: Azure Monitor Application Insights voor Java configureren
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: f349d260fff32427712442615cabf6d3958468ac
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640031"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Configuratie opties-Azure Monitor Application Insights voor Java

> [!WARNING]
> **Als u een upgrade uitvoert van 3,0 Preview**
>
> Controleer alle onderstaande configuratie opties zorgvuldig, omdat de JSON-structuur volledig is gewijzigd, naast de naam van het bestand dat alle kleine letters bevat.

## <a name="connection-string-and-role-name"></a>Verbindings reeks en rolnaam

De verbindings reeks en de rolnaam zijn de meest voorkomende instellingen die nodig zijn om aan de slag te gaan:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

De connection string is vereist en de rolnaam is belang rijk telkens wanneer u gegevens van verschillende toepassingen naar dezelfde Application Insights resource verzendt.

Hieronder vindt u meer informatie en aanvullende configuratie-opties.

## <a name="configuration-file-path"></a>Pad naar configuratie bestand

Application Insights Java 3,0 verwacht dat het configuratie bestand wordt benoemd `applicationinsights.json` en zich in dezelfde map als bevindt `applicationinsights-agent-3.0.2.jar` .

U kunt uw eigen pad naar een configuratie bestand opgeven met

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` omgevings variabele of
* `applicationinsights.configuration.file` Java-systeem eigenschap

Als u een relatief pad opgeeft, wordt dit omgezet ten opzichte van de map waar `applicationinsights-agent-3.0.2.jar` zich bevindt.

## <a name="connection-string"></a>Verbindingsreeks

De verbindings reeks is vereist. U kunt uw connection string vinden in uw Application Insights-resource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Verbindings reeks Application Insights":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

U kunt de connection string ook instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_CONNECTION_STRING` (die vervolgens voor rang krijgt als de Connection String ook is opgegeven in de JSON-configuratie).

Als u de connection string niet instelt, wordt de Java-Agent uitgeschakeld.

## <a name="cloud-role-name"></a>Rolnaam van Cloud

De naam van de Cloud functie wordt gebruikt om het onderdeel op het toepassings overzicht te labelen.

Als u de naam van de Cloud functie wilt instellen:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Als de naam van de Cloud functie niet is ingesteld, wordt de naam van de Application Insights resource gebruikt voor het labelen van het onderdeel op de toepassings toewijzing.

U kunt de naam van de Cloud functie ook instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_ROLE_NAME` (die vervolgens voor rang krijgt als de naam van de Cloud functie ook is opgegeven in de JSON-configuratie).

## <a name="cloud-role-instance"></a>Cloud rolinstantie

De computer naam van de instantie van de Cloud wordt standaard ingesteld.

Als u de Cloud rolinstantie wilt instellen op iets anders dan de naam van de computer:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

U kunt ook de functie-instantie van de Cloud instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_ROLE_INSTANCE` (die vervolgens voor rang krijgt als de Cloud rolinstantie ook is opgegeven in de JSON-configuratie).

## <a name="sampling"></a>Steekproeven

Steek proeven zijn handig als u kosten wilt verlagen.
Steek proeven worden uitgevoerd als een functie van de bewerkings-ID (ook wel traceer-ID genoemd), zodat dezelfde bewerkings-ID altijd resulteert in dezelfde steekproef beslissing. Dit zorgt ervoor dat er geen delen van een gedistribueerde trans actie worden opgehaald, terwijl andere onderdelen hiervan worden bemonsterd.

Als u bijvoorbeeld steek proeven instelt op 10%, worden er slechts 10% van uw trans acties weer geven, maar elke waarde van 10% heeft volledige end-to-end-transactie gegevens.

Hier volgt een voor beeld van het instellen van de steek proef voor het vastleggen **van ongeveer 1/3 van alle trans acties** : Zorg ervoor dat u de gewenste sampling frequentie hebt ingesteld voor uw gebruiks Case:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

U kunt het steekproef percentage ook instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` (die vervolgens voor rang krijgt als het steekproef percentage ook is opgegeven in de JSON-configuratie).

> [!NOTE]
> Kies voor het steekproef percentage een percentage dat dicht bij 100/N ligt waarbij N een geheel getal is. De huidige steek proef biedt geen ondersteuning voor andere waarden.

## <a name="sampling-overrides-preview"></a>Steek proeven van onderdrukkingen (preview-versie)

Deze functie is beschikbaar als preview-versie, vanaf 3.0.3-BETA. 2.

Met bemonsterings onderdrukkingen kunt u het [standaard sampling percentage](#sampling)overschrijven, bijvoorbeeld:
* Stel het steekproef percentage in op 0 (of een kleine waarde) voor slechte status controles.
* Stel het steekproef percentage in op 0 (of een kleine waarde) voor ruis afhankelijke afhankelijkheids aanroepen.
* Stel het steekproef percentage in op 100 voor een belang rijk aanvraag type (bijvoorbeeld `/login` ), hoewel u de standaard steekproef hebt geconfigureerd voor iets lager.

Raadpleeg voor meer informatie de documentatie voor [steek proeven met onderdrukkingen](./java-standalone-sampling-overrides.md) .

## <a name="jmx-metrics"></a>Metrische gegevens van JMX

Als u een aantal aanvullende JMX-metrische gegevens wilt verzamelen:

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

`name` is de metrische naam die wordt toegewezen aan deze JMX-metriek (dit kan alles zijn).

`objectName` is de [object naam](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) van de JMX-MBean die u wilt verzamelen.

`attribute` is de naam van het kenmerk in de JMX-MBean die u wilt verzamelen.

De waarden voor de numerieke en Booleaanse JMX-metriek worden ondersteund. Booleaanse JMX-metrische gegevens worden toegewezen aan `0` voor onwaar en `1` voor waar.

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

`${...}` kan worden gebruikt om de waarde van de opgegeven omgevings variabele bij het opstarten te lezen.

> [!NOTE]
> Als u vanaf versie 3.0.2 een aangepaste dimensie met de naam toevoegt `service.version` , wordt de waarde opgeslagen in de `application_Version` kolom in de tabel Application Insights Logboeken in plaats van als aangepaste dimensie.

## <a name="telemetry-processors-preview"></a>Telemetrie-processors (preview-versie)

Deze functie is beschikbaar als preview-versie.

Hiermee kunt u regels configureren die worden toegepast op aanvraag-, afhankelijkheids-en traceer-telemetrie, bijvoorbeeld:
 * Masker gevoelige gegevens
 * Aangepaste dimensies voorwaardelijk toevoegen
 * Werk de naam van de span bij, die wordt gebruikt voor het samen voegen van vergelijk bare telemetrie in de Azure Portal.
 * Specifieke span attributen verwijderen om opname kosten te bepalen.

Raadpleeg de documentatie van de [telemetrie-processor](./java-standalone-telemetry-processors.md) voor meer informatie.

> [!NOTE]
> Zie [steekproef onderdrukkingen](./java-standalone-sampling-overrides.md)als u specifieke (hele) reeksen wilt verwijderen voor het beheren van opname kosten.

## <a name="auto-collected-logging"></a>Automatisch verzamelde logboek registratie

Log4j, logback en Java. util. logging zijn automatisch instrumenteel en logboek registratie die via deze logboek registratie raamwerken wordt uitgevoerd, wordt automatisch verzameld.

Logboek registratie wordt alleen vastgelegd als het eerst voldoet aan het niveau dat is geconfigureerd voor het logboek registratie raamwerk en de tweede, ook voldoet aan het niveau dat is geconfigureerd voor Application Insights.

Als uw Framework voor logboek registratie bijvoorbeeld is ingesteld op logboek `WARN` (en hoger) van het pakket `com.example` , en Application Insights is geconfigureerd voor het vastleggen `INFO` van (en hoger), wordt door Application Insights alleen het `WARN` pakket vastgelegd (en hierboven) `com.example` .

Het standaard niveau dat is geconfigureerd voor Application Insights is `INFO` . Als u dit niveau wilt wijzigen:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

U kunt het niveau ook instellen met behulp van de omgevings variabele `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` (die vervolgens voor rang krijgt als het niveau ook is opgegeven in de JSON-configuratie).

Dit zijn de geldige `level` waarden die u in het bestand kunt opgeven `applicationinsights.json` en hoe ze overeenkomen met de registratie niveaus in verschillende registratie raamwerken:

| niveau             | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| UIT               | UIT    | UIT     | UIT     |
| FATALE             | FATALE  | ERROR   | ZEER  |
| FOUT (of ernstig) | ERROR  | ERROR   | ZEER  |
| Waarschuwing (of waarschuwing) | WETEN   | WETEN    | WAARSCHUWING |
| VALUTA              | VALUTA   | VALUTA    | VALUTA    |
| CONFIGURATIES            | FOUTOPSPORING  | FOUTOPSPORING   | CONFIGURATIES  |
| Fout opsporing (of fijn)   | FOUTOPSPORING  | FOUTOPSPORING   | BLIJVEN    |
| KLEINERE             | FOUTOPSPORING  | FOUTOPSPORING   | KLEINERE   |
| TRACERen (of het kleinste) | TRACERINGS  | TRACERINGS   | MEEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Als een uitzonderings object wordt door gegeven aan de logboek registratie, worden het logboek bericht (en Details van het uitzonderings object) weer gegeven in de Azure Portal onder de `exceptions` tabel in plaats van de `traces` tabel.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatisch verzamelde metrische gegevens over micrometer (inclusief lente-metrische gegevens over het starten van de klep)

Als uw toepassing gebruikmaakt van [micrometer](https://micrometer.io), worden metrische gegevens die naar het globale REGI ster van micrometer worden verzonden, automatisch verzameld.

Als uw toepassing een [Spring boot-klep](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)gebruikt, worden de metrische gegevens die zijn geconfigureerd met een Spring boot-klep ook automatisch verzameld.

Automatische verzameling van micrometer-metrische gegevens uitschakelen (inclusief lente-metrische gegevens over het starten van de klep):

> [!NOTE]
> Aangepaste metrische gegevens worden afzonderlijk gefactureerd en kunnen extra kosten in rekening worden gebracht. Controleer de gedetailleerde [prijs informatie](https://azure.microsoft.com/pricing/details/monitor/). Als u de metrische gegevens voor micrometer en lente aandrijving wilt uitschakelen, voegt u de onderstaande configuratie toe aan het configuratie bestand.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>Specifieke telemetrie voor automatisch verzamelen onderdrukken

Vanaf versie 3.0.2 kan specifiek automatisch verzamelde telemetrie worden onderdrukt met behulp van deze configuratie opties:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
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
    }
  }
}
```

> Opmerking Als u op zoek bent naar meer nauw keurige controle, bijvoorbeeld om sommige redis-aanroepen, maar niet alle redis-aanroepen te onderdrukken, raadpleegt u voor beeld van [onderdrukkingen](./java-standalone-sampling-overrides.md).


## <a name="heartbeat"></a>Hartslag

Application Insights Java 3,0 verzendt standaard elke 15 minuten een heartbeat-metriek. Als u de heartbeat-metriek gebruikt om waarschuwingen te activeren, kunt u de frequentie van deze heartbeat verhogen:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> U kunt het interval niet langer dan 15 minuten verhogen, omdat de heartbeat-gegevens ook worden gebruikt om Application Insights gebruik bij te houden.

## <a name="http-proxy"></a>HTTP-proxy

Als uw toepassing zich achter een firewall bevindt en niet rechtstreeks verbinding kan maken met Application Insights (Zie [IP-adressen die worden gebruikt door Application Insights](./ip-addresses.md)), kunt u Application Insights Java 3,0 configureren voor het gebruik van een http-proxy:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3,0 is ook van toepassing op het globale `-Dhttps.proxyHost` en `-Dhttps.proxyPort` als deze zijn ingesteld.

## <a name="metric-interval"></a>Interval voor metrische gegevens

Deze functie is beschikbaar als preview-versie.

Standaard worden metrische gegevens elke 60 seconden vastgelegd.

Vanaf versie 3.0.3-Bèta kunt u dit interval wijzigen:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

De instelling geldt voor al deze metrische gegevens:

* Standaard prestatie meter items, bijvoorbeeld CPU en geheugen
* Standaard aangepaste metrische gegevens, bijvoorbeeld Garbage Collection-timing
* Geconfigureerde metrische JMX-gegevens ([Zie hierboven](#jmx-metrics))
* Metrische gegevens over micrometer ([Zie hierboven](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "Opmerking OpenTelemetry-ondersteuning bevindt zich in een persoonlijke preview totdat de OpenTelemetry-API 1,0 bedraagt"

[//]: # "# # Ondersteuning voor OpenTelemetry-API-releases van vóór 1,0"

[//]: # "Ondersteuning voor pre-1,0-versies van OpenTelemetry-API is opt-in, omdat de OpenTelemetry-API nog niet stabiel is"
[//]: # "en daarom ondersteunt elke versie van de agent alleen een specifieke pre-1,0-versie van OpenTelemetry-API"
[//]: # "(deze beperking is niet van toepassing zodra OpenTelemetry API 1,0 wordt uitgebracht."

[//]: # "JSON"
[//]: # "{"
[//]: # "  \"voor beeld \" : {"
[//]: # "    \"openTelemetryApiSupport \" : True"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Zelf diagnostische gegevens

' Zelf diagnostische gegevens ' verwijst naar interne logboek registratie van Application Insights Java 3,0.

Deze functionaliteit kan handig zijn voor het herkennen en diagnosticeren van problemen met Application Insights zichzelf.

Application Insights Java 3,0-logboeken worden standaard op niveau `INFO` naar het bestand `applicationinsights.log` en de console, die overeenkomt met deze configuratie:

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

`destination` Dit kan een van `file` `console` of zijn `file+console` .

`level` Dit kan een van,,,, `OFF` `ERROR` of zijn `WARN` `INFO` `DEBUG` `TRACE` .

`path` Dit kan een absoluut of relatief pad zijn. Relatieve paden worden omgezet in de map waarin zich zich `applicationinsights-agent-3.0.2.jar` bevindt.

`maxSizeMb` is de maximale grootte van het logboek bestand voordat deze wordt doorgevoerd.

`maxHistory` is het aantal doorgevoerde logboek bestanden dat wordt bewaard (naast het huidige logboek bestand).

Vanaf versie 3.0.2 kunt u de zelf diagnostische gegevens ook instellen `level` met behulp van de omgevings variabele `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` (die vervolgens voor rang krijgt als de zelf diagnostische gegevens `level` ook zijn opgegeven in de JSON-configuratie).

## <a name="an-example"></a>Een voor beeld

Dit is slechts een voor beeld om te laten zien hoe een configuratie bestand eruitziet als meerdere onderdelen.
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
