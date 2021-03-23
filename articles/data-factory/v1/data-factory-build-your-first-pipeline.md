---
title: 'Data Factory zelf studie: eerste gegevens pijplijn '
description: In deze Azure Data Factory zelf studie wordt uitgelegd hoe u een data factory maakt en plant waarmee gegevens worden verwerkt met behulp van Hive-script op een Hadoop-cluster.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: e7114dae2a9cfef4a9b710831beb63a65c862643
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785373"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Zelf studie: uw eerste pijp lijn maken om gegevens te transformeren met een Hadoop-cluster
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sjabloon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Quickstart: een gegevensfactory maken met Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

In deze zelf studie bouwt u uw eerste Azure-data factory met een gegevens pijplijn. De pijp lijn transformeert invoer gegevens door een Hive-script uit te voeren op een Azure HDInsight-cluster (Hadoop) om uitvoer gegevens te produceren.  

In dit artikel vindt u een overzicht en vereisten voor de zelf studie. Nadat u de vereisten hebt voltooid, kunt u de zelf studie uitvoeren met een van de volgende hulpprogram ma's/Sdk's: Visual Studio, Power shell, Resource Manager-sjabloon REST API. Selecteer een van de opties in de vervolg keuzelijst aan de hand van de koppelingen aan het einde van dit artikel om de zelf studie uit te voeren met een van deze opties.    

## <a name="tutorial-overview"></a>Overzicht van de zelfstudie
In deze zelfstudie voert u de volgende stappen uit:

1. Maak een **Data Factory**. Een data factory kan een of meer gegevens pijplijnen bevatten waarmee gegevens worden verplaatst en getransformeerd.

    In deze zelf studie maakt u een pijp lijn in de data factory.
2. Een **pijp lijn** maken. Een pijplijn kan een of meer activiteiten bevatten (bijvoorbeeld: kopieeractiviteit, HDInsight Hive-activiteit). In dit voor beeld wordt gebruikgemaakt van de HDInsight Hive-activiteit die een Hive-script uitvoert op een HDInsight Hadoop-cluster. Het script maakt eerst een tabel die verwijst naar de onbewerkte weblogboek gegevens die zijn opgeslagen in Azure Blob-opslag en partitioneert de onbewerkte gegevens vervolgens per jaar en maand.

    In deze zelf studie gebruikt de pijp lijn de Hive-activiteit voor het transformeren van gegevens door een Hive-query uit te voeren op een Azure HDInsight Hadoop cluster.
3. **Gekoppelde services** maken. U maakt een gekoppelde service om een gegevensopslag of een rekenservice te koppelen aan de gegevensfactory. In een gegevensopslag zoals Azure Storage worden invoer-/uitvoergegevens bewaard van activiteiten in de pijplijn. Een compute-service zoals een HDInsight Hadoop-cluster verwerkt/transformeert gegevens.

    In deze zelf studie maakt u twee gekoppelde services: **Azure Storage** en **Azure HDInsight**. De Azure Storage gekoppelde service koppelt een Azure Storage account dat de invoer-en uitvoer gegevens voor de data factory bevat. Gekoppelde Azure HDInsight-Services koppelen een Azure HDInsight-cluster dat wordt gebruikt om gegevens te transformeren naar het data factory.
3. Invoer-en uitvoer **gegevens sets** maken. Een invoergegevensset vertegenwoordigt de invoer voor een activiteit in de pijplijn en een uitvoergegevensset vertegenwoordigt de uitvoer voor de activiteit.

    In deze zelf studie geven de invoer-en uitvoer gegevens sets de locatie op van de invoer-en uitvoer data in de Azure-Blob Storage. De Azure Storage gekoppelde service geeft aan wat Azure Storage-account wordt gebruikt. Een invoer-gegevensset geeft aan waar de invoer bestanden zich bevinden en een uitvoer gegevensset geeft aan waar de uitvoer bestanden worden geplaatst.


Zie [Inleiding tot Azure Data Factory](data-factory-introduction.md) -artikel voor een gedetailleerd overzicht van Azure Data Factory.

Dit is de **weer gave diagram** van het voor beeld Data Factory u in deze zelf studie hebt gemaakt. **MyFirstPipeline** heeft één activiteit van het type component die **AzureBlobInput** -gegevensset gebruikt als invoer en produceert **AzureBlobOutput** -gegevensset als uitvoer.

![Zelfstudie voor de Data Factory-diagramweergave](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


In deze zelf studie bevat de map **input data** van de Azure Blob-container **adfgetstarted** één bestand met de naam input. log. Dit logboek bestand bevat vermeldingen van drie maanden: januari, februari en maart 2016. Hier ziet u de voorbeeldrijen per maand in het invoerbestand.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Wanneer het bestand is verwerkt via de pijplijn met HDInsight Hive-activiteit, wordt met de activiteit een Hive-script uitgevoerd op de HDInsight-cluster waardoor de invoer wordt gesplitst in jaren en maanden. Met het script worden drie uitvoermappen gemaakt die een bestand bevatten met de invoer uit elke maand.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Van de hierboven weer gegeven voorbeeld regels wordt de eerste (met 2016-01-01) naar het 000000_0-bestand in de map month = 1 geschreven. Op dezelfde manier is de tweede regel geschreven naar het bestand in de map maand=2 en de derde naar het bestand in de map maand=3.  

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u aan de volgende vereisten voldoen:

1. U moet beschikken over een **Azure-abonnement**. Als u geen Azure-abonnement hebt, kunt u in enkele minuten een gratis proefaccount maken. Zie het artikel [Gratis proefversie](https://azure.microsoft.com/pricing/free-trial/) voor informatie over het verkrijgen van een gratis proefaccount.
2. U moet gebruikmaken van **Azure Storage**. U gebruikt een Azure-opslagaccount om de gegevens in deze zelfstudie op te slaan. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslag account hebt gemaakt, noteert u de **account naam** en de **toegangs sleutel**. Zie voor informatie over het ophalen van de toegangssleutels voor het opslagaccount [Toegangssleutels voor opslagaccounts beheren](../../storage/common/storage-account-keys-manage.md).
3. Down load en controleer het Hive-query bestand (**HQL**) dat zich bevindt op: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql) . Met deze query worden invoer gegevens getransformeerd om uitvoer gegevens te produceren.
4. Down load en controleer het voorbeeld invoer bestand (**input. log**) dat zich bevindt in: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Maak een BLOB-container met de naam **adfgetstarted** in uw Azure Blob Storage.
6. Upload **partitionweblogs. HQL** -bestand naar de map **script** in de **adfgetstarted** -container. Gebruik hulpprogram ma's zoals [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Upload **input. log** -bestand naar de map **input data** in de container **adfgetstarted** .

Nadat u de vereisten hebt voltooid, selecteert u een van de volgende hulpprogram ma's/Sdk's om de zelf studie uit te voeren:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager-sjabloon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio biedt een grafische manier om uw gegevens fabrieken te bouwen. Overwegende dat Power shell, Resource Manager-sjabloon en REST API opties een script/programmeer manier bieden voor het bouwen van uw gegevens fabrieken.

> [!NOTE]
> Met de gegevenspijplijn in deze zelfstudie worden invoergegevens getransformeerd in uitvoergegevens. Er worden bijvoorbeeld geen gegevens gekopieerd van een brongegevensarchief naar een doelgegevensarchief. Meer informatie over het kopiëren van gegevens met behulp van Azure Data Factory vindt u in [Tutorial: Copy data from Blob storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Zelfstudie: gegevens kopiëren vanuit blobopslag naar SQL Database).
>
> U kunt twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md) voor gedetailleerde informatie.
