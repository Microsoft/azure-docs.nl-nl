---
title: Gegevens verzenden van de Windows Azure Diagnostics-extensie naar Azure Event Hubs
description: Configureer de uitbrei ding van diagnostische gegevens in Azure Monitor zodat u deze kunt verzenden naar een Azure Event hub.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 979535b1f9a237f6975908178fb1e5ed819181b0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611205"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Gegevens verzenden van de Windows Azure Diagnostics-extensie naar Azure Event Hubs
Azure Diagnostics-extensie is een agent in Azure Monitor die bewakings gegevens van het gast besturingssysteem en werk belastingen van virtuele Azure-machines en andere reken bronnen verzamelt. In dit artikel wordt beschreven hoe u gegevens verzendt van de Windows Azure Diagnostic extension (WAD) naar [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/) zodat u kunt door sturen naar locaties buiten Azure.

## <a name="supported-data"></a>Ondersteunde gegevens

De gegevens die worden verzameld uit het gast besturingssysteem dat naar Event Hubs kan worden verzonden, bevatten het volgende. Andere gegevens bronnen die door WAD worden verzameld, zoals IIS-logboeken en crash dumps, kunnen niet naar Event Hubs worden verzonden.

* ETW-gebeurtenissen (Event Tracing for Windows)
* Prestatiemeteritems
* Windows-gebeurtenis logboeken, inclusief toepassings Logboeken in het Windows-gebeurtenis logboek
* Logboeken van Azure Diagnostics-infrastructuur

## <a name="prerequisites"></a>Vereisten

* Diagnostische Windows-extensie 1,6 of hoger. Zie [Azure Diagnostics schema versies en geschiedenis van de extensie configuratie](diagnostics-extension-versions.md) voor een versie geschiedenis en [Azure Diagnostics extensie overzicht](diagnostics-extension-overview.md) voor ondersteunde bronnen.
* Event Hubs naam ruimte moet altijd worden ingericht. Zie [aan de slag met Event hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) voor meer informatie.


## <a name="configuration-schema"></a>Configuratie schema
Zie [Windows Azure Diagnostics extension (WAD) installeren en configureren](diagnostics-extension-windows-install.md) voor verschillende opties voor het inschakelen en configureren van de uitbrei ding voor diagnostische gegevens en het [Azure Diagnostics configuratie schema](diagnostics-extension-schema-windows.md) voor een verwijzing van het configuratie schema. In de rest van dit artikel wordt beschreven hoe u deze configuratie gebruikt voor het verzenden van gegevens naar een Event Hub. 

Azure Diagnostics stuurt altijd logboeken en metrische gegevens naar een Azure Storage-account. U kunt een of meer *gegevens-sinks* configureren waarmee gegevens naar extra locaties worden verzonden. Elke sink wordt gedefinieerd in het [SinksConfig-element](diagnostics-extension-schema-windows.md#sinksconfig-element) van de open bare configuratie met gevoelige informatie in de persoonlijke configuratie. Deze configuratie voor Event hubs gebruikt de waarden in de volgende tabel.

| Eigenschap | Beschrijving |
|:---|:---|
| Name | Beschrijvende naam voor de sink. Wordt gebruikt in de configuratie om op te geven welke gegevens bronnen moeten worden verzonden naar de sink. |
| URL  | De URL van de Event Hub in de vorm \<event-hubs-namespace\> . servicebus.Windows.net/ \<event-hub-name\> .          |
| SharedAccessKeyName | De naam van een gedeeld toegangs beleid voor de Event Hub die ten minste een **Verzend** instantie heeft. |
| SharedAccessKey     | Primaire of secundaire sleutel van het beleid voor gedeelde toegang voor de Event Hub. |

Voor beelden van open bare en persoonlijke configuraties worden hieronder weer gegeven. Dit is een minimale configuratie met één prestatie meter item en gebeurtenis logboek om te laten zien hoe de Event Hub gegevens Sink moet worden geconfigureerd en gebruikt. Zie [Azure Diagnostics configuratie schema](diagnostics-extension-schema-windows.md) voor een complexere voor beeld.

### <a name="public-configuration"></a>Open bare configuratie

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Persoonlijke configuratie

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Configuratie-opties
Als u gegevens wilt verzenden naar een gegevens-sink, geeft u het kenmerk **sinks** op het knoop punt van de gegevens bron op. Waar u het kenmerk **sinks** plaatst, bepaalt het bereik van de toewijzing. In het volgende voor beeld wordt het kenmerk **sinks** gedefinieerd voor het **Performance Counters** -knoop punt, waardoor alle onderliggende prestatie meter items naar de Event hub worden verzonden.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


In het volgende voor beeld wordt het kenmerk **sinks** rechtstreeks toegepast op drie tellers, waardoor alleen de prestatie meter items naar de Event hub worden verzonden. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Configuratie valideren
U kunt verschillende methoden gebruiken om te valideren dat gegevens naar de Event Hub worden verzonden. ne eenvoudige methode is het gebruik van Event Hubs Capture zoals beschreven in [gebeurtenissen vastleggen via azure Event hubs in azure Blob Storage of Azure data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Problemen met Event Hubs-sinks oplossen

- Bekijk de Azure Storage tabel **WADDiagnosticInfrastructureLogsTable** die logboeken en fouten bevat voor Azure Diagnostics. Een optie is het gebruik van een hulp programma zoals [Azure Storage Explorer](https://www.storageexplorer.com) om verbinding te maken met dit opslag account, de tabel te bekijken en een query voor tijds tempel toe te voegen in de afgelopen 24 uur. U kunt het hulp programma gebruiken om een CSV-bestand te exporteren en dit te openen in een toepassing zoals micro soft Excel. In Excel kunt u eenvoudig zoeken naar telefoonkaart teken reeksen, zoals **Event hubs**, om te zien welke fout Er wordt gerapporteerd.  

- Controleer of uw Event Hub is ingericht. Alle verbindings gegevens in het gedeelte **PrivateConfig** van de configuratie moeten overeenkomen met de waarden van uw resource, zoals weer gegeven in de portal. Zorg ervoor dat er een SAS-beleid is gedefinieerd (*SendRule* in het voor beeld) in de portal en dat de machtiging *verzenden* wordt verleend.  

## <a name="next-steps"></a>Volgende stappen

* [Event Hubs-overzicht](../../event-hubs/event-hubs-about.md)
* [Een Event Hub maken](../../event-hubs/event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



