---
title: Voorbeeld gegevens in SQL Server op Azure-team data Science process
description: Voorbeeld gegevens die zijn opgeslagen in SQL Server op Azure met behulp van SQL of de python-programmeer taal, vervolgens naar Azure Machine Learning verplaatsen.
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321861"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Voorbeeldgegevens in SQL Server op Azure

In dit artikel wordt beschreven hoe u gegevens kunt opslaan in SQL Server op Azure met behulp van SQL of de python-programmeer taal. Ook wordt uitgelegd hoe u voorbeeld gegevens verplaatst naar Azure Machine Learning door deze op te slaan in een bestand, naar een Azure-Blob te uploaden en deze vervolgens te lezen in Azure Machine Learning Studio.

In de python-steek proef wordt gebruikgemaakt van de [pyodbc](https://code.google.com/p/pyodbc/) ODBC-bibliotheek om verbinding te maken met SQL Server op Azure en de [Panda](https://pandas.pydata.org/) -bibliotheek om de steek proeven te doen.

> [!NOTE]
> In de voor beeld-SQL-code in dit document wordt ervan uitgegaan dat de gegevens zich in een SQL Server in azure bevindt. Als dat niet het geval is, raadpleegt u [gegevens verplaatsen naar SQL Server in azure](move-sql-server-virtual-machine.md) article voor instructies over het verplaatsen van uw gegevens naar SQL Server in Azure.
> 
> 

**Waarom een voor beeld van uw gegevens?**
Als de gegevensset die u wilt analyseren groot is, is het doorgaans een goed idee om de gegevens te verlagen om deze te verminderen tot een kleinere, maar representatieve en meer beheersbare grootte. Bemonstering vereenvoudigt het leren van gegevens, het verkennen en functie-engineering. De rol van het [team data Science process (TDSP)](./index.yml) is om snel een prototype van de functies voor gegevens verwerking en machine learning modellen mogelijk te maken.

Deze steekproef taak is een stap in het [team data Science process (TDSP)](./index.yml).

## <a name="using-sql"></a><a name="SQL"></a>SQL gebruiken
In deze sectie worden verschillende methoden beschreven voor het gebruik van SQL om een wille keurige steek proef uit te voeren op de gegevens in de data base. Kies een methode op basis van uw gegevens grootte en de distributie ervan.

De volgende twee items laten zien hoe u `newid` in SQL Server kunt gebruiken om de steek proeven uit te voeren. De methode die u kiest, is afhankelijk van hoe wille keurig het voor beeld moet worden (pk_id in de volgende voorbeeld code wordt aangenomen dat het een automatisch gegenereerde primaire sleutel is).

1. Minder strikt wille keurig voor beeld

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Meer wille keurig voor beeld 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

Tablesample kan worden gebruikt om de gegevens ook te bemonsteren. Deze optie is mogelijk een betere benadering als de grootte van uw gegevens groot is (ervan uitgaande dat de gegevens op verschillende pagina's niet worden gecorreleerd) en de query in een redelijke periode wordt voltooid.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> U kunt functies van deze voorbeeld gegevens verkennen en genereren door deze op te slaan in een nieuwe tabel
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
U kunt de bovenstaande voorbeeld query's rechtstreeks in de module Azure Machine Learning [gegevens importeren][import-data] gebruiken om de gegevens op de vlucht te verlagen en deze naar een Azure machine learning experiment te brengen. Een scherm opname van het gebruik van de Lees module om de voorbeeld gegevens te lezen, worden hier weer gegeven:

![Reader SQL][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>De python-programmeer taal gebruiken
In deze sectie wordt gedemonstreerd hoe u de [pyodbc-bibliotheek](https://code.google.com/p/pyodbc/) gebruikt om een ODBC-verbinding te maken met een SQL server-data base in python. De data base connection string is als volgt: (Vervang servername, dbname, username en password door uw configuratie):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

De bibliotheek [Pandas](https://pandas.pydata.org/) in python biedt een uitgebreide set gegevens structuren en hulpprogram ma's voor gegevens analyse voor het bewerken van gegevens voor python-programmering. Met de volgende code wordt een voor beeld van 0,1% gelezen van de gegevens uit een tabel in Azure SQL Database naar een Panda-gegevens:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

U kunt nu met de voorbeeld gegevens in het gegevens frame van Pandas werken. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Verbinding maken met Azure Machine Learning
U kunt de volgende voorbeeld code gebruiken om de niet-bemonsterde gegevens in een bestand op te slaan en te uploaden naar een Azure-Blob. De gegevens in de BLOB kunnen rechtstreeks in een Azure Machine Learning experiment worden gelezen met behulp van de module [gegevens importeren][import-data] . De stappen zijn als volgt: 

1. Het gegevens frame van Pandas naar een lokaal bestand schrijven

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Lokaal bestand uploaden naar Azure Blob

    ```python
    from azure.storage import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```

3. Gegevens lezen uit een Azure-Blob met behulp van Azure Machine Learning [gegevens module importeren][import-data] , zoals wordt weer gegeven in de volgende scherm afbeelding:

![Lees-BLOB][2]

## <a name="the-team-data-science-process-in-action-example"></a>Het team data Science-proces in actie voorbeeld
Voor een voor beeld van het team data Science process a met behulp van een open bare gegevensset raadpleegt u [team data Science process in actie: using SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data