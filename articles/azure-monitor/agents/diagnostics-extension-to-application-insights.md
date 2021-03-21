---
title: Azure Diagnostics gegevens naar Application Insights verzenden
description: De Azure Diagnostics open bare configuratie bijwerken om gegevens te verzenden naar Application Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: b9e9d6b1b5939804b24fd523bf8b7444ed41178f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708591"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>De diagnostische gegevens voor de Cloud service, virtuele machine of Service Fabric verzenden naar Application Insights
Cloud Services, Virtual Machines, Virtual Machine Scale Sets en Service Fabric gebruiken allemaal de Azure Diagnostics extensie voor het verzamelen van gegevens.  Diagnostische gegevens van Azure worden verzonden naar Azure Storage tabellen.  U kunt echter ook alle of een subset van de gegevens naar andere locaties pipeen met Azure Diagnostics extensie 1,5 of hoger.

In dit artikel wordt beschreven hoe u gegevens van de Azure Diagnostics extensie verzendt naar Application Insights.

## <a name="diagnostics-configuration-explained"></a>Configuratie van diagnostische gegevens wordt uitgelegd
De Azure Diagnostics-extensie 1,5 geïntroduceerde sinks, die extra locaties zijn waar u Diagnostische gegevens kunt verzenden.

Voorbeeld configuratie van een Sink voor Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- Het kenmerk **sink** *name* is een teken reeks waarde waarmee de Sink uniek wordt geïdentificeerd.

- Het element **ApplicationInsights** geeft instrumentatie sleutel aan van de Application Insights-resource waar de diagnostische gegevens van Azure worden verzonden.
    - Als u geen bestaande Application Insights resource hebt, raadpleegt u [een nieuwe Application Insights resource maken](../app/create-new-resource.md) voor meer informatie over het maken van een resource en het ophalen van de instrumentatie sleutel.
    - Als u een Cloud service ontwikkelt met Azure SDK 2,8 en hoger, wordt deze instrumentatie sleutel automatisch ingevuld. De waarde is gebaseerd op de configuratie-instelling **APPINSIGHTS_INSTRUMENTATIONKEY** service wanneer het Cloud service project wordt ingepakt. Zie [Application Insights gebruiken met Cloud Services](../app/cloudservices.md).

- Het element **channels** bevat een of meer **kanaal** elementen.
    - Het *naam* kenmerk verwijst uniek naar dat kanaal.
    - Met het kenmerk *LogLevel* kunt u het logboek niveau opgeven dat door het kanaal wordt toegestaan. De beschik bare logboek niveaus in volg orde van de meest minimale informatie zijn:
        - Uitgebreid
        - Informatie
        - Waarschuwing
        - Fout
        - Kritiek

Een kanaal fungeert als een filter en biedt u de mogelijkheid specifieke logboek niveaus te selecteren om naar de doel-sink te verzenden. U kunt bijvoorbeeld uitgebreide logboeken verzamelen en deze naar opslag verzenden, maar alleen fouten naar de Sink verzenden.

In de volgende afbeelding ziet u deze relatie.

![Open bare configuratie van diagnostische gegevens](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

In de volgende afbeelding ziet u een overzicht van de configuratie waarden en hoe ze werken. U kunt meerdere sinks in de configuratie op verschillende niveaus in de hiërarchie toevoegen. De Sink op het hoogste niveau fungeert als globale instelling en de filter die is opgegeven bij het afzonderlijke element fungeert als een onderdrukking voor die globale instelling.

![Configuratie van diagnose filters met Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Voor beeld van de Sink-configuratie volt ooien
Hier volgt een volledig voor beeld van het open bare configuratie bestand dat
1. stuurt alle fouten naar Application Insights (opgegeven op het knoop punt **DiagnosticMonitorConfiguration** )
2. verzendt ook uitgebreide logboeken voor de toepassings Logboeken (opgegeven op het knoop punt **Logboeken** ).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
In de vorige configuratie hebben de volgende regels de volgende betekenis:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Alle gegevens die worden verzameld door Azure Diagnostics verzenden

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Alleen fouten logboeken naar de Application Insights-Sink verzenden

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Uitgebreide toepassings logboeken naar Application Insights verzenden

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Beperkingen

- **Logboek type alleen kanalen en geen prestatie meter items.** Als u een kanaal met een prestatie meter element opgeeft, wordt dit genegeerd.
- **Het logboek niveau voor een kanaal kan niet groter zijn dan het logboek niveau voor de gegevens die worden verzameld door Azure Diagnostics.** U kunt bijvoorbeeld geen toepassings logboek fouten verzamelen in het element logs en proberen uitgebreide logboeken te verzenden naar de Sink voor het inzicht van de toepassing. Het kenmerk *scheduledTransferLogLevelFilter* moet altijd gelijke of meer logboeken verzamelen dan de logboeken die u probeert te verzenden naar een sink.
- **U kunt geen BLOB-gegevens verzenden die zijn verzameld door de Azure Diagnostics-extensie naar Application Insights.** Bijvoorbeeld iets dat is opgegeven onder het knoop punt *directory's* . Voor crash dumps wordt de daad werkelijke crash dump verzonden naar de Blob-opslag en alleen een melding dat de crash dump is gegenereerd, wordt verzonden naar Application Insights.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het weer geven van uw Azure diagnostische gegevens](../app/cloudservices.md) in Application Insights.
* Gebruik [Power shell](../../cloud-services/cloud-services-diagnostics-powershell.md) om de Azure Diagnostics-extensie voor uw toepassing in te scha kelen.
* [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) gebruiken om de Azure Diagnostics-extensie voor uw toepassing in te scha kelen

