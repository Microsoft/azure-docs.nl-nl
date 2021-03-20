---
title: Ondersteunde gegevens platforms
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de ondersteunde gegevens platforms en hulpprogram ma's voor Azure Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 927e945a0d045abcd1caa2951dbd484224b2f425
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519538"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Gegevens platforms die worden ondersteund op de Data Science Virtual Machine

Met een Data Science Virtual Machine (DSVM) kunt u uw analyses maken op basis van een breed scala aan gegevens platforms. Naast interfaces voor externe gegevens platforms biedt de DSVM een lokaal exemplaar voor snelle ontwikkeling en prototypen.

De volgende hulpprogram ma's voor gegevens platforms worden ondersteund op de DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   | Een lokale relationele-data base-instantie      |
| Ondersteunde DSVM-edities      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Typische toepassingen      | <ul><li>Snelle ontwikkeling lokaal met kleinere gegevensset</li><li>In-data base-R uitvoeren</li></ul> |
| Koppelingen naar voor beelden      | <ul><li>Een klein voor beeld van een nieuwe gegevensset in Utrecht City wordt geladen in de SQL database:<br/>  `nyctaxi`</li><li>Jupyter-voor beeld met Microsoft Machine Learning Server en in-data base Analytics vindt u op:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| Gerelateerde hulpprogram ma's op de DSVM       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC-Stuur Programma's</li><li>pyodbc, RODBC</li><li>Apache Drill</li></ul> |

> [!NOTE]
> SQL Server Developer Edition kan alleen worden gebruikt voor ontwikkelings-en test doeleinden. U hebt een licentie of een van de SQL Server Vm's nodig om deze in productie uit te voeren.


### <a name="setup"></a>Instellen

De database server is al vooraf geconfigureerd en de Windows-services die betrekking hebben op SQL Server (zoals `SQL Server (MSSQLSERVER)` ) zijn ingesteld om automatisch te worden uitgevoerd. Voor de enige hand matige stap moet u in-data base Analytics inschakelen met behulp van Microsoft Machine Learning Server. U kunt Analytics inschakelen door de volgende opdracht uit te voeren als een eenmalige actie in SQL Server Management Studio (SSMS). Voer deze opdracht uit nadat u zich hebt aangemeld als machine beheerder, open een nieuwe query in SSMS en zorg ervoor dat de geselecteerde data base `master` :

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(Vervang% ComputerName% door de naam van uw virtuele machine.)

Als u SQL Server Management Studio wilt uitvoeren, kunt u zoeken op ' SQL Server Management Studio ' in de lijst met Program ma's, of Windows Search gebruiken om het bestand te zoeken en uit te voeren. Wanneer u om referenties wordt gevraagd, selecteert u **Windows-verificatie** en gebruikt u de computer naam of ```localhost``` in het veld **SQL Server naam** .

### <a name="how-to-use-and-run-it"></a>Hoe gebruiken en uitvoeren

Standaard wordt de database server met het standaard exemplaar van de data base automatisch uitgevoerd. U kunt hulpprogram ma's zoals SQL Server Management Studio op de virtuele machine gebruiken om lokaal toegang te krijgen tot de SQL Server-Data Base. Lokale beheerders accounts hebben beheerders toegang tot de data base.

Daarnaast wordt de DSVM geleverd met ODBC-en JDBC-Stuur Programma's om te praten met SQL Server, Azure SQL-data bases en Azure Synapse Analytics van toepassingen die in meerdere talen zijn geschreven, waaronder python en Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM? 

 SQL Server wordt op de normale manier geïnstalleerd. U kunt het vinden op `C:\Program Files\Microsoft SQL Server` . De Machine Learning Server-instantie in de data base is gevonden op `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` . De DSVM heeft ook een afzonderlijke zelfstandige Machine Learning Server instantie, die wordt geïnstalleerd op `C:\Program Files\Microsoft\R Server\R_SERVER` . Deze twee Machine Learning Server exemplaren delen geen bibliotheken.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2. x (zelfstandige versie)

| Categorie | Waarde |
| ------------- | ------------- |
| Wat is het?   | Een zelfstandig exemplaar (één knoop punt in het proces) van het populaire Apache Spark platform; een systeem voor snelle, grootschalige gegevens verwerking en machine learning     |
| Ondersteunde DSVM-edities      | Linux     |
| Typische toepassingen      | <ul><li>Snelle ontwikkeling van Spark/PySpark-toepassingen lokaal met een kleinere gegevensset en een latere implementatie op grote Spark-clusters, zoals Azure HDInsight</li><li>Microsoft Machine Learning Server Spark-context testen</li><li>Gebruik SparkML of de open-source [MMLSpark](https://github.com/Azure/mmlspark) -bibliotheek van micro soft om ml-toepassingen te bouwen</li></ul> |
| Koppelingen naar voor beelden      |    Voor beeld van Jupyter:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (Spark-context):/dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| Gerelateerde hulpprogram ma's op de DSVM       | <ul><li>PySpark, scala</li><li>Jupyter (Spark/PySpark-kernels)</li><li>Microsoft Machine Learning Server, Spark, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>Gebruik
U kunt Spark-taken verzenden op de opdracht regel door de of-opdracht uit te voeren `spark-submit` `pyspark` . U kunt ook een Jupyter-notebook maken door een nieuw notitie blok te maken met de Spark-kernel.

U kunt Spark van R gebruiken met behulp van bibliotheken als Spark, Sparklyr en Microsoft Machine Learning Server, die beschikbaar zijn in de DSVM. Zie aanwijzers naar voor beelden in de voor gaande tabel.

### <a name="setup"></a>Instellen
Voordat u in een Spark-context in Microsoft Machine Learning Server op Ubuntu Linux DSVM-editie uitvoert, moet u een installatie stap van één keer volt ooien om een lokaal Hadoop-en garen-exemplaar van één knoop punt in te scha kelen. Hadoop-services worden standaard geïnstalleerd, maar uitgeschakeld op de DSVM. Als u deze wilt inschakelen, voert u de volgende opdrachten uit als hoofd de eerste keer:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

U kunt de Hadoop-gerelateerde services stoppen wanneer u deze niet meer nodig hebt door uit te voeren ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Een voor beeld dat laat zien hoe u MEVR kunt ontwikkelen en testen in een externe Spark-context (dit is de zelfstandige Spark-instantie op de DSVM), wordt beschikbaar gesteld in de `/dsvm/samples/MRS` Directory.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM? 
|Platform|Installatie locatie ($SPARK _HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotheken om toegang te krijgen tot gegevens van Azure Blob-opslag of Azure Data Lake Storage, met behulp van de micro soft MMLSpark machine learning-bibliotheken, zijn vooraf geïnstalleerd in $SPARK _HOME/Jars. Deze potten worden automatisch geladen wanneer Spark wordt gestart. Standaard gebruikt Spark gegevens op de lokale schijf. 

Voor de Spark-instantie in de DSVM om toegang te krijgen tot gegevens die zijn opgeslagen in Blob Storage of Azure Data Lake Storage, moet u het bestand maken en configureren op `core-site.xml` basis van de sjabloon die is gevonden in $SPARK _HOME/conf/core-site.xml. Temp late. U moet ook de juiste referenties hebben voor toegang tot de Blob-opslag en de Azure Data Lake Storage. (Houd er rekening mee dat de sjabloon bestanden tijdelijke aanduidingen gebruiken voor Blob Storage en Azure Data Lake Storage configuraties.)

Zie [verificatie met Azure data Lake Storage gen1](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)voor meer gedetailleerde informatie over het maken van Azure data Lake Storage-service referenties. Nadat de referenties voor Blob Storage of Azure Data Lake Storage in het core-site.xml bestand zijn ingevoerd, kunt u verwijzen naar de gegevens die in deze bronnen zijn opgeslagen via het URI-voor voegsel van wasb://of adl://.