---
title: NSG-stroom logboeken beheren met Grafana
titleSuffix: Azure Network Watcher
description: Beheer van stroom logboeken voor netwerk beveiligings groepen in azure beheren en analyseren met behulp van Network Watcher en Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: d522d305c70214009b8aa2886d07d2d5403dd2b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656305"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Stroom logboeken voor netwerk beveiligings groepen beheren en analyseren met behulp van Network Watcher en Grafana

[Stroom logboeken voor netwerk beveiligings groepen (NSG)](network-watcher-nsg-flow-logging-overview.md) bevatten informatie die kan worden gebruikt voor het begrijpen van binnenkomend en IP-verkeer op netwerk interfaces. In deze stroom logboeken worden uitgaande en inkomende stromen weer gegeven op basis van een per NSG regel, de NIC waarop de stroom van toepassing is, 5-tuple informatie over de stroom (bron/doel-IP, bron/doel poort, Protocol) en als het verkeer is toegestaan of geweigerd.

U kunt veel Nsg's in uw netwerk hebben waarvoor stroom logboek registratie is ingeschakeld. Deze hoeveelheid logboek registratie gegevens maakt het lastig om uw logboeken te parseren en inzicht te krijgen. Dit artikel biedt een oplossing voor het centraal beheren van deze NSG-stroom logboeken met behulp van Grafana, een open-source Graphing tool, ElasticSearch, een gedistribueerde zoek-en analyse-engine en Logstash, een open-source-pipeline voor gegevens verwerking.  

## <a name="scenario"></a>Scenario

NSG-stroom logboeken zijn ingeschakeld met Network Watcher en worden opgeslagen in Azure Blob-opslag. Een Logstash-invoeg toepassing wordt gebruikt om stroom logboeken te verbinden en te verwerken vanuit Blob Storage en deze te verzenden naar ElasticSearch.  Zodra de stroom logboeken zijn opgeslagen in ElasticSearch, kunnen ze worden geanalyseerd en gevisualiseerd in aangepaste Dash boards in Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Installatiestappen

### <a name="enable-network-security-group-flow-logging"></a>Logboek registratie van stroom van netwerk beveiligings groep inschakelen

Voor dit scenario moet u logboek registratie van stroom netwerk beveiligings groep ingeschakeld hebben op ten minste één netwerk beveiligings groep in uw account. Raadpleeg het volgende artikel [Inleiding tot stroom logboeken voor netwerk beveiligings groepen](network-watcher-nsg-flow-logging-overview.md)voor instructies over het inschakelen van Logboeken voor netwerk beveiligings stromen.

### <a name="setup-considerations"></a>Overwegingen bij de installatie

In dit voor beeld worden Grafana, ElasticSearch en Logstash geconfigureerd op een Ubuntu 16,04 LTS-server die is geïmplementeerd in Azure. Deze minimale installatie wordt gebruikt voor het uitvoeren van alle drie de onderdelen – ze worden allemaal uitgevoerd op dezelfde VM. Deze installatie mag alleen worden gebruikt voor test doeleinden en niet-essentiële workloads. Logstash, Elasticsearch en Grafana kunnen allemaal worden ontworpen om onafhankelijk van elkaar te schalen. Zie de documentatie voor elk van deze onderdelen voor meer informatie.

### <a name="install-logstash"></a>Logstash installeren

U gebruikt Logstash om de door JSON geformatteerde stroom logboeken samen te voegen met een stroom-tuple niveau.

1. Als u Logstash wilt installeren, voert u de volgende opdrachten uit:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configureer Logstash om de stroom logboeken te parseren en te verzenden naar ElasticSearch. Maak een Logstash. conf-bestand met behulp van:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Voeg de volgende inhoud toe aan het bestand. Wijzig de naam van het opslag account en de toegangs sleutel zodat deze overeenkomen met de gegevens van uw opslag account:

   ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
        add_field => {
          "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
          "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
          "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
          "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
          "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
          "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
          "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
          "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
    "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
    "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
    "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
    "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
    "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
        }
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
        match => ["unixtimestamp" , "UNIX"]
      }
    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
   ```

Het opgegeven Logstash-configuratie bestand bestaat uit drie delen: de invoer, het filter en de uitvoer.
In het gedeelte invoer wordt de invoer bron aangegeven van de logboeken die door Logstash worden verwerkt. in dit geval gaan we een ' azureblob-invoeg toepassing gebruiken (geïnstalleerd in de volgende stappen) waarmee we toegang krijgen tot de NSG flow log-logboek bestanden die zijn opgeslagen in Blob Storage. 

De sectie filter wordt vervolgens elk logboek bestand van de stroom afgevlakt zodat elke afzonderlijke stroom tuple en de bijbehorende eigenschappen een afzonderlijke Logstash-gebeurtenis worden.

Ten slotte stuurt het gedeelte uitvoer elke Logstash-gebeurtenis naar de ElasticSearch-server. U kunt het Logstash-configuratie bestand aanpassen aan uw specifieke behoeften.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>De Logstash-invoer-invoeg toepassing voor Azure Blob Storage installeren

Met deze Logstash-invoeg toepassing kunt u rechtstreeks toegang krijgen tot de stroom logboeken van het opgegeven Blob Storage-account. Als u deze invoeg toepassing wilt installeren, voert u de volgende opdracht uit in de standaard Logstash-installatiemap (in dit geval/usr/share/logstash/bin):

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Zie voor meer informatie over deze invoeg toepassing [Logstash-invoer-invoeg toepassing voor Azure Storage-blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>ElasticSearch installeren

U kunt het volgende script gebruiken om ElasticSearch te installeren. Zie [elastische stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html)voor meer informatie over het installeren van ElasticSearch.

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Grafana installeren

Voer de volgende opdrachten uit om Grafana te installeren en uit te voeren:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Zie [installaties op Debian/Ubuntu](https://docs.grafana.org/installation/debian/)voor aanvullende informatie over de installatie.

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>De ElasticSearch-server toevoegen als gegevens bron

Vervolgens moet u de ElasticSearch-index met stroom logboeken toevoegen als een gegevens bron. U kunt een gegevens bron toevoegen door **gegevens bron toevoegen** te selecteren en het formulier met de relevante gegevens in te vullen. Een voor beeld van deze configuratie vindt u in de volgende scherm afbeelding:

![Gegevensbron toevoegen](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Een dashboard maken

Nu u Grafana hebt geconfigureerd voor het lezen van de ElasticSearch-index met NSG-stroom logboeken, kunt u Dash boards maken en personaliseren. Als u een nieuw dash board wilt maken, selecteert u **uw eerste dash board maken**. De volgende voorbeeld grafiek configuratie toont stromen, gesegmenteerd door NSG regel:

![Dashboard grafiek](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

In de volgende scherm afbeelding ziet u een grafiek en grafiek met de belangrijkste stromen en hun frequentie. Stromen worden ook weer gegeven door de NSG-regel en stromen op beslissing. Grafana is zeer aanpasbaar, dus het is raadzaam om Dash boards te maken op basis van uw specifieke bewakings behoeften. In het volgende voor beeld ziet u een typisch dash board:

![Scherm opname van de configuratie van de voorbeeld grafiek met stromen gesegmenteerd door NSG regel.](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusie

Als u Network Watcher integreert met ElasticSearch en Grafana, hebt u nu een handige en gecentraliseerde manier om NSG-stroom logboeken en andere gegevens te beheren en visualiseren. Grafana heeft een aantal andere krachtige grafiek functies die ook kunnen worden gebruikt om stroom logboeken verder te beheren en inzicht te krijgen in het netwerk verkeer. Nu u een Grafana-exemplaar hebt ingesteld en verbonden met Azure, kunt u door gaan met het verkennen van de andere functionaliteit die het biedt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van [Network Watcher](network-watcher-monitoring-overview.md).

