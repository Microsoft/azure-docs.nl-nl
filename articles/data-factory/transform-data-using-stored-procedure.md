---
title: Gegevens transformeren met behulp van de opgeslagen procedure-activiteit
description: Hierin wordt uitgelegd hoe u SQL Server opgeslagen procedure activiteit gebruikt om een opgeslagen procedure in een Azure SQL Database/Data Warehouse aan te roepen vanuit een Data Factory-pijp lijn.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: b9ba2f9de82522d4348fa341ad0b41d43c3eebcc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375643"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Gegevens transformeren met behulp van de SQL Server opgeslagen procedure activiteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-stored-proc-activity.md)
> * [Huidige versie](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U gebruikt gegevens transformatie activiteiten in een Data Factory- [pijp lijn](concepts-pipelines-activities.md) om onbewerkte gegevens te transformeren en te verwerken in voor spellingen en inzichten. De opgeslagen procedure activiteit is een van de transformatie activiteiten die Data Factory ondersteunt. In dit artikel vindt u informatie over het [gegevens](transform-data.md) transformatie-artikel, dat een algemeen overzicht geeft van de gegevens omzetting en de ondersteunde transformatie activiteiten in Data Factory.

> [!NOTE]
> Als u niet bekend bent met Azure Data Factory, leest u [Inleiding tot Azure Data Factory](introduction.md) en voert u de zelf studie uit: [zelf studie: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat dit artikel wordt gelezen. 

U kunt de opgeslagen procedure activiteit gebruiken voor het aanroepen van een opgeslagen procedure in een van de volgende gegevens archieven in uw onderneming of op een virtuele machine van Azure (VM): 

- Azure SQL Database
- Azure Synapse Analytics
- SQL Server-Data Base.  Als u SQL Server gebruikt, installeert u zelf-hostende Integration runtime op dezelfde computer waarop de data base wordt gehost of op een afzonderlijke computer die toegang heeft tot de data base. Self-Hosted Integration runtime is een onderdeel dat gegevens bronnen on-premises/op Azure VM met Cloud Services op een veilige en beheerde manier verbindt. Zie [zelf-hostend Integration runtime](create-self-hosted-integration-runtime.md) -artikel voor meer informatie.

> [!IMPORTANT]
> Bij het kopiëren van gegevens naar Azure SQL Database of SQL Server, kunt u de **SqlSink** in Kopieer activiteit configureren om een opgeslagen procedure aan te roepen met behulp van de eigenschap **sqlWriterStoredProcedureName** . Zie voor meer informatie over de eigenschap de volgende connector artikelen: [Azure SQL database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Het aanroepen van een opgeslagen procedure bij het kopiëren van gegevens naar een Azure Synapse-analyse met behulp van een Kopieer activiteit wordt niet ondersteund. Maar u kunt de opgeslagen procedure-activiteit gebruiken om een opgeslagen procedure in azure Synapse Analytics aan te roepen. 
>
> Bij het kopiëren van gegevens uit Azure SQL Database of SQL Server of Azure Synapse Analytics kunt u **SqlSource** configureren in de Kopieer activiteit om een opgeslagen procedure aan te roepen voor het lezen van gegevens uit de bron database met behulp van de eigenschap **sqlReaderStoredProcedureName** . Zie voor meer informatie de volgende connector artikelen: [Azure SQL database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Syntaxis Details
Dit is de JSON-indeling voor het definiëren van een opgeslagen procedure activiteit:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

In de volgende tabel worden deze JSON-eigenschappen beschreven:

| Eigenschap                  | Beschrijving                              | Vereist |
| ------------------------- | ---------------------------------------- | -------- |
| naam                      | Naam van de activiteit                     | Ja      |
| beschrijving               | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt | Nee       |
| type                      | Voor de opgeslagen procedure activiteit is het type activiteit **SqlServerStoredProcedure** | Ja      |
| linkedServiceName         | Verwijzing naar de **Azure SQL database** of **Azure Synapse Analytics** of **SQL Server** geregistreerd als een gekoppelde service in Data Factory. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service. | Ja      |
| storedProcedureName       | Geef de naam op van de opgeslagen procedure die moet worden aangeroepen. | Ja      |
| storedProcedureParameters | Geef de waarden op voor opgeslagen procedure parameters. Gebruiken `"param1": { "value": "param1Value","type":"param1Type" }` voor het door geven van parameter waarden en het type ervan dat door de gegevens bron wordt ondersteund. Als u null wilt door geven voor een para meter, gebruikt u `"param1": { "value": null }` (alle kleine letters). | Nee       |

## <a name="parameter-data-type-mapping"></a>Toewijzing van parameter gegevens type
Het gegevens type dat u opgeeft voor de para meter is het Azure Data Factory type dat wordt toegewezen aan het gegevens type in de gegevens bron die u gebruikt. U kunt de gegevens type toewijzingen voor uw gegevens bron vinden in het gebied connectors. Enkele voor beelden zijn

| Gegevensbron          | Toewijzing van gegevens type |
| ---------------------|-------------------|
| Azure Synapse Analytics | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |




## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Aangepaste .NET-activiteit](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio-batchuitvoeringsactiviteit (klassiek)](transform-data-using-machine-learning.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)
