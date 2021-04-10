---
title: Uw eerste data factory bouwen (Visual Studio)
description: In deze zelfstudie maakt u een Azure Data Factory-voorbeeldpijplijn met behulp van Visual Studio.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.custom: vs-azure
ms.date: 01/22/2018
ms.openlocfilehash: 7431dcbdcd94b74482786625a052bc5abc31c164
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783163"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Zelfstudie: Een gegevensfactory maken met behulp van Visual Studio
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Overzicht en vereisten](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sjabloon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Quickstart: een gegevensfactory maken met Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

In deze zelfstudie leert u hoe u een Azure-gegevensfactory maakt met Visual Studio. U maakt een Visual Studio-project met de Data Factory-projectsjabloon. Daarna definieert u Data Factory-entiteiten (gekoppelde services, gegevenssets en pipeline) in de JSON-indeling en vervolgens publiceert/implementeert u deze entiteiten in de cloud. 

De pijplijn in deze zelfstudie heeft één activiteit: **HDInsight Hive-activiteit**. Deze activiteit voert een Hive-script uit op een Azure HDInsight-cluster dat invoergegevens transformeert om uitvoergegevens te produceren. De pijplijn is gepland on één keer per maand tussen de opgegeven begin- en eindtijd te worden uitgevoerd. 

> [!NOTE]
> In deze zelfstudie wordt niet getoond hoe u gegevens met Azure Data Factory kopieert. Meer informatie over het kopiëren van gegevens met behulp van Azure Data Factory vindt u in [Tutorial: Copy data from Blob storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Zelfstudie: gegevens kopiëren vanuit blobopslag naar SQL Database).
> 
> Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie.


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>Walkthrough: Data Factory-entiteiten maken en publiceren
Hier volgen de stappen die u uitvoert als onderdeel van deze walkthrough:

1. Twee gekoppelde services maken: **AzureStorageLinkedService1** en **HDInsightOnDemandLinkedService1**. 
   
    In deze zelfstudie bevinden de invoer- en uitvoergegevens voor de Hive-activiteit zich in dezelfde Azure Blob Storage. U gebruikt een HDInsight-cluster op aanvraag om bestaande invoergegevens te verwerken en uitvoergegevens te produceren. Wanneer de invoergegevens gereed zijn om te worden verwerkt, wordt het HDInsight-cluster op aanvraag automatisch in runtime voor u gemaakt door Azure Data Factory. U moet uw gegevensarchieven of computes aan uw data factory koppelen, zodat de Data Factory-service deze in runtime kan verbinden. Daarom koppelt u uw Azure Storage-Account aan de data factory met behulp van de AzureStorageLinkedService1 en koppelt u een HDInsight-cluster op aanvraag met behulp van de HDInsightOnDemandLinkedService1. Wanneer u publiceert, geeft u de naam op van de data factory die moet worden gemaakt of van een bestaande data factory.  
2. Maak twee gegevenssets: **InputDataset** en **OutputDataset**. Deze staan voor de invoer- en uitvoergegevens die zijn opgeslagen in Azure Blob Storage. 
   
    Deze gegevenssetdefinities verwijzen naar de gekoppelde service Azure Storage die u in de vorige stap hebt gemaakt. Voor de InputDataset geeft u de blobcontainer (adfgetstarted) op en de map (inputdata) die een blob met de invoergegevens bevat. Voor de OutputDataset geeft u de blobcontainer (adfgetstarted) op en de map (partitioneddata) die de uitvoergegevens bevat. U geeft ook andere eigenschappen op, zoals de structuur, de beschikbaarheid en het beleid.
3. Maak een pijplijn met de naam **MyFirstPipeline**. 
  
    De pijplijn in deze walkthrough heeft maar één activiteit: **HDInsight Hive-activiteit**. Deze activiteit transformeert invoergegevens om uitvoergegevens te produceren, door een Hive-script uit te voeren op een HDInsight-cluster op aanvraag. Zie [Hive-activiteit](data-factory-hive-activity.md) voor meer informatie over Hive-activiteiten 
4. Maak een data factory met de naam **DataFactoryUsingVS**. Implementeer de gegevensfactory en alle gegevensfactory-entiteiten (gekoppelde services, tabellen en de pijplijn).
5. Na het publiceren kunt u de pijplijn bewaken met de blades in Azure Portal en met de app Bewaking en beheer. 
  
### <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Lees het artikel [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md) en voer de **vereiste** stappen uit. U kunt ook bovenaan de optie **Overzicht en vereisten** in de vervolgkeuzelijst selecteren om naar het artikel te gaan. Wanneer u aan de vereisten hebt voldaan, kunt u naar dit artikel terugkeren door in de vervolgkeuzelijst de optie **Visual Studio** te selecteren.
2. Als u Data Factory-exemplaren wilt maken, moet u lid zijn van de rol [Inzender Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) op abonnements-/resourcegroepsniveau.  
3. De volgende zaken moeten op uw computer zijn geïnstalleerd:
   * Visual Studio 2013 of Visual Studio 2015
   * Download de Azure SDK voor Visual Studio 2013 of Visual Studio 2015. Ga naar de [Azure-downloadpagina](https://azure.microsoft.com/downloads/) en klik in het gedeelte **.NET** op **VS 2013** of **VS 2015**.
   * Download de nieuwste Azure Data Factory-invoegtoepassing voor Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) of [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). U kunt de invoegtoepassing ook bijwerken door de volgende stappen uit te voeren: Klik in het menu op **Extra** -> **Extensies en updates** -> **Online** -> **Visual Studio-galerie** -> **Microsoft Azure Data Factory-hulpprogramma's voor Visual Studio** -> **Bijwerken**.

Nu gaan we met Visual Studio een Azure-gegevensfactory maken.

### <a name="create-visual-studio-project"></a>Een Visual Studio-project maken
1. Open **Visual Studio 2013** of **Visual Studio 2015**. Klik op **File**, houd de muisaanwijzer op **New** en klik op **Project**. Het dialoogvenster **New Project** wordt weergegeven.  
2. Selecteer in het dialoogvenster **New Project** de sjabloon **DataFactory** en klik op **Empty Data Factory Project**.   

    ![Het dialoogvenster New Project](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Geef een **naam** op voor het project, geef een **locatie** op en geef een naam op voor de **oplossing**. Klik vervolgens op **OK**.

    ![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Gekoppelde services maken
In deze stap maakt u twee gekoppelde services: **Azure Storage** en **HDInsight on-demand**. 

De gekoppelde service Azure Storage koppelt uw Azure Storage-account aan de data factory door de verbindingsgegevens te verstrekken. De Data Factory-service gebruikt de verbindingsreeks van de gekoppelde service-instelling om in runtime verbinding te maken met de Azure-opslag. Deze opslag bevat invoer- en uitvoergegevens voor de pijplijn, en het Hive-scriptbestand dat door de Hive-activiteit wordt gebruikt. 

Met de gekoppelde service HDInsight op aanvraag wordt het HDInsight-cluster automatisch in runtime gemaakt wanneer de invoergegevens gereed zijn om te worden verwerkt. Het cluster wordt automatisch verwijderd wanneer de verwerking is voltooid en het gedurende een opgegeven tijd inactief is. 

> [!NOTE]
> U maakt een data factory door de naam en instellingen ervan op te geven op het moment dat uw Data Factory-oplossing wordt gepubliceerd.

#### <a name="create-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken
1. Klik in Solution Explorer met de rechtermuisknop op **Linked Services**. Houd de muisaanwijzer op **Add** en klik op **New Item**.      
2. Selecteer in het dialoogvenster **Add New Item** de optie **Azure Storage Linked Service** in de lijst en klik op **Add**.
    ![Schermopname met een Azure Storage gekoppelde service gemarkeerd in de lijst.](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. Vervang `<accountname>` en `<accountkey>` door de naam van uw Azure-opslagaccount en de bijbehorende sleutel. Zie [Toegangssleutels voor opslagaccounts beheren](../../storage/common/storage-account-keys-manage.md) voor informatie over het ophalen van uw toegangssleutel voor opslag.
    ![Gekoppelde Azure Storage-service](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Sla het bestand **AzureStorageLinkedService1.json** op.

#### <a name="create-azure-hdinsight-linked-service"></a>Een gekoppelde HDInsight-service maken
1. Klik in **Solution Explorer** met de rechtermuisknop op **Linked Services**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
2. Selecteer **HDInsight On Demand Linked Service** en klik op **Add**.
3. Vervang de **JSON** door de volgende JSON:

     ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

    Eigenschap | Beschrijving
    -------- | ----------- 
    ClusterSize | De grootte van het HDInsight Hadoop-cluster.
    TimeToLive | Geeft aan hoelang het HDInsight-cluster inactief moet zijn voordat het wordt verwijderd.
    linkedServiceName | Het opslagaccount dat wordt gebruikt om de logboeken op te slaan die door het HDInsight Hadoop-cluster worden gegenereerd. 

    > [!IMPORTANT]
    > Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (linkedServiceName). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit gedrag is standaard. Met de gekoppelde service HDInsight op aanvraag wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment wordt verwerkt, tenzij er een bestaand livecluster is (timeToLive). Het cluster wordt verwijderd wanneer het verwerken is voltooid.
    > 
    > Naarmate er meer segmenten worden verwerkt, verschijnen er meer containers in uw Azure-blobopslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers volgen een patroon: `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. Gebruik hulpprogramma's zoals [Microsoft Azure Storage Explorer](https://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.

    Zie de [Compute linked services](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Gekoppelde Compute Services) voor meer informatie over JSON-eigenschappen. 
4. Sla het bestand **HDInsightOnDemandLinkedService1.json** op.

### <a name="create-datasets"></a>Gegevenssets maken
In deze stap maakt u gegevenssets die de invoer- en uitvoergegevens voor Hive-verwerking vertegenwoordigen. Deze gegevenssets verwijzen naar de **AzureStorageLinkedService1** die u eerder in deze zelfstudie hebt gemaakt. De gekoppelde service verwijst naar een Azure-opslagaccount en in de gegevenssets vindt u de container, map en bestandsnaam in de opslag van de invoer- en uitvoergegevens.   

#### <a name="create-input-dataset"></a>Invoergegevensset maken
1. Klik in **Solution Explorer** met de rechtermuisknop op **Tables**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
2. Selecteer **Azure Blob** in de lijst, wijzig de naam van het bestand in **InputDataSet.json** en klik op **Add**.
3. Vervang de **JSON** in de editor door het volgende JSON-fragment:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
    In dit JSON-fragment wordt een gegevensset met de naam **AzureBlobInput** gedefinieerd die de invoergegevens representeert voor de Hive-activiteit in de pijplijn. U geeft op dat de invoergegevens zich bevinden in de blobcontainer `adfgetstarted` en de map `inputdata`.

    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

    Eigenschap | Beschrijving |
    -------- | ----------- |
    type |De eigenschap Type wordt ingesteld op **AzureBlob**, omdat de gegevens zich in Azure Blob Storage bevinden.
    linkedServiceName | De AzureStorageLinkedService1 die u eerder hebt gemaakt.
    fileName |Deze eigenschap is optioneel. Als u deze eigenschap niet opgeeft, worden alle bestanden uit folderPath gekozen. In dit geval wordt alleen input.log verwerkt.
    type | Omdat de logboekbestanden tekstbestanden zijn, gebruiken we TextFormat. |
    columnDelimiter | Kolommen in de logboekbestanden worden gescheiden door een komma (`,`)
    frequency/interval | Als frequency wordt ingesteld op Month en de interval 1 is, betekent dat dat de invoersegmenten één keer per maand beschikbaar worden gemaakt.
    external | Deze eigenschap wordt ingesteld op true als de invoergegevens voor de activiteit niet worden gegenereerd door de pijplijn. Deze eigenschap wordt alleen opgegeven voor invoergegevenssets. Stel deze eigenschap voor de invoergegevensset van de eerste activiteit altijd in op true.
4. Sla het bestand **InputDataset.json** op.

#### <a name="create-output-dataset"></a>Uitvoergegevensset maken
U maakt nu de uitvoergegevensset die staat voor uitvoergegevens die worden opgeslagen in de Azure-blobopslag.

1. Klik in **Solution Explorer** met de rechtermuisknop op **Tables**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
2. Selecteer **Azure Blob** in de lijst, wijzig de naam van het bestand in **OutputDataset.json** en klik op **Add**.
3. Vervang de **JSON** in de editor door de volgende JSON:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
    In dit JSON-fragment wordt een gegevensset met de naam **AzureBlobOutput** gedefinieerd die uitvoergegevens representeert die door de Hive-activiteit in de pijplijn zijn geproduceerd. U geeft op dat de uitvoergegevens die door de componentactiviteit worden geproduceerd, in de blobcontainer `adfgetstarted` en de map `partitioneddata` worden geplaatst. 
    
    In het gedeelte **availability** wordt opgegeven dat de uitvoergegevensset op maandelijkse basis wordt geproduceerd. De uitvoergegevensset stuurt het schema van de pijplijn aan. De pijplijn wordt maandelijks tussen de opgegeven begin- en eindtijden uitgevoerd. 

    Raadpleeg het gedeelte **Invoergegevensset maken** voor een beschrijving van deze eigenschappen. U stelt de externe eigenschap niet in op een uitvoergegevensset, omdat de gegevensset wordt geproduceerd door de pijplijn.
4. Sla het bestand **OutputDataset.json** op.

### <a name="create-pipeline"></a>Pijplijn maken
Tot dusver hebt u de gekoppelde service Azure Storage en de invoer- en uitvoergegevenssets gemaakt. Nu gaat u een pijplijn met een **HDInsightHive**-activiteit maken. De **invoer** voor de componentactiviteit is ingesteld op **AzureBlobInput** en de **uitvoer** is ingesteld op **AzureBlobOutput**. Een segment van een invoergegevensset is maandelijks beschikbaar (frequentie: Maand, interval: 1), en het uitvoersegment wordt ook maandelijks geproduceerd. 

1. Klik in **Solution Explorer** met de rechtermuisknop op **Pipelines**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
2. Selecteer **Hive Transformation Pipeline** in de lijst en klik op **Add**.
3. Vervang de **JSON** door het volgende codefragment:

    > [!IMPORTANT]
    > Vervang `<storageaccountname>` door de naam van uw opslagaccount.

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
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
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > Vervang `<storageaccountname>` door de naam van uw opslagaccount.

    In het JSON-codefragment wordt een pijplijn gedefinieerd die uit één activiteit (componentactiviteit) bestaat. Deze activiteit voert een Hive-script uit om invoergegevens op een HDInsight-cluster op aanvraag te transformeren en uitvoergegevens te produceren. In de sectie met activiteiten van de pijplijn-JSON ziet u maar één activiteit in de matrix. Het type ervan is ingesteld op **HDInsightHive**. 

    In de type-eigenschappen die specifiek zijn voor de HDInsight-componentactiviteit, geeft u op welke gekoppelde Azure Storage-service het Hive-scriptbestand bevat, het pad naar het scriptbestand en de parameters naar het scriptbestand. 

    Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het Azure-opslagaccount (opgegeven door de scriptLinkedService) en in de map `script` van de container `adfgetstarted`.

    De sectie `defines` wordt gebruikt om de runtime-instellingen op te geven die worden doorgegeven aan het Hive-script, zoals Hive-configuratiewaarden (bijvoorbeeld `${hiveconf:inputtable}`, `${hiveconf:partitionedtable})`).

    Met de eigenschappen **start** en **end** van de pijplijn wordt opgegeven in welke periode de pijplijn actief is. U hebt de gegevensset geconfigureerd om maandelijks te worden geproduceerd. Daarom wordt er maar één segment door de pijplijn geproduceerd (omdat de maand in de begin- en einddatum hetzelfde is).

    In de activiteits-JSON geeft u op dat het Hive-script wordt uitgevoerd in de berekening die is opgegeven door de **linkedServiceName** – **HDInsightOnDemandLinkedService**.
4. Sla het bestand **HiveActivity1.json** op.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>partitionweblogs.hql en input.log toevoegen als afhankelijkheid
1. Klik in het **Solution Explorer**-venster met de rechtermuisknop op **Dependencies**. Houd de muisaanwijzer op **Add** en klik op **Existing Item**.  
2. Navigeer naar **C:\ADFGettingStarted** en selecteer de bestanden **partitionweblogs.hql** en **input.log**. Klik vervolgens op **Add**. U hebt deze twee bestanden gemaakt als onderdeel van de vereisten in het [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md).

Wanneer u de oplossing in de volgende stap publiceert, wordt het bestand **partitionweblogs.hql** geüpload naar de **script** map in de blobcontainer `adfgetstarted`.   

### <a name="publishdeploy-data-factory-entities"></a>Data Factory-entiteiten publiceren/implementeren
In deze stap publiceert u de Data Factory-entiteiten (gekoppelde services, gegevenssets en pijplijn) in uw project naar de Azure Data Factory-service. Tijdens het publicatieproces geeft u de naam voor de data factory op. 

1. Klik met de rechtermuisknop op het project in Solution Explorer. Klik vervolgens op **Publish**.
2. Als u het dialoogvenster **Sign in to your Microsoft account** ziet, voert u uw referenties in voor het account met het Azure-abonnement en klikt u op **Sign in**.
3. Het volgende dialoogvenster wordt weergegeven:

   ![Het dialoogvenster Publish](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. Op de pagina **Configure data factory** voert u de volgende stappen uit:

    ![Publiceren - Nieuwe data factory-instellingen](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. Selecteer **Create New Data Factory**.
   2. Voer een unieke **naam** in voor de gegevensfactory. Bijvoorbeeld: **DataFactoryUsingVS09152016**. De naam moet wereldwijd uniek zijn.
   3. Selecteer het juiste abonnement voor het veld **Subscription**. 
        > [!IMPORTANT]
        > Als u geen abonnement niet ziet, controleert u of u bent aangemeld met een account dat een beheerder of co-beheerder is van het abonnement.
   4. Selecteer de **resourcegroep** voor de gegevensfactory die u wilt maken.
   5. Selecteer de **regio** voor de gegevensfactory.
   6. Klik op **Next** om over te schakelen naar de pagina **Publish Items**. (Druk op **TAB** als u het veld Naam wilt verlaten, maar de knop **Next** is uitgeschakeld.)

      > [!IMPORTANT]
      > Als u tijdens het publiceren de foutmelding **Data factory name “DataFactoryUsingVS” is not available** ziet, wijzigt u de naam (bijvoorbeeld in yournameDataFactoryUsingVS). Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.   
1. Op de pagina **Publish Items** controleert u of alle Data Factory-entiteiten zijn geselecteerd en klikt u op **Next** om over te schakelen naar de pagina **Summary**.

    ![Pagina Items publiceren](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Controleer de samenvatting en klik op **Next** om te beginnen met het implementatieproces en om de **implementatiestatus** te bekijken.

    ![Overzichtspagina](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. Op de pagina **Deployment Status** ziet u de status van het implementatieproces. Klik op Finish wanneer de implementatie is uitgevoerd.

Belangrijke punten om op te letten:

- Als de volgende foutmelding wordt weergegeven: **Dit abonnement is niet geregistreerd voor gebruik van de naamruimte Microsoft.DataFactory**, voert u een van de volgende stappen uit en probeert u opnieuw te publiceren:
    - Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-provider te registreren.
        ```powershell   
        Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        U kunt de volgende opdracht uitvoeren om te bevestigen dat de Data Factory-provider is geregistreerd.

        ```powershell
        Get-AzResourceProvider
        ```
    - Meld u bij de [Azure Portal](https://portal.azure.com) aan met behulp van het Azure-abonnement en navigeer naar een Data Factory-blade of maak een gegevensfactory in de Azure Portal. Door deze actie wordt de provider automatisch voor u geregistreerd.
- De naam van de gegevensfactory wordt in de toekomst mogelijk geregistreerd als DNS-naam en wordt daarmee ook voor iedereen zichtbaar.
- Als u Data Factory-exemplaren wilt maken, moet u beheerder/co-beheerder van het Azure-abonnement zijn.

### <a name="monitor-pipeline"></a>De pijplijn bewaken
In deze stap bewaakt u de pijplijn in de diagramweergave van de data factory. 

#### <a name="monitor-pipeline-using-diagram-view"></a>De pijplijn bewaken met Diagramweergave
1. Meld u aan bij [Azure Portal](https://portal.azure.com/) en doorloop de volgende stappen:
   1. Klik op **Meer services** en op **Gegevensfactory's**.
       
        ![Door gegevensfactory’s bladeren](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Selecteer de naam van uw gegevensfactory (bijvoorbeeld: **DataFactoryUsingVS09152016** in de lijst met gegevensfactory’s.
   
       ![Uw gegevensfactory selecteren](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. Klik op de startpagina van uw gegevensfactory op **Diagram**.

    ![Tegel Diagram](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. In de diagramweergave ziet u een overzicht van de pijplijnen en gegevenssets die voor deze zelfstudie worden gebruikt.

    ![Diagramweergave](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. Als u alle activiteiten in de pijplijn wilt weergeven, klikt u met de rechtermuisknop op de pijplijn in het diagram. Klik vervolgens op Pijplijn openen.

    ![Menu Pijplijn openen](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Bevestig dat u de HDInsightHive-activiteit in de pijplijn ziet.

    ![Pijplijnweergave openen](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Als u naar de vorige weergave wilt terugkeren, klikt u op **Gegevensfactory** in het koppelingenmenu bovenaan.
6. Dubbelklik in de **diagramweergave** op de gegevensset **AzureBlobInput**. Controleer of het segment de status **Gereed** heeft. Het kan enkele minuten duren voordat het segment de status Gereed heeft. Als dit niet is gebeurd nadat u enige tijd hebt gewacht, controleert u of het invoerbestand (input.log) in de juiste container (`adfgetstarted`) en in de juiste map (`inputdata`) staat. Zorg er ook voor dat de eigenschap **extern** voor de invoergegevensset is ingesteld op **true**. 

   ![Invoersegment met de status Gereed](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Klik op **X** om de blade **AzureBlobInput** te sluiten.
8. Dubbelklik in de **diagramweergave** op de gegevensset **AzureBlobOutput**. U ziet het segment dat momenteel wordt verwerkt.

   ![Schermopname met daarin gemarkeerd de gegevensset van AzureBlobOutput.](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Als het verwerken is voltooid, ziet u dat het segment de status **Gereed** heeft.

   > [!IMPORTANT]
   > Het maken van een on-demand HDInsight-cluster duurt normaal gesproken enige tijd (ongeveer 20 minuten). Daarom kunt u ervan uitgaan dat het **ongeveer 30 minuten** duurt voordat het segment in de pijplijn is verwerkt.  
   
    ![Gegevensset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. Wanneer het segment de status **Gereed** heeft, controleert u de map `partitioneddata` in de container `adfgetstarted` in uw blobopslag voor de uitvoergegevens.  

    ![Uitvoergegevens](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Klik op het segment voor details van het segment in de blade **Gegevenssegment**.

    ![Details gegevenssegment](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Klik in de lijst **Uitvoeringen van activiteit** op een activiteit die wordt uitgevoerd om details van een bepaalde activiteit die wordt uitgevoerd (Hive-activiteit in ons scenario) te bekijken in het venster **Details uitvoering van activiteit**. 
  
    ![Details uitvoering van activiteit](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    In de logboekbestanden ziet u de Hive-query die is uitgevoerd en de statusinformatie. Deze logboeken komen van pas bij het oplossen van problemen.  

Zie [Gegevenssets en pijplijn bewaken](data-factory-monitor-manage-pipelines.md) voor instructies over het gebruik van de Azure Portal om de pijplijn en gegevenssets te bewaken die u tijdens deze zelfstudie hebt gemaakt.

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>De pijplijn bewaken met de app Bewaking en beheer
U kunt de toepassing Bewaking en beheer ook gebruiken om uw pijplijnen te bewaken. Zie [Azure Data Factory-pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van deze toepassing.

1. Klik op de tegel Bewaking en beheer.

    ![De tegel Bewaking en beheer](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. De toepassing Bewaking en beheer wordt weergegeven. Wijzig **Begintijd** en **Eindtijd**, zodat deze overeenkomen met de starttijd (04-01-2016 12:00 uur) en de eindtijd (04-02-2016 12:00 uur) van uw pijplijn. Klik op **Toepassen**.

    ![De app Bewaking en beheer](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Als u de details van een activiteitsvenster wilt bekijken, selecteert u het venster in de **lijst met activiteitsvensters**.
    ![Details van activiteitsvenster](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> Het invoerbestand wordt verwijderd zodra het segment is verwerkt. Als u het segment dus opnieuw wilt uitvoeren of als u de zelfstudie opnieuw wilt doorlopen, uploadt u het invoerbestand (input.log) naar de map `inputdata` van de container `adfgetstarted`.

### <a name="additional-notes"></a>Aanvullende opmerkingen
- Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Bijvoorbeeld een kopieeractiviteit om gegevens van een bron- naar een doelgegevensopslagplaats te kopiëren en een HDInsight Hive-activiteit om een Hive-script uit te voeren voor het transformeren van invoergegevens. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor alle bronnen en sinks die worden ondersteund door de kopieerbewerking. Zie [Gekoppelde services berekenen](data-factory-compute-linked-services.md) voor de lijst met compute-services die worden ondersteund door Data Factory.
- Met gekoppelde services worden gegevensarchieven of compute-services gekoppeld aan een Azure-gegevensfactory. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor alle bronnen en sinks die worden ondersteund door de kopieerbewerking. Zie [Gekoppelde Compute Services](data-factory-compute-linked-services.md) voor de lijst met Compute Services die worden ondersteund door Data Factory en de [transformatieactiviteiten](data-factory-data-transformation-activities.md) die daarop kunnen worden uitgevoerd.
- Zie [Move data from/to Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) (Gegevens van en naar Azure Blob verplaatsen) voor meer informatie over de JSON-eigenschappen die worden gebruikt in de definitie van de gekoppelde Azure Storage-service.
- U kunt uw eigen HDInsight-cluster gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde services berekenen](data-factory-compute-linked-services.md) voor meer informatie.
-  Data Factory maakt voor u een HDInsight-cluster **op basis van Linux** met de eerder vermelde JSON. Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie.
- Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (linkedServiceName). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit gedrag is standaard. Met de gekoppelde service HDInsight op aanvraag wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment wordt verwerkt, tenzij er een bestaand livecluster is (timeToLive). Het cluster wordt verwijderd wanneer het verwerken is voltooid.
    
    Naarmate er meer segmenten worden verwerkt, verschijnen er meer containers in uw Azure-blobopslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers volgen een patroon: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Gebruik hulpprogramma's zoals [Microsoft Azure Storage Explorer](https://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.
- Op dit moment wordt de planning gebaseerd op de uitvoergegevensset. Daarom moet u ook een uitvoergegevensset maken als er tijdens de activiteit geen uitvoer wordt geproduceerd. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan. 
- In deze zelfstudie wordt niet getoond hoe u gegevens met Azure Data Factory kopieert. Meer informatie over het kopiëren van gegevens met behulp van Azure Data Factory vindt u in [Tutorial: Copy data from Blob storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Zelfstudie: gegevens kopiëren vanuit blobopslag naar SQL Database).


## <a name="use-server-explorer-to-view-data-factories"></a>Server Explorer gebruiken om gegevensfactory’s weer te geven
1. Klik in het menu van **Visual Studio** op **View** en vervolgens op **Server Explorer**.
2. Vouw in het Server Explorer-venster **Azure** en **Data Factory** uit. Wanneer u **Sign in to Visual Studio** ziet, voert u het **account** in dat aan uw Azure-abonnement is gekoppeld, en klikt u op **Continue**. Voer het **wachtwoord** in en klik op **Sign in**. Visual Studio haalt informatie op uit alle Azure Data Factory’s in uw abonnement. U ziet de status van deze bewerking in het venster **Data Factory Task List**.

    ![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Klik met de rechtermuisknop op een gegevensfactory en selecteer **Export Data Factory to New Project** om een Visual Studio-project te maken op basis van een bestaande gegevensfactory.

    ![Een gegevensfactory exporteren](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Data Factory-hulpprogramma's voor Visual Studio bijwerken
Voer de volgende stappen uit om Azure Data Factory-hulpprogramma's voor Visual Studio bij te werken:

1. Klik in het menu op **Extra** en selecteer **Extensies en updates**.
2. Selecteer **Updates** in het linkerdeelvenster en selecteer vervolgens **Visual Studio-galerie**.
3. Selecteer **Azure Data Factory-hulpprogramma's voor Visual Studio** en klik op **Bijwerken**. Als u deze vermelding niet ziet, beschikt u al over de nieuwste versie van de hulpprogramma's.

## <a name="use-configuration-files"></a>Configuratiebestanden gebruiken
U kunt in Visual Studio configuratiebestanden gebruiken om de eigenschappen voor gekoppelde services/tabellen/pijplijnen anders te configureren voor elke omgeving.

Overweeg de volgende JSON-definitie te gebruiken voor een gekoppelde Azure Storage-service. Geef **connectionString** op met verschillende waarden voor accountname en accountkey op basis van de omgeving (ontwikkeling/tests/productie) waarin u Data Factory-entiteiten implementeert. U kunt dit gedrag bewerkstelligen door een afzonderlijk configuratiebestand te gebruiken voor elke omgeving.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Een configuratiebestand toevoegen
Voeg een configuratiebestand voor elke omgeving toe door de volgende stappen uit te voeren:   

1. Klik met de rechtermuisknop op het Data Factory-project in uw Visual Studio-oplossing, houd de muisaanwijzer op **Add** en klik op **New item**.
2. Selecteer in de lijst met geïnstalleerde sjablonen aan de linkerkant de optie **Config**, selecteer **Configuration File**, voer een **naam** in voor het configuratiebestand en klik op **Add**.

    ![Een configuratiebestand toevoegen](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Voeg configuratieparameters en de bijbehorende waarden toe in de volgende indeling:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    In dit voorbeeld configureert u de eigenschap connectionString van een gekoppelde Azure Storage-service en een gekoppelde Azure SQL-service. De syntaxis voor het opgeven van de naam is [JsonPath](https://goessner.net/articles/JsonPath/).   

    Als JSON een eigenschap heeft met een matrix van waarden zoals in de volgende code wordt weergegeven:  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Eigenschappen configureren zoals wordt weergegeven in het volgende configuratiebestand (gebruik op nul gebaseerde indexering):

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Eigenschapnamen met spaties
Als de naam van een eigenschap spaties bevat, gebruikt u vierkante haken, zoals in het volgende voorbeeld wordt weergegeven (databaseservernaam):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Een oplossing implementeren met behulp van een configuratie
Wanneer u Azure Data Factory-entiteiten publiceert in de VS, kunt u opgeven welke configuratie u voor die publicatiebewerking wilt gebruiken.

Entiteiten publiceren in een Azure Data Factory-project via een configuratiebestand:   

1. Klik met de rechtermuisknop op het Data Factory-project en klik op **Publish** om het dialoogvenster **Publish Items** weer te geven.
2. Selecteer een bestaande gegevensfactory of geef op de pagina **Configure data factory** waarden op voor het maken van een nieuwe gegevensfactory. Klik vervolgens op **Next**.   
3. Op de pagina **Publish Items**: u ziet een vervolgkeuzelijst met beschikbare configuraties voor het veld **Select Deployment Config**.

    ![Een configuratiebestand selecteren](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Selecteer het **configuratiebestand** dat u wilt gebruiken en klik op **Next**.
5. Controleer of u de naam van het JSON-bestand ziet op de pagina **Summary** en klik op **Next**.
6. Klik op **Finish** nadat de implementatiebewerking is voltooid.

Tijdens de implementatie worden de waarden van het configuratiebestand gebruikt voor de eigenschappen in de JSON-bestanden voor Data Factory-entiteiten voordat de entiteiten worden geïmplementeerd in de Azure Data Factory-service.   

## <a name="use-azure-key-vault"></a>Azure Key Vault gebruiken
Het wordt niet aangeraden en het is vaak in strijd met het beveiligingsbeleid om gevoelige gegevens, zoals verbindingsreeksen, op te slaan in de codeopslagplaats. Zie het voorbeeld [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish) op GitHub voor meer informatie over de opslag van vertrouwelijke gegevens in Azure Key Vault en het gebruik daarvan tijdens de publicatie van Data Factory-entiteiten. Met de extensie Secure Publish voor Visual Studio kunnen de geheimen worden opgeslagen in Key Vault en worden alleen verwijzingen naar deze geheimen opgegeven in de gekoppelde services/implementatieconfiguraties. Deze verwijzingen worden opgelost wanneer u Data Factory-entiteiten publiceert naar Azure. Deze bestanden kunnen vervolgens worden doorgevoerd naar een bronopslagplaats zonder dat er geheimen worden weergegeven.

## <a name="summary"></a>Samenvatting
In deze zelfstudie hebt u een Azure-gegevensfactory gemaakt voor het verwerken van gegevens. Dit hebt u gedaan door Hive-script uit te voeren op een HDInsight Hadoop-cluster. U hebt in de Azure Portal de Data Factory-editor gebruikt om de volgende stappen uit te voeren:  

1. U hebt een Azure-**gegevensfactory** gemaakt.
2. U hebt twee **gekoppelde services** gemaakt:
   1. Een gekoppelde **Azure Storage**-service om te koppelen aan de Azure-blobopslag die de invoer-/uitvoerbestanden van de gegevensfactory bevat.
   2. Een gekoppelde on-demand **Azure HDInsight**-service om te koppelen aan een on-demand HDInsight Hadoop-cluster van de gegevensfactory. Azure Data Factory maakt op tijd een HDInsight Hadoop-cluster om invoergegevens te verwerken en uitvoergegevens te produceren.
3. U hebt twee **gegevenssets** gemaakt, waarin de invoer- en uitvoergegevens van de HDInsight Hive-activiteit in de pijplijn worden beschreven.
4. U hebt een **pijplijn** gemaakt met een **HDInsight Hive**-activiteit.  

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een pijplijn gemaakt met een transformatieactiviteit (HDInsight-activiteit) waarvoor een Hive-script wordt uitgevoerd op een on-demand HDInsight-cluster. Meer informatie over het gebruiken van een kopieeractiviteit om gegevens van een Azure-blob te kopiëren naar Azure SQL vindt u in [Zelfstudie: gegevens van een Azure-blob kopiëren naar Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

U kunt twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md) voor gedetailleerde informatie. 


## <a name="see-also"></a>Zie ook

| Onderwerp | Beschrijving |
|:--- |:--- |
| [Pijplijnen](data-factory-create-pipelines.md) |Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) |Op basis van dit artikel krijgt u inzicht in de gegevenssets in Azure Data Factory. |
| [Activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) |Dit artikel bevat een lijst van activiteiten voor gegevenstransformatie (zoals de HDInsight Hive-transformatie die u in deze zelfstudie hebt gebruikt) die door Azure Data Factory worden ondersteund. |
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) |In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. |
| [Pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) |In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app voor bewaking en beheer. |
