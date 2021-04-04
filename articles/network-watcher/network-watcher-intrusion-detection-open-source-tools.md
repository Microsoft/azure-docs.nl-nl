---
title: Detectie van netwerk inbraak uitvoeren met open source-hulpprogram ma's
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u Azure Network Watcher en open source-hulpprogram ma's gebruikt voor het detecteren van inbreuk op het netwerk
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b4b38112e32a55739ea14d5ff06e327819171557
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014549"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Detectie van binnendringing van het netwerk met Network Watcher en open source-hulpprogram ma's uitvoeren

Pakket opnames zijn een belang rijk onderdeel voor het implementeren van indringings detectie systemen (ID'S) voor netwerk beveiliging en het uitvoeren van netwerk beveiligings bewaking (NSM). Er zijn verschillende open source-id-hulpprogram ma's die pakket opnames verwerken en zoeken naar hand tekeningen van mogelijke netwerk indringers en schadelijke activiteiten. Met behulp van de pakket opnames van Network Watcher, kunt u uw netwerk analyseren op schadelijke indringers of beveiligings problemen.

Een dergelijk open source-hulp programma is Suricata, een id-engine die gebruikmaakt van rules om netwerk verkeer te bewaken en waarschuwingen te activeren wanneer er verdachte gebeurtenissen optreden. Suricata biedt een multi thread-engine, wat betekent dat IT netwerk verkeer analyse kan uitvoeren met verhoogde snelheid en efficiëntie. Ga voor meer informatie over Suricata en de mogelijkheden ervan naar hun website op https://suricata-ids.org/ .

## <a name="scenario"></a>Scenario

In dit artikel wordt uitgelegd hoe u uw omgeving instelt voor het detecteren van inbreuk op het netwerk met behulp van Network Watcher, Suricata en de elastische stack. Network Watcher biedt u de pakket opnames die worden gebruikt voor het detecteren van inbreuk op het netwerk. Suricata verwerkt de pakket opnames en trigger waarschuwingen op basis van pakketten die overeenkomen met de opgegeven Rules van dreigingen. Deze waarschuwingen worden opgeslagen in een logboek bestand op uw lokale computer. Met behulp van de elastische stack kunnen de logboeken die door Suricata worden gegenereerd, worden geïndexeerd en gebruikt voor het maken van een Kibana-dash board, zodat u een visuele weer gave van de logboeken en een manier krijgt om snel inzicht te krijgen in mogelijke netwerk problemen.  

![scenario voor eenvoudige webtoepassingen][1]

Beide open source-hulpprogram ma's kunnen worden ingesteld op een virtuele Azure-machine, zodat u deze analyse kunt uitvoeren binnen uw eigen Azure-netwerk omgeving.

## <a name="steps"></a>Stappen

### <a name="install-suricata"></a>Suricata installeren

Voor alle andere installatie methoden gaat u naar https://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. Voer de volgende opdrachten uit in de opdracht regel terminal van uw VM:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Als u de installatie wilt controleren, voert u de opdracht uit `suricata -h` om de volledige lijst met opdrachten weer te geven.

### <a name="download-the-emerging-threats-ruleset"></a>De Rules voor opkomende bedreigingen downloaden

In deze fase zijn er geen regels voor het uitvoeren van Suricata. U kunt uw eigen regels maken als er specifieke dreigingen zijn voor uw netwerk die u wilt detecteren, of u kunt ook ontwikkelde regel sets gebruiken van een aantal providers, zoals opkomende dreigingen of VRT regels van snort. We gebruiken hier de ruleset met vrije beschik bare opkomende bedreigingen:

Down load de regelset en kopieer deze naar de map:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Pakket opnames verwerken met Suricata

Als u pakket opnames wilt verwerken met Suricata, voert u de volgende opdracht uit:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Lees het bestand Fast. log om de resulterende waarschuwingen te controleren:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>De elastische stack instellen

Hoewel de logboeken die Suricata genereren waardevolle informatie bevatten over wat er op ons netwerk gebeurt, zijn deze logboek bestanden niet het eenvoudigst te lezen en te begrijpen. Door Suricata te verbinden met de elastische stack, kunnen we een Kibana-dash board maken waarmee we in de logboeken inzichten kunnen zoeken, tekenen, analyseren en afleiden.

#### <a name="install-elasticsearch"></a>Elasticsearch installeren

1. Voor de elastische stack van versie 5,0 en hoger is Java 8 vereist. Voer de opdracht uit `java -version` om uw versie te controleren. Als Java niet is geïnstalleerd, raadpleegt u de documentatie op de [Azure-Suppored JDKs](/azure/developer/java/fundamentals/java-jdk-long-term-support).

1. Down load het juiste binaire pakket voor uw systeem:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Andere installatie methoden vindt u op [Elasticsearch-installatie](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html) .

1. Controleer of Elasticsearch wordt uitgevoerd met de opdracht:

    ```
    curl http://127.0.0.1:9200
    ```

    Er wordt een antwoord weer gegeven dat er ongeveer als volgt uitziet:

    ```
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Raadpleeg de pagina- [installatie](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html) voor meer instructies voor het installeren van elastisch zoeken

### <a name="install-logstash"></a>Logstash installeren

1. Voer de volgende opdrachten uit om Logstash te installeren:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Vervolgens moet u Logstash configureren om de uitvoer van eve.jsin het bestand te lezen. Maak een logstash. conf-bestand met behulp van:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Voeg de volgende inhoud toe aan het bestand (zorg ervoor dat het pad naar de eve.jsin het bestand juist is):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Zorg ervoor dat u de juiste machtigingen hebt voor de eve.jsin het bestand, zodat het bestand door Logstash kan worden opgenomen.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Voer de volgende opdracht uit om Logstash te starten:

    ```
    sudo /etc/init.d/logstash start
    ```

Raadpleeg de [officiële documentatie](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html) voor meer instructies voor het installeren van Logstash.

### <a name="install-kibana"></a>Kibana installeren

1. Voer de volgende opdrachten uit om Kibana te installeren:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Gebruik de volgende opdrachten om Kibana uit te voeren:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Als u uw Kibana web interface wilt weer geven, gaat u naar `http://localhost:5601`
1. Voor dit scenario is het index patroon dat wordt gebruikt voor de logboeken van de Suricata "logstash-*"

1. Als u het Kibana-dash board extern wilt weer geven, maakt u een inkomende NSG-regel waarmee toegang tot **poort 5601** wordt toegestaan.

### <a name="create-a-kibana-dashboard"></a>Een Kibana-dash board maken

Voor dit artikel hebben we een voor beeld van een dash board voor u gegeven om trends en Details in uw waarschuwingen weer te geven.

1. Down load het dashboard bestand [hier](https://aka.ms/networkwatchersuricatadashboard) [, het visualisatie bestand en](https://aka.ms/networkwatchersuricatavisualization)het opgeslagen Zoek bestand [hier](https://aka.ms/networkwatchersuricatasavedsearch).

1. Ga op het tabblad **beheer** van Kibana naar **opgeslagen objecten** en importeer alle drie de bestanden. Op het tabblad **dash board** kunt u het voorbeeld dashboard openen en laden.

U kunt ook uw eigen visualisaties en dash boards maken die zijn afgestemd op metrische gegevens van uw eigen interesse. Lees meer over het maken van Kibana-visualisaties in de [officiële documentatie](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)van Kibana.

![kibana-dash board][2]

### <a name="visualize-ids-alert-logs"></a>Waarschuwings logboeken voor ID'S visualiseren

Het voorbeeld Dashboard bevat verschillende visualisaties van de Suricata-waarschuwings logboeken:

1. Waarschuwingen per GeoIP: een kaart waarop de distributie van waarschuwingen wordt weer gegeven op basis van de geografische locatie (bepaald door IP)

    ![Geo-IP][3]

1. Top 10 van waarschuwingen: een samen vatting van de tien meest frequent geactiveerde waarschuwingen en de bijbehorende beschrijvingen. Als u op een afzonderlijke waarschuwing klikt, wordt het dash board gefilterd op de informatie die betrekking heeft op die specifieke waarschuwing.

    ![afbeelding 4][4]

1. Aantal waarschuwingen: het totale aantal waarschuwingen dat door de ruleset wordt geactiveerd

    ![afbeelding 5][5]

1. Top 20 van bron/doel-Ip's/poorten-cirkel grafieken met de top 20 Ip's en poorten waarop waarschuwingen zijn geactiveerd. U kunt filteren op specifieke Ip's/poorten om te zien hoeveel en wat voor soort waarschuwingen worden geactiveerd.

    ![afbeelding 6][6]

1. Overzicht van waarschuwingen: een tabel met een overzicht van specifieke details van elke afzonderlijke waarschuwing. U kunt deze tabel aanpassen om andere belang rijke para meters voor elke waarschuwing weer te geven.

    ![afbeelding 7][7]

Zie [de officiële documentatie van Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html)voor meer informatie over het maken van aangepaste visualisaties en dash boards.

## <a name="conclusion"></a>Conclusie

Door pakket opnames te combi neren die worden verstrekt door Network Watcher en open source Identifier-hulpprogram ma's, zoals Suricata, kunt u de detectie van het netwerk inbraak voor een breed scala aan bedreigingen uitvoeren. Deze Dash boards bieden u de mogelijkheid om trends en afwijkingen in uw netwerk snel op te sporen, evenals de gegevens om hoofd oorzaken van waarschuwingen te detecteren, zoals kwaadwillende gebruikers agenten of kwets bare poorten. Met deze geëxtraheerde gegevens kunt u weloverwogen beslissingen nemen over het reageren op en het beschermen van uw netwerk tegen schadelijke aanvallen en regels maken om te voor komen dat uw netwerk in de toekomst binnenkomt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het activeren van pakket opnames op basis van waarschuwingen met [behulp van pakket opname gebruiken voor proactieve netwerk bewaking met Azure functions](network-watcher-alert-triggered-packet-capture.md)

Meer informatie over het visualiseren van uw NSG-stroom logboeken met Power BI door [visuals NSG flow-logboeken te bezoeken met Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png