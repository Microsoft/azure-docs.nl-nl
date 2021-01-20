---
title: Uw eerste gegevensfactory bouwen (Azure Portal)
description: In deze zelfstudie maakt u een Azure Data Factory-voorbeeldpijplijn met behulp van de Data Factory-editor in Azure Portal.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: 4e19991de20b130b878a230313d87ca09d31a84e
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556440"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Zelfstudie: uw eerste data factory bouwen met behulp van de Azure-portal
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Azure Resource Manager-sjabloon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory, die algemeen beschikbaar is. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Quickstart: Een gegevensfactory maken met behulp van Data Factory](../quickstart-create-data-factory-dot-net.md).

> [!WARNING]
> De JSON-editor in Azure Portal voor het ontwerpen en het implementeren van ADF v1-pijplijnen wordt uitgeschakeld op 31 juli 2019. Na 31 juli 2019 kunt u [ADF v1 Powershell-cmdlets](/powershell/module/az.datafactory/), [ADF v1 .Net SDK](/dotnet/api/microsoft.azure.management.datafactories.models) en [ADF v1 REST APIs](/rest/api/datafactory/) blijven gebruiken om uw ADF v1-pijplijnen te ontwerpen en implementeren.

In dit artikel leert u hoe u [Azure Portal](https://portal.azure.com/) gebruikt om uw eerste data factory te maken. Als u de zelfstudie wilt volgen met andere hulpprogramma's/SDK's, selecteert u een van de opties uit de vervolgkeuzelijst. 

De pijplijn in deze zelfstudie heeft één activiteit, namelijk een Azure HDInsight Hive-activiteit. Deze activiteit voert een Hive-script uit op een HDInsight-cluster dat invoergegevens transformeert om uitvoergegevens te produceren. De pijplijn is gepland on één keer per maand tussen de opgegeven begin- en eindtijd te worden uitgevoerd. 

> [!NOTE]
> Met de gegevenspijplijn in deze zelfstudie worden invoergegevens getransformeerd in uitvoergegevens. Zie voor meer informatie over het kopiëren van gegevens met behulp van Data Factory [Zelfstudie: Gegevens kopiëren vanuit Azure Blob Storage naar Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie.

## <a name="prerequisites"></a>Vereisten
Lees het [Zelfstudieoverzicht](data-factory-build-your-first-pipeline.md) en volg de stappen in het gedeelte Vereisten.

Dit artikel biedt geen conceptueel overzicht van de Data Factory-service. Lees voor meer informatie over de service [Inleiding tot Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Een voorbeeld is een kopieeractiviteit die gegevens kopieert van een brongegevensarchief naar een doelgegevensarchief. Een ander voorbeeld is een HDInsight Hive-activiteit die een Hive-script uitvoert dat invoergegevens omzet om uitvoergegevens te produceren. 

Volg deze stappen om een data factory te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **Nieuw** > **Gegevens en analyses** > **Data Factory**.

   ![Blade maken](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

1. Voer op de blade **Nieuwe data factory**, onder **Naam**, **GetStartedDF** in.

   ![Blade voor een nieuwe gegevensfactory](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > De naam van de data factory moet een Globally Unique Identifier zijn. Als u de fout 'Naam data factory GetStartedDF is niet beschikbaar' ontvangt, wijzigt u de naam van de data factory. Gebruik bijvoorbeeld uwgebruikersnaamGetStartedDF en maak de data factory opnieuw. Zie voor meer informatie over naamgevingsregels [Data Factory: naamgevingsregels](data-factory-naming-rules.md).
   >
   > De naam van de data factory wordt in de toekomst mogelijk geregistreerd als DNS-naam en mogelijk voor iedereen zichtbaar.
   >
   >
1. Selecteer onder **Abonnement** het Azure-abonnement waarvoor u de data factory wilt maken.

1. Selecteer een bestaande resourcegroep of maak een nieuwe resourcegroep. Maak voor deze zelfstudie een resourcegroep met de naam **ADFGetStartedRG**.

1. Selecteer onder **Locatie** een locatie voor de data factory. Alleen regio's die worden ondersteund door de Data Factory-service worden weergegeven in de vervolgkeuzelijst.

1. Schakel het selectievakje bij **Vastmaken aan dashboard** in.

1. Selecteer **Maken**.

   > [!IMPORTANT]
   > Als u Data Factory-exemplaren wilt maken, moet u lid zijn van de rol [Inzender Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) op abonnements-/resourcegroepniveau.
   >
   >
1. Op het dashboard ziet u de volgende tegel met de status: **Data factory implementeren**:    

   ![Status Data factory implementeren](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

1. Nadat de data factory is gemaakt, ziet u de pagina **Data factory**. Hier wordt de inhoud van de data factory weergegeven.     

    ![Blade Data factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Voordat u in de data factory een pijplijn maakt, moet u eerst enkele data factory-entiteiten maken. U maakt eerst gekoppelde services om de gegevensarchieven/compute-services te koppelen aan uw gegevensarchief. Vervolgens definieert u in- en uitvoergegevenssets die de invoer- en uitvoergegevens in de gekoppelde gegevensarchieven vertegenwoordigen. Ten slotte maakt u de pijplijn met een activiteit die deze gegevenssets gebruikt.

## <a name="create-linked-services"></a>Gekoppelde services maken
In deze stap koppelt u uw Azure Storage-account en een on-demand HDInsight-cluster aan uw data factory. Het Storage-account bevat de in- en uitvoergegevens van de pijplijn in dit voorbeeld. De gekoppelde HDInsight-service wordt gebruikt om een Hive-script uit te voeren dat is opgegeven in de activiteit van de pijplijn in dit voorbeeld. Bepaal welke [gegevensarchieven](data-factory-data-movement-activities.md)/[compute-services](data-factory-compute-linked-services.md) er in uw scenario worden gebruikt. Koppel die services vervolgens aan de data factory door gekoppelde services te maken.  

### <a name="create-a-storage-linked-service"></a>Een gekoppelde Storage-service maken
In deze stap koppelt u uw opslagaccount aan uw data factory. Voor deze zelfstudie gebruikt u hetzelfde opslagaccount om invoer- en uitvoergegevens en het HQL-scriptbestand op te slaan.

1. Op de blade **Data factory** voor **GetStartedDF** selecteert u **Maken en implementeren**. U ziet de Data Factory Editor.

   ![Tegel ontwerpen en implementeren](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

1. Selecteer **Nieuwe gegevensopslag** en kies **Azure Storage**.

   ![Blade Nieuwe gegevensopslag](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

1. U ziet het JSON-script voor het maken van een gekoppelde Storage-service in de editor.

   ![Gekoppelde Storage-service](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Vervang de **accountnaam** door de naam van uw opslagaccount. Vervang de **accountsleutel** met de toegangssleutel van uw opslagaccount. Zie [Toegangssleutels voor opslagaccounts beheren](../../storage/common/storage-account-keys-manage.md) voor informatie over het ophalen van uw toegangssleutel voor opslag.

1. Selecteer in de opdrachtbalk **Implementeren** om de gekoppelde service te implementeren.

    ![Implementatieknop](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Nadat de gekoppelde service is geïmplementeerd, verdwijnt het venster Draft-1. U ziet **AzureStorageLinkedService** in de structuurweergave links.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>Een gekoppelde HDInsight-service maken
In deze stap koppelt u een on-demand HDInsight-cluster aan uw gegevensfactory. Het HDInsight-cluster wordt automatisch gemaakt tijdens de runtime. Wanneer de verwerking is voltooid en het cluster gedurende een opgegeven tijd inactief is, wordt het verwijderd.

1. Selecteer in de Data Factory Editor **Meer** > **Opnieuw berekenen** > **On-demand HDInsight-cluster**.

    ![Nieuwe berekening](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

1. Kopieer het onderstaande codefragment en plak het in het venster Draft-1. Het JSON-codefragment bevat de eigenschappen die worden gebruikt om het on-demand HDInsight-cluster te maken.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt.

   | Eigenschap | Beschrijving |
   |:--- |:--- |
   | clusterSize |Geeft de grootte van het HDInsight-cluster aan. |
   | timeToLive | Geeft aan hoelang het HDInsight-cluster inactief moet zijn voordat het wordt verwijderd. |
   | linkedServiceName | Geeft het opslagaccount aan dat wordt gebruikt voor het opslaan van de logboeken die door HDInsight worden gegenereerd. |

    Houd rekening met de volgende punten:

     a. Met de JSON-eigenschappen maakt de data factory voor u een HDInsight-cluster op basis van Linux. Zie voor meer informatie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. U kunt uw eigen HDInsight-cluster gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) voor meer informatie.

     c. Het HDInsight-cluster maakt een standaardcontainer in de blobopslag die u hebt opgegeven in de JSON-eigenschap (**linkedServiceName**). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit gedrag is standaard. Met een gekoppelde on-demand HDInsight-service wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment wordt verwerkt, tenzij er een bestaand livecluster is (**timeToLive**). Het cluster wordt verwijderd wanneer het verwerken is voltooid.

     Naarmate er meer segmenten worden verwerkt, verschijnen er meer containers in uw blobopslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers worden als volgt opgebouwd: adf **naamvanuwdatafactory**-**naamvangekoppeldeservice**-datum-/tijdstempel. Gebruik een hulpprogramma als [Azure Storage Explorer](https://storageexplorer.com/) om containers in uw blobopslag te verwijderen.

     Zie voor meer informatie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

1. Selecteer in de opdrachtbalk **Implementeren** om de gekoppelde service te implementeren.

    ![Optie implementeren](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

1. Controleer of u in de structuurweergave links zowel **AzureStorageLinkedService** als **HDInsightOnDemandLinkedService** ziet.

    ![Schermopname waarin AzureStorageLinkedService en HDInsightOnDemandLinkedService zijn gekoppeld.](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Gegevenssets maken
In deze stap maakt u gegevenssets die de invoer- en uitvoergegevens voor Hive-verwerking vertegenwoordigen. Deze gegevenssets verwijzen naar AzureStorageLinkedService, die u eerder in deze zelfstudie hebt gemaakt. De gekoppelde service verwijst naar een opslagaccount. Gegevenssets bepalen de container, map en bestandsnaam in de opslag waarin de invoer- en uitvoergegevens zijn opgeslagen.   

### <a name="create-the-input-dataset"></a>De invoergegevensset maken
1. Selecteer in de Data Factory Editor **Meer** > **Nieuwe gegevensset** > **Azure Blob-opslag**.

    ![Nieuwe gegevensset](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

1. Kopieer het onderstaande codefragment en plak het in het venster Draft-1. U maakt in het JSON-fragment een gegevensset met de naam **AzureBlobInput** die voor de invoergegevens van een activiteit in de pijplijn staat. Daarbij geeft u op dat de invoergegevens zich bevinden in de blobcontainer met de naam **adfgetstarted** en de map met de naam **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt.

   | Eigenschap | Genest onder | Beschrijving |
   |:--- |:--- |:--- |
   | type | properties |De eigenschap type wordt ingesteld op **AzureBlob**, omdat de gegevens zich in de blobopslag bevinden. |
   | linkedServiceName | indeling |Deze eigenschap verwijst naar AzureStorageLinkedService, die u eerder hebt gemaakt. |
   | folderPath | typeProperties | Deze eigenschap verwijst naar de blobcontainer en de map die de blobs voor invoer bevat. | 
   | fileName | typeProperties |Deze eigenschap is optioneel. Als u deze eigenschap niet opgeeft, worden alle bestanden uit folderPath gekozen. In deze zelfstudie wordt alleen het input.log-bestand verwerkt. |
   | type | indeling |Omdat de logboekbestanden tekstbestanden zijn, gebruikt u **TextFormat**. |
   | columnDelimiter | indeling |De kolommen in de logboekbestanden worden gescheiden door een komma (`,`). |
   | frequency/interval | availability |Als frequency wordt ingesteld op **Month** en de interval **1** is, betekent dit dat de invoersegmenten één keer per maand beschikbaar worden gemaakt. |
   | external | properties | Deze eigenschap wordt ingesteld op **true** als de invoergegevens niet worden gegenereerd door deze pijplijn. In deze zelfstudie wordt het bestand input.log niet gegenereerd door deze pijplijn. Daarom is de eigenschap ingesteld op **true**. |

    Zie [Azure Blob-connectoren](data-factory-azure-blob-connector.md#dataset-properties) voor meer informatie over deze JSON-eigenschappen.

1. Selecteer in de opdrachtbalk **Implementeren** om de zojuist gemaakte gegevensset te implementeren. U ziet de gegevensset in de structuurweergave links.

### <a name="create-the-output-dataset"></a>De uitvoergegevensset maken
U maakt nu de uitvoergegevensset die staat voor de uitvoergegevens die worden opgeslagen in de blobopslag.

1. Selecteer in de Data Factory Editor **Meer** > **Nieuwe gegevensset** > **Azure Blob-opslag**.

1. Kopieer het onderstaande codefragment en plak het in het venster Draft-1. In het JSON-codefragment maakt u een gegevensset met de naam **AzureBlobOutput** om de gegevensstructuur op te geven die door het Hive-script wordt geproduceerd. U geeft ook op dat de resultaten worden opgeslagen in de blobcontainer met de naam **adfgetstarted** en in de map met de naam **partitioneddata**. In het gedeelte **availability** wordt opgegeven dat de uitvoergegevensset maandelijks wordt geproduceerd.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Voor beschrijvingen van deze eigenschappen raadpleegt u het gedeelte 'De invoergegevensset maken'. U stelt de externe eigenschap niet in op een uitvoergegevensset, omdat de gegevensset wordt geproduceerd door de Data Factory-service.

1. Selecteer in de opdrachtbalk **Implementeren** om de zojuist gemaakte gegevensset te implementeren.

1. Controleer of de gegevensset is gemaakt.

    ![Structuurweergave met gekoppelde services](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u uw eerste pijplijn met een HDInsight Hive-activiteit. Het invoersegment is maandelijks beschikbaar (frequency is Month, interval is 1). Het uitvoersegment wordt maandelijks geproduceerd. Ook de scheduler-eigenschap voor de activiteit is ingesteld op maandelijks. De instellingen voor de uitvoergegevensset en de activiteitenplanner moeten overeenkomen. Op dit moment wordt de planning gebaseerd op de uitvoergegevensset. Daarom moet u ook een uitvoergegevensset maken als er tijdens de activiteit geen uitvoer wordt geproduceerd. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan. De eigenschappen die in het volgende JSON-codefragment worden gebruikt, worden aan het einde van dit gedeelte beschreven.

1. Selecteer in de Data Factory Editor **Meer** > **Nieuwe pijplijn**.

    ![Optie Nieuwe pijplijn](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

1. Kopieer het onderstaande codefragment en plak het in het venster Draft-1.

   > [!IMPORTANT]
   > Vervang **storageaccountname** door de naam van uw opslagaccount in het JSON-codefragment.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    In het JSON-codefragment maakt u een pijplijn die bestaat uit een enkele activiteit waarvoor gebruik wordt gemaakt van Hive om gegevens in een HDInsight-cluster te verwerken.

    Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het opslagaccount (opgegeven door scriptLinkedService) met de naam **AzureStorageLinkedService**. U vindt dit in de map **script** in de container **adfgetstarted**.

    Het gedeelte **defines** wordt gebruikt om de runtime-instellingen op te geven die worden doorgegeven aan het Hive-script, zoals Hive-configuratiewaarden. Voorbeelden zijn ${hiveconf:inputtable} en ${hiveconf:partitionedtable}.

    Met de eigenschappen **start** en **end** van de pijplijn wordt opgegeven in welke periode de pijplijn actief is.

    In de activiteits-JSON geeft u op dat het Hive-script wordt uitgevoerd in de berekening die is opgegeven door **linkedServiceName**: **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Voor meer informatie over de JSON-eigenschappen die in dit voorbeeld worden gebruikt, raadpleegt u het gedeelte 'Pijplijn JSON' in [Pijplijnen en activiteiten in Data Factory](data-factory-create-pipelines.md).
   >
   >
1. Bevestig het volgende:

   a. Het bestand **input.log** staat in de map **inputdata** van de container **adfgetstarted** in de blobopslag.

   b. Het bestand **partitionweblogs.hql** staat in de map **script** van de container **adfgetstarted** in de blobopslag. Als u deze bestanden niet ziet, volgt u de stappen in het gedeelte Vereisten in [Zelfstudieoverzicht](data-factory-build-your-first-pipeline.md).

   c. U hebt **storageaccountname** vervangen door de naam van uw opslagaccount in de pijplijn-JSON.

1. Selecteer in de opdrachtbalk **Implementeren** om de pijplijn te implementeren. Omdat de tijden voor **start** en **end** in het verleden vallen en **isPaused** is ingesteld op **false**, wordt de pijplijn (activiteit in de pijplijn) direct na het implementeren uitgevoerd.

1. Controleer of de pijplijn in de structuurweergave wordt weergegeven.

    ![Structuurweergave met de pijplijn](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Een pijplijn bewaken
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Een pijplijn bewaken met behulp van de diagramweergave
1. Op de blade **Data factory** selecteert u **Diagram**.

    ![Tegel Diagram](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

1. In de **Diagram** weergave ziet u een overzicht van de pijplijnen en gegevenssets die voor deze zelfstudie worden gebruikt.

    ![Diagramweergave](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

1. Als u alle activiteiten in de pijplijn wilt bekijken, klikt u met de rechtermuisknop op de pijplijn in het diagram. Selecteer vervolgens **Pijplijn openen**.

    ![Menu Pijplijn openen](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

1. Bevestig dat u de **Hive-activiteit** in de pijplijn ziet.

    ![Pijplijnweergave openen](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Als u wilt teruggaan naar de vorige weergave, selecteert u **Data factory** in het menu bovenaan.

1. Dubbelklik in de **Diagram** weergave op de gegevensset **AzureBlobInput**. Controleer of het segment de status **Gereed** heeft. Het kan enkele minuten duren voordat het segment de status **Gereed** krijgt. Als de status niet is verschenen nadat u enige tijd hebt gewacht, controleert u of het invoerbestand (**input.log**) in de juiste container (**adfgetstarted**) en in de juiste map (**inputdata**) staat.

   ![Invoersegment met de status Gereed](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

1. Sluit de blade **AzureBlobInput**.

1. Dubbelklik in de **Diagram** weergave op de gegevensset **AzureBlobOutput**. U ziet het segment dat momenteel wordt verwerkt.

   ![Verwerking gegevensset wordt uitgevoerd](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

1. Nadat de verwerking is voltooid, ziet u het segment met de status **Gereed**.

   ![Gegevensset met de status Gereed](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > Het maken van een on-demand HDInsight-cluster duurt normaal gesproken ongeveer 20 minuten. U kunt ervan uitgaan dat het ongeveer 30 minuten duurt voordat het segment in de pijplijn is verwerkt.
   >
   >

1. Wanneer het segment de status **Gereed** heeft, controleert u de map **partitioneddata** in de container **adfgetstarted** in uw blobopslag voor de uitvoergegevens.  

   ![Uitvoergegevens](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

1. Selecteer het segment voor meer informatie over het segment op de blade **Gegevenssegment**.

    ![Informatie van het gegevenssegment](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

1. Selecteer een uitvoering in de lijst **Uitvoeringen van activiteit** om er meer informatie over te bekijken. (In dit scenario is het een Hive-activiteit.) De informatie wordt weergegeven op de blade **Details uitvoering van activiteit**.   

    ![Venster Details uitvoering van activiteit](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   In de logboekbestanden ziet u de Hive-query die is uitgevoerd, evenals de bijbehorende statusinformatie. Deze logboeken komen van pas bij het oplossen van problemen.
   Voor meer informatie bekijkt u [Pijplijnen bewaken en beheren met Azure Portal-blades](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> Het invoerbestand wordt verwijderd zodra het segment is verwerkt. Als u het segment dus opnieuw wilt uitvoeren of als u de zelfstudie opnieuw wilt doorlopen, uploadt u het invoerbestand (**input.log**) naar de map met **invoergegevens** van de container **adfgetstarted**.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Een pijplijn bewaken met behulp van de toepassing Bewaking en beheer
U kunt de app Bewaking en beheer ook gebruiken om uw pijplijnen te bewaken. Voor meer informatie over hoe u deze app gebruikt, raadpleegt u [Monitor and manage Data Factory pipelines by using the Monitor & Manage app](data-factory-monitor-manage-app.md) (Data Factory-pijplijnen bewaken en beheren met behulp van de app Bewaking en beheer).

1. Selecteer de tegel **Bewaking en beheer** op de startpagina van uw datafactory.

    ![De tegel Bewaking en beheer](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

1. Wijzig in de app Bewaking en beheer de **Begintijd** en **Eindtijd** zodanig dat deze overeenkomen met de begintijd en de eindtijd van uw pijplijn. Selecteer **Toepassen**.

    ![De app Bewaking en beheer](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

1. Selecteer een activiteitsvenster in de lijst **Activiteitsvensters** voor informatie over het betreffende venster.

    ![Lijst Activiteitsvensters](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Samenvatting
In deze zelfstudie hebt u een data factory gemaakt voor het verwerken van gegevens. Dit hebt u gedaan door een Hive-script uit te voeren op een HDInsight Hadoop-cluster. U hebt de Data Factory Editor in Azure Portal gebruikt om het volgende te doen:  

* Een data factory maken.
* Twee gekoppelde services maken:
   * Een gekoppelde Storage-service om te koppelen aan de blobopslag die de invoer-/uitvoerbestanden van de data factory bevat.
   * Een gekoppelde on-demand HDInsight-service om te koppelen aan een on-demand HDInsight Hadoop-cluster van de data factory. Data Factory maakt op tijd een HDInsight Hadoop-cluster om invoergegevens te verwerken en uitvoergegevens te produceren.
* Twee gegevenssets maken waarin de invoer- en uitvoergegevens van een HDInsight Hive-activiteit in de pijplijn worden beschreven.
* Een pijplijn maken met een HDInsight Hive-activiteit.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een pijplijn gemaakt met een transformatieactiviteit (HDInsight-activiteit) waarvoor een Hive-script wordt uitgevoerd op een on-demand HDInsight-cluster. Voor informatie over hoe u een kopieeractiviteit gebruikt om gegevens vanuit blobopslag naar Azure SQL Datebase te kopiëren, raadpleegt u [Zelfstudie: Copy data from Blob storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Zelfstudie: gegevens kopiëren vanuit blobopslag naar SQL Database).

## <a name="see-also"></a>Zie ook
| Onderwerp | Beschrijving |
|:--- |:--- |
| [Pijplijnen](data-factory-create-pipelines.md) |Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) |Op basis van dit artikel krijgt u inzicht in de gegevenssets in Data Factory. |
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) |In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Data Factory-toepassingsmodel. |
| [Pijplijnen bewaken en beheren met de app Bewaking en beheer](data-factory-monitor-manage-app.md) |In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app Bewaking en beheer. |