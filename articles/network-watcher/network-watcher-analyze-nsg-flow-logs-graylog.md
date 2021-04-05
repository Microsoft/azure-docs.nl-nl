---
title: Stroom logboeken van de Azure-netwerk beveiligings groep analyseren-Graylog | Microsoft Docs
description: Meer informatie over het beheren en analyseren van stroom logboeken voor netwerk beveiligings groepen in azure met behulp van Network Watcher en Graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 8b363d90d05e95b017c3a655b57dbabc3712a155
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965541"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Stroom logboeken van netwerk beveiligings groepen beheren en analyseren in azure met behulp van Network Watcher en Graylog

[Stroom logboeken voor netwerk beveiligings groepen](network-watcher-nsg-flow-logging-overview.md) bevatten informatie die u kunt gebruiken om te begrijpen binnenkomend en IP-verkeer voor Azure-netwerk interfaces. Stroom logboeken tonen uitgaande en binnenkomende stromen op basis van een regel per netwerk beveiligings groep. de netwerk interface van de stroom is van toepassing op 5-tuplegegevens (bron/doel-IP, bron/doel poort, Protocol) over de stroom en als het verkeer is toegestaan of geweigerd.

U kunt veel netwerk beveiligings groepen in uw netwerk hebben waarvoor stroom logboek registratie is ingeschakeld. Verschillende netwerk beveiligings groepen waarvoor stroom logboek registratie is ingeschakeld, kunnen het lastig maken om uw logboeken te parseren en inzicht te krijgen. In dit artikel vindt u een oplossing voor het centraal beheren van deze stroom logboeken voor netwerk beveiligings groepen met behulp van Graylog, een open-source logboek beheer en analyse tool, en Logstash, een gegevens verwerkings pijplijn op basis van een open-source-server.

> [!Warning]
> De volgende stappen werken met stroom logboeken versie 1. Zie [Inleiding tot flow-logboek registratie voor netwerk beveiligings groepen](network-watcher-nsg-flow-logging-overview.md)voor meer informatie. De volgende instructies werken niet met versie 2 van de logboek bestanden, zonder aanpassing.

## <a name="scenario"></a>Scenario

Stroom logboeken voor netwerk beveiligings groepen zijn ingeschakeld met behulp van Network Watcher. Stroom logboeken stroomt in Azure Blob-opslag. Een Logstash-invoeg toepassing wordt gebruikt om stroom logboeken te verbinden en te verwerken vanuit Blob Storage en deze te verzenden naar Graylog. Zodra de stroom logboeken zijn opgeslagen in Graylog, kunnen ze worden geanalyseerd en gevisualiseerd in aangepaste Dash boards.

![Graylog-werk stroom](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Installatie stappen

### <a name="enable-network-security-group-flow-logging"></a>Logboek registratie van stroom van netwerk beveiligings groep inschakelen

Voor dit scenario moet u logboek registratie van stroom netwerk beveiligings groep ingeschakeld hebben op ten minste één netwerk beveiligings groep in uw account. Raadpleeg het volgende artikel [Inleiding tot stroom registratie voor netwerk beveiligings groepen](network-watcher-nsg-flow-logging-overview.md)voor instructies over het inschakelen van stroom logboeken voor de netwerk beveiligings groep.

### <a name="setting-up-graylog"></a>Graylog instellen

In dit voor beeld worden zowel Graylog als Logstash geconfigureerd op een Ubuntu 14,04-server, geïmplementeerd in Azure.

- Raadpleeg de [documentatie](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) van Graylog voor stapsgewijze instructies voor het installeren op Ubuntu.
- Zorg ervoor dat u ook de Graylog-webinterface configureert door de [documentatie](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif)te volgen.

In dit voor beeld wordt de minimale Graylog-installatie gebruikt (dat wil zeggen Eén exemplaar van een Graylog), maar Graylog kan worden ontworpen om te schalen over resources, afhankelijk van uw systeem-en productie behoeften. Zie de [documentatie](https://docs.graylog.org/en/2.2/pages/architecture.html) en de [architectuur handleiding](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)van Graylog voor meer informatie over architectuur overwegingen of een diep gaande architectuur handleiding.

Graylog kunnen op verschillende manieren worden geïnstalleerd, afhankelijk van uw platform en voor keuren. Raadpleeg de officiële [documentatie](https://docs.graylog.org/en/2.2/pages/installation.html)van Graylog voor een volledige lijst met mogelijke installatie methoden. De Graylog-server toepassing wordt uitgevoerd op Linux-distributies en heeft de volgende vereisten:

-  Java SE 8 of hoger – [Azul Azure jdk-documentatie](/azure/developer/java/fundamentals/java-jdk-long-term-support)
-  Elastisch zoeken 2. x (2.1.0 of hoger)- [installatie documentatie voor Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2,4 of hoger – [installatie documentatie voor MongoDb](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Logstash installeren

Logstash wordt gebruikt voor het samen voegen van de stroom logboeken met JSON-indeling naar een stroom tuple-niveau. Door de stroom logboeken samen te voegen, kunnen de logboeken eenvoudiger worden ingedeeld en in Graylog worden gezocht.

1. Als u Logstash wilt installeren, voert u de volgende opdrachten uit:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Configureer Logstash om de stroom logboeken te parseren en te verzenden naar Graylog. Maak een bestand Logstash. conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Voeg de volgende inhoud toe aan het bestand. Wijzig de gemarkeerde waarden zodat deze overeenkomen met de gegevens van uw opslag account:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => ","}
        add_field => {
                    "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                    "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                    "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                    "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                    "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                    "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                    "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                    "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   Het opgegeven Logstash-configuratie bestand bestaat uit drie delen: de invoer, het filter en de uitvoer. In het gedeelte invoer wordt de invoer bron aangegeven van de logboeken die door Logstash worden verwerkt. in dit geval gebruikt u een Azure blog invoer-invoeg toepassing (die in de volgende stappen is geïnstalleerd) waarmee de toegang tot de JSON-bestanden van het stroom logboek van de netwerk beveiligings groep wordt opgeslagen in Blob Storage.

De sectie filter wordt vervolgens elk logboek bestand van de stroom afgevlakt zodat elke afzonderlijke stroom tuple en de bijbehorende eigenschappen een afzonderlijke Logstash-gebeurtenis worden.

Ten slotte stuurt het gedeelte uitvoer elke Logstash-gebeurtenis naar de Graylog-server. Wijzig, indien nodig, het Logstash-configuratie bestand op basis van uw specifieke behoeften.

   > [!NOTE]
   > In het vorige configuratie bestand wordt ervan uitgegaan dat de Graylog-server is geconfigureerd op het back-upip-adres 127.0.0.1 van de lokale host. Als dat niet het geval is, moet u de para meter host wijzigen in de sectie uitvoer naar het juiste IP-adres.

Zie de Logstash- [documentatie](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)voor meer instructies voor het installeren van Logstash.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>De invoeg toepassing voor Logstash-invoer voor Azure Blob Storage installeren

Met de Logstash-invoeg toepassing kunt u rechtstreeks toegang krijgen tot de stroom logboeken van het opgegeven Blob Storage-account. Als u de invoeg toepassing wilt installeren, voert u de volgende opdracht uit in de standaard Logstash-installatie directory (in dit geval/usr/share/logstash/bin):

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Raadpleeg de [documentatie](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)voor meer informatie over deze invoeg toepassing.

### <a name="set-up-connection-from-logstash-to-graylog"></a>Verbinding van Logstash naar Graylog instellen

Nu u een verbinding tot stand hebt gebracht met de stroom logboeken met behulp van Logstash en de Graylog-server hebt ingesteld, moet u Graylog configureren om de binnenkomende logboek bestanden te accepteren.

1. Navigeer naar uw Graylog server-webinterface met behulp van de URL die u hiervoor hebt geconfigureerd. U kunt toegang krijgen tot de interface door uw browser naar te sturen naar `http://<graylog-server-ip>:9000/`

2. Als u naar de configuratie pagina wilt gaan, selecteert u de vervolg keuzelijst **systeem** in de bovenste navigatie balk aan de rechter kant en klikt u vervolgens op **invoer**.
   U kunt ook naar `http://<graylog-server-ip>:9000/system/inputs`

   ![Aan de slag](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Als u de nieuwe invoer wilt starten, selecteert u *GELF UDP* in de vervolg keuzelijst **invoer selecteren** en vult u het formulier in. GELF staat voor de uitgebreide logboek indeling van Graylog. De GELF-indeling wordt ontwikkeld door Graylog. Zie de Graylog- [documentatie](https://docs.graylog.org/en/2.2/pages/gelf.html)voor meer informatie over de voor delen.

   Zorg ervoor dat u de invoer verbindt met het IP-adres waarmee u uw Graylog-server hebt geconfigureerd. Het IP-adres moet overeenkomen met het veld **host** van de UDP-uitvoer van het configuratie bestand Logstash. De standaard poort moet *12201* zijn. Zorg ervoor dat de poort overeenkomt met het **poort** veld in de UDP-uitvoer die is opgegeven in het Logstash-configuratie bestand.

   ![Scherm afbeelding toont de Graylog-invoer, met opties voor het starten en zoeken van invoer.](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Zodra u de invoer hebt gestart, wordt deze weer gegeven onder de sectie **lokale invoer** , zoals wordt weer gegeven in de volgende afbeelding:

   ![Scherm afbeelding met de sectie lokale invoer die de invoer bevat die u hebt gestart.](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Raadpleeg de [documentatie](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs)voor meer informatie over Graylog-bericht invoer.

4. Zodra deze configuraties zijn gemaakt, kunt u Logstash starten om stroom logboeken te lezen met de volgende opdracht: `sudo systemctl start logstash.service` .

### <a name="search-through-graylog-messages"></a>Zoeken in Graylog-berichten

Nadat u enige tijd hebt geduurd dat uw Graylog-server berichten kan verzamelen, kunt u de berichten doorzoeken. Als u wilt controleren of de berichten naar uw Graylog-server worden verzonden, klikt u op de pagina voor de configuratie van de **invoer** op de knop **ontvangen berichten weer geven** van de UDP-invoer van GELF die u hebt gemaakt. U wordt omgeleid naar een scherm dat lijkt op de volgende afbeelding: 

![Scherm afbeelding toont de Graylog-server die het Zoek resultaat, het histogram en de berichten weergeeft.](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Als u op de blauwe koppeling '% {Message} ' klikt, wordt elk bericht uitgevouwen om de para meters van elke stroom-tuple weer te geven, zoals wordt weer gegeven in de volgende afbeelding:

![Scherm opname toont de bericht gegevens van de Graylog-server.](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Standaard worden alle bericht velden in de zoek opdracht opgenomen als u geen specifiek bericht veld selecteert om naar te zoeken. Als u wilt zoeken naar specifieke berichten (bijvoorbeeld – stroom Tuples van een specifiek bron-IP-adres) u kunt de Graylog-Zoek query taal gebruiken als [beschreven](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Stroom logboeken van netwerk beveiligings groepen analyseren met behulp van Graylog

Nu Graylog het is ingesteld, kunt u een deel van de functionaliteit gebruiken om uw stroom logboek gegevens beter te begrijpen. Een dergelijke manier is door Dash boards te gebruiken om specifieke weer gaven van uw gegevens te maken.

### <a name="create-a-dashboard"></a>Een dashboard maken

1. Selecteer in de bovenste navigatie balk de optie **Dash boards** of navigeren naar `http://<graylog-server-ip>:9000/dashboards/`

2. Klik vervolgens op de knop groen **dash board maken** en vul het korte formulier in met de titel en beschrijving van uw dash board. Klik op de knop **Opslaan** om het nieuwe dash board te maken. Er wordt een dash board weer gegeven dat vergelijkbaar is met de volgende afbeelding:

    ![Scherm opname toont Graylog server-Dash boards, met de opties voor het maken en bewerken van Dash boards.](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Widgets toevoegen

U kunt op de titel van het dash board klikken om deze te zien, maar dit is nu leeg, omdat we geen widgets hebben toegevoegd. Een eenvoudig en handig object type dat u aan het dash board kunt toevoegen, zijn **snelle waarden** grafieken, die een lijst met waarden van het geselecteerde veld weer geven, en de bijbehorende distributie.

1. Ga terug naar de zoek resultaten van de UDP-invoer die stroom logboeken ontvangt door **zoeken** te selecteren in de bovenste navigatie balk.

2. Zoek in het deel venster **Zoek resultaten** aan de linkerkant van het scherm naar het tabblad **velden** , waarin de verschillende velden van elk bericht van elke binnenkomende stroom tuple worden weer gegeven.

3. Selecteer een gewenste para meter in die u wilt visualiseren (in dit voor beeld is de IP-bron geselecteerd). Als u de lijst met mogelijke widgets wilt weer geven, klikt u op de blauwe vervolg keuze pijl links van het veld en selecteert u **snelle waarden** om de widget te genereren. De volgende afbeelding ziet er ongeveer als volgt uit:

   ![Bron-IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Hier kunt u de knop **toevoegen aan dash board** selecteren in de rechter bovenhoek van de widget en het bijbehorende dash board selecteren dat u wilt toevoegen.

5. Ga terug naar het dash board om de widget weer te geven die u zojuist hebt toegevoegd.

   U kunt allerlei andere widgets, zoals histogrammen en aantallen, toevoegen aan uw dash board om belang rijke metrische gegevens bij te houden, zoals het voorbeeld dashboard dat wordt weer gegeven in de volgende afbeelding:

   ![Flowlogs-dash board](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Raadpleeg de [documentatie](https://docs.graylog.org/en/2.2/pages/dashboards.html)van Graylog voor meer uitleg over Dash boards en de andere typen widgets.

Als u Network Watcher integreert met Graylog, hebt u nu een handige en gecentraliseerde manier om stroom logboeken voor netwerk beveiligings groepen te beheren en visualiseren. Graylog heeft een aantal andere krachtige functies, zoals stromen en waarschuwingen, die ook kunnen worden gebruikt om stroom logboeken verder te beheren en inzicht te krijgen in het netwerk verkeer. Nu u Graylog hebt ingesteld en verbonden met Azure, kunt u door gaan met het verkennen van de andere functies die worden aangeboden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het visualiseren van stroom logboeken voor netwerk beveiligings groepen met Power BI door in [visualiseren netwerk beveiligings groep stroom logboeken met Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)te bezoeken.