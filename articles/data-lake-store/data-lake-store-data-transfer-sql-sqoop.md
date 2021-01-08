---
title: Gegevens kopiëren tussen Data Lake Storage Gen1 en Azure SQL-Sqoop | Microsoft Docs
description: Gebruik Sqoop om gegevens te kopiëren tussen Azure SQL Database en Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "92109184"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Gegevens kopiëren tussen Data Lake Storage Gen1 en Azure SQL Database met behulp van Sqoop

Meer informatie over het gebruik van Apache Sqoop voor het importeren en exporteren van gegevens tussen Azure SQL Database en Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Wat is Sqoop?

Big Data-toepassingen zijn een natuurlijke keuze voor het verwerken van ongestructureerde en semi-gestructureerde gegevens, zoals Logboeken en bestanden. Het is echter ook mogelijk dat u gestructureerde gegevens moet verwerken die zijn opgeslagen in relationele data bases.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) is een hulp programma dat is ontworpen om gegevens over te dragen tussen relationele data bases en een Big data opslag plaats, zoals data Lake Storage gen1. U kunt deze gebruiken voor het importeren van gegevens uit een relationele Database Management System (RDBMS), zoals Azure SQL Database in Data Lake Storage Gen1. U kunt de gegevens vervolgens transformeren en analyseren met behulp van big data workloads en vervolgens de gegevens weer exporteren naar een RDBMS. In dit artikel gebruikt u een data base in Azure SQL Database als uw relationele data base die u wilt importeren/exporteren.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van het account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage gen1-account. Zie [een HDInsight-cluster met data Lake Storage gen1 maken](data-lake-store-hdinsight-hadoop-use-portal.md). In dit artikel wordt ervan uitgegaan dat u een HDInsight Linux-cluster hebt met Data Lake Storage Gen1 toegang.
* **Azure SQL-database**. Zie [een Data Base maken in Azure SQL database](../azure-sql/database/single-database-create-quickstart.md) voor instructies over het maken van een data base in Azure SQL database.

## <a name="create-sample-tables-in-the-database"></a>Voorbeeld tabellen maken in de data base

1. Maak twee voorbeeld tabellen in de data base om te starten. Gebruik [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) of Visual Studio om verbinding te maken met de data base en voer vervolgens de volgende query's uit.

    **Maken Tabel1**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **Tabel2 maken**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. Voer de volgende opdracht uit om een aantal voorbeeld gegevens toe te voegen aan **Tabel1**. Laat de instelling **tabel2** empty leeg. Later importeert u gegevens van **Tabel1** in data Lake Storage gen1. Vervolgens exporteert u gegevens van Data Lake Storage Gen1 naar **tabel2**.

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Sqoop gebruiken vanuit een HDInsight-cluster met toegang tot Data Lake Storage Gen1

De Sqoop-pakketten van An HDInsight cluster zijn al beschikbaar. Als u het HDInsight-cluster hebt geconfigureerd voor het gebruik van Data Lake Storage Gen1 als extra opslag, kunt u Sqoop (zonder configuratie wijzigingen) gebruiken om gegevens te importeren/exporteren tussen een relationele data base, zoals Azure SQL Database, en een Data Lake Storage Gen1-account.

1. Voor dit artikel wordt ervan uitgegaan dat u een Linux-cluster hebt gemaakt, zodat u SSH moet gebruiken om verbinding te maken met het cluster. Zie [verbinding maken met een HDInsight-cluster op basis van Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Controleer of u toegang hebt tot het Data Lake Storage Gen1-account vanuit het cluster. Voer de volgende opdracht uit vanaf de SSH-prompt:

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   Met deze opdracht geeft u een lijst van bestanden/mappen in het Data Lake Storage Gen1-account.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Gegevens importeren van Azure SQL Database naar Data Lake Storage Gen1

1. Navigeer naar de map waar Sqoop-pakketten beschikbaar zijn. Normaal gesp roken is deze locatie `/usr/hdp/<version>/sqoop/bin` .

1. Importeer de gegevens van **Tabel1** in het data Lake Storage gen1-account. Gebruik de volgende syntaxis:

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   De naam van de server waarop de data base wordt uitgevoerd, vertegenwoordigt de tijdelijke aanduiding voor de **SQL-Data Base-Server naam** . **SQL-data base-naam** tijdelijke aanduiding staat voor de werkelijke database naam.

   Bijvoorbeeld:

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. Controleer of de gegevens zijn overgedragen naar het Data Lake Storage Gen1-account. Voer de volgende opdracht uit:

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   De volgende uitvoer wordt weergegeven.

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   Elk **deel-m-** _ bestand komt overeen met een rij in de bron tabel, _ *Tabel1 * *. U kunt de inhoud van de deel-m-bestanden weer geven* om te controleren.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Gegevens exporteren van Data Lake Storage Gen1 naar Azure SQL Database

1. Exporteer de gegevens uit het Data Lake Storage Gen1-account naar de lege tabel, **tabel2**, in de Azure SQL database. Gebruik de volgende syntaxis.

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   Bijvoorbeeld:

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. Controleer of de gegevens zijn geüpload naar de tabel SQL Database. Gebruik [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) of Visual Studio om verbinding te maken met de Azure SQL database en voer vervolgens de volgende query uit.

    ```tsql
    SELECT * FROM TABLE2
    ```

   Deze opdracht moet de volgende uitvoer hebben.

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Prestatie overwegingen bij het gebruik van Sqoop

Zie het [blog bericht](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)over de Sqoop-prestaties voor informatie over het afstemmen van de prestaties van uw Sqoop-taak om gegevens te kopiëren naar Data Lake Storage gen1.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)