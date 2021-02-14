---
title: Veelgestelde vragen over Azure Data Factory
description: Veelgestelde vragen over Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4ddadec327f1a4f6aa22d51ac073b9aebb0dee82
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383191"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Veelgestelde vragen over Azure Data Factory
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Veelgestelde vragen-Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Algemene vragen
### <a name="what-is-azure-data-factory"></a>Wat is Azure Data Factory?
Data Factory is een service voor gegevens integratie **in de Cloud waarmee de verplaatsing en trans formatie van gegevens worden geautomatiseerd**. Net als bij een fabriek die apparatuur uitvoert om grond stoffen te maken en deze te transformeren in voltooide producten, Data Factory worden bestaande services die onbewerkte gegevens verzamelen, omgezet in kant-en-klare informatie.

Met Data Factory kunt u gegevensgestuurde werk stromen maken om gegevens te verplaatsen tussen zowel on-premises als in de cloud opgeslagen gegevens, en gegevens verwerken/transformeren met behulp van reken services zoals Azure HDInsight en Azure Data Lake Analytics. Nadat u een pijp lijn hebt gemaakt die de actie uitvoert die u nodig hebt, kunt u deze zo plannen dat deze regel matig wordt uitgevoerd (elk uur, dagelijks, wekelijks, enzovoort).   

Zie [overzicht van & basis concepten](data-factory-introduction.md)voor meer informatie.

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Waar vind ik prijs informatie voor Azure Data Factory?
Zie de [pagina met prijs informatie voor Data Factory][adf-pricing-details] voor de prijs informatie voor de Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hoe kan ik aan de slag met Azure Data Factory?
* Zie [Inleiding tot Azure Data Factory](data-factory-introduction.md)voor een overzicht van Azure Data Factory.
* Zie [gegevens kopiëren van Azure Blob Storage naar Azure SQL database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor een zelf studie over het **kopiëren/verplaatsen van gegevens** met behulp van Kopieer activiteit.
* Voor een zelf studie over het **transformeren van gegevens** met HDInsight Hive-activiteit. Bekijk [gegevens verwerken door Hive-script uit te voeren in een Hadoop-cluster](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Wat is de beschik baarheid van de beData Factorys regio?
Data Factory is beschikbaar in **VS West** en **Europa-Noord**. De compute-en opslag services die door de gegevens fabrieken worden gebruikt, kunnen zich in andere regio's bevindt. Zie [ondersteunde regio's](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Wat zijn de limieten voor het aantal gegevens fabrieken/pijp lijnen/activiteiten/gegevens sets?
Zie de sectie **Azure Data Factory limieten** van het artikel [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Wat is de ontwerp-en ontwikkelaars ervaring met Azure Data Factory Service?
Met een van de volgende hulpprogram ma's/Sdk's kunt u gegevens fabrieken ontwerpen/maken:

* **Visual Studio** U kunt Visual Studio gebruiken om een Azure data factory te maken. Zie [uw eerste gegevens pijplijn bouwen met Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) voor meer informatie.
* **Azure PowerShell** Zie [Azure Data Factory maken en bewaken met behulp van Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) voor een zelf studie/walkthrough voor het maken van een Data Factory met Power shell. Zie [Data Factory cmdlet-referentie][adf-powershell-reference] -inhoud in de MSDN-bibliotheek voor een uitgebreide documentatie van Data Factory-cmdlets.
* **.Net-klassebibliotheek** U kunt via een programma gegevens fabrieken maken met behulp van Data Factory .NET SDK. Zie [gegevens fabrieken maken, bewaken en beheren met behulp van .NET SDK](data-factory-create-data-factories-programmatically.md) voor een overzicht van het maken van een Data Factory met behulp van .NET SDK. Zie [Data Factory Class Library Reference][msdn-class-library-reference] (Engelstalig) voor een uitgebreide documentatie over Data Factory .NET SDK.
* **rest API** U kunt ook de REST API weer gegeven door de Azure Data Factory-service gebruiken om gegevens fabrieken te maken en te implementeren. Zie [Data Factory rest API Reference][msdn-rest-api-reference] voor een uitgebreide documentatie van Data Factory rest API.
* **Azure Resource Manager sjabloon** Zie [zelf studie: uw eerste Azure-Data Factory bouwen met behulp van Azure Resource Manager sjabloon](data-factory-build-your-first-pipeline-using-arm.md) fo-gegevens.

### <a name="can-i-rename-a-data-factory"></a>Kan ik de naam van een data factory wijzigen?
Nee. Net als bij andere Azure-resources kan de naam van een Azure-data factory niet worden gewijzigd.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Kan ik een data factory van het ene Azure-abonnement naar het andere verplaatsen?
Ja. Gebruik de knop **verplaatsen** op de blade Data Factory zoals in het volgende diagram wordt weer gegeven:

![data factory verplaatsen](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Wat zijn de reken omgevingen die door Data Factory worden ondersteund?
De volgende tabel bevat een lijst met reken omgevingen die worden ondersteund door Data Factory en de activiteiten die hierop kunnen worden uitgevoerd.

| Compute-omgeving | activities |
| --- | --- |
| [Hdinsight-cluster op aanvraag](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) of [uw eigen hdinsight-cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop-streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klassiek)](data-factory-compute-linked-services.md#azure-machine-learning-studio-classic-linked-service) |[Studio-activiteiten (klassiek): batch uitvoering en resource bijwerken](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure Synapse Analytics](data-factory-compute-linked-services.md#azure-synapse-analytics-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Opgeslagen procedure](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hoe kan Azure Data Factory vergelijken met SQL Server Integration Services (SSIS)? 
Raadpleeg de presentatie over [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) van een van onze mvp's (de meest waardevolle professionals): Reza rad. Sommige van de recente wijzigingen in Data Factory worden mogelijk niet weer gegeven in het diamodel. We voegen continu meer mogelijkheden toe aan Azure Data Factory. We voegen continu meer mogelijkheden toe aan Azure Data Factory. Deze updates worden opgenomen in de vergelijking van technologieën van micro soft die later dit jaar worden geïntegreerd.   

## <a name="activities---faq"></a>Activiteiten-Veelgestelde vragen
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Wat zijn de verschillende soorten activiteiten die u in een Data Factory pijp lijn kunt gebruiken?
* [Gegevens verplaatsings activiteiten](data-factory-data-movement-activities.md) om gegevens te verplaatsen.
* [Activiteiten voor gegevens transformatie](data-factory-data-transformation-activities.md) voor het verwerken/transformeren van gegevens.

### <a name="when-does-an-activity-run"></a>Wanneer wordt een activiteit uitgevoerd?
De instelling **beschikbaarheids** configuratie in de uitvoer gegevens tabel bepaalt wanneer de activiteit wordt uitgevoerd. Als er invoer gegevens sets zijn opgegeven, controleert de activiteit of aan alle invoergegevens afhankelijkheden is voldaan (dat wil zeggen, status **gereed** ) voordat deze wordt gestart.

## <a name="copy-activity---faq"></a>Kopieer activiteit-Veelgestelde vragen
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Is het beter om een pijp lijn te hebben met meerdere activiteiten of een afzonderlijke pijp lijn voor elke activiteit?
Pijp lijnen moeten gerelateerde activiteiten bundelen. Als de gegevens sets waarmee ze zijn verbonden, niet worden gebruikt door andere activiteiten buiten de pijp lijn, kunt u de activiteiten in één pijp lijn blijven. Op deze manier hoeft u geen pijp lijn actieve Peri Oden te koppelen zodat ze met elkaar worden uitgelijnd. De gegevens integriteit in de tabellen intern voor de pijp lijn is ook beter behouden bij het bijwerken van de pijp lijn. Met pijplijn update worden alle activiteiten in de pijp lijn in wezen gestopt, verwijderd en opnieuw gemaakt. Vanuit het ontwerp perspectief is het mogelijk dat de stroom van gegevens binnen de gerelateerde activiteiten in één JSON-bestand voor de pijp lijn eenvoudiger kan worden weer gegeven.

### <a name="what-are-the-supported-data-stores"></a>Wat zijn de ondersteunde gegevens archieven?
De kopieeractiviteit in Data Factory kopieert gegevens van een brongegevensarchief naar een sinkgegevensarchief. Data Factory ondersteunt de volgende gegevensarchieven. Gegevens vanuit elke willekeurige bron kunnen naar een sink worden geschreven. Klik op een gegevensarchief voor informatie over het kopiëren van gegevens naar en van dat archief.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gegevensarchieven met een * kunnen zich on-premises of op Azure IaaS bevinden. Hiervoor moet u [Data Management Gateway](data-factory-data-management-gateway.md) installeren op een on-premises/Azure IaaS-computer.

### <a name="what-are-the-supported-file-formats"></a>Wat zijn de ondersteunde bestands indelingen?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Waar wordt de Kopieer bewerking uitgevoerd?
Zie de sectie [wereld wijd beschik bare gegevens verplaatsing](data-factory-data-movement-activities.md#global) voor meer informatie. Kortom, wanneer een on-premises gegevens archief betrokken is, wordt de Kopieer bewerking uitgevoerd door de Data Management Gateway in uw on-premises omgeving. En wanneer de verplaatsing tussen twee Cloud archieven plaatsvindt, wordt de Kopieer bewerking uitgevoerd in de regio die het dichtst in de buurt is van de Sink-locatie in dezelfde geografie.

## <a name="hdinsight-activity---faq"></a>HDInsight-activiteit-Veelgestelde vragen
### <a name="what-regions-are-supported-by-hdinsight"></a>Welke regio's worden ondersteund door HDInsight?
Zie de sectie geografische Beschik baarheid in het volgende artikel: of [prijs informatie voor HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Welke regio wordt gebruikt door een HDInsight-cluster op aanvraag?
Het HDInsight-cluster op aanvraag wordt gemaakt in dezelfde regio waar de opslag ruimte die u hebt opgegeven voor het cluster bestaat.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Hoe koppelt u extra opslag accounts aan uw HDInsight-cluster?
Als u uw eigen HDInsight-cluster gebruikt (BYOC uw eigen cluster te maken), raadpleegt u de volgende onderwerpen:

* [Een HDInsight-cluster gebruiken met alternatieve opslag accounts en meta Stores][hdinsight-alternate-storage]
* [Extra opslag accounts gebruiken met HDInsight-Hive][hdinsight-alternate-storage-2]

Als u een cluster op aanvraag gebruikt dat is gemaakt door de Data Factory-Service, geeft u extra opslag accounts op voor de gekoppelde HDInsight-service zodat de Data Factory-service namens u kan worden geregistreerd. In de JSON-definitie voor de gekoppelde on-demand-service gebruikt u de eigenschap **additionalLinkedServiceNames** om alternatieve opslag accounts op te geven, zoals wordt weer gegeven in het volgende JSON-fragment:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
In het bovenstaande voor beeld vertegenwoordigen otherLinkedServiceName1 en otherLinkedServiceName2 gekoppelde services waarvan de definities referenties bevatten die het HDInsight-cluster nodig heeft om toegang te krijgen tot alternatieve opslag accounts.

## <a name="slices---faq"></a>Segmenten-Veelgestelde vragen
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Waarom zijn mijn invoer segmenten niet de status gereed?
Bij een veelvoorkomende fout wordt er geen **externe** eigenschap ingesteld op **True** in de invoer-gegevensset wanneer de invoer gegevens zich buiten het Data Factory bevinden (niet gemaakt door de Data Factory).

In het volgende voor beeld hoeft u alleen **extern** in te stellen op True in **dataset1**.  

**DataFactory1** Pijp lijn 1: dataset1-> activiteit1-> dataset2-> activiteit2-> dataset3-pijp lijn 2: dataset3-> activiteit3-> dataset4

Als u een andere data factory hebt met een pijp lijn die dataset4 (geproduceerd door pijp lijn 2 in data factory 1), markeert u dataset4 als een externe gegevensset, omdat de gegevensset wordt geproduceerd door een andere data factory (DataFactory1, niet DataFactory2).  

**DataFactory2**    
Pijp lijn 1: dataset4->activity4->dataset5

Als de externe eigenschap op de juiste wijze is ingesteld, controleert u of de invoer gegevens bestaan op de locatie die is opgegeven in de definitie van de invoer gegevensset.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hoe kan een segment op een ander tijdstip worden uitgevoerd dan middernacht wanneer het segment dagelijks wordt geproduceerd?
Gebruik de eigenschap **Offset** om de tijd op te geven waarop u het segment wilt produceren. Zie de sectie [Beschik baarheid van gegevensset](data-factory-create-datasets.md#dataset-availability) voor meer informatie over deze eigenschap. Hier volgt een kort voor beeld:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Dagelijkse segmenten beginnen bij **6 uur** in plaats van de standaard middernacht.     

### <a name="how-can-i-rerun-a-slice"></a>Hoe kan ik een segment opnieuw uitvoeren?
U kunt een segment op een van de volgende manieren opnieuw uitvoeren:

* Gebruik de app controleren en beheren om een activiteiten venster of segment opnieuw uit te voeren. Zie de [geselecteerde activiteit opnieuw uitvoeren](data-factory-monitor-manage-app.md#perform-batch-actions) voor instructies.   
* Klik op **uitvoeren** in de opdracht balk op de Blade **gegevens segment** voor het segment in de Azure Portal.
* Voer de cmdlet **set-AzDataFactorySliceStatus** uit met de status ingesteld op **wachten** op het segment.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Zie [set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] voor meer informatie over de cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Hoe lang duurt het om een segment te verwerken?
Gebruik de activiteiten venster Verkenner in monitor & app beheren om te zien hoe lang het duurde om een gegevens segment te verwerken. Zie [activiteiten venster Verkenner](data-factory-monitor-manage-app.md#activity-window-explorer) voor meer informatie.

U kunt ook het volgende doen in het Azure Portal:  

1. Klik op de tegel gegevens **sets** op de Blade **Data FACTORY** voor uw Data Factory.
2. Klik op de Blade **gegevens sets** op de gegevensset.
3. Selecteer het segment waarin u geïnteresseerd bent in de lijst met **recente segmenten** op de Blade **tabel** .
4. Klik op de uitvoering van de activiteit in de lijst met **uitgevoerde activiteiten** op de Blade **gegevens segment** .
5. Klik op **Eigenschappen** tegel op de Blade **Details uitvoering van activiteit** .
6. Het veld **duur** wordt weer geven met een waarde. Deze waarde is de tijd die nodig is om het segment te verwerken.   

### <a name="how-to-stop-a-running-slice"></a>Hoe kan ik een actief segment stoppen?
Als u wilt stoppen met het uitvoeren van de pijp lijn, kunt u de cmdlet [suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) gebruiken. Op dit moment stopt het onderbreken van de pijp lijn niet de segment uitvoeringen die worden uitgevoerd. Zodra het uitvoeren van de uitvoering is voltooid, wordt er geen extra segment opgenomen.

Als u de uitvoeringen echt onmiddellijk wilt stoppen, is de enige manier om de pijp lijn te verwijderen en opnieuw te maken. Als u ervoor kiest om de pijp lijn te verwijderen, hoeft u geen tabellen en gekoppelde services te verwijderen die door de pijp lijn worden gebruikt.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: /archive/blogs/cindygross/use-additional-storage-accounts-with-hdinsight-hive