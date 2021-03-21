---
title: HBase-clusters maken in een Virtual Network-Azure
description: Aan de slag met HBase in azure HDInsight. Meer informatie over het maken van HDInsight HBase-clusters op Azure Virtual Network.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 9f179981aa39402681b4830d58a29f5b1259c7e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946128"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Apache HBase-clusters maken op HDInsight in azure Virtual Network

Meer informatie over het maken van Azure HDInsight Apache HBase-clusters in een [Azure-Virtual Network](https://azure.microsoft.com/services/virtual-network/).

Met de integratie van virtuele netwerken kunnen Apache HBase-clusters worden geïmplementeerd in hetzelfde virtuele netwerk als uw toepassingen, zodat toepassingen rechtstreeks kunnen communiceren met HBase. Enkele voordelen:

* Directe connectiviteit van de webtoepassing naar de knoop punten van het HBase-cluster, waarmee communicatie via HBase Java Remote Procedure Call (RPC) Api's mogelijk wordt.
* De prestaties zijn verbeterd doordat het verkeer niet via meerdere gateways en load balancers kan worden uitgevoerd.
* De mogelijkheid om gevoelige informatie op een veiligere manier te verwerken zonder een openbaar eind punt zichtbaar te maken.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Een Apache HBase-cluster maken in een virtueel netwerk

In deze sectie maakt u een op Linux gebaseerd Apache HBase-cluster met het afhankelijke Azure Storage account in een virtueel Azure-netwerk met behulp van een [Azure Resource Manager sjabloon](../../azure-resource-manager/templates/deploy-powershell.md). Zie [HDInsight-clusters maken](../hdinsight-hadoop-provision-linux-clusters.md)voor andere methoden voor het maken van een cluster en de uitleg van de instellingen. Zie [Apache Hadoop clusters in Hdinsight maken met behulp van Azure Resource Manager-sjablonen](../hdinsight-hadoop-create-linux-clusters-arm-templates.md) voor meer informatie over het gebruik van een sjabloon voor het maken van Apache Hadoop clusters in hdinsight.

> [!NOTE]  
> Sommige eigenschappen worden vastgelegd in de sjabloon. Bijvoorbeeld:
>
> * **Locatie**: VS-Oost 2
> * **Cluster-versie**: 3.6
> * **Aantal cluster worker-knoop punten**: 2
> * **Standaard opslag account**: een unieke teken reeks
> * **Naam van virtueel netwerk**: clustername-vnet
> * **Adres ruimte van virtueel netwerk**: 10.0.0.0/16
> * **Subnetnaam**: subnet1
> * **Adres bereik van subnet**: 10.0.0.0/24
>
> `CLUSTERNAME` wordt vervangen door de naam van het cluster dat u opgeeft wanneer u de sjabloon gebruikt.

1. Selecteer de volgende afbeelding om de sjabloon in Azure Portal te openen. De sjabloon bevindt zich in [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Selecteer in het dialoog venster **aangepaste implementatie** de optie **sjabloon bewerken**.

1. Wijzig op regel 165 de waarde `Standard_A3` in `Standard_A4_V2` . Selecteer vervolgens **Opslaan**.

1. Voltooi de resterende sjabloon met de volgende informatie:

    |Eigenschap |Waarde |
    |---|---|
    |Abonnement|Selecteer een Azure-abonnement dat is gebruikt voor het maken van het HDInsight-cluster, het afhankelijke opslag account en het virtuele Azure-netwerk.|
    Resourcegroep|Selecteer **nieuwe maken** en geef een nieuwe naam op voor de resource groep.|
    |Locatie|Selecteer een locatie voor de resourcegroep.|
    |Clusternaam|Voer een naam in voor het Hadoop-cluster dat moet worden gemaakt.|
    |Gebruikers naam en wacht woord voor cluster aanmelding|De standaard gebruikersnaam is **admin**. Geef een wacht woord op.|
    |Gebruikers naam en wacht woord voor SSH|De standaard gebruikers naam is **sshuser**.  Geef een wachtwoord op.|

    Selecteer **Ik ga akkoord met de voor waarden die hierboven worden vermeld**.

1. Selecteer **Aankoop**. Het duurt ongeveer 20 minuten om een cluster te maken. Zodra het cluster is gemaakt, kunt u het cluster in de portal selecteren om het te openen.

Nadat u het artikel hebt voltooid, wilt u het cluster misschien verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt. Zie [Apache Hadoop clusters in HDInsight beheren met behulp van de Azure Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters)voor instructies over het verwijderen van een cluster.

Als u wilt werken met uw nieuwe HBase-cluster, kunt u de procedures in aan de [slag met Apache HBase gebruiken met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Verbinding maken met het Apache HBase-cluster met behulp van Apache HBase Java RPC-Api's

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele IaaS-machine (Infrastructure as a Service) in hetzelfde virtuele Azure-netwerk en hetzelfde subnet. Zie [een virtuele machine met Windows Server maken](../../virtual-machines/windows/quick-create-portal.md)voor instructies over het maken van een nieuwe virtuele IaaS-machine. Wanneer u de stappen in dit document volgt, moet u de volgende waarden voor de netwerk configuratie gebruiken:

* **Virtueel netwerk**: clustername-vnet
* **Subnet**: subnet1

> [!IMPORTANT]  
> Vervang door `CLUSTERNAME` de naam die u hebt gebruikt bij het maken van het HDInsight-cluster in de vorige stappen.

Als u deze waarden gebruikt, wordt de virtuele machine in hetzelfde virtuele netwerk en subnet geplaatst als het HDInsight-cluster. Met deze configuratie kunnen ze direct met elkaar communiceren. Er is een manier om een HDInsight-cluster te maken met een leeg Edge-knoop punt. Het Edge-knoop punt kan worden gebruikt om het cluster te beheren.  Zie [lege Edge-knoop punten in HDInsight gebruiken](../hdinsight-apps-use-edge-node.md)voor meer informatie.

### <a name="obtain-fully-qualified-domain-name"></a>Fully Qualified Domain Name verkrijgen

Wanneer u een Java-toepassing gebruikt om extern verbinding te maken met HBase, moet u de Fully Qualified Domain Name (FQDN) gebruiken. Om dit te bepalen, moet u het verbindingsspecifiek DNS-achtervoegsel van het HBase-cluster ophalen. Hiervoor kunt u een van de volgende methoden gebruiken:

* Een webbrowser gebruiken om een [Apache Ambari](https://ambari.apache.org/) -aanroep te maken:

    Blader naar `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Het retourneert een JSON-bestand met de DNS-achtervoegsels.

* De Ambari-website gebruiken:

    1. Blader naar `https://CLUSTERNAME.azurehdinsight.net`.
    2. Selecteer **hosts** in het bovenste menu.

* Krul gebruiken om REST-aanroepen te maken:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Zoek in de JavaScript Object Notation (JSON) geretourneerde gegevens de vermelding ' host_name '. De FQDN-naam voor de knoop punten in het cluster bevat. Bijvoorbeeld:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Het deel van de domein naam die begint met de cluster naam is het DNS-achtervoegsel. Bijvoorbeeld `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>De communicatie in het virtuele netwerk controleren

Als u wilt controleren of de virtuele machine kan communiceren met het HBase-cluster, gebruikt u de opdracht `ping headnode0.<dns suffix>` van de virtuele machine. Bijvoorbeeld `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Als u deze informatie wilt gebruiken in een Java-toepassing, kunt u de stappen in [Apache Maven gebruiken om Java-toepassingen te bouwen die Apache HBase gebruiken met HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) om een toepassing te maken. Als u wilt dat de toepassing verbinding maakt met een externe HBase-server, wijzigt u in dit voor beeld het **hbase-site.xml** -bestand om de FQDN voor Zookeeper te gebruiken. Bijvoorbeeld:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> Zie [name resolution (DNS) (Engelstalig)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)voor meer informatie over naam omzetting in azure Virtual Networks, inclusief het gebruik van uw eigen DNS-server.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een Apache HBase-cluster maakt. Raadpleeg voor meer informatie:

* [Aan de slag met HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Lege Edge-knoop punten in HDInsight gebruiken](../hdinsight-apps-use-edge-node.md)
* [Apache HBase-replicatie in HDInsight configureren](apache-hbase-replication.md)
* [Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Aan de slag met Apache HBase en Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Overzicht van Virtual Network](../../virtual-network/virtual-networks-overview.md)
