---
title: Toegang tot ingebouwde metrieken-Azure IoT Edge
description: Externe toegang tot ingebouwde metrische gegevens van de IoT Edge runtime-onderdelen
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a78db821c0fab01ad5d6752216a8f7682fb2c46
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200500"
---
# <a name="access-built-in-metrics"></a>Ingebouwde metrische gegevens openen

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

De IoT Edge runtime-onderdelen, IoT Edge hub en IoT Edge agent, produceren ingebouwde metrische gegevens in de [Prometheus Exposition-indeling](https://prometheus.io/docs/instrumenting/exposition_formats/). U kunt op afstand toegang krijgen tot deze gegevens om de status van een IoT Edge apparaat te controleren en te begrijpen.

Vanaf de release-1.0.10 worden metrische gegevens standaard automatisch beschikbaar gesteld op **poort 9600** van de **EdgeHub** -en **edgeAgent** -modules ( `http://edgeHub:9600/metrics` en `http://edgeAgent:9600/metrics` ). Deze poorten zijn standaard niet toegewezen aan de host.

Toegang tot metrische gegevens van de host door de metrische poort van de module weer te geven en toe te wijzen `createOptions` . In het volgende voor beeld wordt de standaard poort voor metrische gegevens toegewezen aan poort 9601 op de host:

```
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Kies verschillende en unieke host-poort nummers als u de metrische gegevens van de edgeHub-en edgeAgent-eind punten toewijst.

> [!NOTE]
> De omgevings variabele `httpSettings__enabled` mag niet worden ingesteld op `false` voor ingebouwde meet waarden die beschikbaar zijn voor de verzameling.
>
> Omgevings variabelen die kunnen worden gebruikt om metrische gegevens uit te scha kelen, worden weer gegeven in het [Azure-iotedge opslag plaats-document](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).

## <a name="available-metrics"></a>Beschikbare metrische gegevens

Metrische gegevens bevatten Tags waarmee u de aard van de metrische gegevens die worden verzameld, kunt identificeren. Alle metrische gegevens bevatten de volgende Tags:

| Tag | Description |
|-|-|
| iothub | De hub waarmee het apparaat is praten |
| edge_device | De ID van het huidige apparaat |
| instance_number | Een GUID die de huidige runtime weergeeft. Bij het opnieuw opstarten worden alle metrische gegevens opnieuw ingesteld. Deze GUID maakt het gemakkelijker om opnieuw op te starten. |

In de indeling Prometheus Exposition zijn vier metrische waardetypen beschikbaar: teller, meter, histogram en samen vatting. Zie de [documentatie van Prometheus metric types](https://prometheus.io/docs/concepts/metric_types/)voor meer informatie over de verschillende metrische typen.

De quantiles voor het ingebouwde histogram en de samenvattings gegevens zijn 0,1, 0,5, 0,9 en 0,99.

De **edgeHub** -module levert de volgende metrische gegevens:

| Name | Dimensies | Beschrijving |
|-|-|-|
| `edgehub_gettwin_total` | `source` (bewerkings bron)<br> `id` (module-ID) | Type: teller<br> Totaal aantal GetTwin-aanroepen |
| `edgehub_messages_received_total` | `route_output` (uitvoer die bericht heeft verzonden)<br> `id` | Type: teller<br> Totaal aantal berichten ontvangen van clients |
| `edgehub_messages_sent_total` | `from` (bericht bron)<br> `to` (bericht bestemming)<br>`from_route_output`<br> `to_route_input` (invoer van bericht bestemming)<br> `priority` (bericht prioriteit naar doel) | Type: teller<br> Totaal aantal berichten dat is verzonden naar clients of upstream<br> `to_route_input` is leeg wanneer `to` is $upstream |
| `edgehub_reported_properties_total` | `target`(doel van update)<br> `id` | Type: teller<br> Totale aantal gerapporteerde eigenschappen van updates |
| `edgehub_message_size_bytes` | `id`<br> | Type: samen vatting<br> Bericht grootte van clients<br> Waarden kunnen worden gerapporteerd alsof `NaN` er voor een bepaalde periode (momenteel 10 minuten) geen nieuwe metingen worden gerapporteerd; voor `summary` type, corresponderende `_count` en `_sum` tellers worden verzonden. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Type: samen vatting<br> Gebruikte tijd voor het ophalen van dubbele bewerkingen |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Type: samen vatting<br> De tijd die nodig is om een bericht te verzenden |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Type: samen vatting<br> Gebruikte tijd voor het verwerken van een bericht uit de wachtrij |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Type: samen vatting<br> Gebruikte tijd voor het bijwerken van de gerapporteerde eigenschappen |
| `edgehub_direct_method_duration_seconds` | `from` aanroeper<br> `to` ontvangst | Type: samen vatting<br> De tijd die nodig is om een direct bericht op te lossen |
| `edgehub_direct_methods_total` | `from`<br> `to` | Type: teller<br> Totaal aantal verzonden directe berichten |
| `edgehub_queue_length` | `endpoint` (bericht bron)<br> `priority` (wachtrij prioriteit) | Type: meter<br> Huidige lengte van de wachtrij van de edgeHub voor een bepaalde prioriteit |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | Type: teller<br> Totaal aantal verwijderde berichten vanwege een andere reden |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Type: teller<br> Totaal aantal niet-bevestigde berichten omdat opslag fout is opgetreden |
| `edgehub_offline_count_total` | `id` | Type: teller<br> Het totale aantal keren dat edgeHub offline is gegaan |
| `edgehub_offline_duration_seconds`| `id` | Type: samen vatting<br> Time Edge hub is offline |
| `edgehub_operation_retry_total` | `id`<br> `operation` (bewerkings naam) | Type: teller<br> Totaal aantal keer dat er opnieuw edgeHub bewerkingen zijn uitgevoerd |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (niet geverifieerd)<br> | Type: teller<br> Totaal aantal keren dat clients geen verbinding kunnen maken met edgeHub |

De **edgeAgent** -module levert de volgende metrische gegevens:

| Name | Dimensies | Beschrijving |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Type: meter<br> De hoeveelheid tijd die de module in de implementatie heeft opgegeven en de status actief heeft |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Type: meter<br> De hoeveelheid tijd die de module in de implementatie heeft opgegeven |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Type: teller<br> Aantal keer dat de door edgeAgent aangevraagde docker de module heeft gestart |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Type: teller<br> Aantal keer dat de door edgeAgent gestelde docker de module heeft gestopt |
| `edgeAgent_command_latency_seconds` | `command` | Type: meter<br> Hoe lang het duurde om de opgegeven opdracht uit te voeren met docker. Mogelijke opdrachten zijn: maken, bijwerken, verwijderen, starten, stoppen en opnieuw starten |
| `edgeAgent_iothub_syncs_total` | | Type: teller<br> Aantal keer dat edgeAgent heeft geprobeerd de twee geslaagde en mislukte synchronisatie te synchroniseren. Dit aantal bevat zowel agent aanvragen die een dubbele als een hub op de hoogte stellen van een dubbele update |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Type: teller<br> Aantal keren dat edgeAgent niet is gesynchroniseerd met iotHub. |
| `edgeAgent_deployment_time_seconds` | | Type: teller<br> De hoeveelheid tijd die nodig was om een nieuwe implementatie te volt ooien na ontvangst van een wijziging. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Type: teller<br> Aantal keer dat een ingebouwde edgeAgent direct-methode wordt aangeroepen, zoals ping of opnieuw starten. |
| `edgeAgent_host_uptime_seconds` | | Type: meter<br> Hoe lang de host is ingeschakeld? |
| `edgeAgent_iotedged_uptime_seconds` | | Type: meter<br> Hoelang iotedged is actief |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Type: meter<br> Hoeveelheid resterende ruimte op de schijf |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Type: meter<br> Grootte van de schijf |
| `edgeAgent_used_memory_bytes` | `module_name` | Type: meter<br> Hoeveelheid RAM-geheugen dat door alle processen wordt gebruikt |
| `edgeAgent_total_memory_bytes` | `module_name` | Type: meter<br> Beschikbaar RAM-geheugen |
| `edgeAgent_used_cpu_percent` | `module_name` | Type: histogram<br> Percentage van CPU dat door alle processen wordt gebruikt |
| `edgeAgent_created_pids_total` | `module_name` | Type: meter<br> Het aantal processen of threads dat de container heeft gemaakt |
| `edgeAgent_total_network_in_bytes` | `module_name` | Type: meter<br> Het aantal bytes dat van het netwerk is ontvangen |
| `edgeAgent_total_network_out_bytes` | `module_name` | Type: meter<br> Het aantal bytes dat is verzonden naar het netwerk |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Type: meter<br> Het aantal gelezen bytes van de schijf |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Type: meter<br> Het aantal geschreven bytes naar de schijf |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Type: meter<br> Algemene meta gegevens over het apparaat. De waarde is altijd 0, gegevens worden in de Tags gecodeerd. Let op `experimental_features` en `host_information` zijn JSON-objecten. `host_information` ziet er als volgt uit ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . Opmerking `ServerVersion` is de docker-versie en `Version` is de IOT Edge versie van de beveiligings-daemon. |

## <a name="next-steps"></a>Volgende stappen

* [Inzicht in de runtime van Azure IoT Edge en de architectuur ervan](iot-edge-runtime.md)
* [Eigenschappen van de IoT Edge agent en IoT Edge hub-module apparaatdubbels](module-edgeagent-edgehub.md)
