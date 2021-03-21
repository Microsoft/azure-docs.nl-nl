---
title: Azure Data Factory-voor beelden
description: Bevat details over de voor beelden die bij de Azure Data Factory-service worden geleverd.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 82c871e8914d70dde7f2586b7dbf6556ebd2621f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387033"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory-voor beelden
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Power shell-voor beelden in Data Factory](../samples-powershell.md) en [code voorbeelden in de galerie met Azure-code voorbeelden](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Voorbeelden op GitHub
De [github-opslag plaats van Azure-DataFactory](https://github.com/azure/azure-datafactory) bevat verschillende voor beelden waarmee u snel kunt werken met Azure Data Factory Service (of) de scripts kunt wijzigen en deze in de eigen toepassing kunt gebruiken. De map Samples\JSON bevat JSON-fragmenten voor algemene scenario's.

| Voorbeeld | Beschrijving |
|:--- |:--- |
| [ADF-walkthrough](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Dit voor beeld bevat een end-to-end-procedure voor het verwerken van logboek bestanden met behulp van Azure Data Factory om gegevens van logboek bestanden in te scha kelen naar inzichten. <br/><br/>In deze walkthrough verzamelt de Data Factory-pijp lijn voorbeeld logboeken, verwerkt en verrijkt de gegevens van logboeken met referentie gegevens, en worden de gegevens getransformeerd om de effectiviteit te beoordelen van een marketing campagne die onlangs is gestart. |
| [JSON-voor beelden](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Dit voor beeld bevat JSON-voor beelden voor algemene scenario's. |
| [Voor beeld van http-gegevens-Downloader](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |In dit voor beeld wordt het downloaden van gegevens van een HTTP-eind punt naar Azure Blob Storage met aangepaste .NET-activiteit gedemonstreerd. |
| [Voor beeld van meerdere AppDomain dot-activiteit](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Met dit voor beeld kunt u een aangepaste .NET-activiteit ontwerpen die niet is beperkt tot assembly-versies die worden gebruikt door de ADF Launcher (bijvoorbeeld WindowsAzure. Storage v 4.3.0, Newtonsoft.Jsop v 6.0. x, enzovoort). |
| [R-script uitvoeren](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Dit voor beeld bevat de Data Factory aangepaste activiteit die kan worden gebruikt om RScript.exe aan te roepen. Dit voor beeld werkt alleen met uw eigen (niet op aanvraag) HDInsight-cluster waarop R al is geïnstalleerd. |
| [Spark-taken aanroepen in HDInsight Hadoop-cluster](../tutorial-transform-data-spark-portal.md) |In dit voor beeld ziet u hoe u MapReduce-activiteit kunt gebruiken om een Spark-programma aan te roepen. Het Spark-programma kopieert alleen gegevens van de ene Azure Blob-container naar een andere. |
| [Twitter-analyse met behulp van Azure Machine Learning Studio (klassieke) batch Score activiteit](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Dit voor beeld laat zien hoe u AzureMLBatchScoringActivity kunt gebruiken om een Azure Machine Learning model aan te roepen waarmee Twitter-sentiment analyse, Score ring, voor spelling, enzovoort worden uitgevoerd. |
| [Twitter-analyse met aangepaste activiteit](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |In dit voor beeld ziet u hoe u een aangepaste .NET-activiteit gebruikt om een Azure Machine Learning model aan te roepen waarmee Twitter-sentiment analyse, Score ring, voor spelling, enzovoort worden uitgevoerd. |
| [Pijp lijnen met para meters voor Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Het voor beeld biedt een end-to-end C#-code voor het implementeren van N pijp lijnen voor het scoren en opnieuw trainen van elk met een andere regio parameter, waarbij de lijst met regio's afkomstig is uit een parameters.txt bestand, dat deel uitmaakt van dit voor beeld. |
| [Referentie gegevens vernieuwen voor Azure Stream Analytics taken](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Dit voor beeld laat zien hoe u Azure Data Factory gebruikt en Azure Stream Analytics samen om de query's uit te voeren met referentie gegevens en het vernieuwen voor referentie gegevens volgens een planning in te stellen. |
| [Hybride pijp lijn met on-premises Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |In het voor beeld wordt een on-premises Hadoop-cluster gebruikt als reken doel voor het uitvoeren van taken in Data Factory net zoals u andere reken doelen zou toevoegen, zoals een Hadoop-cluster op basis van HDInsight in de Cloud. |
| [JSON-conversie programma](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Met dit hulp programma kunt u JSONs converteren van versie vóór 2015-07-01-preview naar meest recente of 2015-07-01-Preview (standaard). |
| [Voor beeld van een U-SQL-invoer bestand](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Dit bestand is een voorbeeld bestand dat door een U-SQL-activiteit wordt gebruikt. |
| [BLOB-bestand verwijderen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | In dit voor beeld wordt een C#-bestand getoond dat kan worden gebruikt als onderdeel van de aangepaste .net-activiteit van ADF om bestanden te verwijderen van de bron-Azure Blob-locatie nadat de bestanden zijn gekopieerd.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen
U kunt de volgende Azure Resource Manager sjablonen vinden voor Data Factory op GitHub.

| Template | Beschrijving |
| --- | --- |
| [Kopiëren van Azure-Blob Storage naar Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Als u deze sjabloon implementeert, wordt een Azure-data factory gemaakt met een pijp lijn waarmee gegevens uit de opgegeven Azure Blob-opslag worden gekopieerd naar Azure SQL Database |
| [Kopiëren van Sales Force naar Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Als u deze sjabloon implementeert, wordt een Azure-data factory gemaakt met een pijp lijn waarmee gegevens worden gekopieerd van het opgegeven Sales Force-account naar de Azure Blob-opslag. |
| [Gegevens transformeren door Hive-script uit te voeren op een Azure HDInsight-cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Als u deze sjabloon implementeert, wordt een Azure-data factory gemaakt met een pijp lijn die gegevens transformeert door het script voor het voorbeeld Hive uit te voeren op een Azure HDInsight Hadoop cluster. |

## <a name="samples-in-azure-portal"></a>Voor beelden in Azure Portal
U kunt de tegel **voorbeeld pijplijnen** gebruiken op de start pagina van uw Data Factory om voorbeeld pijplijnen en de bijbehorende entiteiten (gegevens sets en gekoppelde services) in te implementeren in uw Data Factory.

1. Maak een data factory of open een bestaande data factory. Zie [gegevens kopiëren van Blob Storage naar SQL database met behulp van Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stappen om een Data Factory te maken.
2. Klik in de Blade **Data Factory** voor de Data Factory op de tegel **voorbeeld pijplijnen** .

    ![Tegel voor voorbeeld pijplijnen](./media/data-factory-samples/SamplePipelinesTile.png)
3. Klik op de Blade **voorbeeld pijplijnen** op het voor **beeld** dat u wilt implementeren.

    ![Blade voorbeeld pijplijnen](./media/data-factory-samples/SampleTile.png)
4. Geef de configuratie-instellingen voor het voor beeld op. Bijvoorbeeld uw Azure Storage-account naam en de account sleutel, de naam van de logische SQL-Server, de data base, de gebruikers-ID en het wacht woord, enzovoort.

    ![Voorbeeld Blade](./media/data-factory-samples/SampleBlade.png)
5. Nadat u klaar bent met het opgeven van de configuratie-instellingen, klikt u op **maken** om de voorbeeld pijplijnen en gekoppelde services/tabellen die door de pijp lijnen worden gebruikt, te maken/te implementeren.
6. U ziet de status van de implementatie op de voorbeeld tegel waarop u eerder hebt geklikt op de Blade **voorbeeld pijplijnen** .

    ![Implementatiestatus](./media/data-factory-samples/DeploymentStatus.png)
7. Wanneer het bericht **implementatie voltooid** wordt weer gegeven op de tegel voor het voor beeld, sluit u de Blade **voorbeeld pijplijnen** .  
8. Op de Blade **Data Factory** ziet u dat gekoppelde services, gegevens sets en pijp lijnen aan uw Data Factory worden toegevoegd.  

    ![Blade Gegevensfactory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Voor beelden in Visual Studio
### <a name="prerequisites"></a>Vereisten
De volgende zaken moeten op uw computer zijn geïnstalleerd:

* Visual Studio 2013 of Visual Studio 2015
* Download de Azure SDK voor Visual Studio 2013 of Visual Studio 2015. Ga naar de [Azure-downloadpagina](https://azure.microsoft.com/downloads/) en klik in het gedeelte **.NET** op **VS 2013** of **VS 2015**.
* Download de nieuwste Azure Data Factory-invoegtoepassing voor Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) of [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Als u Visual Studio 2013 gebruikt, kunt u de invoeg toepassing ook bijwerken door de volgende stappen uit te voeren: Klik in het menu op **extra**  ->  **extensies en updates**  ->  **online**  ->  **Visual Studio-galerie**  ->  **Microsoft Azure Data Factory Tools for Visual Studio**  ->  **Update**.

### <a name="use-data-factory-templates"></a>Data Factory sjablonen gebruiken
1. Klik op **bestand** in het menu, wijs **Nieuw** aan en klik op **project**.
2. Voer in het dialoogvenster **Nieuw project** de volgende stappen uit:

   1. Selecteer **DataFactory** onder **Templates**.
   2. Selecteer **Data Factory sjablonen** in het rechterdeel venster.
   3. Voer een **naam** in voor het project.
   4. Selecteer een **locatie** voor het project.
   5. Klik op **OK**.

      ![Het dialoogvenster New Project](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Selecteer in het dialoog venster **Data Factory sjablonen** de voorbeeld sjabloon in de sectie **use-case-sjablonen** en klik op **volgende**. De volgende stappen begeleiden u bij het gebruik van de sjabloon voor **klant profilering** . De stappen zijn vergelijkbaar voor de andere voor beelden.

    ![Het dialoog venster Data Factory sjablonen](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Klik in het dialoog venster **Data Factory configuratie** op **volgende** op de pagina **Data Factory Basics** .
5. Voer de volgende stappen uit op de pagina **Data Factory configureren** :
   1. Selecteer **nieuwe Data Factory maken**. U kunt ook **bestaande Data Factory gebruiken** selecteren.
   2. Voer een **naam** in voor de Data Factory.
   3. Selecteer het **Azure-abonnement** waarin u de Data Factory wilt maken.
   4. Selecteer de **resource groep** voor de Data Factory.
   5. Selecteer **VS-West**, **VS-Oost** of **Europa-Noord** voor de **regio**.
   6. Klik op **Volgende**.
6. Geef op de pagina **gegevens archieven configureren** een bestaande **Data base op in Azure SQL database** en **Azure Storage-account** (of) data base/opslag maken en klik op volgende.
7. Selecteer op de pagina **Compute configureren** de optie standaard instellingen en klik op **volgende**.
8. Controleer op de pagina **samen vatting** alle instellingen en klik op **volgende**.
9. Wacht tot de implementatie is voltooid op de pagina **Implementatie status** en klik op **volt ooien**.
10. Klik met de rechtermuisknop op het project in Solution Explorer. Klik vervolgens op **Publish**.
11. Als u het dialoogvenster **Sign in to your Microsoft account** ziet, voert u uw referenties in voor het account met het Azure-abonnement en klikt u op **Sign in**.
12. Het volgende dialoogvenster wordt weergegeven:

    ![Het dialoogvenster Publish](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Op de pagina **Configure data factory** voert u de volgende stappen uit:

    1. Controleer of de optie **bestaande Data Factory gebruiken** .
    2. Selecteer de **Data Factory** die u hebt geselecteerd bij het gebruik van de sjabloon.
    3. Klik op **Next** om over te schakelen naar de pagina **Publish Items**. (Druk op **TAB** als u het veld Naam wilt verlaten, maar de knop **Next** is uitgeschakeld.)
14. Op de pagina **Publish Items** controleert u of alle Data Factory-entiteiten zijn geselecteerd en klikt u op **Next** om over te schakelen naar de pagina **Summary**.     
15. Controleer de samenvatting en klik op **Next** om te beginnen met het implementatieproces en om de **implementatiestatus** te bekijken.
16. Op de pagina **Deployment Status** ziet u de status van het implementatieproces. Klik op Finish wanneer de implementatie is uitgevoerd.

Zie [uw eerste Data Factory bouwen (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) voor meer informatie over het gebruik van Visual studio om Data Factory entiteiten te maken en te publiceren naar Azure.