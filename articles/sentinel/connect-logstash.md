---
title: Verbinding maken met gegevens bronnen via Logstash met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van Logstash voor het door sturen van logboeken van externe gegevens bronnen naar Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: da7d540a4b7982c7f743a7ae968515485b45aa5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035420"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Logstash gebruiken om gegevens bronnen te verbinden met Azure Sentinel

> [!IMPORTANT]
> Gegevens opname met behulp van de Logstash-uitvoer-invoeg toepassing is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de uitvoer-invoeg toepassing van Azure Sentinel voor de **Logstash-engine voor gegevens verzameling** kunt u elk type logboek dat u wilt door Logstash rechtstreeks naar uw log Analytics-werk ruimte verzenden in azure Sentinel. Uw logboeken worden verzonden naar een aangepaste tabel die u gaat definiëren met behulp van de uitvoer-invoeg toepassing.

Zie aan de slag [met Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)voor meer informatie over het werken met de Logstash-engine voor het verzamelen van gegevens.

## <a name="overview"></a>Overzicht

### <a name="architecture-and-background"></a>Architectuur en achtergrond

![Diagram van de architectuur voor logboek opslag.](./media/connect-logstash/logstash-architecture.png)

De Logstash-engine bestaat uit drie onderdelen:

- Invoer-invoeg toepassingen: aangepaste verzameling van gegevens uit verschillende bronnen.
- Filter-invoeg toepassingen: manipulatie en normalisatie van gegevens volgens opgegeven criteria.
- Uitvoer-invoeg toepassingen: aangepaste verzen ding van verzamelde en verwerkte gegevens naar verschillende bestemmingen.

> [!NOTE]
> Azure Sentinel ondersteunt alleen de eigen meegeleverde uitvoer bare invoeg toepassing. Het biedt geen ondersteuning voor uitvoer-invoeg toepassingen van derden voor Azure Sentinel of een andere Logstash-invoeg toepassing van elk type.

De Azure Sentinel output-invoeg toepassing voor Logstash verzendt gegevens in JSON-indeling naar uw Log Analytics-werk ruimte met behulp van de Log Analytics HTTP-gegevens verzamelaar REST API. De gegevens worden opgenomen in aangepaste Logboeken.

- Meer informatie over de [Log Analytics rest API](/rest/api/loganalytics/create-request).
- Meer informatie over [aangepaste logboeken](../azure-monitor/agents/data-sources-custom-logs.md).

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>De Azure Sentinel output-invoeg toepassing implementeren in Logstash

### <a name="step-1-installation"></a>Stap 1: installatie

De Azure Sentinel output-invoeg toepassing is beschikbaar in de Logstash-verzameling.

- Volg de instructies in het document Logstash [Working with pluginss](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) voor het installeren van de ***[micro soft-Logstash-output-Azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)*** -invoeg toepassing.
   
- Als uw Logstash-systeem geen Internet toegang heeft, volgt u de instructies in het Logstash [offline-invoeg toepassing](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) voor het beheren van een offline-invoeg toepassing. (Hiervoor moet u een ander Logstash-systeem met Internet toegang maken.)

### <a name="step-2-configuration"></a>Stap 2: Configuratie

Gebruik de informatie in de Logstash- [structuur van een config file](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) -document en voeg de invoeg toepassing Azure Sentinel output toe aan de configuratie met de volgende sleutels en waarden. (De juiste syntaxis van het configuratie bestand wordt weer gegeven na de tabel.)

| Veldnaam | Gegevenstype | Description |
|----------------|---------------|-----------------|
| `workspace_id` | tekenreeks | Voer uw werk ruimte-ID-GUID in. * |
| `workspace_key` | tekenreeks | Voer de GUID van de primaire sleutel van uw werk ruimte in. * |
| `custom_log_table_name` | tekenreeks | Stel de naam in van de tabel waarin de logboeken worden opgenomen. Er kan slechts één tabel naam per uitvoer-invoeg toepassing worden geconfigureerd. De logboek tabel wordt weer gegeven in azure Sentinel onder **Logboeken**, in **tabellen** in de categorie **aangepaste logboeken** met een `_CL` achtervoegsel. |
| `endpoint` | tekenreeks | Optioneel veld. Standaard is dit het Log Analytics-eind punt. Gebruik dit veld om een alternatief eind punt in te stellen. |
| `time_generated_field` | tekenreeks | Optioneel veld. Met deze eigenschap wordt het standaard **TimeGenerated** -veld in log Analytics overschreven. Voer de naam van het tijds tempel veld in de gegevens bron in. De gegevens in het veld moeten voldoen aan de ISO 8601-indeling ( `YYYY-MM-DDThh:mm:ssZ` ) |
| `key_names` | matrix | Geef een lijst met Log Analytics uitvoer schema velden op. Elk lijst item moet tussen enkele aanhalings tekens worden geplaatst en de items worden gescheiden door komma's en de volledige lijst tussen vier Kante haken. Zie onderstaand voorbeeld. |
| `plugin_flush_interval` | getal | Optioneel veld. Stel in om het maximum interval (in seconden) te definiëren tussen het verzenden van berichten naar Log Analytics. De standaard waarde is 5. |
    | `amount_resizing` | booleaans | Waar of onwaar. Het mechanisme voor automatisch schalen in-of uitschakelen, waarbij de grootte van de bericht buffer wordt aangepast op basis van het volume van de ontvangen logboek gegevens. |
| `max_items` | getal | Optioneel veld. Geldt alleen indien `amount_resizing` ingesteld op ' false '. Gebruiken om een limiet in te stellen voor de buffer grootte van het bericht (in records). De standaardwaarde is 2000.  |
| `azure_resource_id` | tekenreeks | Optioneel veld. Hiermee definieert u de ID van de Azure-resource waarin de gegevens zich bevinden. <br>De resource-ID-waarde is vooral nuttig als u [resource-context RBAC](resource-context-rbac.md) gebruikt om alleen toegang te bieden tot specifieke gegevens. |
| | | |

* U kunt de werk ruimte-ID en primaire sleutel vinden in de werkruimte resource, onder **agents beheren**.

#### <a name="sample-configurations"></a>Voorbeeld configuraties

Hier volgen enkele voor beelden van configuraties met een aantal verschillende opties.

- Een basis configuratie die gebruikmaakt van een filebeat-invoer pipe:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Een basis configuratie die gebruikmaakt van een TCP-invoer pipe:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Een geavanceerde configuratie:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > Ga naar de uitvoer-invoeg toepassing [github opslag plaats](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) voor meer informatie over de interne instellingen, configuratie en prestaties.

### <a name="step-3-restart-logstash"></a>Stap 3: Logstash opnieuw starten

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>Stap 4: inkomende Logboeken in azure-Sentinel weer geven

1. Controleer of berichten worden verzonden naar de uitvoer-invoeg toepassing.

1. Klik in het navigatie menu van de Azure-Sentinel op **Logboeken**. Vouw onder de kop **tabellen** de categorie **aangepaste logboeken** uit. Zoek en klik op de naam van de tabel die u hebt opgegeven (met een `_CL` achtervoegsel) in de configuratie.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Scherm opname van aangepaste logboeken van log-opslag.":::

1. Als u records in de tabel wilt weer geven, moet u een query uitvoeren op de tabel met behulp van de tabel naam als schema.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Scherm opname van de query voor aangepaste logboeken van log-opslag.":::

## <a name="monitor-output-plugin-audit-logs"></a>Audit logboeken voor uitvoer van invoeg toepassingen bewaken

Als u de connectiviteit en activiteit van de Azure Sentinel output-invoeg toepassing wilt controleren, schakelt u het juiste Logstash-logboek bestand in. Zie het document [Logstash Directory-indeling](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) voor de locatie van het logboek bestand.

Als u geen gegevens in dit logboek bestand ziet, genereert en verzendt u gebeurtenissen lokaal (via de invoer-en filter-invoeg toepassingen) om te controleren of de uitvoer-invoeg toepassing gegevens ontvangt. Azure Sentinel ondersteunt alleen problemen met betrekking tot de uitvoer-invoeg toepassing.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Logstash kunt gebruiken om externe gegevens bronnen met Azure Sentinel te verbinden. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp [van ingebouwde](tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.
