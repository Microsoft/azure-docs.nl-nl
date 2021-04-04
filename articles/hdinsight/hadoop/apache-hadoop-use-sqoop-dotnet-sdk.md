---
title: Apache Sqoop-taken uitvoeren met behulp van .NET en HDInsight-Azure
description: Meer informatie over het gebruik van de HDInsight .NET SDK voor het uitvoeren van Apache Sqoop import en export tussen een Apache Hadoop cluster en een Azure SQL Database.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: a06326f5b4cf34a06475c10e368c6dcbb49ca9e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928338"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Apache Sqoop-taken uitvoeren met behulp van .NET SDK voor Apache Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informatie over het gebruik van de Azure HDInsight .NET SDK voor het uitvoeren van Apache Sqoop-taken in HDInsight voor het importeren en exporteren tussen een HDInsight-cluster en een Azure SQL Database-of SQL Server-Data Base.

## <a name="prerequisites"></a>Vereisten

* Volt ooien van de [instelling test omgeving](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) [voor het gebruik van Apache Sqoop met Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Vertrouwd met Sqoop. Zie [Sqoop User Guide (Engelstalig](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)) voor meer informatie.

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Sqoop in HDInsight-clusters gebruiken met de .NET SDK

De HDInsight .NET SDK biedt .NET-client Bibliotheken, zodat u gemakkelijker met HDInsight-clusters van .NET kunt werken. In deze sectie maakt u een C#-console toepassing voor het exporteren `hivesampletable` van de Azure SQL database tabel die u hebt gemaakt op basis van de vereisten.

## <a name="set-up"></a>Instellen

1. Start Visual Studio en maak een C#-console toepassing.

1. Ga naar de **hulpprogram ma's**  >  **NuGet package manager**  >  **Package Manager console** en voer de volgende opdracht uit:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop exporteren

Van Hive naar SQL Server.  In dit voor beeld worden gegevens uit de Hive- `hivesampletable` tabel naar de `mobiledata` tabel in SQL database geëxporteerd.

1. Gebruik de volgende code in het bestand Program. cs. Bewerk de code voor het instellen van de waarden voor `ExistingClusterName` en `ExistingClusterPassword` .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Selecteer de toets **F5** om het programma uit te voeren.

## <a name="sqoop-import"></a>Sqoop importeren

Van SQL Server naar Azure Storage. Dit voor beeld is afhankelijk van de hierboven uitgevoerde export.  In dit voor beeld worden gegevens uit de `mobiledata` tabel in SQL database geïmporteerd naar de `wasb:///tutorials/usesqoop/importeddata` map in het standaard opslag account van het cluster.

1. Vervang de bovenstaande code in het `//sqoop start //sqoop end` blok door de volgende code:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Selecteer de toets **F5** om het programma uit te voeren.

## <a name="limitations"></a>Beperkingen

HDInsight op basis van Linux biedt de volgende beperkingen:

* Bulk export: de Sqoop-connector die wordt gebruikt om gegevens te exporteren naar Microsoft SQL Server of Azure SQL Database biedt momenteel geen ondersteuning voor bulksgewijs invoegen.

* Batching: door gebruik te maken van de `-batch` switch voert Sqoop meerdere inserts uit in plaats van de bewerking insert.

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u Sqoop kunt gebruiken. Raadpleeg voor meer informatie:

* [Apache Oozie gebruiken met HDInsight](../hdinsight-use-oozie-linux-mac.md): gebruik de actie Sqoop in een Oozie-werk stroom.
* [Gegevens uploaden naar hdinsight](../hdinsight-upload-data.md): andere methoden voor het uploaden van gegevens naar Hdinsight of Azure Blob Storage zoeken.
