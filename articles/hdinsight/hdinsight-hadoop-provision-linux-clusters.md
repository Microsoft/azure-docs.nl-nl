---
title: Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer
description: Stel Hadoop-, Kafka-, Spark-, HBase-, R Server-of Storm-clusters in voor HDInsight vanuit een browser, de klassieke CLI van Azure, Azure PowerShell, REST of SDK.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: 4e81ccb541b188fedfefd150233082e9cb1f8c22
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945542"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Meer informatie over het instellen en configureren van Apache Hadoop, Apache Spark, Apache Kafka, interactieve query, Apache HBase, ML Services of Apache Storm in HDInsight. Meer informatie over het aanpassen van clusters en het toevoegen van beveiliging door ze te koppelen aan een domein.

Een Hadoop-cluster bestaat uit meerdere virtuele machines (knoop punten) die worden gebruikt voor gedistribueerde verwerking van taken. Azure HDInsight verwerkt implementatie details van de installatie en configuratie van afzonderlijke knoop punten, zodat u alleen algemene configuratie-informatie hoeft op te geven.

> [!IMPORTANT]  
> De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. De facturering wordt pro-rato per minuut berekend, dus u moet altijd uw cluster verwijderen wanneer het niet meer wordt gebruikt. Meer informatie over het [verwijderen van een cluster.](hdinsight-delete-cluster.md)

Als u meerdere clusters tegelijk gebruikt, wilt u een virtueel netwerk maken. Als u een Spark-cluster gebruikt, wilt u ook de Hive Warehouse Connector gebruiken. Zie [Plan a virtual network voor Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) en [Integrate Apache Spark and Apache Hive with the Hive Warehouse Connector](interactive-query/apache-hive-warehouse-connector.md) voor meer informatie.

## <a name="cluster-setup-methods"></a>Cluster installatie methoden

In de volgende tabel ziet u de verschillende methoden die u kunt gebruiken om een HDInsight-cluster in te stellen.

| Clusters die zijn gemaakt met | Webbrowser | Opdrachtregel | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure-portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure-CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Dit artikel begeleidt u bij het instellen van de [Azure Portal](https://portal.azure.com), waar u een HDInsight-cluster kunt maken.

## <a name="basics"></a>Basisbeginselen

![opties voor hdinsight maken aangepaste snelle](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Projectgegevens

[Azure Resource Manager](../azure-resource-manager/management/overview.md) helpt u bij het werken met de resources in uw toepassing als een groep, een Azure- [resource groep](../azure-resource-manager/management/overview.md#resource-groups)genoemd. U kunt in één gecoördineerde bewerking alle resources voor uw toepassing implementeren, bijwerken, bewaken of verwijderen.

### <a name="cluster-details"></a>Cluster Details

#### <a name="cluster-name"></a>Clusternaam

Voor de namen van HDInsight-clusters gelden de volgende beperkingen:

* Toegestane tekens: a-z, 0-9, A-Z
* Maximale lengte: 59
* Gereserveerde namen: apps
* Het bereik van de cluster naamgeving geldt voor alle Azure, in alle abonnementen. De cluster naam moet dus wereld wijd uniek zijn.
* De eerste zes tekens moeten uniek zijn in een virtueel netwerk

#### <a name="region"></a>Regio

U hoeft de cluster locatie niet expliciet op te geven: het cluster bevindt zich op dezelfde locatie als de standaard opslag. Voor een lijst met ondersteunde regio's selecteert u de vervolg keuzelijst **regio** voor prijzen voor [HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Clustertype

Azure HDInsight biedt momenteel de volgende cluster typen, elk met een aantal onderdelen voor bepaalde functies.

> [!IMPORTANT]  
> HDInsight-clusters zijn beschikbaar in verschillende typen, elk voor één werk belasting of technologie. Er is geen ondersteunde methode voor het maken van een cluster dat meerdere typen combineert, zoals Storm en HBase op één cluster. Als uw oplossing technologieën vereist die verspreid zijn over meerdere typen HDInsight-clusters, kan een [virtueel Azure-netwerk](../virtual-network/index.yml) verbinding maken met de vereiste cluster typen.

| Clustertype | Functionaliteit |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch-query en analyse van opgeslagen gegevens |
| [HBase](hbase/apache-hbase-overview.md) |Verwerking voor grote hoeveel heden schemaloos, NoSQL-gegevens |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |In-memory cache voor interactieve en snellere Hive-query's |
| [Kafka](kafka/apache-kafka-introduction.md) | Een gedistribueerd streaming-platform dat kan worden gebruikt om realtime streaming-gegevens pijplijnen en toepassingen te maken |
| [ML Services](r-server/r-server-overview.md) |Diverse big data statistieken, voorspellende modellen en machine learning mogelijkheden |
| [Spark](spark/apache-spark-overview.md) |Verwerking in het geheugen, interactieve query's, micro batch-stroom verwerking |
| [Storm](storm/apache-storm-overview.md) |Gebeurtenissen in realtime verwerken |

#### <a name="version"></a>Versie

Kies de versie van HDInsight voor dit cluster. Zie [ondersteunde HDInsight-versies](hdinsight-component-versioning.md#supported-hdinsight-versions)voor meer informatie.

### <a name="cluster-credentials"></a>Cluster referenties

Met HDInsight-clusters kunt u twee gebruikers accounts configureren tijdens het maken van het cluster:

* Gebruikers naam voor cluster aanmelding: de standaard gebruikersnaam is *admin*. Hierbij wordt gebruikgemaakt van de basis configuratie op het Azure Portal. Soms wordt de naam ' cluster gebruiker ' of ' HTTP gebruiker ' genoemd.
* SSH-gebruikers naam (Secure Shell): wordt gebruikt om via SSH verbinding te maken met het cluster. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

De HTTP-gebruikers naam heeft de volgende beperkingen:

* Toegestane speciale tekens: `_` en `@`
* Tekens zijn niet toegestaan: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ ruimte
* Maximale lengte: 20

De SSH-gebruikers naam heeft de volgende beperkingen:

* Toegestane speciale tekens: `_` en `@`
* Tekens zijn niet toegestaan: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ ruimte
* Maximale lengte: 64
* Gereserveerde namen: Hadoop, users, oozie, Hive, mapred, ambari-QA, Zookeeper, TEZ, hdfs, sqoop, garens, hcat, AMS, hbase, Storm, Administrator, admin, User, gebruiker1, test,, test1, user3, admin1, 1, 123, a, actuser, adm, Admin2, ASPNET, backup, console, David, gast, John, owner, root, Server, SQL, ondersteuning, support_388945a0, sys, Test2, Test3, user4, user5, Spark

## <a name="storage"></a>Storage

![Instellingen voor cluster opslag: HDFS-compatibele eind punten](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Hoewel een on-premises installatie van Hadoop gebruikmaakt van de Hadoop Distributed File System (HDFS) voor opslag op het cluster, gebruikt u in de Cloud opslag eindpunten die zijn verbonden met het cluster. Door gebruik te maken van Cloud opslag kunt u de HDInsight-clusters die worden gebruikt voor berekeningen, veilig verwijderen terwijl uw gegevens behouden blijven.

HDInsight-clusters kunnen de volgende opslag opties gebruiken:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage Algemeen v2
* Azure Storage Algemeen v1
* Azure Storage blok-BLOB (**alleen ondersteund als secundaire opslag**)

Zie voor meer informatie over opslag opties met HDInsight de [Opties voor het vergelijken van opslag voor gebruik met Azure HDInsight-clusters](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Het gebruik van een extra opslag account in een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

Tijdens de configuratie geeft u voor het standaard opslag eindpunt een BLOB-container van een Azure Storage account of Data Lake Storage op. De standaard opslag bevat toepassings-en systeem Logboeken. Desgewenst kunt u aanvullende gekoppelde Azure Storage accounts en Data Lake Storage accounts opgeven waartoe het cluster toegang heeft. Het HDInsight-cluster en de afhankelijke opslag accounts moeten zich op dezelfde Azure-locatie beslaan.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Het inschakelen van beveiligde opslag overdracht na het maken van een cluster kan leiden tot fouten bij het gebruik van uw opslag account en wordt niet aanbevolen. Het is beter om een nieuw cluster te maken met behulp van een opslag account waarvoor beveiligde overdracht al is ingeschakeld.

> [!Note]  
> Azure HDInsight brengt uw opgeslagen gegevens in Azure Storage van de ene regio naar de andere niet automatisch over, verplaatst of kopieert deze.

### <a name="metastore-settings"></a>Meta Store-instellingen

U kunt een optioneel Hive-of Apache Oozie-meta Store maken. Maar niet alle cluster typen ondersteunen meta Stores en Azure Synapse Analytics is niet compatibel met meta Stores.

Zie [externe meta gegevens archieven gebruiken in azure HDInsight](./hdinsight-use-external-metadata-stores.md)voor meer informatie.

> [!IMPORTANT]  
> Wanneer u een aangepaste meta Store maakt, gebruikt u geen streepjes, afbreek streepjes of spaties in de database naam. Dit kan ertoe leiden dat het proces voor het maken van een cluster mislukt.

#### <a name="sql-database-for-hive"></a>SQL database voor Hive

Als u de Hive-tabellen wilt behouden nadat u een HDInsight-cluster hebt verwijderd, gebruikt u een aangepaste meta Store. U kunt de meta Store vervolgens koppelen aan een ander HDInsight-cluster.

An HDInsight meta Store dat is gemaakt voor één HDInsight-cluster versie kan niet worden gedeeld tussen verschillende versies van het HDInsight-cluster. Zie [ondersteunde hdinsight-versies](hdinsight-component-versioning.md#supported-hdinsight-versions)voor een lijst met hdinsight-versies.

> [!IMPORTANT]
> De standaard-META Store biedt een Azure SQL Database met een **elementaire 5 DTU-limiet (niet uitbreidbaar)**. Geschikt voor basis test doeleinden. Voor grote of productie werkbelasting raden wij u aan de migratie naar een externe meta Store te migreren.

#### <a name="sql-database-for-oozie"></a>SQL database voor Oozie

Gebruik een aangepaste meta Store om de prestaties te verbeteren wanneer u Oozie gebruikt. Een meta Store kan ook toegang bieden tot Oozie-taak gegevens nadat u uw cluster hebt verwijderd.

#### <a name="sql-database-for-ambari"></a>SQL database voor Ambari

Ambari wordt gebruikt om HDInsight-clusters te controleren, configuratie wijzigingen aan te brengen en Cluster beheer gegevens op te slaan, evenals de taak geschiedenis. Met de functie Custom Ambari DB kunt u een nieuw cluster en installatie-Ambari implementeren in een externe data base die u beheert. Zie [Custom AMBARI DB](./hdinsight-custom-ambari-db.md)(Engelstalig) voor meer informatie.

> [!IMPORTANT]  
> U kunt een aangepaste Oozie-meta Store niet opnieuw gebruiken. Als u een aangepaste Oozie-meta Store wilt gebruiken, moet u een lege Azure SQL Database opgeven bij het maken van het HDInsight-cluster.

## <a name="security--networking"></a>Beveiliging en netwerken

![opties voor het maken van hdinsight het ondernemings beveiligings pakket kiezen](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Enter prise-beveiligings pakket

Voor Hadoop-, Spark-, HBase-, Kafka-en interactieve query cluster typen kunt u ervoor kiezen om de **Enterprise Security Package** in te scha kelen. Met dit pakket beschikt u over een veiligere cluster installatie door Apache zwerver te gebruiken en te integreren met Azure Active Directory. Zie [overzicht van Bedrijfs beveiliging in azure HDInsight](./domain-joined/hdinsight-security-overview.md)voor meer informatie.

Met het beveiligings pakket van de onderneming kunt u HDInsight integreren met Active Directory en Apache zwerver. Meerdere gebruikers kunnen worden gemaakt met behulp van het Enter prise-beveiligings pakket.

Zie voor meer informatie over het maken van een domein toegevoegd HDInsight-cluster maken een in [domein gekoppelde hdinsight sandbox-omgeving](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

### <a name="tls"></a>TLS

Zie [Transport Layer Security](./transport-layer-security.md) voor meer informatie.

### <a name="virtual-network"></a>Virtueel netwerk

Als uw oplossing technologieën vereist die verspreid zijn over meerdere typen HDInsight-clusters, kan een [virtueel Azure-netwerk](../virtual-network/index.yml) verbinding maken met de vereiste cluster typen. Met deze configuratie kunnen de clusters en alle code die u hier implementeert, direct met elkaar communiceren.

Zie [een virtueel netwerk plannen voor hdinsight](hdinsight-plan-virtual-network-deployment.md)voor meer informatie over het gebruik van een virtueel Azure-netwerk met hdinsight.

Zie [Apache Spark Structured streaming gebruiken met Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md)voor een voor beeld van het gebruik van twee cluster typen in een virtueel Azure-netwerk. Zie [een virtueel netwerk plannen voor HDInsight](hdinsight-plan-virtual-network-deployment.md)voor meer informatie over het gebruik van HDInsight met een virtueel netwerk, inclusief specifieke configuratie vereisten voor het virtuele netwerk.

### <a name="disk-encryption-setting"></a>Instelling voor schijf versleuteling

Zie door de [klant beheerde sleutel schijf versleuteling](./disk-encryption.md)voor meer informatie.

### <a name="kafka-rest-proxy"></a>Kafka REST-proxy

Deze instelling is alleen beschikbaar voor cluster type Kafka. Zie [using a rest proxy](./kafka/rest-proxy.md)(Engelstalig) voor meer informatie.

### <a name="identity"></a>Identiteit

Zie [beheerde identiteiten in azure HDInsight](./hdinsight-managed-identities.md)voor meer informatie.

## <a name="configuration--pricing"></a>Configuratie en prijzen

![HDInsight de grootte van het knoop punt kiezen](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

U wordt gefactureerd voor het gebruik van knoop punten zolang het cluster bestaat. De facturering begint wanneer een cluster wordt gemaakt en stopt wanneer het cluster wordt verwijderd. De toewijzing van clusters kan niet ongedaan worden gemaakt of in de wacht stand worden geplaatst.

### <a name="node-configuration"></a>Knooppuntconfiguratie

Elk cluster type heeft een eigen aantal knoop punten, terminologie voor knoop punten en standaard VM-grootte. In de volgende tabel staat het aantal knoop punten voor elk knooppunt type tussen haakjes.

| Type | Knooppunten | Diagram |
| --- | --- | --- |
| Hadoop |Hoofd knooppunt (2), Worker-knoop punt (1 +) |![HDInsight Hadoop-cluster knooppunten](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Hoofd server (2), regio server (1 +), hoofd-ZooKeeper knoop punt (3) |![Setup van HDInsight HBase-cluster type](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus-knoop punt (2), supervisor server (1 +), ZooKeeper-knoop punt (3) |![Instellingen voor het cluster type voor HDInsight Storm](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hoofd knooppunt (2), Worker-knoop punt (1 +), ZooKeeper-knoop punt (3) (gratis voor de VM-grootte van a1 ZooKeeper) |![Setup van HDInsight Spark-cluster type](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Zie [standaard knooppunt configuratie en grootten voor virtuele machines](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) in ' wat zijn de Hadoop-onderdelen en versies in HDInsight? ' voor meer informatie.

De kosten voor HDInsight-clusters worden bepaald door het aantal knoop punten en de grootte van de virtuele machines voor de knoop punten.

Verschillende cluster typen hebben verschillende knooppunt typen, aantal knoop punten en knooppunt grootten:
* Standaard Hadoop-cluster type:
    * Twee *hoofd knooppunten*  
    * Vier *worker-knoop punten*
* Storm-cluster type standaard:
    * Twee *Nimbus-knoop punten*
    * Drie *ZooKeeper-knoop punten*
    * Vier *supervisor knooppunten*

Als u alleen een HDInsight probeert uit te voeren, raden we u aan om één worker-knoop punt te gebruiken. Zie [prijzen voor hdinsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)voor meer informatie over de prijzen voor hdinsight.

> [!NOTE]  
> De limiet voor de cluster grootte is afhankelijk van Azure-abonnementen. Neem contact op met de [ondersteuning voor Azure-facturering](../azure-portal/supportability/how-to-create-azure-support-request.md) om de limiet te verhogen.

Wanneer u de Azure Portal gebruikt om het cluster te configureren, is de grootte van het knoop punt beschikbaar via het tabblad **configuratie en prijzen** . In de portal kunt u ook de kosten zien die zijn gekoppeld aan de verschillende knooppunt grootten.

### <a name="virtual-machine-sizes"></a>Grootten van virtuele machines

Wanneer u clusters implementeert, kiest u Compute-resources op basis van de oplossing die u wilt implementeren. De volgende virtuele machines worden gebruikt voor HDInsight-clusters:

* A-en D1-4-serie-Vm's: [algemeen gebruik Linux VM-grootten](../virtual-machines/sizes-general.md)
* D11-VM van de 14-serie: door [geheugen geoptimaliseerde Linux VM-grootten](../virtual-machines/sizes-memory.md)

Als u wilt weten welke waarde u moet gebruiken om een VM-grootte op te geven tijdens het maken van een cluster met behulp van de verschillende Sdk's of wanneer u Azure PowerShell gebruikt, raadpleegt u [VM-grootten voor HDInsight-clusters](../cloud-services/cloud-services-sizes-specs.md#size-tables). Gebruik in dit gekoppelde artikel de waarde in de kolom **grootte** van de tabellen.

> [!IMPORTANT]  
> Als u meer dan 32 worker-knoop punten in een cluster nodig hebt, moet u een hoofd knooppunt grootte selecteren met ten minste 8 kernen en 14 GB aan RAM-geheugen.

Zie [grootten voor virtuele machines](../virtual-machines/sizes.md)voor meer informatie. Zie [prijzen voor HDInsight](https://azure.microsoft.com/pricing/details/hdinsight)voor meer informatie over de prijzen van de verschillende grootten.

### <a name="add-application"></a>Toepassing toevoegen

Een HDInsight-toepassing is een toepassing die gebruikers kunnen installeren op een op Linux gebaseerd HDInsight-cluster. U kunt gebruikmaken van toepassingen die door micro soft, derden of door u worden meegeleverd. Zie [Apache Hadoop toepassingen van derden installeren op Azure HDInsight](hdinsight-apps-install-applications.md)voor meer informatie.

De meeste HDInsight-toepassingen worden geïnstalleerd op een leeg Edge-knoop punt.  Een lege Edge-knoop punt is een virtuele Linux-machine waarop dezelfde client hulpprogramma's zijn geïnstalleerd en geconfigureerd als in het hoofd knooppunt. U kunt het Edge-knoop punt gebruiken om toegang te krijgen tot het cluster, uw client toepassingen te testen en uw client toepassingen te hosten. Zie [lege Edge-knoop punten in HDInsight gebruiken](hdinsight-apps-use-edge-node.md)voor meer informatie.

### <a name="script-actions"></a>Script acties

U kunt extra onderdelen installeren of cluster configuratie aanpassen met behulp van scripts tijdens het maken. Dergelijke scripts worden aangeroepen via **script actie**, een configuratie optie die kan worden gebruikt vanuit de Azure Portal, Hdinsight Windows Power shell-cmdlets of de HDINSIGHT .NET SDK. Zie [HDInsight-cluster aanpassen met script actie](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie.

Sommige systeem eigen Java-onderdelen, zoals Apache mahout en trapsgewijs, kunnen worden uitgevoerd op het cluster als Java Archive-bestanden (JAR). Deze JAR-bestanden kunnen worden gedistribueerd naar Azure Storage en worden verzonden naar HDInsight-clusters met een Hadoop-taak indienings mechanismen. Zie [Apache Hadoop-taken via een programma verzenden](hadoop/submit-apache-hadoop-jobs-programmatically.md)voor meer informatie.

> [!NOTE]  
> Neem contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/)als u problemen hebt met het implementeren van jar-bestanden op hdinsight-clusters of het aanroepen van jar-bestanden in hdinsight-clusters.
>
> Trapsgewijs bijwerken wordt niet ondersteund door HDInsight en komt niet in aanmerking voor Microsoft Ondersteuning. Zie [Wat is er nieuw in de cluster versies van HDInsight](hdinsight-component-versioning.md)voor lijsten met ondersteunde onderdelen.

Soms wilt u tijdens het aanmaak proces de volgende configuratie bestanden configureren:

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

Zie [HDInsight-clusters aanpassen met Boots trap](hdinsight-hadoop-customize-cluster-bootstrap.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met het maken van clusters oplossen met Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Wat zijn HDInsight, het Apache Hadoop ecosysteem en Hadoop-clusters?](hadoop/apache-hadoop-introduction.md)
* [Aan de slag met Apache Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Werken in Apache Hadoop op HDInsight vanaf een Windows-computer](hdinsight-hadoop-windows-tools.md)