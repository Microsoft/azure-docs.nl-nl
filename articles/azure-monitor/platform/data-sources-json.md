---
title: Aangepaste JSON-gegevens bronnen met de Log Analytics-agent voor Linux verzamelen in Azure Monitor
description: Aangepaste JSON-gegevens bronnen kunnen worden verzameld in Azure Monitor met behulp van de Log Analytics-agent voor Linux.  Deze aangepaste gegevens bronnen kunnen eenvoudige scripts zijn die JSON als resultaat hebben, zoals krul of een van de 300 + invoeg toepassingen met een vloeiende waarde. In dit artikel wordt de configuratie beschreven die vereist is voor het verzamelen van gegevens.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa6931fc91838173a856ef500145fa49f2606271
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655200"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Aangepaste JSON-gegevens bronnen met de Log Analytics-agent voor Linux verzamelen in Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Aangepaste JSON-gegevens bronnen kunnen worden verzameld in [Azure monitor](data-platform.md) met behulp van de log Analytics-agent voor Linux.  Deze aangepaste gegevens bronnen kunnen eenvoudige scripts zijn die JSON als resultaat hebben, zoals [krul](https://curl.haxx.se/) of een van [de 300 + invoeg toepassingen met een vloeiende](https://www.fluentd.org/plugins/all)waarde. In dit artikel wordt de configuratie beschreven die vereist is voor het verzamelen van gegevens.


> [!NOTE]
> Log Analytics agent voor Linux v 1.1.0-217 + is vereist voor aangepaste JSON-gegevens

## <a name="configuration"></a>Configuratie

### <a name="configure-input-plugin"></a>Invoer-plugin configureren

Als u JSON-gegevens in Azure Monitor wilt verzamelen, voegt `oms.api.` u toe aan het begin van een vloeiende tag in een invoer-invoeg toepassing.

Het volgende is bijvoorbeeld een afzonderlijk configuratie bestand `exec-json.conf` in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` .  Hierbij wordt gebruikgemaakt van de gefluente invoeg toepassing `exec` om elke 30 seconden een krul opdracht uit te voeren.  De uitvoer van deze opdracht wordt verzameld door de JSON-uitvoer-invoeg toepassing.

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

Het eigendom van het configuratie bestand `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` dat u hebt toegevoegd, moet zijn gewijzigd met de volgende opdracht.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Invoeg toepassing voor uitvoer configureren 
Voeg de volgende configuratie voor de uitvoer-invoeg toepassing toe aan de hoofd configuratie in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` of als een afzonderlijk configuratie bestand dat is geplaatst in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Log Analytics agent voor Linux opnieuw starten
Start de Log Analytics-agent voor Linux-service opnieuw met de volgende opdracht.

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>Uitvoer
De gegevens worden verzameld in Azure Monitor met een record type van `<FLUENTD_TAG>_CL` .

Bijvoorbeeld de aangepaste tag `tag oms.api.tomcat` in azure monitor met het record type `tomcat_CL` .  U kunt alle records van dit type ophalen met de volgende logboek query.

```console
Type=tomcat_CL
```

Geneste JSON-gegevens bronnen worden ondersteund, maar worden geïndexeerd op basis van een van de bovenliggende velden. De volgende JSON-gegevens worden bijvoorbeeld geretourneerd van een logboek query als `tag_s : "[{ "a":"1", "b":"2" }]` .

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen. 
