---
title: Gegevens in Azure Blob Storage verkennen met Pandas-team data Science process
description: Informatie over het verkennen van gegevens die zijn opgeslagen in Azure Blob-container met het python-pakket van Panda.
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
ms.openlocfilehash: 855998b887f1d446ee8d196ff4628e066cb5d675
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805675"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Gegevens in Azure Blob Storage verkennen met Pandas

In dit artikel wordt beschreven hoe u gegevens die zijn opgeslagen in Azure Blob-container kunt verkennen met behulp van het python-pakket van [Panda](https://pandas.pydata.org/) .

Deze taak is een stap in het [team data Science process](overview.md).

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u het volgende hebt:

* Een Azure-opslag account gemaakt. Als u instructies nodig hebt, raadpleegt u [een Azure Storage account maken](../../storage/common/storage-account-create.md)
* Uw gegevens zijn opgeslagen in een Azure Blob Storage-account. Zie [gegevens verplaatsen van en naar Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md) als u instructies nodig hebt.

## <a name="load-the-data-into-a-pandas-dataframe"></a>De gegevens laden in een Panda data frame
Als u een gegevensset wilt verkennen en bewerken, moet deze eerst worden gedownload van de BLOB-bron naar een lokaal bestand, dat vervolgens in een Panda data frame kan worden geladen. Dit zijn de stappen die u moet volgen voor deze procedure:

1. Down load de gegevens van Azure Blob met het volgende python-code voorbeeld met Blob service. Vervang de variabele in de volgende code door uw specifieke waarden:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))
    ```

1. Lees de gegevens in een Panda data frame van het gedownloade bestand.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

U bent nu klaar om de gegevens te verkennen en functies in deze gegevensset te genereren.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Voor beelden van het verkennen van gegevens met behulp van Pandas
Hier volgen enkele voor beelden van manieren om gegevens te verkennen met behulp van Panda:

1. Het **aantal rijen en kolommen** controleren

    ```python
    print('the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape)
    ```

1. **Inspecteer** de eerste of laatste paar **rijen** in de volgende gegevensset:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Controleer het **gegevens type** dat elke kolom heeft geïmporteerd, zoals met behulp van de volgende voorbeeld code

    ```python
    for col in dataframe_blobdata.columns:
        print(dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype)
    ```

1. Controleer als volgt de **basis statistieken** voor de kolommen in de gegevensset

    ```python
    dataframe_blobdata.describe()
    ```

1. Ga als volgt te kijken naar het aantal items voor elke kolom waarde

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Aantal ontbrekende waarden** ten opzichte van het werkelijke aantal vermeldingen in elke kolom met de volgende voorbeeld code

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print(miss_num)
    ```

1. Als u de **waarden** voor een bepaalde kolom in de gegevens ontbreken, kunt u deze als volgt verwijderen:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Een andere manier om ontbrekende waarden te vervangen, is met de functie mode:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Een **histogram** maken met behulp van een variabel aantal opslag locaties om de distributie van een variabele uit te zetten

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Bekijk **correlaties** tussen variabelen met behulp van een scatterplot of gebruik de ingebouwde correlatie functie

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
