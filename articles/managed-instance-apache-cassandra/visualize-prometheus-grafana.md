---
title: Grafana configureren voor het visualiseren van metrische gegevens die vanuit een beheerd exemplaar van Azure worden verzonden voor Apache Cassandra
description: Meer informatie over het installeren en configureren van Grafana in een VM voor het visualiseren van metrische gegevens die afkomstig zijn van een door Azure beheerd exemplaar voor Apache Cassandra cluster.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745591"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Grafana configureren voor het visualiseren van metrische gegevens die afkomstig zijn van het beheerde exemplaar van het cluster

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Wanneer u een door Azure beheerd exemplaar voor Apache Cassandra-cluster implementeert, is de service van toepassing op een server die als host fungeert voor [Prometheus](https://prometheus.io/) die kan worden gebruikt door diverse client hulpprogramma's. Prometheus is een open source-bewakings oplossing. In het beheerde exemplaar worden metrische gegevens gegeven en worden er 10 minuten of 10 GB (afhankelijk van de drempel waarde voor het eerst) bewaard. In dit artikel wordt beschreven hoe u Grafana configureert voor het visualiseren van metrische gegevens die afkomstig zijn uit het cluster van het beheerde exemplaar. De volgende taken zijn vereist voor het visualiseren van metrische gegevens:

* Implementeer een Ubuntu-virtuele machine in de Azure-Virtual Network waar het beheerde exemplaar aanwezig is.
* Installeer het open-source [Grafana-hulp programma](https://grafana.com/grafana/) om Dash boards te bouwen en metrische gegevens te visualiseren die afkomstig zijn van Prometheus.

## <a name="deploy-a-ubuntu-server"></a>Een Ubuntu-Server implementeren

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Ga naar de resource groep waar uw beheerde exemplaar van het cluster zich bevindt. Selecteer **toevoegen** en zoeken naar **Ubuntu Server 18,04 LTS** -installatie kopie:

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Zoek de Ubuntu-Server installatie kopie van de Azure Portal." border="true":::

1. Kies de installatie kopie en selecteer **maken**.

1. Voer op de Blade **een virtuele machine maken** waarden in voor de volgende velden, u kunt de standaard waarden voor andere velden laten staan:

   * **Naam van virtuele machine** : Voer een naam in voor de VM.
   * **Regio** : Selecteer de regio waarin uw Virtual Network is geïmplementeerd.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Vul het formulier in om een VM te maken met een Ubuntu-Server installatie kopie." border="true":::

1. Selecteer op het tabblad **netwerken** het Virtual Network waarin uw beheerde exemplaar is geïmplementeerd:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Configureer de netwerk instellingen van de Ubuntu-Server." border="true":::

1. Selecteer vervolgens **controleren + maken** om uw Grafana-server te maken.

## <a name="install-grafana"></a>Grafana installeren

1. Open in de Azure Portal de Virtual Network waar u het beheerde exemplaar en de Grafana-server hebt geïmplementeerd. U ziet een installatie kopie van een virtuele-machine schaalset met de naam **Cassandra-Jump (instantie 0)**. Deze metrische Prometheus-gegevens worden gehost in deze schaalset voor virtuele machines. Noteer het IP-adres van deze instantie:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Het IP-adres van het Prometheus-exemplaar ophalen." border="true":::

1. Maak verbinding met uw zojuist gemaakte Ubuntu-server met behulp van [Azure cli](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) of uw voorkeurs client hulpprogramma om verbinding te maken via SSH.

1. Nadat u verbinding hebt gemaakt met de virtuele machine, moet u Grafana installeren en configureren om verbinding te maken met de schaalset voor virtuele machines waar de metrische gegevens worden gehost. Open een opdracht prompt en voer de `nano` opdracht in om een nano-tekst editor te openen. Plak het volgende script in de tekst editor en vervang het door `<prometheus IP address>` het IP-adres dat u in de vorige stap hebt genoteerd:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Typ `ctrl + X` om het bestand op te slaan. U kunt het bestand een naam `grafana.sh` .

1. Voer de `./grafana.sh` opdracht in de opdracht prompt in om Grafana te installeren.

1. Nadat de installatie is voltooid, is Grafana beschikbaar op **poort 3000** in het IP-adres van de server, zoals wordt weer gegeven in de volgende scherm afbeelding:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Voer Grafana uit op poort 3000." border="true":::

1. U kunt kiezen uit open-source Dash boards die zijn gemaakt voor Apache Cassandra in Grafana, zoals het JSON-bestand van het [cluster Overview](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) . Down load en importeer de JSON-definitie van het dash board in Grafana:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="De JSON-definitie van Grafana importeren." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="De JSON-definitie Grafana uploaden." border="true":::

1. U kunt vervolgens uw beheerde exemplaar van het Cassandra-cluster controleren met het dash board dat u hebt geselecteerd:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Bekijk de metrische gegevens van het beheerde exemplaar van Cassandra in het dash board." border="true":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Dash boards kunt configureren voor het visualiseren van metrische gegevens in Prometheus met behulp van Grafana. Meer informatie over het beheerde exemplaar van Azure voor Apache Cassandra vindt u in de volgende artikelen:

* [Overzicht van het beheerde exemplaar van Azure voor Apache Cassandra](introduction.md)
* [Een beheerd Apache Spark cluster implementeren met Azure Databricks (preview)](deploy-cluster-databricks.md)
