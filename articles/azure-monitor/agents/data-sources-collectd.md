---
title: Gegevens verzamelen van verzamelde in Azure Monitor | Microsoft Docs
description: Verzamelde is een open-source Linux-daemon waarmee periodiek gegevens worden verzameld van toepassingen en systeem niveau gegevens.  Dit artikel bevat informatie over het verzamelen van gegevens uit verzamelde in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: daeb587349d885b3e6820b8da2951c3790eb1ac6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039577"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Gegevens verzamelen van verzamelde op Linux-agents in Azure Monitor
[Verzamelde](https://collectd.org/) is een open-source Linux-daemon waarmee periodiek prestatie gegevens worden verzameld van toepassingen en systeem niveau gegevens. Voor beelden van toepassingen zijn de Java Virtual Machine (JVM), MySQL-server en nginx. Dit artikel bevat informatie over het verzamelen van prestatie gegevens van verzamelde in Azure Monitor.

Een volledige lijst met beschik bare invoeg toepassingen vindt u in de [tabel met invoeg toepassingen](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Overzicht van verzamelde](media/data-sources-collectd/overview.png)

De volgende verzamelde-configuratie is opgenomen in de Log Analytics-agent voor Linux om verzamelde-gegevens te routeren naar de Log Analytics-agent voor Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

Als u een versie van verzamelde vóór 5,5 gebruikt, moet u ook de volgende configuratie gebruiken.

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

De verzamelde-configuratie maakt gebruik van de standaard- `write_http` invoeg toepassing voor het verzenden van metrische gegevens over de prestaties via poort 26000 naar log Analytics-agent voor Linux. 

> [!NOTE]
> Deze poort kan zo nodig worden geconfigureerd voor een aangepaste poort.

De Log Analytics-agent voor Linux luistert ook naar poort 26000 voor metrische gegevens over verzamelde en converteert deze vervolgens naar Azure Monitor schema-metrische gegevens. Hieronder vindt u de configuratie van de Log Analytics-agent voor Linux  `collectd.conf` .

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

> [!NOTE]
> Verzamelde is standaard ingesteld op het lezen van waarden met een [interval](https://collectd.org/wiki/index.php/Interval)van 10 seconden. Aangezien dit rechtstreeks van invloed is op het volume van de gegevens die worden verzonden naar Azure Monitor logboeken, moet u dit interval mogelijk binnen de verzamelde-configuratie afstemmen om een goede balans tussen de bewakings vereisten en de bijbehorende kosten en het gebruik voor Azure Monitor-logboeken te kunnen aansturen.

## <a name="versions-supported"></a>Ondersteunde versies
- Azure Monitor biedt momenteel ondersteuning voor verzamelde-versie 4,8 en hoger.
- Log Analytics-agent voor Linux v 1.1.0-217 of hoger is vereist voor de verzameling van verzamelde-metrische gegevens.


## <a name="configuration"></a>Configuratie
Hieronder volgen de basis stappen voor het configureren van het verzamelen van verzamelde-gegevens in Azure Monitor.

1. Configureer verzamelde om gegevens te verzenden naar de Log Analytics-agent voor Linux met behulp van de write_http-invoeg toepassing.  
2. Configureer de Log Analytics-agent voor Linux om te Luis teren naar de verzamelde-gegevens op de juiste poort.
3. Start de verzamelde-en Log Analytics-agent voor Linux opnieuw.

### <a name="configure-collectd-to-forward-data"></a>Verzamelde configureren voor het door sturen van gegevens 

1. Als u verzamelde-gegevens naar de Log Analytics-agent voor Linux wilt routeren, `oms.conf` moet u deze toevoegen aan de configuratiemap van verzamelde. Het doel van dit bestand is afhankelijk van de Linux-distributie van uw computer.

    Als uw map verzamelde config zich in/etc/collectd.d/bevindt:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    Als uw map verzamelde config zich in/etc/collectd/collectd.conf.d/bevindt:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >Voor verzamelde-versies vóór 5,5 moet u de tags wijzigen in `oms.conf` zoals hierboven wordt weer gegeven.
    >

2. Kopieer verzamelde. conf naar de map omsagent Configuration van de gewenste werk ruimte.

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. Start de verzamelde-en Log Analytics-agent voor Linux opnieuw met de volgende opdrachten.

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Verzamelde meet waarden voor Azure Monitor schema conversie
Voor het onderhouden van een bekend model tussen infrastructuur gegevens die al zijn verzameld door Log Analytics agent voor Linux en de nieuwe metrische gegevens die door verzamelde worden verzameld, wordt de volgende schema toewijzing gebruikt:

| Het veld waarde verzamelde | Azure Monitor veld |
|:--|:--|
| `host` | Computer |
| `plugin` | Geen |
| `plugin_instance` | Exemplaar naam<br>Als **plugin_instance** *Null* is, then INSTANCENAME = "*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Als **type_instance** *Null* is, dan CounterName =**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | Geen |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboek query's](../logs/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen. 
* Gebruik [aangepaste velden](../logs/custom-fields.md) voor het parseren van gegevens van syslog-records in afzonderlijke velden.