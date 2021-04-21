---
title: ElasticSearch implementeren op een virtuele ontwikkelmachine in Azure
description: De Elastic Stack (ELK) installeren op een linux-ontwikkel-VM in Azure
services: virtual-machines
author: rloutlaw
manager: justhe
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: 3ce28efa30b04a2a762c39f47dac739633eac209
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818472"
---
# <a name="install-the-elastic-stack-elk-on-an-azure-vm"></a>De Elastic Stack (ELK) installeren op een Azure-VM

Dit artikel begeleidt u bij het implementeren van [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) en [Kibana](https://www.elastic.co/products/kibana) op een Ubuntu-VM in Azure. Als u Elastic Stack in actie wilt zien, kunt u verbinding maken met Kibana en de voorbeeldlogboekgegevens gebruiken. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Ubuntu-VM maken in een Azure-resourcegroep
> * Elasticsearch, Logstash en Kibana installeren op de virtuele machine
> * Voorbeeldgegevens met Logstash verzenden naar Elasticsearch 
> * Poorten openen en met gegevens werken in de Kibana-console


 Deze implementatie is geschikt voor basisontwikkeling met de Elastic Stack. Zie de [Documentatie bij Elastic](https://www.elastic.co/guide/index.html) en het [Azure Architecture Center](/azure/architecture/elasticsearch/) voor meer informatie over de Elastic Stack, inclusief aanbevelingen voor een productie-omgeving.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.4 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM met de opdracht [az vm create](/cli/azure/vm). 

In het volgende voorbeeld wordt een VM gemaakt met de naam *myVM* en worden er SSH-sleutels gemaakt, als deze nog niet bestaan op een standaardsleutellocatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Wanneer de virtuele machine is gemaakt, toont de Azure CLI informatie die lijkt op de informatie in het volgende voorbeeld. Noteer het `publicIpAddress`. Dit adres wordt gebruikt voor toegang tot de virtuele machine.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>SSH in uw virtuele machine

Als u het openbare IP-adres van uw VM niet weet, voert u de opdracht [az network public-ip list](/cli/azure/network/public-ip) uit:

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Gebruik de volgende opdracht om een SSH-sessie te starten voor de virtuele machine. Gebruik hierbij het juiste openbare IP-adres van uw virtuele machine. In dit voorbeeld is het IP-adres *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>De Elastic Stack installeren

Importeer de handtekeningsleutel van Elasticsearch en werk uw lijst met APT-bronnen bij zodat ook de opslagplaats voor Elastic-pakketten wordt opgenomen:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Installeer de Java Virtual Machine op de virtuele machine en configureer de variabele JAVA_HOME. Dit is nodig voor de uitvoering van de Elastic Stack-onderdelen.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Voer de volgende opdrachten uit om bronnen van Ubuntu-pakketten bij te werken en Elasticsearch, Kibana en Logstash te installeren.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Gedetailleerde installatie-instructies, waaronder directory-indelingen en een eerste configuratie, worden bijgehouden in de [documentatie van Elastic](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Elasticsearch starten 

Voer de volgende opdracht uit als u Elasticsearch op uw virtuele machine wilt starten:

```bash
sudo systemctl start elasticsearch.service
```

Deze opdracht produceert geen uitvoer. Controleer daarom met deze `curl`-opdracht of Elasticsearch op de virtuele machine wordt uitgevoerd:

```bash
sudo curl -XGET 'localhost:9200/'
```

Als Elasticsearch wordt uitgevoerd, ziet u ongeveer de volgende uitvoer:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Logstash starten en gegevens toevoegen aan Elasticsearch

Start Logstash met de volgende opdracht:

```bash 
sudo systemctl start logstash.service
```

Test Logstash in de interactieve modus om te controleren of het correct werkt:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Dit is een eenvoudige Logstash-pijplijn [die](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) standaardinvoer echot naar standaarduitvoer. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Stel Logstash in om de kernelberichten van deze virtuele machine door te sturen naar Elasticsearch. Maak een nieuw bestand in een lege map met de naam `vm-syslog-logstash.conf` en plak daarin de volgende Logstash-configuratie:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Test deze configuratie en stuur de syslog-gegevens naar Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

De syslog-vermeldingen worden in uw terminal herhaald wanneer ze zijn verzonden naar Elasticsearch. Gebruik `CTRL+C` om Logstash af te sluiten nadat u wat gegevens hebt verzonden.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Kibana starten en de gegevens visualiseren in Elasticsearch

Bewerk `/etc/kibana/kibana.yml` en wijzig het IP-adres dat Kibana controleert, zodat u het vanuit de webbrowser kunt openen.

```bash
server.host: "0.0.0.0"
```

Start Kibana met de volgende opdracht:

```bash
sudo systemctl start kibana.service
```

Open poort 5601 vanuit de Azure CLI om externe toegang tot de Kibana-console toe te staan:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Open de Kibana-console en selecteer **Create** om een standaardindex te genereren op basis van de syslog-gegevens die u eerder naar Elasticsearch hebt verzonden. 

![Schermopname van de Kibana-console, met de knop Create gemarkeerd.](media/elasticsearch-install/kibana-index.png)

Selecteer **Discover** op de Kibana-console om syslog-gebeurtenissen te zoeken, te filteren of erdoorheen te bladeren.

![Door syslog-gebeurtenissen in Kibana bladeren](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de Elastic Stack geïmplementeerd op een virtuele ontwikkelmachine in Azure. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Ubuntu-VM maken in een Azure-resourcegroep
> * Elasticsearch, Logstash en Kibana installeren op de virtuele machine
> * Voorbeeldgegevens vanuit Logstash verzenden naar Elasticsearch 
> * Poorten openen en met gegevens werken in de Kibana-console
