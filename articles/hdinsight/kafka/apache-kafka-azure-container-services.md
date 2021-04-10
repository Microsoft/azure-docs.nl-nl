---
title: Azure Kubernetes service gebruiken met Kafka in HDInsight
description: Meer informatie over het gebruik van Kafka op HDInsight vanuit container installatie kopieën die worden gehost in azure Kubernetes service (AKS).
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 2216eb5893b77761f4d31c5819d152ceeb985abc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869647"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Azure Kubernetes-service gebruiken met Apache Kafka op HDInsight

Meer informatie over het gebruik van Azure Kubernetes service (AKS) met [Apache Kafka](https://kafka.apache.org/) op HDInsight-cluster. De stappen in dit document gebruiken een Node.js toepassing die wordt gehost in AKS om de verbinding met Kafka te controleren. Deze toepassing maakt gebruik van het [Kafka-knoop punt](https://www.npmjs.com/package/kafka-node) om te communiceren met Kafka. [Socket.io](https://socket.io/) wordt gebruikt voor het verzenden van berichten tussen de browser-client en de back-end die wordt GEHOST in AKS.

[Apache Kafka](https://kafka.apache.org) is een open-source gedistribueerd streamingplatform dat kan worden gebruikt voor het bouwen van pijplijnen en toepassingen voor realtime streaming van gegevens. Met de Azure Kubernetes-service beheert u uw gehoste Kubernetes-omgeving en kunt u snel en eenvoudig container toepassingen implementeren. Met behulp van een Azure-Virtual Network kunt u de twee services verbinden.

> [!NOTE]  
> De focus van dit document bestaat uit de stappen die nodig zijn om de Azure Kubernetes-service te laten communiceren met Kafka in HDInsight. Het voor beeld zelf is slechts een eenvoudige Kafka-client om aan te tonen dat de configuratie werkt.

## <a name="prerequisites"></a>Vereisten

* [Azure CLI](/cli/azure/install-azure-cli)
* Een Azure-abonnement

In dit document wordt ervan uitgegaan dat u bekend bent met het maken en gebruiken van de volgende Azure-Services:

* Kafka op HDInsight
* Azure Kubernetes Service
* Virtuele netwerken van Azure

In dit document wordt ook ervan uitgegaan dat u werd uitgelegd hoe hebt via de [Azure Kubernetes service-zelf studie](../../aks/tutorial-kubernetes-prepare-app.md). In dit artikel maakt u een container service, maakt u een Kubernetes-cluster, een container register en configureert u het `kubectl` hulp programma.

## <a name="architecture"></a>Architectuur

### <a name="network-topology"></a>Netwerktopologie

Zowel HDInsight als AKS gebruiken een Azure-Virtual Network als een container voor reken resources. Als u communicatie tussen HDInsight en AKS wilt inschakelen, moet u de communicatie tussen hun netwerken inschakelen. De stappen in dit document gebruiken Virtual Network peering naar de netwerken. Andere verbindingen, zoals VPN, moeten ook werken. Zie het document [peering van het virtuele netwerk](../../virtual-network/virtual-network-peering-overview.md) voor meer informatie over peering.

In het volgende diagram ziet u de netwerk topologie die in dit document wordt gebruikt:

:::image type="content" source="./media/apache-kafka-azure-container-services/kafka-aks-architecture.png" alt-text="HDInsight in één virtueel netwerk, AKS in een andere, met behulp van peering" border="false":::

> [!IMPORTANT]  
> Naam omzetting is niet ingeschakeld tussen de peered netwerken, zodat IP-adres Sering wordt gebruikt. Kafka op HDInsight is standaard geconfigureerd voor het retour neren van hostnamen in plaats van IP-adressen wanneer clients verbinding maken. Met de stappen in dit document wijzigt u Kafka om in plaats daarvan IP-reclame te gebruiken.

## <a name="create-an-azure-kubernetes-service-aks"></a>Een Azure Kubernetes-service maken (AKS)

Als u nog geen AKS-cluster hebt, gebruikt u een van de volgende documenten voor meer informatie over hoe u er een kunt maken:

* [Een AKS-cluster (Azure Kubernetes service) implementeren-Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Een AKS-cluster (Azure Kubernetes service) implementeren-CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS maakt tijdens de installatie een virtueel netwerk in een **extra** resource groep. De extra resource groep volgt de naam Conventie van **MC_resourceGroup_AKSclusterName_location**.  
> Dit netwerk is gekoppeld aan het account dat is gemaakt voor HDInsight in de volgende sectie.

## <a name="configure-virtual-network-peering"></a>Peering van virtuele netwerken configureren

### <a name="identify-preliminary-information"></a>Voorlopige gegevens identificeren

1. Zoek vanuit het [Azure Portal](https://portal.azure.com)de extra **resource groep** die het virtuele netwerk voor uw AKS-cluster bevat.

2. Selecteer de bron van het __virtuele netwerk__ in de resource groep. Noteer deze naam voor later gebruik.

3. Selecteer onder **Instellingen** de optie __Adresruimte__. Noteer de adres ruimte die wordt weer gegeven.

### <a name="create-virtual-network"></a>Virtueel netwerk maken

1. Als u een virtueel netwerk voor HDInsight wilt maken, gaat u naar __+ een resource__  >  __netwerken__  >  __virtueel netwerk__ maken.

1. Maak het netwerk aan de hand van de volgende richt lijnen voor bepaalde eigenschappen:

    |Eigenschap | Waarde |
    |---|---|
    |Adresruimte|U moet een adres ruimte gebruiken die niet overlapt met die van het AKS-cluster netwerk.|
    |Locatie|Gebruik dezelfde __locatie__ voor het virtuele netwerk dat u hebt gebruikt voor het AKS-cluster.|

1. Wacht tot het virtuele netwerk is gemaakt voordat u naar de volgende stap gaat.

### <a name="configure-peering"></a>Peering configureren

1. Als u de peering tussen het HDInsight-netwerk en het AKS-cluster netwerk wilt configureren, selecteert u het virtuele netwerk en selecteert u vervolgens __peerings__.

1. Selecteer __+ toevoegen__ en gebruik de volgende waarden om het formulier in te vullen:

    |Eigenschap |Waarde |
    |---|---|
    |De naam van de peering van \<this VN> naar het externe virtuele netwerk|Voer een unieke naam in voor deze peering-configuratie.|
    |Virtueel netwerk|Selecteer het virtuele netwerk voor het **AKS-cluster**.|
    |De naam van de peering van \<AKS VN> naar \<this VN>|Voer een unieke naam in.|

    Zorg ervoor dat alle andere velden op de standaard waarde staan en selecteer __OK__ om de peering te configureren.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Apache Kafka cluster maken op HDInsight

Wanneer u het Kafka in HDInsight-cluster maakt, moet u lid worden van het virtuele netwerk dat u eerder hebt gemaakt voor HDInsight. Zie het document [een Apache Kafka cluster maken](apache-kafka-get-started.md) voor meer informatie over het maken van een Kafka-cluster.

## <a name="configure-apache-kafka-ip-advertising"></a>Apache Kafka IP-reclame configureren

Gebruik de volgende stappen om Kafka te configureren voor het adverteren van IP-adressen in plaats van domein namen:

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`. Vervang CLUSTERNAME door de naam van de Kafka in HDInsight-cluster.

    Wanneer u hierom wordt gevraagd, gebruikt u de HTTPS-gebruikers naam en het wacht woord voor het cluster. De Ambari-webgebruikersinterface voor het cluster wordt weer gegeven.

2. Als u informatie over Kafka wilt weer geven, selecteert u __Kafka__ in de lijst aan de linkerkant.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-service.png" alt-text="Service lijst met Kafka gemarkeerd" border="true":::

3. Als u de configuratie van Kafka wilt weer geven, selecteert u __configuraties__ in het bovenste midden.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-config1.png" alt-text="Configuratie van Apache Ambari Services" border="true":::

4. Als u de __Kafka-env-__ configuratie wilt vinden, voert u `kafka-env` in het veld __filter__ in de rechter bovenhoek in.

    :::image type="content" source="./media/apache-kafka-azure-container-services/search-for-kafka-env.png" alt-text="Kafka-configuratie voor Kafka-env" border="true":::

5. Als u Kafka wilt configureren voor het adverteren van IP-adressen, voegt u de volgende tekst toe onder aan het veld __Kafka-env-Temp late__ :

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Als u de interface wilt configureren waarop Kafka luistert, voert u `listeners` in het veld __filter__ in de rechter bovenhoek in.

7. Als u Kafka wilt configureren om te Luis teren op alle netwerk interfaces, wijzigt u de waarde in het veld __listeners__ in `PLAINTEXT://0.0.0.0:9092` .

8. Gebruik de knop __Opslaan__ om de configuratie wijzigingen op te slaan. Voer een tekst bericht in waarin de wijzigingen worden beschreven. Selecteer __OK__ zodra de wijzigingen zijn opgeslagen.

    :::image type="content" source="./media/apache-kafka-azure-container-services/save-configuration-button.png" alt-text="Apache Ambari-configuratie opslaan" border="true":::

9. Als u fouten wilt voor komen bij het opnieuw starten van Kafka, gebruikt u de knop __service acties__ en selecteert u __onderhouds modus inschakelen__. Selecteer OK om deze bewerking te volt ooien.

    :::image type="content" source="./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png" alt-text="Service acties, met ingeschakeld onderhoud inschakelen" border="true":::

10. Als u Kafka opnieuw wilt starten, gebruikt u de knop __opnieuw opstarten__ en selecteert u __alle betrokkenen opnieuw opstarten__. Bevestig het opnieuw opstarten en gebruik vervolgens de knop __OK__ nadat de bewerking is voltooid.

    :::image type="content" source="./media/apache-kafka-azure-container-services/restart-required-button.png" alt-text="Knop opnieuw opstarten met alle betrokken gemarkeerd voor opnieuw opstarten" border="true":::

11. Als u de onderhouds modus wilt uitschakelen, gebruikt u de knop __service acties__ en selecteert u __onderhouds modus uitschakelen__. Selecteer **OK** om deze bewerking te volt ooien.

## <a name="test-the-configuration"></a>De configuratie testen

Op dit moment zijn de Kafka-en Azure Kubernetes-service in communicatie via de gekoppelde virtuele netwerken. Als u deze verbinding wilt testen, gebruikt u de volgende stappen:

1. Maak een Kafka-onderwerp dat door de test toepassing wordt gebruikt. Zie het document [Create a Apache Kafka cluster](apache-kafka-get-started.md) voor meer informatie over het maken van Kafka-onderwerpen.

2. Down load de voorbeeld toepassing van [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) .

3. Bewerk het `index.js` bestand en wijzig de volgende regels:

    * `var topic = 'mytopic'`: Vervang door `mytopic` de naam van het onderwerp Kafka dat door deze toepassing wordt gebruikt.
    * `var brokerHost = '176.16.0.13:9092`: Vervang door `176.16.0.13` het interne IP-adres van een van de Broker-hosts voor uw cluster.

        Als u het interne IP-adres van de Broker-hosts (workernodes) in het cluster wilt vinden, raadpleegt u het document [Apache Ambari rest API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) . Kies IP-adres van een van de vermeldingen waarbij de domein naam begint met `wn` .

4. Installeer afhankelijkheden vanaf een opdracht regel in de `src` Directory en gebruik docker om een installatie kopie voor implementatie te maken:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Pakketten die door deze toepassing zijn vereist, worden in de opslag plaats ingecheckt, zodat u het hulp programma niet hoeft te gebruiken `npm` om ze te installeren.

5. Meld u aan bij uw Azure Container Registry (ACR) en zoek de naam van de login server:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Als u de naam van uw Azure Container Registry niet kent of als u niet bekend bent met het gebruik van de Azure CLI om te werken met de Azure Kubernetes-service, raadpleegt u de [AKS-zelf studies](../../aks/tutorial-kubernetes-prepare-app.md).

6. Label de lokale `kafka-aks-test` installatie kopie met de login server van uw ACR. Voeg ook toe `:v1` aan het einde om de installatie kopie versie aan te geven:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Push de installatiekopie naar het register:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Het volt ooien van deze bewerking duurt enkele minuten.

8. Bewerk het Kubernetes-manifest bestand ( `kafka-aks-test.yaml` ) en vervang door `microsoft` de naam van de ACR-login server die u in stap 4 hebt opgehaald.

9. Gebruik de volgende opdracht om de toepassings instellingen te implementeren vanuit het manifest:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Gebruik de volgende opdracht om de toepassing te bekijken `EXTERNAL-IP` :

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Wanneer een extern IP-adres is toegewezen, gebruikt u __CTRL + C__ om het horloge af te sluiten

11. Open een webbrowser en voer het externe IP-adres voor de service in. U ontvangt een pagina die vergelijkbaar is met de volgende afbeelding:

    :::image type="content" source="./media/apache-kafka-azure-container-services/test-web-page-image1.png" alt-text="Afbeelding van de webpagina van Apache Kafka test" border="true":::

12. Voer tekst in het veld in en selecteer vervolgens de knop __verzenden__ . De gegevens worden verzonden naar Kafka. Vervolgens leest de Kafka-consument in de toepassing het bericht en voegt deze toe aan de sectie __berichten van Kafka__ .

    > [!WARNING]  
    > Mogelijk wordt er meerdere exemplaren van een bericht weer gegeven. Dit probleem treedt meestal op wanneer u uw browser vernieuwt nadat u verbinding hebt gemaakt of meerdere browser verbindingen met de toepassing hebt geopend.

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende koppelingen voor meer informatie over het gebruik van Apache Kafka in HDInsight:

* [Aan de slag met Apache Kafka op HDInsight](apache-kafka-get-started.md)

* [MirrorMaker gebruiken voor het maken van een replica van Apache Kafka in HDInsight](apache-kafka-mirroring.md)

* [Apache Storm gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Apache Spark gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Verbinding maken met Apache Kafka via een Azure-Virtual Network](apache-kafka-connect-vpn-gateway.md)
