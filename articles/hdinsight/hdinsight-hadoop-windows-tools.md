---
title: Een Windows-PC gebruiken met Hadoop in HDInsight-Azure
description: Werk vanaf een Windows-PC in Hadoop op HDInsight. Beheer en vraag clusters met Power shell-, Visual Studio-en Linux-hulpprogram ma's. Ontwikkel big data oplossingen met .NET.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: ceb83c8c1754f0bc298290f522a8ae532db32140
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434607"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Werken in het Apache Hadoop ecosysteem op HDInsight vanaf een Windows-computer

Meer informatie over de opties voor ontwikkelen en beheren op de Windows-PC voor het werken in het Apache Hadoop ecosysteem op HDInsight.

HDInsight is gebaseerd op Apache Hadoop-en Hadoop-onderdelen, open-source technologieën die zijn ontwikkeld op Linux. HDInsight-versie 3,4 en hoger maakt gebruik van de Ubuntu Linux distributie als het onderliggende besturings systeem voor het cluster. U kunt echter met HDInsight werken vanuit een Windows-client of Windows-ontwikkel omgeving.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Power shell gebruiken voor implementatie-en beheer taken

Azure PowerShell is een script omgeving die u kunt gebruiken om implementatie-en beheer taken in HDInsight van Windows te beheren en te automatiseren.

Voor beelden van taken die u kunt uitvoeren met Power shell:

* [Clusters maken met behulp van Power shell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Voer Apache Hive Query's uit met behulp van Power shell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Clusters beheren met Power shell](hdinsight-administer-use-powershell.md).

Volg de stappen voor het [installeren en configureren van Azure Power shell](/powershell/azure/install-az-ps) om de nieuwste versie te verkrijgen.

## <a name="utilities-you-can-run-in-a-browser"></a>Hulpprogram ma's die u kunt uitvoeren in een browser

De volgende hulpprogram ma's hebben een web-UI die wordt uitgevoerd in een browser:
* **[Azure Cloud shell](../cloud-shell/overview.md)** is een interactieve, opdracht regel shell die wordt uitgevoerd in uw browser en vanuit de Azure Portal.

* De **[Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md)** is een hulp programma voor beheer en bewaking dat beschikbaar is in het Azure Portal dat kan worden gebruikt voor het beheren van verschillende soorten taken, zoals:
    * [Apache Ambari gebruiken met de REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive weer geven in Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache TEZ View in Apache Ambari](./index.yml)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop)-Hulpprogram Ma's voor Visual Studio

Gebruik Data Lake-Hulpprogram Ma's voor Visual Studio om Storm-topologieën te implementeren en te beheren. Met Data Lake-Hulpprogram Ma's wordt ook de SCP.NET-SDK geïnstalleerd, waarmee u C# Storm-topologieën kunt ontwikkelen met Visual Studio.

Voordat u naar de volgende voor beelden gaat, [installeert en probeert u data Lake-Hulpprogram ma's voor Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Voor beelden van taken die u kunt uitvoeren met Visual Studio en Data Lake-Hulpprogram Ma's voor Visual Studio:
* [Storm-topologieën implementeren en beheren vanuit Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Ontwikkel C#-topologieën voor Storm met Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). De bits bevatten voorbeeld sjablonen voor Storm-topologieën waarmee u verbinding kunt maken met data bases, zoals Azure Cosmos DB en SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio en de .NET SDK

U kunt Visual Studio met de .NET SDK gebruiken voor het beheren van clusters en het ontwikkelen van big data toepassingen. U kunt andere Ide's gebruiken voor de volgende taken, maar voor beelden worden weer gegeven in Visual Studio.

Voor beelden van taken die u kunt uitvoeren met de .NET SDK in Visual Studio:
* [Azure HDInsight-SDK voor .net](/dotnet/api/overview/azure/hdinsight).
* [Voer Apache Hive Query's uit met behulp van de .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Door de gebruiker gedefinieerde C#-functies gebruiken met Apache Hive en Apache Pig-streaming op Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>IntelliJ idee en eclips IDE voor Spark-clusters

Zowel [IntelliJ-ideeën](https://www.jetbrains.com/idea/download) als de [eclips-IDE](https://www.eclipse.org/downloads/) kunnen worden gebruikt voor het volgende:
* Ontwikkel en verzend een scala Spark-toepassing op een HDInsight Spark-cluster.
* Toegang tot Spark-cluster resources.
* Ontwikkel een Scala Spark-toepassing en voer deze lokaal uit.

In deze artikelen ziet u hoe:
* IntelliJ idee: [maak Apache Spark-toepassingen met behulp van de Azure Toolkit for IntelliJ-invoeg toepassing en de scala-SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclips IDE of scala IDE voor eclips: [Apache Spark-toepassingen en de Azure-Toolkit voor eclipse maken](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notebooks op Spark voor gegevens wetenschappers

Apache Spark clusters in HDInsight zijn Apache Zeppelin-notebooks en kernels die kunnen worden gebruikt met Jupyter-notebooks.

* [Meer informatie over het gebruik van kernels op Apache Spark clusters met Jupyter-notebooks voor het testen van Spark-toepassingen](spark/apache-spark-zeppelin-notebook.md)
* [Meer informatie over het gebruik van Apache Zeppelin-notebooks op Apache Spark-clusters om Spark-taken uit te voeren](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Linux-hulpprogram ma's en-technologieën uitvoeren op Windows

Als u in een situatie komt waarin u een hulp programma of technologie wilt gebruiken die alleen beschikbaar is op Linux, kunt u de volgende opties overwegen:

* **Bash op Ubuntu in Windows 10** biedt een Linux-subsysteem in Windows. Met bash kunt u rechtstreeks Linux-hulpprogram ma's uitvoeren zonder dat u een speciale Linux-installatie hoeft te onderhouden. Zie [Installatiehandleiding voor Windows 10 voor Windows-subsysteem voor Linux](/windows/wsl/install-win10) voor installatiestappen.  Andere [UNIX-shells](https://www.gnu.org/software/bash/) kunnen ook worden gebruikt.
* **Docker voor Windows** biedt toegang tot veel Linux-hulpprogram ma's en kan rechtstreeks vanuit Windows worden uitgevoerd. U kunt docker bijvoorbeeld gebruiken om de Beeline-client voor Hive rechtstreeks vanuit Windows uit te voeren. U kunt docker ook gebruiken om een lokale Jupyter Notebook uit te voeren en op afstand verbinding te maken met Spark in HDInsight. [Aan de slag met docker voor Windows](https://docs.docker.com/docker-for-windows/)
* Met **[MobaXTerm](https://mobaxterm.mobatek.net/)** kunt u het bestands systeem van het cluster grafisch doorzoeken via een SSH-verbinding.

## <a name="cross-platform-tools"></a>Hulpprogram ma's voor meerdere platformen

De Azure-opdrachtregelinterface (CLI) is de platformoverschrijdende opdrachtregelervaring voor het beheren van Azure-resources.  Zie [Azure Command-Line interface (CLI)](/cli/azure/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u geen ervaring hebt met het werken in op Linux gebaseerde clusters, raadpleegt u de volgende artikelen:
* [Apache Hadoop, Apache Kafka, Apache Spark of andere clusters instellen](hdinsight-hadoop-provision-linux-clusters.md)
* [Tips voor HDInsight-clusters in Linux](hdinsight-hadoop-linux-information.md)