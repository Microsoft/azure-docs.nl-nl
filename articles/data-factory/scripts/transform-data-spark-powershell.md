---
title: Gegevens transformeren in de Cloud met behulp van Power shell
description: Met dit Power shell-script worden gegevens in de Cloud getransformeerd door een Spark-programma uit te voeren op een Azure HDInsight Spark cluster.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 2e1178f91344038fc86f1411fca1beafbe901d05
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389821"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Power shell-script: gegevens transformeren in de Cloud met behulp van Azure Data Factory

Met dit Power shell-voorbeeld script maakt u een pijp lijn waarmee gegevens in de cloud worden getransformeerd door een Spark-programma uit te voeren op een Azure HDInsight Spark cluster. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een Azure Storage-account**. Maak een python-script en een invoer bestand en upload deze naar de Azure-opslag. De uitvoer van het Spark-programma wordt opgeslagen in dit opslagaccount. Het Spark-cluster op aanvraag gebruikt hetzelfde opslagaccount als de primaire opslag.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Python-script uploaden naar het Blob-opslagaccount
1. Maak een Python-bestand met de naam **WordCount_Spark.py** met de volgende inhoud: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Vervang **&lt;storageaccountname&gt;** door de naam van uw Azure Storage-account. Sla het bestand vervolgens op. 
3. Maak in de Azure Blob-opslag een container met de naam **adftutorial** als deze nog niet bestaat. 
4. Maak een map met de naam **spark**.
5. Maak in de map **spark** een submap met de naam **script**. 
6. Upload het bestand **WordCount_Spark.py** naar de submap **script**. 


### <a name="upload-the-input-file"></a>Invoerbestand uploaden
1. Maak een bestand met de naam **minecraftstory.txt** met wat tekst. In het Spark-programma wordt het aantal woorden in deze tekst geteld. 
2. Maak een submap `inputfiles` met de naam in de `spark` map van de BLOB-container. 
3. Upload de `minecraftstory.txt` naar de submap`inputfiles`. 

## <a name="sample-script"></a>Voorbeeldscript
> [!IMPORTANT]
> Met dit script worden JSON-bestanden gemaakt waarmee Data Factory entiteiten (gekoppelde service, gegevensset en pijp lijn) op de vaste schijf worden gedefinieerd in de c:\ map.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat u het voorbeeld script hebt uitgevoerd, kunt u de volgende opdracht gebruiken om de resource groep en alle bijbehorende resources te verwijderen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Als u de data factory uit de resource groep wilt verwijderen, voert u de volgende opdracht uit: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Een data factory maken. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Hiermee maakt u een gekoppelde service in de data factory. Een gekoppelde service koppelt een gegevens archief of kan worden berekend op een data factory. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Hiermee maakt u een pijp lijn in de data factory. Een pijp lijn bevat een of meer activiteiten die een bepaalde bewerking uitvoeren. In deze pijp lijn transformeert een Spark-activiteit gegevens door een programma uit te voeren op een Azure HDInsight Spark cluster. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Hiermee maakt u een uitvoering voor de pijp lijn. Met andere woorden, de pijp lijn uitvoeren. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Haalt Details op over de uitvoering van de activiteit (uitvoering van de activiteit) in de pijp lijn. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory Power shell-voorbeeld scripts vindt u in de [Azure Data Factory Power shell](../samples-powershell.md)-voor beelden.