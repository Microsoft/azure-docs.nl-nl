---
title: Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer
description: Hadoop-, Kafka-, Spark-, HBase-, R Server- of Storm-clusters instellen voor HDInsight vanuit een browser, de klassieke Azure CLI, Azure PowerShell, REST of SDK.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18, devx-track-azurecli
ms.date: 08/06/2020
ms.openlocfilehash: 3d1059ab46ff0e3722d1f6538bba61cdc4e8cb59
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482684"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Meer informatie over het instellen en configureren van Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services of Apache Storm in HDInsight. Leer ook hoe u clusters kunt aanpassen en beveiliging kunt toevoegen door ze toe te voegen aan een domein.

Een Hadoop-cluster bestaat uit verschillende virtuele machines (knooppunten) die worden gebruikt voor gedistribueerde verwerking van taken. Azure HDInsight verwerkt implementatiedetails van de installatie en configuratie van afzonderlijke knooppunten, zodat u alleen algemene configuratiegegevens hoeft op te geven.

> [!IMPORTANT]  
> De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. De facturering wordt pro-rato per minuut berekend, dus u moet altijd uw cluster verwijderen wanneer het niet meer wordt gebruikt. Meer informatie over [het verwijderen van een cluster.](hdinsight-delete-cluster.md)

Als u meerdere clusters tegelijk gebruikt, wilt u een virtueel netwerk maken. Als u een Spark-cluster gebruikt, wilt u ook de Hive Warehouse Connector gebruiken. Zie [Plan a virtual network voor Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) en [Integrate Apache Spark and Apache Hive with the Hive Warehouse Connector](interactive-query/apache-hive-warehouse-connector.md) voor meer informatie.

## <a name="cluster-setup-methods"></a>Installatiemethoden voor clusters

In de volgende tabel ziet u de verschillende methoden die u kunt gebruiken om een HDInsight-cluster in te stellen.

| Clusters die zijn gemaakt met | Webbrowser | Opdrachtregel | REST-API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure-portal](hdinsight-hadoop-create-linux-clusters-portal.md) |ââ" |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |ââ" |ââ" |ââ" |ââ" |
| [Azure-CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |ââ" |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |ââ" |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |ââ" |ââ" |&nbsp; |
| [Azure Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |ââ" |&nbsp; |&nbsp; |

In dit artikel wordt beschreven hoe u het [Azure Portal](https://portal.azure.com), waar u een HDInsight-cluster kunt maken.

## <a name="basics"></a>Basisbeginselen

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png" alt-text="hdinsight create options custom quick":::

### <a name="project-details"></a>Projectgegevens

[Azure Resource Manager](../azure-resource-manager/management/overview.md) helpt u bij het werken met de resources in uw toepassing als een groep, ook wel een [Azure-resourcegroep genoemd.](../azure-resource-manager/management/overview.md#resource-groups) U kunt alle resources voor uw toepassing implementeren, bijwerken, bewaken of verwijderen in één gecoördineerde bewerking.

### <a name="cluster-details"></a>Clusterdetails

#### <a name="cluster-name"></a>Clusternaam

Namen van HDInsight-clusters hebben de volgende beperkingen:

* Toegestane tekens: a-z, 0-9, A-Z
* Maximale lengte: 59
* Gereserveerde namen: apps
* Het clusternaamgevingsbereik is voor alle Azure-abonnementen. De clusternaam moet dus wereldwijd uniek zijn.
* De eerste zes tekens moeten uniek zijn binnen een virtueel netwerk

#### <a name="region"></a>Region

U hoeft de clusterlocatie niet expliciet op te geven: het cluster bevindt zich op dezelfde locatie als de standaardopslag. Selecteer de vervolgkeuzelijst Regio in HDInsight-prijzen **voor** een lijst met [ondersteunde regio's.](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)

#### <a name="cluster-type"></a>Clustertype

Azure HDInsight biedt momenteel de volgende clustertypen, elk met een set onderdelen om bepaalde functies te bieden.

> [!IMPORTANT]  
> HDInsight-clusters zijn beschikbaar in verschillende typen, elk voor één workload of technologie. Er is geen ondersteunde methode voor het maken van een cluster waarin meerdere typen worden gecombineerd, zoals Storm en HBase op één cluster. Als uw oplossing technologieën vereist die zijn verdeeld over meerdere HDInsight-clustertypen, kan een virtueel [Azure-netwerk](../virtual-network/index.yml) de vereiste clustertypen verbinden.

| Clustertype | Functionaliteit |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batchquery en analyse van opgeslagen gegevens |
| [HBase](hbase/apache-hbase-overview.md) |Verwerking voor grote hoeveelheden schemaloze NoSQL-gegevens |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |In-memory caching voor interactieve en snellere Hive-query's |
| [Kafka](kafka/apache-kafka-introduction.md) | Een gedistribueerd streamingplatform dat kan worden gebruikt voor het bouwen van pijplijnen en toepassingen voor realtime streaminggegevens |
| [ML Services](r-server/r-server-overview.md) |Diverse big data, voorspellende modellering en machine learning mogelijkheden |
| [Spark](spark/apache-spark-overview.md) |In-memory verwerking, interactieve query's, verwerking van microbatchstreams |
| [Storm](storm/apache-storm-overview.md) |Gebeurtenissen in realtime verwerken |

#### <a name="version"></a>Versie

Kies de versie van HDInsight voor dit cluster. Zie Ondersteunde [HDInsight-versies voor meer informatie.](hdinsight-component-versioning.md#supported-hdinsight-versions)

### <a name="cluster-credentials"></a>Clusterreferenties

Met HDInsight-clusters kunt u twee gebruikersaccounts configureren tijdens het maken van het cluster:

* Gebruikersnaam voor cluster aanmelding: de standaard gebruikersnaam is *admin*. Hierbij wordt de basisconfiguratie op de Azure Portal. Soms wordt deze 'Clustergebruiker' of 'HTTP-gebruiker' genoemd.
* SSH-gebruikersnaam (Secure Shell) : wordt gebruikt om via SSH verbinding te maken met het cluster. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

De HTTP-gebruikersnaam heeft de volgende beperkingen:

* Toegestane speciale tekens: `_` en `@`
* Tekens zijn niet toegestaan: #;.', \/ :'!*?$() {} []<>|&--=+%~^space
* Maximale lengte: 20

De SSH-gebruikersnaam heeft de volgende beperkingen:

* Toegestane speciale tekens: `_` en `@`
* Tekens zijn niet toegestaan: #;.', \/ :'!*?$() {} []<>|&--=+%~^space
* Maximale lengte: 64
* Gereserveerde namen: hadoop, users, oozie, hive, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, yarn, hcat, ams, hbase, storm, administrator, admin, user, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, console, david, guest, john, owner, root, server, sql, support, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>Storage

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png" alt-text="Instellingen voor clusteropslag: HDFS-compatibele eindpunten":::

Hoewel een on-premises installatie van Hadoop gebruikmaakt van de Hadoop Distributed File System (HDFS) voor opslag op het cluster, gebruikt u in de cloud opslag-eindpunten die zijn verbonden met het cluster. Als u cloudopslag gebruikt, kunt u veilig de HDInsight-clusters verwijderen die worden gebruikt voor berekeningen terwijl uw gegevens behouden blijven.

HDInsight-clusters kunnen gebruikmaken van de volgende opslagopties:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage Algemeen v2
* Azure Storage Algemeen v1
* Azure Storage Blok-blob **(alleen ondersteund als secundaire opslag)**

Zie Opslagopties vergelijken voor gebruik met clusterclusters voor meer informatie over opslagopties [Azure HDInsight HDInsight.](hdinsight-hadoop-compare-storage-options.md)

> [!WARNING]  
> Het gebruik van een extra opslagaccount op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

Tijdens de configuratie geeft u voor het standaardopslag-eindpunt een blobcontainer van een Azure Storage-account of -Data Lake Storage. De standaardopslag bevat toepassings- en systeemlogboeken. U kunt desgewenst aanvullende gekoppelde accounts Azure Storage en Data Lake Storage-accounts die het cluster kan openen. Het HDInsight-cluster en de afhankelijke opslagaccounts moeten zich op dezelfde Azure-locatie bevinden.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Het inschakelen van beveiligde opslagoverdracht na het maken van een cluster kan leiden tot fouten met het gebruik van uw opslagaccount. Dit wordt niet aanbevolen. Het is beter om een nieuw cluster te maken met behulp van een opslagaccount met beveiligde overdracht al ingeschakeld.

> [!Note]  
> Azure HDInsight verplaatst of kopieert uw gegevens die zijn opgeslagen in Azure Storage niet automatisch van de ene regio naar de andere.

### <a name="metastore-settings"></a>Metastore-instellingen

U kunt optionele Hive- of Apache Oozie-metastores maken. Niet alle clustertypen ondersteunen echter metastores en Azure Synapse Analytics niet compatibel met metastores.

Zie Use external metadata stores in Azure HDInsight [(Externe metagegevensopslag gebruiken in Azure HDInsight).](./hdinsight-use-external-metadata-stores.md)

> [!IMPORTANT]  
> Wanneer u een aangepaste metastore maakt, moet u geen streepjes, afbreekstreepels of spaties in de databasenaam gebruiken. Dit kan ertoe leiden dat het maken van het cluster mislukt.

#### <a name="sql-database-for-hive"></a>SQL database voor Hive

Als u uw Hive-tabellen wilt behouden nadat u een HDInsight-cluster hebt verwijderd, gebruikt u een aangepaste metastore. Vervolgens kunt u de metastore koppelen aan een ander HDInsight-cluster.

Een HDInsight-metastore die is gemaakt voor één HDInsight-clusterversie kan niet worden gedeeld met verschillende HDInsight-clusterversies. Zie Ondersteunde HDInsight-versies voor een lijst [met HDInsight-versies.](hdinsight-component-versioning.md#supported-hdinsight-versions)

> [!IMPORTANT]
> De standaard metastore biedt een Azure SQL Database met een DTU-limiet van basic **laag 5 (niet bij te werken)**! Geschikt voor eenvoudige testdoeleinden. Voor grote of productieworkloads wordt u aangeraden te migreren naar een externe metastore.

#### <a name="sql-database-for-oozie"></a>SQL database voor Oozie

Als u de prestaties wilt verbeteren bij het gebruik van Oozie, gebruikt u een aangepaste metastore. Een metastore kan ook toegang bieden tot Oozie-taakgegevens nadat u uw cluster hebt verwijderd.

#### <a name="sql-database-for-ambari"></a>SQL database voor Ambari

Ambari wordt gebruikt om HDInsight-clusters te bewaken, configuratiewijzigingen aan te brengen en informatie over clusterbeheer en taakgeschiedenis op te slaan. Met de aangepaste Ambari DB-functie kunt u een nieuw cluster implementeren en Ambari instellen in een externe database die u beheert. Zie Aangepaste [Ambari DB voor meer informatie.](./hdinsight-custom-ambari-db.md)

> [!IMPORTANT]  
> U kunt een aangepaste Oozie-metastore niet opnieuw gebruiken. Als u een aangepaste Oozie-metastore wilt gebruiken, moet u een lege Azure SQL Database bij het maken van het HDInsight-cluster.

## <a name="security--networking"></a>Beveiliging en netwerken

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png" alt-text="hdinsight create options choose enterprise security package (Opties voor hdinsight maken) kiezen voor Enterprise Security Package":::

### <a name="enterprise-security-package"></a>Enterprise-beveiligingspakket

Voor hadoop-, Spark-, HBase-, Kafka- en Interactive Query-clustertypen kunt u ervoor kiezen om de **Enterprise Security Package.** Dit pakket biedt de mogelijkheid om een veiliger cluster in te stellen met behulp van Apache Ranger en te integreren met Azure Active Directory. Zie Overzicht van bedrijfsbeveiliging in Azure HDInsight voor [meer Azure HDInsight.](./domain-joined/hdinsight-security-overview.md)

Met het Enterprise-beveiligingspakket kunt u HDInsight integreren met Active Directory en Apache Ranger. Er kunnen meerdere gebruikers worden gemaakt met behulp van het Enterprise-beveiligingspakket.

Zie Create domain-joined HDInsight sandbox environment (Aan een domein toevoegende [HDInsight-sandboxomgeving](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)maken) voor meer informatie over het maken van een HDInsight-cluster dat lid is van een domein.

### <a name="tls"></a>TLS

Zie voor meer informatie [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Virtueel netwerk

Als uw oplossing technologieën vereist die zijn verdeeld over meerdere HDInsight-clustertypen, kan een virtueel [Azure-netwerk](../virtual-network/index.yml) de vereiste clustertypen verbinden. Met deze configuratie kunnen de clusters en alle code die u in deze clusters implementeert, rechtstreeks met elkaar communiceren.

Zie Een virtueel netwerk plannen voor HDInsight voor meer informatie over het gebruik van een virtueel [Azure-netwerk met HDInsight.](hdinsight-plan-virtual-network-deployment.md)

Zie Apache Spark Structured Streaming gebruiken met Apache Kafka voor een voorbeeld van het gebruik van [twee clustertypen in een virtueel Azure-netwerk.](hdinsight-apache-kafka-spark-structured-streaming.md) Zie Een virtueel netwerk plannen voor HDInsight voor meer informatie over het gebruik van [HDInsight](hdinsight-plan-virtual-network-deployment.md)met een virtueel netwerk, inclusief specifieke configuratievereisten voor het virtuele netwerk.

### <a name="disk-encryption-setting"></a>Instelling voor schijfversleuteling

Zie Schijfversleuteling met door [de klant beheerde sleutel voor meer informatie.](./disk-encryption.md)

### <a name="kafka-rest-proxy"></a>Kafka REST-proxy

Deze instelling is alleen beschikbaar voor het clustertype Kafka. Zie Using [a REST proxy (Een REST-proxy gebruiken) voor meer informatie.](./kafka/rest-proxy.md)

### <a name="identity"></a>Identiteit

Zie Beheerde identiteiten in Azure HDInsight voor [meer Azure HDInsight.](./hdinsight-managed-identities.md)

## <a name="configuration--pricing"></a>Configuratie en prijzen

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png" alt-text="HDInsight uw knooppuntgrootte kiezen":::

U wordt gefactureerd voor het gebruik van knooppunt zolang het cluster bestaat. Facturering begint wanneer een cluster wordt gemaakt en stopt wanneer het cluster wordt verwijderd. Clusters kunnen niet worden verwijderd of in de wacht worden gezet.

### <a name="node-configuration"></a>Knooppuntconfiguratie

Elk clustertype heeft een eigen aantal knooppunten, terminologie voor knooppunten en standaard VM-grootte. In de volgende tabel staat het aantal knooppunten voor elk knooppunttype tussen haakjes.

| Type | Knooppunten | Diagram |
| --- | --- | --- |
| Hadoop |Hoofd-knooppunt (2), werkpunt (1+) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png" alt-text="HDInsight Hadoop-clusterknooppunten" border="false"::: |
| HBase |Hoofdserver (2), regioserver (1+), hoofd-/ZooKeeper-knooppunt (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png" alt-text="HdInsight HBase-clustertype instellen" border="false"::: |
| Storm |Nimbus-knooppunt (2), supervisorserver (1+), ZooKeeper-knooppunt (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png" alt-text="HdInsight Storm-clustertype instellen" border="false"::: |
| Spark |Hoofd knooppunt (2), werkpunt (1+), ZooKeeper-knooppunt (3) (gratis voor A1 ZooKeeper VM-grootte) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png" alt-text="Installatie van hdinsight spark-clustertype" border="false"::: |

Zie Standaardconfiguratie van knooppunt en grootten voor virtuele machines voor [clusters](hdinsight-supported-node-configuration.md) in Wat zijn de Hadoop-onderdelen en -versies in HDInsight? voor meer informatie.

De kosten van HDInsight-clusters worden bepaald door het aantal knooppunten en de grootte van virtuele machines voor de knooppunten.

Verschillende clustertypen hebben verschillende knooppunttypen, aantallen knooppunten en knooppuntgrootten:
* Standaard hadoop-clustertype:
    * Twee *hoofdknooppunten*
    * Vier *werkknooppunten*
* Standaardtype Storm-cluster:
    * Twee *Nimbus-knooppunten*
    * Drie *ZooKeeper-knooppunten*
    * Vier *supervisor-knooppunten*

Als u alleen HDInsight probeert, raden we u aan één Worker-knooppunt te gebruiken. Zie Prijzen voor HDInsight voor meer informatie [over hdinsight-prijzen.](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)

> [!NOTE]  
> De limiet voor de clustergrootte verschilt per Azure-abonnement. Neem contact [op met ondersteuning voor Azure-facturering](../azure-portal/supportability/how-to-create-azure-support-request.md) om de limiet te verhogen.

Wanneer u de Azure Portal om het cluster te configureren, is de knooppuntgrootte beschikbaar via **het tabblad Configuratie en** prijzen. In de portal ziet u ook de kosten die zijn gekoppeld aan de verschillende knooppuntgrootten.

### <a name="virtual-machine-sizes"></a>Grootten van virtuele machines

Wanneer u clusters implementeert, kiest u rekenbronnen op basis van de oplossing die u wilt implementeren. De volgende VM's worden gebruikt voor HDInsight-clusters:

* VM's uit de A- en D1-4-serie: [VM's](../virtual-machines/sizes-general.md) voor algemeen gebruik voor Linux
* VM uit de D11-14-serie: voor geheugen [geoptimaliseerde Linux-VM-grootten](../virtual-machines/sizes-memory.md)

Zie VM-grootten die moeten worden gebruikt voor [HDInsight-clusters](../cloud-services/cloud-services-sizes-specs.md#size-tables)als u wilt weten welke waarde u moet gebruiken om een VM-grootte op te geven tijdens het maken van een cluster met behulp van de verschillende SDK's of tijdens het gebruik van Azure PowerShell. Gebruik in dit gekoppelde artikel de waarde in de **kolom Grootte** van de tabellen.

> [!IMPORTANT]  
> Als u meer dan 32 werkknooppunten in een cluster nodig hebt, moet u een hoofdknooppuntgrootte met ten minste 8 kernen en 14 GB RAM selecteren.

Zie Grootten voor [virtuele machines voor meer informatie.](../virtual-machines/sizes.md) Zie [HDInsight-prijzen](https://azure.microsoft.com/pricing/details/hdinsight)voor informatie over prijzen van verschillende grootten.

### <a name="add-application"></a>Toepassing toevoegen

Een HDInsight-toepassing is een toepassing die gebruikers kunnen installeren op een op Linux gebaseerd HDInsight-cluster. U kunt toepassingen gebruiken die worden geleverd door Microsoft, derden of die u zelf ontwikkelt. Zie Apache [Hadoop-toepassingen van derden installeren](hdinsight-apps-install-applications.md)op Azure HDInsight voor meer Azure HDInsight.

De meeste HDInsight-toepassingen worden geïnstalleerd op een leeg edge-knooppunt.  Een leeg edge-knooppunt is een virtuele Linux-machine met dezelfde clienthulpprogramma's geïnstalleerd en geconfigureerd als in het hoofd-knooppunt. U kunt het edge-knooppunt gebruiken voor toegang tot het cluster, het testen van uw clienttoepassingen en het hosten van uw clienttoepassingen. Zie Lege edge-knooppunten [gebruiken in HDInsight voor meer informatie.](hdinsight-apps-use-edge-node.md)

### <a name="script-actions"></a>Scriptacties

U kunt extra onderdelen installeren of de clusterconfiguratie aanpassen met behulp van scripts tijdens het maken. Dergelijke scripts worden aangeroepen via **scriptactie**. Dit is een configuratieoptie die kan worden gebruikt vanuit de Azure Portal-, HDInsight-Windows PowerShell-cmdlets of de HDInsight .NET SDK. Zie [HDInsight-cluster aanpassen met scriptactie voor meer informatie.](hdinsight-hadoop-customize-cluster-linux.md)

Sommige systeemeigen Java-onderdelen, zoals Apache Mahout en Cascading, kunnen als Java Archive-bestanden (JAR) worden uitgevoerd op het cluster. Deze JAR-bestanden kunnen worden gedistribueerd naar Azure Storage verzonden naar HDInsight-clusters met Hadoop-mechanismen voor het indienen van een taak. Zie Apache [Hadoop-taken programmatisch verzenden voor meer informatie.](hadoop/submit-apache-hadoop-jobs-programmatically.md)

> [!NOTE]  
> Als u problemen hebt met het implementeren van JAR-bestanden in HDInsight-clusters of het aanroepen van JAR-bestanden op HDInsight-clusters, neem dan contact [op met Microsoft-ondersteuning](https://azure.microsoft.com/support/options/).
>
> Cascading wordt niet ondersteund door HDInsight en komt niet in aanmerking voor Microsoft-ondersteuning. Zie Wat is er nieuw [in de clusterversies van HDInsight](hdinsight-component-versioning.md)voor lijsten met ondersteunde onderdelen.

Soms wilt u de volgende configuratiebestanden configureren tijdens het aanmaakproces:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Zie [HDInsight-clusters aanpassen met Bootstrap voor meer informatie.](hdinsight-hadoop-customize-cluster-bootstrap.md)

## <a name="next-steps"></a>Volgende stappen

* [Fouten bij het maken van clusters oplossen met Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Wat zijn HDInsight, het Apache Hadoop-ecosysteem en Hadoop-clusters?](hadoop/apache-hadoop-introduction.md)
* [Aan de slag met Apache Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Werken in Apache Hadoop in HDInsight vanaf een Windows-pc](hdinsight-hadoop-windows-tools.md)
