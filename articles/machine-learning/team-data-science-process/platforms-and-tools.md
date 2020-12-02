---
title: Platformen en hulpprogram ma's voor data Science-projecten-team data Science process
description: Specificatie en bespreekt de gegevens-en analyse bronnen die beschikbaar zijn voor ondernemingen die worden gestandardization op het team data Science process.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ba7b8af9b50b9173f5e2040bb8b623eeafdd538
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453846"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformen en hulpprogram ma's voor data Science-projecten

Micro soft biedt een volledig spectrum van analyse bronnen voor zowel Cloud-als on-premises platforms. Ze kunnen worden geïmplementeerd om de uitvoering van uw data Science-projecten efficiënt en schaalbaar te maken. Richt lijnen voor teams die data Science-projecten implementeren in een bijgehouden, gecontroleerde versie en samenwerkings wijze worden verstrekt door het [team data Science process](overview.md) (TDSP).  Zie [rollen en taken voor team data Science process](roles-tasks.md)voor een overzicht van de personeels rollen en de bijbehorende taken die worden verwerkt door een Data Science-team dat wordt gestandardization voor dit proces.

De analyse bronnen die beschikbaar zijn voor data Science teams die gebruikmaken van de TDSP zijn:

- Data Science Virtual Machines (zowel Windows-als Linux-CentOS)
- HDInsight Spark-clusters
- Azure Synapse Analytics
- Azure Data Lake
- HDInsight-Hive-clusters
- Azure File Storage
- SQL Server 2019 R-en python-Services
- Azure Databricks

In dit document beschrijven we de resources kort en bieden koppelingen naar de zelf studies en instructies die de TDSP teams hebben gepubliceerd. Hierin leert u hoe u deze stap voor stap kunt gebruiken om uw intelligente toepassingen te bouwen. Meer informatie over deze bronnen is beschikbaar op de bijbehorende product pagina's. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

De virtuele machine voor data technologie die door micro soft wordt aangeboden in Windows en Linux, bevat populaire hulpprogram ma's voor het model leren van gegevens wetenschappen en ontwikkelings activiteiten. Het bevat hulpprogram ma's zoals:

- Microsoft R Server Developer Edition 
- Anaconda Python-distributie
- Jupyter-notebooks voor python en R 
- Visual Studio Community Edition met python-en R-Hulpprogram Ma's in Windows/intereclips op Linux
- Power BI bureau blad voor Windows
- SQL Server 2016 Developer Edition op Windows/post gres in Linux

Het bevat ook **ml-en AI-hulpprogram ma's** zoals xgboost, Mxnet en Vowpal Wabbit.

Momenteel is DSVM beschikbaar in **Windows** -en **Linux CentOS** -besturings systemen. Kies de grootte van uw DSVM (aantal CPU-kernen en de hoeveelheid geheugen) op basis van de behoeften van de data Science-projecten die u wilt uitvoeren. 

Zie [micro soft data Science virtual machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) op de Azure Marketplace voor meer informatie over Windows-edities van DSVM. Zie [linux data Science virtual machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)voor de Linux-editie van de DSVM.

Zie [tien dingen die u kunt doen op de data Science-virtuele machine](../data-science-virtual-machine/vm-do-ten-things.md) voor meer informatie over het efficiënt uitvoeren van een aantal algemene data Science-taken op de DSVM


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark clusters

Apache Spark is een open-source framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. De Spark-verwerkings engine is gebouwd voor snelheid, gebruiks gemak en geavanceerde analyses. De berekenings mogelijkheden van Spark bieden een goede keuze voor iteratieve algoritmen in machine learning en voor het berekenen van grafieken. Spark is ook compatibel met Azure Blob Storage (WASB), zodat uw bestaande gegevens die zijn opgeslagen in azure eenvoudig kunnen worden verwerkt met behulp van Spark.

Wanneer u in HDInsight een Spark-cluster maakt, maakt u rekenresources in Azure, waarbij Spark is geïnstalleerd en geconfigureerd. Het duurt ongeveer 10 minuten om een Spark-cluster in HDInsight te maken. Sla de gegevens op die moeten worden verwerkt in Azure Blob-opslag. Zie voor meer informatie over het gebruik van Azure Blob Storage met een cluster, [HDFS-compatibele Azure Blob Storage gebruiken met Hadoop in HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Het TDSP-team van micro soft heeft twee end-to-end-procedures gepubliceerd over het gebruik van Azure HDInsight Spark-clusters om oplossingen voor data technologie te bouwen, één met python en de andere scala. Zie [overzicht: Apache Spark in HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md)voor meer informatie over Azure hdinsight **Spark-clusters**. Zie [overzicht van data Science met behulp van Spark in azure HDInsight](spark-overview.md)voor meer informatie over het bouwen van een Data Science-oplossing met behulp van **Python** in een Azure HDInsight Spark cluster. Voor meer informatie over het bouwen van een Data Science-oplossing met behulp van **scala** op een Azure HDInsight Spark-cluster, Zie [Data Wetenschappen met scala en Spark op Azure](scala-walkthrough.md). 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Met Azure Synapse Analytics kunt u eenvoudig en in enkele seconden reken bronnen schalen, zonder dat dit meer hoeft te zijn dan het inrichten of over-betalen. Het biedt ook de unieke optie om het gebruik van reken resources te onderbreken, waardoor u de vrijheid hebt om uw Cloud kosten beter te beheren. De mogelijkheid om schaal bare reken resources te implementeren, maakt het mogelijk om al uw gegevens in azure Synapse Analytics te brengen. De opslag kosten zijn mini maal en u kunt Compute alleen uitvoeren op de onderdelen van gegevens sets die u wilt analyseren. 

Zie de website van [Azure Synapse Analytics](https://azure.microsoft.com/services/sql-data-warehouse) voor meer informatie over Azure Synapse Analytics. Zie voor meer informatie over het bouwen van end-to-end geavanceerde analyse oplossingen met Azure Synapse Analytics [het proces voor team data Science in actie: Azure Synapse Analytics gebruiken](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake is een opslag plaats in de hele onderneming van elk type gegevens dat op één locatie wordt verzameld, vóór eventuele formele vereisten of het schema dat wordt opgelegd. Met deze flexibiliteit kan elk type gegevens in een Data Lake worden bewaard, ongeacht de grootte of structuur of hoe snel het wordt opgenomen. Organisaties kunnen vervolgens Hadoop of Advanced Analytics gebruiken om patronen te vinden in deze gegevens-meren. Gegevensmeren kan ook worden gebruikt als opslag plaats voor de voor bereiding van goedkope gegevens voordat de gegevens worden verstuurd en verplaatst naar een Data Warehouse.

Zie [Inleiding tot Azure data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/)voor meer informatie over Azure data Lake. Voor meer informatie over het bouwen van een schaal bare end-to-end oplossing voor gegevens wetenschap met Azure Data Lake raadpleegt u [Scalable data Science in azure data Lake: een end-to-end-procedure](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive-clusters (Hadoop)

Apache Hive is een Data Warehouse systeem voor Hadoop, waarmee gegevens overzichten, query's en de analyse van gegevens worden ingeschakeld met HiveQL, een query taal die vergelijkbaar is met SQL. Hive kan worden gebruikt om uw gegevens interactief te verkennen of om herbruikbare batch verwerkings taken te maken.

Met hive kunt u de structuur projecteren op grotendeels ongestructureerde gegevens. Nadat u de structuur hebt gedefinieerd, kunt u Hive gebruiken om de gegevens in een Hadoop-cluster op te vragen zonder dat u dit hoeft te doen, of zelfs met een Java-of MapReduce. Met HiveQL (de Hive-query taal) kunt u query's schrijven met instructies die vergelijkbaar zijn met T-SQL.

Voor gegevens wetenschappers kan Hive python User-Defined functions (UDFs) uitvoeren in Hive-query's om records te verwerken. Deze mogelijkheid breidt de mogelijkheden van Hive-query's in gegevens analyse aanzienlijk uit. Met name de IT-afdeling maakt het mogelijk dat gegevens wetenschappers schaal bare functies gebruiken in talen die ze voornamelijk kennen: de SQL-achtige HiveQL en python. 

Zie [Hive en HiveQL gebruiken met Hadoop in HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md)voor meer informatie over Azure HDInsight-Hive-clusters. Zie voor meer informatie over het bouwen van een schaal bare end-to-end data Science-oplossing met Azure HDInsight Hive-clusters [het team data Science process in actie: HDInsight Hadoop-clusters gebruiken](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage is een service die bestands shares in de Cloud aanbiedt met behulp van het standaard SMB-protocol (Server Message Block). Zowel SMB 2.1 als SMB 3.0 wordt ondersteund. Met Azure File Storage kunt u oudere toepassingen die afhankelijk zijn van bestandsshares, snel en zonder kostbare regeneraties naar Azure migreren. Toepassingen die worden uitgevoerd op virtuele machines of in cloudservices van Azure, of op on-premises clients, kunnen een bestandsshare in de cloud koppelen, op dezelfde manier zoals een bureaubladtoepassing een gewone SMB-share koppelt. Daarna kan een willekeurig aantal toepassingsonderdelen de File Storage-share tegelijkertijd koppelen en gebruiken.

Vooral handig voor data Science-projecten is de mogelijkheid om een Azure-bestands archief te maken als de plaats om project gegevens te delen met uw projectteam leden. Elk daarvan heeft toegang tot dezelfde kopie van de gegevens in de Azure file storage. Ze kunnen ook deze bestands opslag gebruiken om functie sets te delen die zijn gegenereerd tijdens de uitvoering van het project. Als het project een client afspraak is, kunnen uw clients een Azure-bestands opslag maken onder hun eigen Azure-abonnement om de project gegevens en-functies met u te delen. Op deze manier heeft de client volledig beheer over de gegevensassets van het project. Zie aan de [slag met Azure file storage in Windows](../../storage/files/storage-dotnet-how-to-use-files.md) en [Azure File Storage gebruiken met Linux](../../storage/files/storage-how-to-use-files-linux.md)voor meer informatie over Azure file storage.


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R-en python-Services

R Services (in-data base) biedt een platform voor het ontwikkelen en implementeren van intelligente toepassingen die nieuwe inzichten kunnen opsporen. U kunt de uitgebreide en krachtige taal van het R-pakket gebruiken, inclusief de vele pakketten van de R-Community, om modellen te maken en voor spellingen te genereren op basis van uw SQL Server gegevens. Omdat R Services (in-data base) de R-taal integreert met SQL Server, behouden de analyses zich dicht bij de gegevens, waardoor de kosten en beveiligings Risico's voor het verplaatsen van gegevens worden geëlimineerd.

R Services (in-data base) biedt ondersteuning voor de open source R-taal met een uitgebreide set SQL Server-hulpprogram ma's en-technologieën. Ze bieden superieure prestaties, beveiliging, betrouw baarheid en beheersbaarheid. U kunt R-oplossingen implementeren met behulp van handige en vertrouwde hulpprogram ma's. Uw productie toepassingen kunnen de R runtime aanroepen en de voor spellingen en visuele elementen ophalen met behulp van Transact-SQL. U kunt ook de Schalende bibliotheken gebruiken om de schaal en prestaties van uw R-oplossingen te verbeteren. Zie [SQL Server R Services](/sql/advanced-analytics/r/sql-server-r-services)voor meer informatie.

Het TDSP-team van micro soft heeft twee end-to-end-procedures gepubliceerd die laten zien hoe u oplossingen voor data technologie bouwt in SQL Server 2016 R Services: één voor R-programmeurs en één voor SQL-ontwikkel aars. Voor **R-programmeurs** raadpleegt [u end-to-end-scenario voor data](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)technologie. Zie [in-data base Advanced Analytics voor SQL-ontwikkel aars (zelf studie)](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)voor **SQL-ontwikkel aars**.


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Bijlage: Hulpprogram Ma's voor het instellen van data Science-projecten

### <a name="install-git-credential-manager-on-windows"></a>Git Credential Manager installeren in Windows

Als u de TDSP in **Windows** volgt, moet u de **Git CREDENTIAL Manager (GCM)** installeren om te communiceren met de Git-opslag plaatsen. Als u GCM wilt installeren, moet u eerst **chocolaty** installeren. Als u chocolaty en GCM wilt installeren, voert u de volgende opdrachten uit in Windows Power shell als **beheerder**:  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>Git installeren op Linux-machines (CentOS)

Voer de volgende bash-opdracht uit om Git op Linux-machines (CentOS) te installeren:

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Open bare SSH-sleutel op Linux-machines (CentOS) genereren

Als u Linux-machines (CentOS) gebruikt om de Git-opdrachten uit te voeren, moet u de open bare SSH-sleutel van uw computer toevoegen aan de Azure DevOps-Services, zodat deze computer wordt herkend door de Azure DevOps-Services. Eerst moet u een open bare SSH-sleutel genereren en de sleutel toevoegen aan open bare SSH-sleutels op de pagina beveiligings instellingen van Azure DevOps Services. 

1. Voer de volgende twee opdrachten uit om de SSH-sleutel te genereren: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Opdrachten voor het genereren van de SSH-sleutel](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Kopieer de volledige SSH-sleutel *, inclusief ssh-rsa*. 
1. Meld u aan bij uw Azure DevOps-Services. 
1. Klik op **<uw \> naam** in de rechter bovenhoek van de pagina en klik op **beveiliging**. 
    
   ![Klik op uw naam en klik vervolgens op beveiliging](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klik op **open bare SSH-sleutels** en klik op **+ toevoegen**. 

   ![Klik op open bare SSH-sleutels en klik vervolgens op + toevoegen](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Plak de SSH-sleutel die is gekopieerd naar het tekstvak en sla het op.


## <a name="next-steps"></a>Volgende stappen

Volledige end-to-end-scenario's waarin alle stappen in het proces voor **specifieke scenario's** worden getoond, worden ook gegeven. Ze worden weer gegeven en gekoppeld aan miniatuur beschrijvingen in het onderwerp [voorbeeld scenario's](walkthroughs.md) . Ze illustreren het combi neren van Cloud, on-premises hulpprogram ma's en services in een werk stroom of pijp lijn om een intelligente toepassing te maken. 

Voor voor beelden die laten zien hoe u de stappen in het team data Science process uitvoert met behulp van Azure Machine Learning Studio (klassiek), raadpleegt u het leer traject [van Azure ml](./index.yml) .