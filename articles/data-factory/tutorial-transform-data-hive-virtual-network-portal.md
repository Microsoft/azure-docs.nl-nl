---
title: Gegevens met behulp van Hive in een virtueel Azure-netwerk transformeren met Azure Portal
description: Deze zelfstudie biedt stapsgewijze instructies voor het transformeren van gegevens met behulp van Hive-activiteit in Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/04/2018
ms.openlocfilehash: 4c8ae67720cf6ac9d577286898b95cdd10f38152
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377955"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory-using-the-azure-portal"></a>Gegevens in een virtueel Azure-netwerk transformeren met behulp van Hive-activiteit in Azure Data Factory met Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie gebruikt u Azure Portal om een Data Factory-pijplijn te maken waarmee gegevens worden getransformeerd met behulp van Hive-activiteit in een HDInsight-cluster in een virtueel Azure-netwerk (VNet). In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken. 
> * Een zelf-hostende Integration Runtime maken
> * Gekoppelde Azure Storage- en Azure HDInsight-services maken
> * Een pijplijn maken met Hive-activiteit.
> * Een pijplijnuitvoering activeren.
> * De pijplijnuitvoering controleren. 
> * De uitvoer controleren

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Een Azure Storage-account**. U maakt een Hive-script en uploadt dit script naar de Azure-opslag. De uitvoer van het Hive-script wordt opgeslagen in dit opslagaccount. In dit voorbeeld gebruikt het HDInsight-cluster dit Azure Storage-account als primaire opslag. 
- **Een virtueel Azure-netwerk.** Als u geen virtueel Azure-netwerk hebt, maakt u er een door [deze instructies](../virtual-network/quick-create-portal.md) te volgen. In dit voorbeeld bevindt HDInsight zich in een virtueel Azure-netwerk. Hier volgt een voorbeeldconfiguratie van een virtueel Azure-netwerk. 

    ![Virtueel netwerk maken](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **HDInsight-cluster.** Maak een HDInsight-cluster en koppel dit aan het virtuele netwerk dat u in de vorige stap hebt gemaakt, door de instructies in dit artikel te volgen: [Azure HDInsight uitbreiden met behulp van een virtueel Azure-netwerk](../hdinsight/hdinsight-plan-virtual-network-deployment.md). Hier volgt een voorbeeldconfiguratie van HDInsight in een virtueel netwerk. 

    ![HDInsight gebruiken in een virtueel netwerk](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. Volg de instructies in [How to install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Azure PowerShell installeren en configureren).
- **Een virtuele machine**. Maak een Azure VM en koppel deze aan hetzelfde virtuele netwerk dat het HDInsight-cluster bevat. Zie [Virtuele machines maken](../virtual-network/quick-create-portal.md#create-virtual-machines) voor meer informatie. 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hive-script uploaden naar het Blob-opslagaccount

1. Maak een Hive SQL-bestand met de naam **hivescript.hql** met de volgende inhoud:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Maak in de Azure Blob-opslag een container met de naam **adftutorial** als deze nog niet bestaat.
3. Maak een map met de naam **hivescripts**.
4. Upload het bestand **hivescript.hql** naar de submap **hivescripts**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).    
2. Klik op **Nieuw** in het linkermenu en klik vervolgens op **Gegevens en analyses** en **Data Factory**. 
   
   ![Nieuw -> DataFactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. Voer op de pagina **New data factory** **ADFTutorialHiveFactory** in bij **Name**. 
      
     ![De pagina Nieuwe data factory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als u het volgende foutbericht krijgt, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamMyAzureSsisDataFactory) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
    *De data factory-naam 'MyAzureSsisDataFactory' is niet beschikbaar*
3. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
4. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
   - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
   - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
4. Selecteer **V2** als de **versie**.
5. Selecteer de **locatie** voor de gegevensfactory. In de lijst zie u alleen locaties die worden ondersteund voor het maken van gegevensfactory’s.
6. Selecteer **Vastmaken aan dashboard**.     
7. Klik op **Create**.
8. Op het dashboard ziet u de volgende tegel met de status: **Gegevensfactory implementeren**. 

     ![tegel met de status 'gegevensfactory implementeren'](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding.
   
    ![Startpagina van de gegevensfactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Klik op **Maken en controleren** om de gebruikersinterface (UI) van Azure Data Factory te openen op een afzonderlijk tabblad.
11. Ga op de pagina **aan de slag** naar het tabblad **Bewerken** in het linkervenster, zoals wordt weergegeven in de volgende afbeelding: 

    ![Tabblad Bewerken](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Een zelf-hostende Integration Runtime maken
Als de Hadoop-cluster zich in een virtueel netwerk bevindt, moet u een zelf-hostende Integration Runtime (IR) in hetzelfde virtuele netwerk installeren. In deze sectie maakt u een nieuwe virtuele machine die u aan hetzelfde virtuele netwerk toevoegt en waarop u een zelf-hostende IR installeert. Met de zelf-hostende IR kan de Data Factory-service verwerkingsaanvragen binnen een virtueel netwerk verzenden naar een rekenresource zoals HDInsight. Ook kunt u hiermee gegevens uit of naar gegevensarchieven binnen een virtueel netwerk verplaatsen naar Azure. U gebruikt een zelf-hostende IR wanneer ook het gegevensarchief of de rekenresource zich in een on-premises-omgeving bevindt. 

1. Klik in de UI van Azure Data Factory op **Connections** onderaan het venster, ga naar het tabblad **Integration Runtimes** en klik op de knop **+ New** op de werkbalk. 

   ![Menu nieuwe integratieruntime](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. In het venster **Integration Runtime Setup** selecteert u de optie **Perform data movement and dispatch activities to external computes** en klikt u op **Next**. 

   ![Selecteer optie Perform data movement and dispatch activities to external computes](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Selecteer **Privénetwerk** en klik op **Volgende**.
    
   ![Privénetwerk selecteren](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Voer **MySelfHostedIR** in bij **Name** en klik op **Next**. 

   ![Naam van de integratieruntime opgeven](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Kopieer de **verificatiesleutel** voor de Integration Runtime door te klikken op de knop voor kopiëren en sla de sleutel op. Houd het venster geopend. U gebruikt deze sleutel om de in een virtuele machine geïnstalleerde IR te registreren. 

   ![Verificatiesleutel kopiëren](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>IR installeren op een virtuele machine

1. Download de [zelf-hostende integratieruntime](https://www.microsoft.com/download/details.aspx?id=39717) op de virtuele Azure-machine. Gebruik de in de vorige stap verkregen **verificatiesleutel** om de zelf-hostende Integration Runtime handmatig te registreren. 

    ![Integratieruntime registreren](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. U ziet het volgende bericht wanneer de zelf-hostende Integration Runtime is geregistreerd. 
   
    ![Registratie is voltooid](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Klik op **Launch Configuration Manager**. Wanneer het knooppunt is verbonden met de cloudservice, ziet u de volgende pagina: 
   
    ![Knooppunt is verbonden](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Zelf-hostende IR in de UI van Azure Data Factory

1. In de **Azure Data Factory UI** moet de naam van de zelf-hostende VM en de status ervan worden weergegeven.

   ![Bestaande zelf-hostende knooppunten](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Klik op **Finish** om het venster **Integration Runtime Setup** te sluiten. De zelf-hostende IR wordt vermeld in de lijst met integratieruntimes.

   ![Zelf-hostende IR in de lijst](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Gekoppelde services maken

In deze sectie maakt en implementeert u twee gekoppelde services:
- Een **gekoppelde Azure Storage-service** waarmee een Azure Storage-account wordt gekoppeld aan de gegevensfactory. Deze opslag is de primaire opslag die wordt gebruikt voor het HDInsight-cluster. In dit geval gebruikt u dit Azure Storage-account ook om het Hive-script en de uitvoer van het script op te slaan.
- Een **gekoppelde HDInsight-service**. Het Hive-script wordt via Azure Data Factory naar dit HDInsight-cluster verzonden om te worden uitgevoerd.

### <a name="create-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

1. Open het tabblad **New Linked Service** en klik op **New**.

   ![Knop Nieuwe gekoppelde service](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. In het venster **New Linked Service** selecteert u **Azure Blob Storage** en klikt u op **Continue**. 

   ![Azure Blob Storage selecteren](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

    1. Voer **AzureStorageLinkedService** in als **Naam**.
    2. Selecteer **MySelfHostedIR** bij **Connect via integration runtime**.
    3. Selecteer uw Azure storage-account als **Storage account name**. 
    4. Als u de verbinding met de storage-account wilt testen, klikt u op **Test connection**.
    5. Klik op **Opslaan**.
   
        ![Azure Blob Storage-account opgeven](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>Gekoppelde HDInsight-service maken

1. Klik nogmaals op **New** om een andere gekoppelde service te maken. 
    
   ![Knop Nieuwe gekoppelde service](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Open het tabblad **Compute**, selecteer **Azure HDInsight** en klik op **Continue**.

    ![Azure HDInsight Service selecteren](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

    1. Voer **AzureHDInsightLinkedService** in als **Name**.
    2. Selecteer **Bring your own HDInsight**. 
    3. Selecteer uw HDInsight-cluster bij **Hdi cluster**. 
    4. Voer de **gebruikersnaam** voor het HDInsight-cluster in.
    5. Voer het **wachtwoord** voor de gebruiker in. 
    
        ![Instellingen voor Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

In dit artikel wordt ervan uitgegaan dat u via internet toegang hebt tot het cluster. Bijvoorbeeld, dat u verbinding met het cluster kunt maken op `https://clustername.azurehdinsight.net`. Dit adres maakt gebruik van de openbare gateway. Deze is niet beschikbaar als u NSG's (netwerkbeveiligingsgroepen) of door de gebruiker gedefinieerde routes hebt gebruikt om de toegang via internet te beperken. U moet het virtuele Azure-netwerk zo configureren dat de URL kan worden omgezet in een privé-IP-adres of een privégateway voor HDInsight, om ervoor te zorgen dat Data Factory taken kan verzenden naar het HDInsight-cluster in het virtuele Azure-netwerk.

1. Open in Azure Portal het virtuele netwerk waarin het HDInsight-cluster zich bevindt. Open de netwerkinterface met de naam die begint met `nic-gateway-0`. Noteer het bijbehorende privé IP-adres. Bijvoorbeeld: 10.6.0.15. 
2. Als het virtuele Azure-netwerk een DNS-server heeft, werkt u de DNS-record bij zodat de URL van het HDInsight-cluster `https://<clustername>.azurehdinsight.net` kan worden omgezet in `10.6.0.15`. Als u geen DNS-server in uw virtuele Azure-netwerk hebt, kunt u dit tijdelijk oplossen door het hostbestand (C:\Windows\System32\drivers\etc) te bewerken van alle VM's die als knooppunten van Integration Runtime (zelf-hostend) zijn geregistreerd. Dit doet u door een soortgelijke vermelding als de volgende toe te voegen: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Een pijplijn maken 
In deze stap maakt u een nieuwe pijplijn met een Hive-activiteit. Met deze activiteit wordt een Hive-script uitgevoerd om gegevens uit een voorbeeldtabel te retourneren en op te slaan in een pad dat u hebt gedefinieerd.

Houd rekening met de volgende punten:

- **scriptPath** verwijst naar het pad naar het Hive-script in het Azure Storage-account dat u hebt gebruikt voor MyStorageLinkedService. Het pad is hoofdlettergevoelig.
- **Output** is een argument dat wordt gebruikt in het Hive-script. Gebruik de indeling van `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` om dit argument te laten verwijzen naar een bestaande map in de Azure-opslag. Het pad is hoofdlettergevoelig. 

1. Klik in de Data Factory-gebruikersinterface op **+ (plus)** in het linkerdeelvenster en klik op **Pipeline**. 

    ![Menu Nieuwe pijplijn](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. Vouw **HDInsight** uit in de werkset **Activities** en sleep de activiteit **Hive** naar het ontwerpoppervlak voor pijplijnen. 

    ![Hive-activiteit slepen en neerzetten](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. Ga vanuit het eigenschappenvenster naar het tabblad **HDI Cluster** en selecteer **AzureHDInsightLinkedService** bij **HDInsight Linked Service**.

    ![Gekoppelde HDInsight-service selecteren](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Open het tabblad **Scripts** en voer de volgende stappen uit: 

    1. Selecteer **AzureStorageLinkedService** bij **Script Linked Service**. 
    2. Klik bij **File Path** op **Browse Storage**. 
 
        ![In opslag bladeren](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. Ga in het venster **Choose a file or folder** naar de map **hivescripts** van de container **adftutorial**, selecteer **hivescript.hql** en klik op **Finish**.  
        
        ![Een bestand of map kiezen](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Controleer of **adftutorial/hivescripts/hivescript.hql** wordt weergegeven bij **File Path**.

        ![Scriptinstellingen](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. Op het tabblad **Script** vouwt u de sectie **Advanced** uit. 
    6. Klik op **Auto-fill from script** bij **Parameters**. 
    7. Voer de waarde in voor de **Output**-parameter in de volgende indeling: `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Bijvoorbeeld: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Scriptargumenten](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Als u artefacten naar Data Factory wilt publiceren, klikt u op **Publish**.

    ![Schermopname toont de optie om naar een Data Factory te publiceren.](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren

1. Valideer eerst de pijplijn door te klikken op de knop **Validate** op de werkbalk. Sluit het venster **Pipeline Validation Output** door op de **pijl-rechts (>>)** te klikken. 

    ![Pijplijn valideren](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Klik op Trigger op de werkbalk en vervolgens op Trigger Now om een pijplijnuitvoering te activeren. 

    ![Nu activeren](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet een pijplijn die worden uitgevoerd in de lijst **Pipeline Runs**. 

    ![Pijplijnuitvoeringen controleren](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Als u de lijst wilt vernieuwen, klikt u op **Refresh**.
4. Uitvoeringen van activiteit die aan de pijplijn zijn gekoppeld, kunt u bekijken door te klikken op **View activity runs** in de kolom **Action**. Andere actiekoppelingen zijn voor het stoppen en opnieuw uitvoeren van de pijplijn. 

    ![Uitvoeringen van activiteit bekijken](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. U ziet dat er slechts één activiteit actief is omdat er slechts één activiteit in de pijplijn van het type **HDInsightHive** is. Als u wilt terugkeren naar de vorige weergave, klikt u op de koppeling **Pipelines** bovenaan.

    ![Uitvoering van activiteiten](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Controleer of er een uitvoerbestand in de **outputfolder** van de container **adftutorial** wordt weergegeven. 

    ![Uitvoerbestand](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd: 

> [!div class="checklist"]
> * Een data factory maken. 
> * Een zelf-hostende Integration Runtime maken
> * Gekoppelde Azure Storage- en Azure HDInsight-services maken
> * Een pijplijn maken met Hive-activiteit.
> * Een pijplijnuitvoering activeren.
> * De pijplijnuitvoering controleren. 
> * De uitvoer controleren

Ga naar de volgende zelfstudie voor meer informatie over het transformeren van gegevens met behulp van een Spark-cluster in Azure:

> [!div class="nextstepaction"]
>[Een vertakking en keten maken van een Data Factory-controlestroom](tutorial-control-flow-portal.md)