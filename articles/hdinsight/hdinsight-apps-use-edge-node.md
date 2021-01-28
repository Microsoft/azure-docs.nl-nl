---
title: Lege Edge-knoop punten gebruiken in Apache Hadoop clusters in azure HDInsight
description: Een leeg Edge-knoop punt toevoegen aan een HDInsight-cluster. Wordt gebruikt als een-client en test of host uw HDInsight-toepassingen.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: 4061df2dfcfb4cf993e2312fb3438de66863a476
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942682"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Lege Edge-knoop punten op Apache Hadoop clusters in HDInsight gebruiken

Meer informatie over het toevoegen van een leeg Edge-knoop punt aan een HDInsight-cluster. Een lege Edge-knoop punt is een virtuele Linux-machine waarop dezelfde client hulpprogramma's zijn geïnstalleerd en geconfigureerd als in de hoofd knooppunten. Maar zonder dat er [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) services worden uitgevoerd. U kunt het Edge-knoop punt gebruiken om toegang te krijgen tot het cluster, uw client toepassingen te testen en uw client toepassingen te hosten.

Wanneer u het cluster maakt, kunt u een leeg Edge-knoop punt toevoegen aan een bestaand HDInsight-cluster. Het toevoegen van een leeg Edge-knoop punt geschiedt met behulp van Azure Resource Manager sjabloon.  In het volgende voor beeld ziet u hoe u dit kunt doen met behulp van een sjabloon:

```json
"resources": [
    {
        "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
        "type": "Microsoft.HDInsight/clusters/applications",
        "apiVersion": "2015-03-01-preview",
        "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
        "properties": {
            "marketPlaceIdentifier": "EmptyNode",
            "computeProfile": {
                "roles": [{
                    "name": "edgenode",
                    "targetInstanceCount": 1,
                    "hardwareProfile": {
                        "vmSize": "{}"
                    }
                }]
            },
            "installScriptActions": [{
                "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                "uri": "[parameters('installScriptAction')]",
                "roles": ["edgenode"]
            }],
            "uninstallScriptActions": [],
            "httpsEndpoints": [],
            "applicationType": "CustomApplication"
        }
    }
],
```

Zoals in het voor beeld wordt weer gegeven, kunt u eventueel een [script actie](hdinsight-hadoop-customize-cluster-linux.md) aanroepen om extra configuratie uit te voeren. Zoals het installeren van [Apache-tinten](hdinsight-hadoop-hue-linux.md) in het Edge-knoop punt. De script actie script moet openbaar toegankelijk zijn op het web.  Als het script bijvoorbeeld wordt opgeslagen in Azure Storage, gebruikt u open bare containers of open bare blobs.

De grootte van de virtuele machine van het Edge-knoop punt moet voldoen aan de vereisten voor de VM-grootte van het HDInsight-cluster. Zie [Apache Hadoop clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)voor de aanbevolen VM-grootten voor werk knooppunten.

Nadat u een Edge-knoop punt hebt gemaakt, kunt u via SSH verbinding maken met het Edge-knoop punt en client hulpprogramma's uitvoeren om toegang te krijgen tot het Hadoop-cluster in HDInsight.

> [!WARNING]
> Aangepaste onderdelen die zijn geïnstalleerd op het Edge-knoop punt ontvangen commercieel redelijke ondersteuning van micro soft. Dit kan leiden tot problemen oplossen die u tegen komt. Het is ook mogelijk dat u resources van de community voor verdere ondersteuning moet worden genoemd. Hier volgen enkele van de meest actieve sites om hulp van de community te verkrijgen:
>
> * [Micro soft Q&een vraag pagina voor HDInsight] (/Answers/topics/azure-hdinsight.html
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Als u een Apache-technologie gebruikt, kunt u mogelijk ondersteuning vinden via de Apache-project sites op [https://apache.org](https://apache.org) , zoals de [Apache Hadoop](https://hadoop.apache.org/) -site.

> [!IMPORTANT]
> Er worden binnen drie maanden na publicatie een Ubuntu-installatie kopie beschikbaar voor het maken van nieuwe HDInsight-clusters. Vanaf januari 2019 worden clusters **zonder** automatische patches uitgevoerd (inclusief Edge-knoop punten). Klanten moeten script acties of andere mechanismen gebruiken om een actief cluster te patchen.  Zie [patches voor het besturings systeem voor HDInsight](./hdinsight-os-patching.md)voor meer informatie.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Een Edge-knoop punt toevoegen aan een bestaand cluster

In deze sectie gebruikt u een resource manager-sjabloon om een Edge-knoop punt toe te voegen aan een bestaand HDInsight-cluster.  De Resource Manager-sjabloon vindt u in [github](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). De Resource Manager-sjabloon roept een script actie op die zich bevindt in https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh . Het script voert geen acties uit.  Het is om het aanroepen van een script actie te demonstreren vanuit een resource manager-sjabloon.

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en open de Azure Resource Manager sjabloon in de Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Configureer de volgende eigenschappen:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer een Azure-abonnement dat wordt gebruikt voor het maken van het cluster.|
    |Resourcegroep|Selecteer de resource groep die wordt gebruikt voor het bestaande HDInsight-cluster.|
    |Locatie|Selecteer de locatie van het bestaande HDInsight-cluster.|
    |Clusternaam|Voer de naam van een bestaand HDInsight-cluster in.|

1. Ga **akkoord met de bovenstaande voor waarden** en selecteer vervolgens  **aanschaffen** om het Edge-knoop punt te maken.

> [!IMPORTANT]  
> Zorg ervoor dat u de Azure-resource groep voor het bestaande HDInsight-cluster selecteert.  Anders wordt het volgende fout bericht weer gegeven: kan de aangevraagde bewerking niet uitvoeren op een geneste bron. De bovenliggende resource &lt; clustername> is niet gevonden.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Een Edge-knoop punt toevoegen bij het maken van een cluster

In deze sectie gebruikt u een resource manager-sjabloon om een HDInsight-cluster te maken met een Edge-knoop punt.  De Resource Manager-sjabloon vindt u in de [Galerie met sjablonen van Azure Quick](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)start. De Resource Manager-sjabloon roept een script actie op die zich bevindt in https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh . Het script voert geen acties uit.  Het is om het aanroepen van een script actie te demonstreren vanuit een resource manager-sjabloon.

1. Maak een HDInsight-cluster als u er nog geen hebt.  Zie [aan de slag met Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en open de Azure Resource Manager sjabloon in de Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Configureer de volgende eigenschappen:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer een Azure-abonnement dat wordt gebruikt voor het maken van het cluster.|
    |Resourcegroep|Maak een nieuwe resource groep die wordt gebruikt voor het cluster.|
    |Locatie|Selecteer een locatie voor de resourcegroep.|
    |Clusternaam|Voer een naam in voor het nieuwe cluster dat u wilt maken.|
    |Gebruikersnaam voor clusteraanmelding|Voer de Hadoop HTTP-gebruikers naam in.  De standaardnaam is **admin**.|
    |Wachtwoord voor clusteraanmelding|Voer het Hadoop HTTP-gebruikers wachtwoord in.|
    |Ssh-gebruikersnaam|Voer de SSH-gebruikers naam in. De standaard naam is **sshuser**.|
    |Ssh-wachtwoord|Voer het wacht woord voor SSH-gebruikers in.|
    |Script actie installeren|Behoud de standaard waarde voor dit artikel.|

    Sommige eigenschappen zijn vastgelegd in de sjabloon: cluster type, aantal cluster worker-knoop punten, grootte van Edge-knoop punt en rand knooppunt naam.

1. Ga **akkoord met de bovenstaande voor waarden** en selecteer vervolgens  **aankoop** om het cluster met het Edge-knoop punt te maken.

## <a name="add-multiple-edge-nodes"></a>Meerdere Edge-knoop punten toevoegen

U kunt meerdere Edge-knoop punten toevoegen aan een HDInsight-cluster.  De configuratie van knoop punten met meerdere randen kan alleen worden uitgevoerd met Azure Resource Manager sjablonen.  Zie het sjabloon voorbeeld aan het begin van dit artikel.  Werk de **targetInstanceCount** bij zodat deze overeenkomt met het aantal Edge-knoop punten dat u wilt maken.

## <a name="access-an-edge-node"></a>Toegang tot een Edge-knoop punt

Het SSH-eind punt van het Edge-knoop punt is &lt; EdgeNodeName>. &lt; Clustername>-ssh.azurehdinsight.net:22.  Bijvoorbeeld new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Het Edge-knoop punt wordt weer gegeven als een toepassing op het Azure Portal.  De portal geeft u de informatie over de toegang tot het Edge-knoop punt via SSH.

**Het SSH-eind punt van het Edge-knoop punt controleren**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open het HDInsight-cluster met een Edge-knoop punt.
3. Selecteer **toepassingen**. Het Edge-knoop punt wordt weer geven.  De standaard naam is **New-de**.
4. Selecteer het Edge-knoop punt. Het SSH-eind punt wordt weer geven.

**Hive op het Edge-knoop punt gebruiken**

1. Gebruik SSH om verbinding te maken met het edge-knooppunt. Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

2. Nadat u met SSH verbinding hebt gemaakt met het Edge-knoop punt, gebruikt u de volgende opdracht om de Hive-console te openen:

    ```console
    hive
    ```

3. Voer de volgende opdracht uit om Hive-tabellen in het cluster weer te geven:

    ```hiveql
    show tables;
    ```

## <a name="delete-an-edge-node"></a>Een Edge-knoop punt verwijderen

U kunt een Edge-knoop punt verwijderen uit het Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open het HDInsight-cluster met een Edge-knoop punt.
3. Selecteer **toepassingen**. Er wordt een lijst met Edge-knoop punten weer geven.  
4. Klik met de rechter muisknop op het Edge-knoop punt dat u wilt verwijderen en selecteer vervolgens **verwijderen**.
5. Selecteer **Ja** om te bevestigen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een Edge-knoop punt kunt toevoegen en hoe u toegang krijgt tot het Edge-knoop punt. Lees de volgende artikelen voor meer informatie:

* [HDInsight-toepassingen installeren](hdinsight-apps-install-applications.md): informatie over het installeren van een HDInsight-toepassing op uw clusters.
* [Aangepaste hdinsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md): meer informatie over het implementeren van een niet-gepubliceerde hdinsight-toepassing op hdinsight.
* [HDInsight-toepassingen publiceren](hdinsight-apps-publish-applications.md): informatie over het publiceren van aangepaste HDInsight-toepassingen in Azure Marketplace.
* [MSDN: een HDInsight-toepassing installeren](/rest/api/hdinsight/hdinsight-application): informatie over het definiëren van HDInsight-toepassingen.
* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van een scriptactie](hdinsight-hadoop-customize-cluster-linux.md): informatie over het gebruik van een scriptactie om extra toepassingen te installeren.
* [Op Linux gebaseerde Apache Hadoop clusters maken in HDInsight met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informatie over het aanroepen van Resource Manager-sjablonen voor het maken van HDInsight-clusters.