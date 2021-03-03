---
title: Gestructureerd toepassings logboek voor Azure lente-Cloud | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u gestructureerde toepassings logboek gegevens in azure lente-Cloud kunt genereren en verzamelen.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendanm
ms.custom: devx-track-java
ms.openlocfilehash: e846da81444ae1632cb7f9a4cd413bc3f9b7b232
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701940"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Gestructureerd toepassings logboek voor Azure lente-Cloud

In dit artikel wordt uitgelegd hoe u gestructureerde toepassings logboek gegevens in azure lente-Cloud kunt genereren en verzamelen. Met de juiste configuratie biedt Azure veer Cloud nuttige query-en analyse mogelijkheden voor toepassings logboeken via Log Analytics.

## <a name="log-schema-requirements"></a>Schema vereisten voor logboek
Om de ervaring van de logboek query te verbeteren, moet een toepassings logboek in JSON-indeling zijn en voldoen aan een schema. Azure lente Cloud gebruikt dit schema om uw toepassing en stream te parseren naar Log Analytics. 

**Vereisten voor het JSON-schema:**

| JSON-sleutel      | Type JSON-waarde|  Vereist | Kolom in Log Analytics| Beschrijving |
| --------------| ------------|-----------|-----------------|--------------------------|
| tijdstempel     | tekenreeks      |     Ja   | AppTimestamp    | tijds tempel in UTC-notatie  |
| logger        | tekenreeks      |     No    | Logger          | logger                   |
| niveau         | tekenreeks      |     No    | CustomLevel     | logboek niveau                |
| reeks        | tekenreeks      |     No    | Reeks          | reeks                   |
| message       | tekenreeks      |     No    | Bericht         | logboek bericht              |
| stackTrace    | tekenreeks      |     No    | StackTrace      | stacktrace van uitzonde ring    |
| exceptionClass| tekenreeks      |     No    | ExceptionClass  | naam uitzonderings klasse     |
| mdc           | geneste JSON |     Nee    |                 | gekoppelde context voor diagnostische gegevens|
| mdc.traceId   | tekenreeks      |     No    | TraceId         |Trace-id voor gedistribueerde tracering|
| mdc.spanId    | tekenreeks      |     No    | SpanId          |span-ID voor gedistribueerde tracering |
|               |             |           |                 |                          |

* Het veld tijds tempel is vereist en moet de UTC-indeling hebben, alle andere velden zijn optioneel.
* ' traceId ' en ' spanId ' in het veld ' MDC ' worden gebruikt voor tracering van doel einden.
* Registreer elke JSON-record in één regel. 

**Voor beeld van logboek record** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Schema-compatibel JSON-logboek genereren  
Voor lente-toepassingen kunt u een verwachte JSON-logboek indeling genereren met behulp van algemene [registratie raamwerken](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration), zoals [logback](http://logback.qos.ch/) en [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Aanmelden met logback 
Bij het gebruik van veer boot-starters wordt logback standaard gebruikt. Voor logback-apps gebruikt u [logstash-encoder](https://github.com/logstash/logstash-logback-encoder) voor het genereren van logboek met JSON-indeling. Deze methode wordt ondersteund in Spring boot versie 2.1 +. 

De procedure:

1. Voeg logstash afhankelijkheid toe aan uw pom.xml-bestand. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Werk uw logback.xml configuratie bestand bij om de JSON-indeling in te stellen.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Aanmelden met log4j2 

Voor log4j2-apps gebruikt u [JSON-sjabloon-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) voor het genereren van logboek met JSON-indeling. Deze methode wordt ondersteund in Spring boot versie 2.1 +.

De procedure:

1. Sluit `spring-boot-starter-logging` uit `spring-boot-starter` , voeg afhankelijkheden toe `spring-boot-starter-log4j2` `log4j-layout-template-json` in uw pom.xml-bestand.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Een sjabloon bestand voor JSON-indeling voorbereiden jsonTemplate.jsin het pad naar de klasse.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Gebruik deze sjabloon voor JSON-indeling in het configuratie bestand van uw log4j2.xml. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>De logboeken in Log Analytics analyseren

Nadat uw toepassing correct is ingesteld, wordt het logboek van uw toepassings console naar Log Analytics gestreamd. De structuur maakt efficiënte query's mogelijk in Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Controleer de logboek structuur in Log Analytics
Gebruik de volgende procedure:
1. Ga naar de pagina service overzicht van uw service-exemplaar.
2. Klik op `Logs` vermelding onder `Monitoring` sectie.
3. Deze query uit te voeren.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Toepassings logboeken retour neren zoals wordt weer gegeven in de volgende afbeelding:

![JSON-logboek weer geven](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Logboek vermeldingen met fouten weer geven

Voer de volgende query uit om logboek vermeldingen met een fout te controleren:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Gebruik deze query om fouten te vinden of wijzig de query termen om een specifieke uitzonderings klasse of fout code te vinden. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Logboek vermeldingen voor een specifieke traceId weer geven
Voer de volgende query uit om logboek vermeldingen voor een specifieke tracerings-ID trace_id te controleren:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Volgende stappen
* Zie aan de [slag met logboek query's in azure monitor](../azure-monitor/logs/get-started-queries.md) voor meer informatie over de logboek query