---
title: Toepassingen van derden installeren op Azure HDInsight
description: Meer informatie over het installeren van Apache Hadoop toepassingen van derden op Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 46a22b78ade3d137f25b14e5cbdaff3264208228
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868389"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Apache Hadoop-toepassingen van derden installeren op Azure HDInsight

Meer informatie over het installeren van een [Apache Hadoop](https://hadoop.apache.org/) toepassing van derden in azure HDInsight. Zie voor instructies voor de installatie van uw eigen toepassing [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md).

An HDInsight toepassing is een toepassing die gebruikers kunnen installeren op een HDInsight-cluster. Deze toepassingen kunnen zijn ontwikkeld door Microsoft, door onafhankelijke softwareleveranciers (ISV) of door u zelf.  

De volgende lijst bevat de gepubliceerde toepassingen:

|Toepassing |Cluster type (n) | Description |
|---|---|---|
|[AtScale intelligence-platform](https://aws.amazon.com/marketplace/pp/AtScale-AtScale-Intelligence-Platform/B07BWWHH18) |Hadoop |Met AtScale wordt uw HDInsight-cluster omgezet in een scale-out OLAP-server, zodat u miljarden rijen gegevens interactief kunt opvragen met behulp van de BI-hulpprogram ma's die u al kent, eigenaar en liefde – van micro soft Excel, Power BI, tableau software naar QlikView. |
|[CDAP voor HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP is het eerste geïntegreerde integratie platform voor big data dat de time-to-waarde voor Hadoop versnelt en de IT-service in staat stelt om selfservice gegevens te leveren. Open source en uitbreid bare, CDAP verwijdert belemmeringen voor innovatie. Vereisten: 4 regio knooppunten, min D3 v2. |
|[Data meer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Het selfservice schaal bare platform voor het voorbereiden, verkennen en beheren van uw gegevens voor analyse versnelt het omschakelen van complexe gegevens van meerdere bronnen tot waardevolle informatie over het bedrijf, waardoor u sneller, slimmere inzichten kunt leveren op bedrijfs niveau. |
|[Dataiku DSS op HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS in een Enter prise data Science-platform waarmee gegevens wetenschappers en gegevens analisten kunnen samen werken om nieuwe gegevens producten en-services efficiënter te ontwerpen en uit te voeren, waardoor onbewerkte gegevens worden omgezet in impactieve voor spellingen. |
|[WANdisco Fusion HDI-app](https://community.wandisco.com/s/article/Use-WANdisco-Fusion-for-parallel-operation-of-ADLS-Gen1-and-Gen2) |Hadoop, Spark, HBase, Storm, Kafka |Het bijhouden van gegevens consistentie in een gedistribueerde omgeving is een enorme uitdaging voor gegevens bewerkingen. WANdisco Fusion, een software platform op bedrijfs niveau, lost dit probleem op door ongestructureerde gegevens consistentie in alle omgevingen in te scha kelen. |
|[H2O SparklingWater voor HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O mousserend water biedt ondersteuning voor de volgende gedistribueerde algoritmen: GLM, Naïve Bayes, gedistribueerd wille keurig forest, Gradient Boosting machine, diep Neural Networks, diep leren, K-houdt, PCA, gegeneraliseerde lage-positie modellen, anomalie detectie, automatische code ring. |
|[Realtimeplatform voor de integratie van Real-Time gegevens naar HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop, HBase, Storm, Spark, Kafka |Realtimeplatform (uitgesp roken: ' stream ') is een end-to-end streaming Data Integration + intelligence-platform, waarmee u doorlopende opname, verwerking en analyse van verschillende gegevens stromen kunt maken. |
|[Jumbune Enterprise-Accelerating BigData-analyse](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Op hoog niveau helpt Jumbune ondernemingen met, 1. Versnellen van TEZ, MapReduce & op basis van Spark-engine, Java, scala workload-prestaties. 2. Proactieve Hadoop-cluster bewaking, 3. Het kwaliteits beheer van gegevens in een gedistribueerd bestands systeem vaststellen. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Kyligence Enter prise, dat is aangedreven door Apache Kylin, maakt BI op Big data mogelijk. Als een Enter prise OLAP-engine op Hadoop kan Kyligence Enter prise de bedrijfs analist op Hadoop bouwen op basis van de industrie standaard data warehouse en BI-methodologie. |
|[Sterren explosie Presto voor Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto is een snelle en schaal bare gedistribueerde SQL-query-engine. Presto is ontworpen voor de schei ding van opslag en Compute en is ideaal voor het opvragen van gegevens in Azure Data Lake Storage, Azure Blob Storage, SQL-en NoSQL-data bases en andere gegevens bronnen. |
|[StreamSets gegevens verzamelaar voor HDInsight-Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets Data Collector is een licht gewicht krachtige engine waarmee gegevens in realtime worden gestreamd. Gebruik gegevens verzamelaar om gegevens in uw gegevens stromen te routeren en te verwerken. Het wordt geleverd met een proef licentie van 30 dagen. |
|[Trifacta Wrangler Enter prise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop, Spark, HBase |Trifacta Wrangler Enter prise voor HDInsight biedt ondersteuning voor zakelijke gegevens wrangling voor elke schaal van gegevens. De kosten voor het uitvoeren van Trifacta op Azure zijn een combi natie van kosten voor het Trifacta-abonnement plus de kosten van de Azure-infra structuur voor de virtuele machines. |
|[Unifi-gegevens platform](https://www.crunchbase.com/organization/unifi-software) |Hadoop, HBase, Storm, Spark |Het Unifi-gegevens platform is een naadloos geïntegreerde suite van self-service data tools, ontworpen om de zakelijke gebruiker in staat te stellen gegevens uitdagingen aan te pakken waarmee incrementele omzet wordt gestimuleerd, de kosten en de operationele complexiteit worden verminderd. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel data-app voor HDInsight Spark-cluster. |
|[AI-Driven van water lijn Data Catalog](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |De water Raad catalogi, organiseert en bestuurt gegevens met behulp van AI om gegevens automatisch te labelen met bedrijfs voorwaarden. De Business-transcrib Catalog van de water lijn is een essentieel onderdeel van het succes van self-service analyses, naleving en governance en IT-beheer. |

De instructies in dit artikel zijn bestemd voor gebruik met Azure Portal. U kunt ook de Azure Resource Manager sjabloon exporteren vanuit de portal of een kopie van de Resource Manager-sjabloon van leveranciers verkrijgen, en Azure PowerShell en Azure Classic CLI gebruiken om de sjabloon te implementeren.  Zie [Apache Hadoop clusters maken in HDInsight met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Vereisten
Als u HDInsight-toepassingen wilt installeren op een bestaand HDInsight-cluster, hebt u een HDInsight-cluster nodig. Zie [Clusters maken](hadoop/apache-hadoop-linux-tutorial-get-started.md) voor informatie over het maken van een cluster. U kunt ook HDInsight-toepassingen installeren wanneer u een HDInsight-cluster maakt.

## <a name="install-applications-to-existing-clusters"></a>Toepassingen installeren in bestaande clusters
De volgende procedure beschrijft hoe u HDInsight-toepassingen in een bestaand HDInsight-cluster installeert.

**Een HDInsight-toepassing installeren**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga in het menu links naar **alle services**  >  **Analytics**  >  **HDInsight-clusters**.
3. Selecteer een HDInsight-cluster in de lijst.  Als u deze niet hebt, maakt u die eerst.  Zie [Clusters maken](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. Selecteer in de categorie **instellingen** de optie **toepassingen**. In het hoofd venster ziet u een lijst met geïnstalleerde toepassingen. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png" alt-text="Menu van HDInsight-toepassingenportal":::
5. Selecteer **+ toevoegen** in het menu. U kunt een lijst met beschik bare toepassingen weer geven.  Als **+ add** grijs wordt weer gegeven, betekent dit dat er geen toepassingen zijn voor deze versie van het HDInsight-cluster.
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png" alt-text="HDInsight-toepassingen, beschikbare toepassingen":::
6. Selecteer een van de beschik bare toepassingen en volg de instructies om de juridische voor waarden te accepteren.

U kunt de installatie status bekijken via de meldingen van de portal (Selecteer het klok pictogram boven aan de portal). Nadat de toepassing is geïnstalleerd, wordt de toepassing weer gegeven in de lijst met geïnstalleerde apps.

## <a name="install-applications-during-cluster-creation"></a>Toepassingen installeren tijdens het maken van het cluster

U hebt ook de optie HDInsight-toepassingen te installeren wanneer u een cluster maakt. Tijdens het proces worden HDInsight-toepassingen geïnstalleerd nadat het cluster is gemaakt en actief is. Als u toepassingen wilt installeren tijdens het maken van het cluster met behulp van de Azure Portal, klikt u op het tabblad **configuratie + prijzen** en selecteert u **+ toepassing toevoegen**.

:::image type="content" source="./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png" alt-text="Azure Portal cluster configuratie toepassingen":::

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lijst van geïnstalleerde HDInsight-apps en -eigenschappen
De portal toont een lijst van de geïnstalleerde HDInsight-toepassingen voor een cluster en de eigenschappen van elke geïnstalleerde toepassing.

**Lijst met HDInsight-toepassingen en weer gave-eigenschappen**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga in het menu links naar **alle services**  >  **Analytics**  >  **HDInsight-clusters**.
3. Selecteer een HDInsight-cluster in de lijst.
4. Selecteer in de categorie **instellingen** de optie **toepassingen**. In het hoofd venster ziet u een lijst met geïnstalleerde toepassingen. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png" alt-text="Apps die zijn geïnstalleerd met HDInsight-toepassingen":::
5. Selecteer een van de geïnstalleerde toepassingen om de eigenschap weer te geven. De eigenschappen lijsten:

    |Eigenschap | Beschrijving |
    |---|---|
    |Naam van app |Toepassingsnaam. |
    |Status |Toepassings status. |
    |Webpagina |De URL van de webtoepassing die u hebt geïmplementeerd op het Edge-knoop punt. De referenties zijn dezelfde als de HTTP-gebruikersreferenties die u hebt geconfigureerd voor het cluster. |
    |SSH-eind punt |U kunt SSH gebruiken om verbinding te maken met het Edge-knoop punt. De SSH-referenties zijn dezelfde als de SSH-gebruikersreferenties die u hebt geconfigureerd voor het cluster. Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie. |
    |Description | Beschrijving van toepassing. |

6. Als u een toepassing wilt verwijderen, klikt u met de rechter muisknop op de toepassing en klikt u vervolgens op **verwijderen** in het context menu.

## <a name="connect-to-the-edge-node"></a>Verbinding maken met het edge-knooppunt
U kunt verbinding maken met het edge-knooppunt door middel van HTTP en SSH. Informatie over het eindpunt kunt u vinden in de [portal](#list-installed-hdinsight-apps-and-properties). Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

De HTTP-eindpuntreferenties zijn de HTTP-gebruikersreferenties die u hebt geconfigureerd voor het HDInsight-cluster. De SSH-eindpuntreferenties zijn de SSH-referenties die u hebt geconfigureerd voor het HDInsight-cluster.

## <a name="troubleshoot"></a>Problemen oplossen
Zie [Problemen met de installatie oplossen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Volgende stappen
* [Aangepaste hdinsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md): meer informatie over het implementeren van een niet-gepubliceerde hdinsight-toepassing op hdinsight.
* [HDInsight-toepassingen publiceren](hdinsight-apps-publish-applications.md): informatie over het publiceren van aangepaste HDInsight-toepassingen in Azure Marketplace.
* [MSDN: een HDInsight-toepassing installeren](/rest/api/hdinsight/hdinsight-application): informatie over het definiëren van HDInsight-toepassingen.
* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van een scriptactie](hdinsight-hadoop-customize-cluster-linux.md): informatie over het gebruik van een scriptactie om extra toepassingen te installeren.
* [Op Linux gebaseerde Apache Hadoop clusters maken in HDInsight met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informatie over het aanroepen van Resource Manager-sjablonen voor het maken van HDInsight-clusters.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Lege edge-knooppunten gebruiken in HDInsight): meer informatie over het gebruik van een leeg edge-knooppunt om toegang te krijgen tot het HDInsight-cluster, HDInsight toepassingen te testen en HDInsight-toepassingen te hosten.