---
title: Operationeel maken ML Services op HDInsight-Azure
description: Meer informatie over hoe u uw gegevens model kunt operationeel makenen om voor spellingen te doen met ML-Services in azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/27/2018
ms.openlocfilehash: 7d597c4dbb81562050e9523c61d47d0020fc9059
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869462"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operationeel maken ML-cluster in azure HDInsight

Nadat u het server gebruik van MILLILITERs in HDInsight hebt gebruikt om uw gegevens modellering te volt ooien, kunt u het model operationeel maken om voor spellingen te maken. In dit artikel vindt u instructies voor het uitvoeren van deze taak.

## <a name="prerequisites"></a>Vereisten

* Een ML Services-cluster beheren in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **ML Services** voor **Clustertype**.

* Een SSH-client (Secure Shell): er wordt een SSH-client gebruikt om extern verbinding te maken met het HDInsight-cluster en om opdrachten rechtstreeks uit te voeren op het cluster. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operationeel maken ML-cluster met een configuratie met één doos

> [!NOTE]  
> De onderstaande stappen zijn van toepassing op R Server 9,0 en ML Server 9,1. Raadpleeg voor ML Server 9,3 het [beheer programma gebruiken om de uitoefening-configuratie te beheren](/machine-learning-server/operationalize/configure-admin-cli-launch).

1. SSH op het Edge-knooppunt.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor instructies over het gebruik van SSH met Azure hdinsight.

1. Wijzig de map voor de relevante versie en sudo de dot net-dll: 

    - Voor micro soft ML Server 9,1:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - Voor Microsoft R Server 9.0:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. U krijgt de keuze uit de opties waaruit u kunt kiezen. Kies de eerste optie, zoals wordt weer gegeven in de volgende scherm afbeelding, om **ml server voor uitoefening te configureren**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-1.png" alt-text="R Server Administration Utility selecteren" border="true":::

1. U krijgt nu de optie om te kiezen hoe u ML Server wilt operationeel maken. Kies in de weer gegeven opties de eerste door **een** te typen.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-2.png" alt-text="R Server Administration Utility operationeel maken" border="true":::

1. Wanneer u hierom wordt gevraagd, voert u het wacht woord voor een lokale gebruikers beheerder in en voert u het opnieuw in.

1. Er moeten uitvoer resultaten worden weer geven waarin wordt voorgesteld dat de bewerking is geslaagd. U wordt ook gevraagd een andere optie te selecteren in het menu. Selecteer E om terug te gaan naar het hoofd menu.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-3.png" alt-text="R Server Administration-hulp programma geslaagd" border="true":::

1. U kunt ook diagnostische controles uitvoeren door de volgende diagnostische test uit te voeren:

    a. Selecteer in het hoofd menu de optie **6** om diagnostische tests uit te voeren.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic1.png" alt-text="Diagnostische R Server Administration-hulpprogram ma's" border="true":::

    b. Selecteer in het menu diagnostische tests **een**. Wanneer u hierom wordt gevraagd, voert u het wacht woord in dat u hebt opgegeven voor de gebruiker van de lokale beheerder.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic2.png" alt-text="Hulp programma R Server Administration-hulpprogram ma's" border="true":::

    c. Controleer of de uitvoer laat zien dat de algehele status is geslaagd.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic3.png" alt-text="Hulp programma voor het beheer van R-servers" border="true":::

    d. Voer in de weer gegeven menu opties **E** in om terug te keren naar het hoofd menu en voer **8** in om het beheer hulpprogramma af te sluiten.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Lange vertragingen bij het gebruik van webservices op Apache Spark

Als er lange vertragingen optreden bij het gebruik van een webservice die is gemaakt met mrsdeploy-functies in een Apache Spark Compute-context, moet u mogelijk een aantal ontbrekende mappen toevoegen. De Spark-toepassing is van een gebruiker met de naam *rserve2* wanneer de toepassing wordt aangeroepen vanuit een webservice met behulp van mrsdeploy-functies. Dit probleem omzeilen:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

In dit stadium is de configuratie voor uitoefening voltooid. Nu kunt u het `mrsdeploy` pakket op uw rclient gebruiken gebruiken om verbinding te maken met het uitoefening op Edge-knoop punt en de functies, zoals [extern uitvoeren](/machine-learning-server/r/how-to-execute-code-remotely) en [webservices](/machine-learning-server/operationalize/concept-what-are-web-services), te gebruiken. Afhankelijk van of het cluster is ingesteld in een virtueel netwerk of niet, moet u mogelijk forward tunneling via SSH-aanmelding instellen voor de poort. In de volgende secties wordt uitgelegd hoe u deze tunnel instelt.

### <a name="ml-services-cluster-on-virtual-network"></a>Clusters van ML Services in het virtuele netwerk

Zorg ervoor dat u verkeer via poort 12800 naar het Edge-knooppunt toestaat. Op deze manier kunt u het Edge-knooppunt gebruiken om verbinding te maken met de functie Uitoefening.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Als de `remoteLogin()` geen verbinding kan maken met het Edge-knooppunt maar als u wel verbinding hebt via SSH, moet u controleren of de regel op basis waarvan verkeer via poort 12800 is toegestaan, juist is ingesteld of niet. Als dit probleem zich blijft voordoen, kunt u een tijdelijke oplossing gebruiken door forward tunneling via SSH in te stellen voor de poort. Zie de volgende sectie voor instructies:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Het cluster voor ML Services is niet ingesteld op het virtuele netwerk

Als het cluster niet is ingesteld in het virtuele netwerk vnet of als u problemen ondervindt met de connectiviteit via dit netwerk, kunt u forward tunneling via SSH instellen voor de poort:

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Zodra de SSH-sessie actief is, wordt het verkeer van de poort 12800 van de lokale computer doorgestuurd naar de poort 12800 van het Edge-knoop punt via een SSH-sessie. Zorg ervoor dat u `127.0.0.1:12800` gebruikt in de `remoteLogin()`-methode. Dit meldt zich aan bij de uitoefening van het Edge-knoop punt via poort forwarding.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Operationele reken knooppunten schalen op HDInsight worker-knoop punten

Als u de reken knooppunten wilt schalen, moet u de worker-knoop punten eerst buiten gebruik stellen en vervolgens reken knooppunten configureren op de buiten gebruik gestelde worker-knoop punten.

### <a name="step-1-decommission-the-worker-nodes"></a>Stap 1: de worker-knoop punten buiten gebruik stellen

Het cluster voor ML-Services wordt niet beheerd via [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Als de worker-knoop punten niet uit bedrijf worden genomen, werkt de Resource Manager van de garen niet zoals verwacht, omdat het niet bekend is met de resources die door de server worden gebruikt. Om deze sitatie te voorkomen raden we u aan de werkknooppunten uit bedrijf te nemen voordat u de rekenknooppunten uitschaalt.

Volg deze stappen voor het buiten gebruik stellen van worker-knoop punten:

1. Meld u aan bij de Ambari-console van het cluster en klik op het tabblad **hosts** .

1. Werk knooppunten selecteren (om uit bedrijf te nemen).

1. Klik op **acties**  >  **geselecteerde hosts** hosts  >  **host** de  >  **onderhouds modus inschakelen**. In de volgende afbeelding zijn bijvoorbeeld wk3 en wk4 geselecteerd om uit bedrijf te worden genomen.  

   :::image type="content" source="./media/r-server-operationalize/get-started-operationalization.png" alt-text="Apache Ambari-onderhouds modus inschakelen" border="true":::  

* Selecteer **acties**  >  **geselecteerde hosts**  >  **DataNodes** > Klik op uit **bedrijf nemen**.
* Selecteer **acties**  >  **geselecteerde hosts**  >  **NodeManagers** > Klik op uit **bedrijf nemen**.
* Selecteer **acties**  >  **geselecteerde hosts**  >  **DataNodes** > Klik op **Stop**.
* Selecteer **acties**  >  **geselecteerde hosts**  >  **NodeManagers** > Klik op **Stop**.
* Selecteer **acties**  >  **geselecteerde hosts**  >  **host** > Klik op **alle onderdelen stoppen**.
* Hef de selectie van de worker-knooppunten op en selecteer de hoofdknooppunten.
* Selecteer **acties**  >  **geselecteerde hosts** > hosts  >  **alle onderdelen opnieuw starten**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Stap 2: reken knooppunten configureren op elk uit bedrijf genomen worker-knoop punt (en)

1. SSH op elk uit bedrijf genomen werkknooppunt.

1. Voer het hulp programma voor beheer uit met de relevante DLL voor de ML Services-cluster die u hebt. Voer voor ML Server 9,1 de volgende handelingen uit:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Voer **1** in om de optie **ml server configureren voor uitoefening** te selecteren.

1. Voer **C** in om de optie te selecteren `C. Compute node` . Hiermee configureert u het rekenknooppunt op het werkknooppunt.

1. Sluit het beheerprogramma.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Stap 3: Details van reken knooppunten toevoegen op het webknooppunt

Zodra alle uit bedrijf genomen werk knooppunten zijn geconfigureerd om het reken knooppunt uit te voeren, keert u terug naar het Edge-knoop punt en voegt u de IP-adressen van de uit bedrijf genomen werk knooppunten toe in de configuratie van het ML Server-webknooppunt:

1. SSH op het Edge-knooppunt.

1. Voer `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` uit.

1. Zoek naar de sectie Uri's en voeg de IP-en poort gegevens van het worker-knoop punt toe.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Volgende stappen

* [ML Services-cluster beheren in HDInsight](r-server-hdinsight-manage.md)
* [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
* [Opties voor Azure Storage voor ML Services-cluster in HDInsight](r-server-storage.md)