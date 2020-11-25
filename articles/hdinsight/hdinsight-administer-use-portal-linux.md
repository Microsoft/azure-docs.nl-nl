---
title: Apache Hadoop clusters in HDInsight beheren met Azure Portal
description: Meer informatie over het maken en beheren van Azure HDInsight-clusters met behulp van de Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: 7c644583e994e51f11bdf7991755a6ea94b01e76
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012801"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Apache Hadoop clusters in HDInsight beheren door gebruik te maken van de Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Met behulp van de [Azure Portal](https://portal.azure.com)kunt u [Apache Hadoop](https://hadoop.apache.org/) clusters in azure HDInsight beheren. Gebruik de bovenstaande tabblad kiezer voor informatie over het beheren van Hadoop-clusters in HDInsight met behulp van andere hulpprogram ma's.

## <a name="prerequisites"></a>Vereisten

Een bestaande Apache Hadoop cluster in HDInsight.  Zie [op Linux gebaseerde clusters in HDInsight maken met behulp van de Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Aan de slag

Meld u aan bij [https://portal.azure.com](https://portal.azure.com).

## <a name="list-and-show-clusters"></a><a name="showClusters"></a> Clusters vermelden en weer geven

Op de pagina **HDInsight-clusters** worden uw bestaande clusters weer geven.  Vanuit de portal:
1. Selecteer **alle services** in het menu links.
2. Selecteer **HDInsight-clusters** onder **Analytics**.

## <a name="cluster-home-page"></a><a name="homePage"></a> Start pagina van cluster

Selecteer de naam van uw cluster op de pagina [**HDInsight-clusters**](#showClusters) .  Hiermee opent u de **overzichts** weergave, die er ongeveer als volgt uitziet:

![Basis principes van Azure Portal HDInsight-cluster](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Bovenste menu:**  

| Item| Beschrijving |
|---|---|
|Verplaatsen|Hiermee verplaatst u het cluster naar een andere resource groep of naar een ander abonnement.|
|Verwijderen|Hiermee verwijdert u het cluster. |
|Vernieuwen|Hiermee vernieuwt u de weer gave.|

**Linkermenu:**  

  - **Menu linksboven**

    | Item| Beschrijving |
    |---|---|
    |Overzicht|Geeft algemene informatie voor uw cluster.|
    |Activiteitenlogboek|Activiteiten logboeken weer geven en opvragen.|
    |Toegangsbeheer (IAM)|Roltoewijzingen gebruiken.  Zie [roltoewijzingen gebruiken voor het beheren van toegang tot de resources van uw Azure-abonnement](../role-based-access-control/role-assignments-portal.md).|
    |Tags|Hiermee stelt u sleutel/waarde-paren in om een aangepaste taxonomie van uw Cloud Services te definiëren. U kunt bijvoorbeeld een sleutel met de naam **project** maken en vervolgens een algemene waarde gebruiken voor alle services die aan een specifiek project zijn gekoppeld.|
    |Problemen vaststellen en oplossen|Informatie over probleem oplossing weer geven.|
    |Snelstart|Geeft informatie weer waarmee u aan de slag kunt gaan met HDInsight.|
    |Hulpprogramma's|Help-informatie voor aan HDInsight gerelateerde hulpprogram ma's.|

  - **Menu Instellingen**  

    | Item| Beschrijving |
    |---|---|
    |Grootte van cluster|Controleer, verhoog en verklein het aantal cluster worker-knoop punten. Zie [clusters schalen](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Quotum limieten|De gebruikte en beschik bare kernen voor uw abonnement weer geven.|
    |Aanmelden bij SSH en cluster|Toont de instructies voor het maken van verbinding met het cluster met behulp van SSH-verbinding (Secure Shell). Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.|
    |Data Lake Storage Gen1|Configureer de toegangs Data Lake Storage Gen1.  Zie [Quick Start: clusters instellen in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).|
    |Opslagaccounts|Bekijk de opslag accounts en de sleutels. De opslag accounts worden geconfigureerd tijdens het proces voor het maken van het cluster.|
    |Toepassingen|HDInsight-toepassingen toevoegen/verwijderen.  Zie [aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md).|
    |Script acties|Voer bash-scripts uit op het cluster. Zie [HDInsight-clusters op basis van Linux aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md).|
    |Externe meta Stores|Bekijk de [Apache Hive](https://hive.apache.org/) -en [Apache Oozie](https://oozie.apache.org/) -meta Stores. De meta Stores kunnen alleen worden geconfigureerd tijdens het proces voor het maken van het cluster.|
    |HDInsight-partner|De huidige HDInsight-partner toevoegen/verwijderen.|
    |Eigenschappen|De eigenschappen van het [cluster](#properties)weer geven.|
    |Vergrendelingen|Voeg een vergren deling toe om te voor komen dat het cluster wordt gewijzigd of verwijderd.|
    |Sjabloon exporteren|De Azure Resource Manager sjabloon voor het cluster weer geven en exporteren. Op dit moment kunt u alleen het afhankelijke Azure Storage-account exporteren. Zie [Apache Hadoop-clusters op basis van Linux maken in HDInsight met behulp van Azure Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Controle menu**

    | Item| Beschrijving |
    |---|---|
    |Waarschuwingen|De waarschuwingen en acties beheren.|
    |Metrische gegevens|Bewaak de cluster metrieken in Azure Monitor Logboeken.|
    |Diagnose-instellingen|Instellingen voor het opslaan van de metrische gegevens van de diagnose.|
    |Azure Monitor|Controleer uw cluster in Azure Monitor.|

  - **Menu ondersteuning en probleem oplossing**

    | Item| Beschrijving |
    |---|---|
    |Status van resources|Zie [overzicht van Azure resource Health](../service-health/resource-health-overview.md).|
    |Nieuwe ondersteuningsaanvraag|Hiermee kunt u een ondersteunings ticket maken met micro soft ondersteuning.|

## <a name="cluster-properties"></a><a name="properties"></a> Cluster eigenschappen

Ga naar de [Start pagina](#homePage)van het cluster en selecteer Eigenschappen in het **dialoog** venster **instellingen** .

|Item | Beschrijving |
|---|---|
|HOSTNAAM|Cluster naam.|
|CLUSTER-URL|De URL voor de Ambari-web-interface.|
|Privé-eindpunt|Het persoonlijke eind punt voor het cluster.|
|Secure Shell (SSH)|De gebruikers naam en de hostnaam die moeten worden gebruikt om toegang te krijgen tot het cluster via SSH.|
|STATUS|Een van de volgende: afgebroken, geaccepteerde, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operationeel, actief, fout, verwijderen, verwijderd, out, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued of ClusterCustomization.|
|REGIO|Azure-locatie. Zie de vervolg keuzelijst **regio** in [prijzen voor HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)voor een lijst met ondersteunde Azure-locaties.|
|DATUM GEMAAKT|De datum waarop het cluster is geïmplementeerd.|
|Besturingssysteem|**Windows** of **Linux**.|
|TYPE|Hadoop, HBase, Storm, Spark.|
|Versie|Zie [HDInsight-versies](hdinsight-component-versioning.md).|
|Minimale TLS-versie|De TLS-versie.|
|ABONNEMENT|Abonnements naam.|
|STANDAARD GEGEVENS BRON|Het standaard bestandssysteem van het cluster.|
|Grootte van worker-knoop punten|De geselecteerde VM-grootte van de worker-knoop punten.|
|Hoofdknooppuntgrootte|De geselecteerde VM-grootte van de hoofd knooppunten.|
|Virtueel netwerk|De naam van de Virtual Network, die het cluster implementeert, als er een is geselecteerd tijdens de implementatie.|

## <a name="move-clusters"></a>Clusters verplaatsen

U kunt een HDInsight-cluster verplaatsen naar een andere Azure-resource groep of een ander abonnement.

Op de [Start pagina](#homePage)van het cluster:

1. Selecteer **verplaatsen** in het bovenste menu.
2. Selecteer **verplaatsen naar een andere resource groep** of **verplaatsen naar een ander abonnement**.
3. Volg de instructies op de pagina Nieuw.

## <a name="delete-clusters"></a>Clusters verwijderen

Als u een cluster verwijdert, worden het standaard opslag account en de gekoppelde opslag accounts niet verwijderd. U kunt het cluster opnieuw maken met behulp van dezelfde opslag accounts en dezelfde meta Stores. U kunt het beste een nieuwe standaard BLOB-container gebruiken wanneer u het cluster opnieuw maakt.

Op de [Start pagina](#homePage)van het cluster:

1. Selecteer **verwijderen** in het bovenste menu.
2. Volg de instructies op de pagina Nieuw.

Zie ook [clusters onderbreken/afsluiten](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Extra opslagaccounts toevoegen

U kunt extra Azure Storage accounts en Azure Data Lake Storage accounts toevoegen nadat een cluster is gemaakt. Zie [Extra opslagaccounts toevoegen aan HDInsight](./hdinsight-hadoop-add-storage.md) voor meer informatie.

## <a name="scale-clusters"></a>Clusters schalen

Met de functie voor het schalen van clusters kunt u het aantal worker-knoop punten wijzigen dat door een Azure HDInsight-cluster wordt gebruikt, zonder dat u het cluster opnieuw hoeft te maken.

Zie [HDInsight-clusters schalen](./hdinsight-scaling-best-practices.md) voor volledige informatie.

## <a name="pauseshut-down-clusters"></a>Onderbreken/afsluiten van clusters

De meeste Hadoop-taken zijn batch-taken die slechts af en toe worden uitgevoerd. Voor de meeste Hadoop-clusters zijn er grote Peri Oden waarin het cluster niet wordt gebruikt voor verwerking. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt.
Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

U kunt het proces op verschillende manieren Program meren:

- Gebruikers Azure Data Factory. Zie [on-demand Linux-gebaseerde Apache Hadoop clusters maken in HDInsight met behulp van Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) voor het maken van gekoppelde HDInsight-Services op aanvraag.
- Gebruik Azure PowerShell.  Zie [gegevens van vlucht vertraging analyseren](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Gebruik Azure CLI. Zie [Azure HDInsight-clusters beheren met Azure cli](hdinsight-administer-use-command-line.md).
- Gebruik HDInsight .NET SDK. Zie [Apache Hadoop taken verzenden](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Zie [prijzen voor HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)voor meer informatie over prijzen. Zie [clusters verwijderen](#delete-clusters) voor meer informatie over het verwijderen van een cluster uit de portal

## <a name="upgrade-clusters"></a>Upgrade clusters

Zie [HDInsight-cluster upgraden naar een nieuwere versie](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>De Web-UI van Apache Ambari openen

Ambari biedt een intuïtieve, eenvoudig te gebruiken Hadoop Management-webinterface die wordt ondersteund door de REST-Api's. Ambari stelt systeem beheerders in staat Hadoop-clusters te beheren en te controleren.

Op de [Start pagina](#homePage)van het cluster:

1. Selecteer **cluster dashboards**.

    ![Menu van HDInsight Apache Hadoop-cluster](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Selecteer **Ambari Home** op de nieuwe pagina.
1. Voer de gebruikers naam en het wacht woord van het cluster in.  De standaard naam van het cluster is _admin_.

Zie [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md)voor meer informatie.

## <a name="change-passwords"></a>Wacht woorden wijzigen

An HDInsight cluster kan twee gebruikers accounts hebben. Het gebruikers account voor het HDInsight-cluster (HTTP-gebruikers account) en het SSH-gebruikers account worden tijdens het maken gemaakt. U kunt de portal gebruiken om het wacht woord van het cluster gebruikers account te wijzigen en script acties voor het wijzigen van het SSH-gebruikers account.

### <a name="change-the-cluster-user-password"></a>Het wacht woord van de cluster gebruiker wijzigen

> [!NOTE]  
> Het wijzigen van het wacht woord van de cluster gebruiker (admin) kan ertoe leiden dat script acties worden uitgevoerd voor het mislukken van dit cluster. Als er persistente script acties zijn die doel worker-knoop punten hebben, kunnen deze scripts mislukken wanneer u knoop punten aan het cluster toevoegt door grootte van bewerkingen te wijzigen. Zie [HDInsight-clusters aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over script acties.

Op de [Start pagina](#homePage)van het cluster:
1. Selecteer **SSH +-cluster aanmelding** onder **instellingen**.
2. Selecteer **Referentie opnieuw instellen**.
3. Voer in de tekst vakken het nieuwe wacht woord in en bevestig dit.
4. Selecteer **OK**.

Het wacht woord wordt gewijzigd op alle knoop punten in het cluster.

### <a name="change-the-ssh-user-password-or-public-key"></a>Het wacht woord of de open bare sleutel van de SSH-gebruiker wijzigen

1. Gebruik een tekst editor om de volgende tekst op te slaan als een bestand met de naam **changecredentials.sh**.

    > [!IMPORTANT]  
    > U moet een editor gebruiken die LF als regel einde gebruikt. Als de editor gebruikmaakt van CRLF, werkt het script niet.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Upload het bestand naar een opslag locatie die toegankelijk is vanaf HDInsight met behulp van een HTTP-of HTTPS-adres. Bijvoorbeeld een openbaar bestands archief zoals OneDrive of Azure Blob-opslag. Sla de URI (HTTP of HTTPS-adres) op in het bestand, omdat deze URI in de volgende stap nodig is.
3. Selecteer op de [Start pagina](#homePage)van het cluster **script acties** onder **instellingen**.
4. Selecteer op de pagina **script acties** de optie **Nieuw verzenden**.
5. Voer op de pagina **script actie verzenden** de volgende gegevens in:

> [!NOTE]
> SSH-wacht woorden mogen niet de volgende tekens bevatten:
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | Veld | Waarde |
   | --- | --- |
   | Scripttype | Selecteer **-aangepast** in de vervolg keuzelijst.|
   | Name |"SSH-referenties wijzigen" |
   | Bash-script-URI |De URI naar het changecredentials.sh-bestand |
   | Knooppunt type (n): (Head, worker, Nimbus, supervisor of Zookeeper.) |✓ voor alle weer gegeven knooppunt typen |
   | Parameters |Voer de SSH-gebruikers naam en vervolgens het nieuwe wacht woord in. Er moet één spatie tussen de gebruikers naam en het wacht woord zijn. |
   | Deze script actie persistent maken... |Vul dit veld uit. |

6. Selecteer **maken** om het script toe te passen. Zodra het script is voltooid, kunt u met SSH verbinding maken met het cluster met de nieuwe referenties.

## <a name="find-the-subscription-id"></a>De abonnements-ID zoeken

Elk cluster is gekoppeld aan een Azure-abonnement.  De ID van het Azure-abonnement wordt weer gegeven op de [Start pagina](#homePage)van het cluster.

## <a name="find-the-resource-group"></a>De resource groep zoeken

In de Azure Resource Manager modus wordt elk HDInsight-cluster gemaakt met een Azure Resource Manager groep. De Resource Manager-groep is zichtbaar op de [Start pagina](#homePage)van het cluster.

## <a name="find-the-storage-accounts"></a>De opslag accounts zoeken

HDInsight-clusters gebruiken een Azure Storage account of Azure Data Lake Storage om gegevens op te slaan. Elk HDInsight-cluster kan één standaard Storage-account en een aantal gekoppelde opslag accounts hebben. Als u de opslag accounts wilt weer geven, selecteert u op de [Start pagina](#homePage) van het cluster onder **instellingen** de optie **opslag accounts**.

## <a name="monitor-jobs"></a>Taken controleren

Zie [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Grootte van cluster

Op de tegel **cluster grootte** van de [Start pagina](#homePage) van het cluster wordt het aantal kern geheugens weer gegeven dat aan het cluster is toegewezen en hoe deze worden toegewezen voor de knoop punten in dit cluster.

> [!IMPORTANT]  
> Als u de services wilt bewaken die worden meegeleverd met het HDInsight-cluster, moet u Ambari web of de Ambari-REST API gebruiken. Zie [HDInsight-clusters beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md) voor meer informatie over het gebruik van Ambari.

## <a name="connect-to-a-cluster"></a>Verbinding maken met een cluster

- [Apache Hive gebruiken met HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u enkele eenvoudige beheer functies geleerd. Lees de volgende artikelen voor meer informatie:

- [HDInsight beheren met Azure PowerShell](hdinsight-administer-use-powershell.md)
- [HDInsight beheren met Azure CLI](hdinsight-administer-use-command-line.md)
- [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)
- [Meer informatie over het gebruik van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Apache Hive in HDInsight gebruiken](hadoop/hdinsight-use-hive.md)
- [Apache Sqoop gebruiken in HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Met python door de gebruiker gedefinieerde functies (UDF) met Apache Hive en Apache varken in HDInsight gebruiken](hadoop/python-udf-hdinsight.md)
- [Welke versie van Apache Hadoop bevindt zich in azure HDInsight?](hdinsight-component-versioning.md)