---
title: Gegevens verkennen in SQL Server virtuele machine-team data Science process
description: Informatie over het verkennen van gegevens die zijn opgeslagen in een SQL Server VM in azure met behulp van SQL of een programmeer taal zoals python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86085647"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Gegevens in virtuele SQL Server-machine verkennen in Azure

In dit artikel wordt beschreven hoe u gegevens kunt verkennen die zijn opgeslagen in een SQL Server VM in Azure. Gebruik SQL of python voor het onderzoeken van de gegevens.

Deze taak is een stap in het [team data Science process](overview.md).

> [!NOTE]
> In de SQL-voorbeeld instructies in dit document wordt ervan uitgegaan dat de gegevens zich in SQL Server bevindt. Als dat niet het geval is, raadpleegt u de Cloud data Science process-toewijzing voor informatie over het verplaatsen van uw gegevens naar SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>SQL-gegevens verkennen met SQL-scripts
Hier volgen enkele voor beelden van SQL-scripts die kunnen worden gebruikt voor het verkennen van gegevens archieven in SQL Server.

1. Het aantal observaties per dag ophalen
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. De niveaus in een kolom categorische ophalen
   
    `select  distinct <column_name> from <databasename>`
3. Het aantal niveaus in combi natie van twee categorische kolommen ophalen 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. De distributie voor numerieke kolommen ophalen
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Voor een praktijk voorbeeld kunt u de [NYC taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) gebruiken en verwijzen naar de IPNB getiteld [NYC data wrangling met behulp van IPython notebook en SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) voor een end-to-end-instructies.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>SQL-gegevens verkennen met python
Met python voor het verkennen van gegevens en het genereren van functies als de gegevens zich in SQL Server bevinden, is vergelijkbaar met het verwerken van gegevens in azure-blobs met behulp van python, zoals beschreven in [Azure Blob-gegevens in uw data Science-omgeving verwerken](data-blob.md). Laad de gegevens uit de data base in een Panda data frame en kan vervolgens verder worden verwerkt. We documenteren het proces van het maken van een verbinding met de data base en het laden van de gegevens in de data frame in deze sectie.

De volgende connection string indeling kan worden gebruikt om vanuit python verbinding te maken met een SQL Server-Data Base met behulp van pyodbc (Vervang servername, dbname, username en password door uw specifieke waarden):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

De [bibliotheek Pandas](https://pandas.pydata.org/) in python biedt een uitgebreide set gegevens structuren en hulpprogram ma's voor gegevens analyse voor het bewerken van gegevens voor python-programmering. Met de volgende code worden de resultaten van een SQL Server data base in een Pandas-gegevens frame gelezen:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

U kunt nu met de Pandas data frame werken, zoals wordt behandeld in het onderwerp [Azure Blob-gegevens in uw data Science-omgeving verwerken](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Het team data Science-proces in actie voorbeeld
Voor een end-to-end-scenario voor beeld van het Cortana Analytics-proces met behulp van een open bare gegevensset raadpleegt u [het team data Science process in actie: using SQL Server](sql-walkthrough.md).

