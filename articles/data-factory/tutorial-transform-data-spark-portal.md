---
title: 'Gegevens transformeren met behulp van Spark in Azure Data Factory '
description: Deze zelfstudie biedt stapsgewijze instructies voor het transformeren van gegevens met behulp van een Spark-activiteit in Azure Data Factory.
ms.service: data-factory
ms.topic: tutorial
author: nabhishek
ms.author: abnarain
ms.date: 01/10/2018
ms.openlocfilehash: 2e2a50a96402f01fe914c79d5257fc5bb4dc57a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377785"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Gegevens transformeren in de cloud met behulp van een Spark-activiteit in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie gebruikt u Azure Portal om een pijplijn voor Azure Data Factory te maken. Deze pijplijn transformeert gegevens met behulp van een Spark-activiteit en een gekoppelde Azure HDInsight-service op aanvraag. 

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken. 
> * Een pijplijn maken die een Spark-activiteit gebruikt.
> * Een pijplijnuitvoering activeren.
> * De pijplijnuitvoering controleert.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure-opslagaccount**. U maakt een Python-script en een invoerbestand, en upload deze naar Azure Storage. De uitvoer van het Spark-programma wordt opgeslagen in dit opslagaccount. Het Spark-cluster op aanvraag gebruikt hetzelfde opslagaccount als de primaire opslag.  

> [!NOTE]
> HdInsight ondersteunt alleen algemene opslagaccounts met de catogorie Standard. Zorg ervoor dat het account niet een premium of alleen-blob opslagaccount is.

* **Azure PowerShell**. Volg de instructies in [How to install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Azure PowerShell installeren en configureren).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Het Python-script uploaden naar het Blob-opslagaccount
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
1. Vervang *&lt;storageAccountName&gt;* door de naam van uw Azure-opslagaccount. Sla het bestand vervolgens op. 
1. Maak in Azure Blob-opslag een container met de naam **adftutorial** als deze nog niet bestaat. 
1. Maak een map met de naam **spark**.
1. Maak in de map **spark** een submap met de naam **script**. 
1. Upload het bestand **WordCount_Spark.py** naar de submap **script**. 


### <a name="upload-the-input-file"></a>Invoerbestand uploaden
1. Maak een bestand met de naam **minecraftstory.txt** met wat tekst. Het Spark-programma telt het aantal woorden in deze tekst. 
1. Maak in de map **spark** een submap met de naam **inputfiles**. 
1. Upload het bestand **minecraftstory.txt** naar de submap **inputfiles**. 

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Selecteer **Nieuw** in het menu links, selecteer **Gegevens en analyses** en selecteer vervolgens **Data Factory**. 
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**. 
      
   ![De pagina Nieuwe data factory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   De naam van de Azure-gegevensfactory moet *wereldwijd uniek* zijn. Als u de volgende fout ziet, wijzigt u de naam van de gegevensfactory. (Gebruik dan bijvoorbeeld **&lt;uwnaam&gt;ADFTutorialDataFactory**). Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor naamgevingsregels voor Data Factory-artefacten.
  
   ![Fout als de naam niet beschikbaar is](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. Selecteer voor **Abonnement** het Azure-abonnement waarin u de gegevensfactory wilt maken. 
1. Voer een van de volgende stappen uit voor **Resourcegroep**:
     
   - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
   - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
   Voor sommige van de stappen in deze snelstartgids wordt ervan uitgegaan dat u voor de resourcegroep de naam **ADFTutorialResourceGroup** gebruikt. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
1. Selecteer **V2** als **Versie**.
1. Selecteer voor **Locatie** een locatie voor de data factory. 

   Voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio's waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensarchieven (zoals Azure Storage en Azure SQL Database) en berekeningen (zoals Azure HDInsight) die Data Factory gebruikt, kunnen zich in andere regio's bevinden.

1. Selecteer **Maken**.

1. Zodra de bewerking is voltooid, ziet u de pagina **Data Factory**. Selecteer de tegel **Maken en controleren** om de gebruikersinterface van Data Factory te openen op een afzonderlijk tabblad.

    ![Startpagina van Data Factory, met de tegel Maken en controleren](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Gekoppelde services maken
In deze sectie maakt u twee gekoppelde services: 
    
- Een **gekoppelde Azure Storage-service** waarmee een Azure-opslagaccount wordt gekoppeld aan de gegevensfactory. Deze opslag wordt gebruikt voor het HDInsight-cluster op aanvraag. De service bevat ook het Spark-script dat moet worden uitgevoerd. 
- Een **gekoppelde HDInsight-service op aanvraag**. Azure Data Factory maakt automatisch een HDInsight-cluster en voert het Spark-programma uit. Het HDInsight-cluster wordt vervolgens verwijderd als het cluster gedurende een vooraf geconfigureerde tijd inactief is geweest. 

### <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

1. Schakel op de pagina **Aan de slag** naar het tabblad **Bewerken** in het linkerpaneel. 

   ![Pagina Aan de slag](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. Selecteer **Verbindingen** onder aan het venster en selecteer **+ Nieuw**. 

   ![Knoppen om een nieuwe verbinding te maken](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. Selecteer in het venster **Nieuwe gekoppelde service** de optie **Gegevensarchief** > **Azure Blob-opslag** en selecteer vervolgens **Doorgaan**. 

   ![Tegel Azure Blob-opslag geselecteerd](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. Selecteer bij **Naam van opslagaccount** de naam in de lijst en selecteer vervolgens **Opslaan**. 

   ![Vak voor het opgeven van de naam van het opslagaccount](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Een gekoppelde HDInsight-service op aanvraag maken

1. Selecteer nogmaals de knop **+ Nieuw** om een andere gekoppelde service te maken. 
1. Selecteer in het venster **Nieuwe gekoppelde service****Compute** > **Azure HDInsight** en selecteer vervolgens **Doorgaan**. 

   ![De tegel 'Azure HDInsight' selecteren](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit: 

   a. Voer bij **Naam****AzureHDInsightLinkedService** in.
   
   b. Controleer of **On-demand HDInsight** is geselecteerd bij **Type**.
   
   c. Selecteer **AzureBlobStorage1** bij **Gekoppelde Azure Storage-service**. U hebt deze gekoppelde service al eerder gemaakt. Als u een andere naam hebt gebruikt, geeft u hier de juiste naam op. 
   
   d. Selecteer **spark** bij **Clustertype**.
   
   e. Voer bij **Service-principal-id** de id in van de service-principal die gemachtigd is om een HDInsight-cluster te maken. 
   
      Deze service-principal moet lid zijn van de rol Inzender van het abonnement of de resourcegroep waarin het cluster is gemaakt. Zie [Een Azure Active Directory-toepassing en service-principal maken](../active-directory/develop/howto-create-service-principal-portal.md) voor meer informatie. De **service-principal-id** is gelijk aan de *toepassings-id* en een **service-principalsleutel** is gelijk aan de waarde voor een *clientgeheim*.
   
   f. Geef bij **Sleutel van service-principal** de sleutel op. 
   
   g. Selecteer bij **Resourcegroep** dezelfde resourcegroep die u hebt gebruikt bij het maken van de gegevensfactory. Het Spark-cluster wordt in deze resourcegroep gemaakt. 
   
   h. Vouw **OS type** uit.
   
   i. Voer een naam in voor de **gebruikersnaam van het cluster**. 
   
   j. Voer het **clusterwachtwoord** voor de gebruiker in. 
   
   k. Selecteer **Finish**. 

   ![Instellingen gekoppelde HDInsight-service](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Voor Azure HDInsight geldt een beperking voor het totale aantal kernen dat u kunt gebruiken in elke Azure-regio die wordt ondersteund. Het HDInsight-cluster voor de gekoppelde HDInsight-service op aanvraag wordt op dezelfde locatie in de Azure Storage gemaakt die wordt gebruikt als primaire opslag. Zorg ervoor dat u voldoende kerngeheugen hebt om het cluster goed te maken. Zie [Clusters instellen in HDInsight met Hadoop, Spark, Kafka en meer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie. 

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer de knop **+** (plusteken) en selecteer vervolgens **Pijplijn** in het menu.

   ![Knoppen voor het maken van een nieuwe pijplijn](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. Vouw in de werkset **Activiteiten****HDInsight** uit. Sleep de activiteit **Spark** vanuit de werkset **Activities** naar het ontwerpoppervlak voor pijplijnen. 

   ![De activiteit Spark slepen](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. Voer de volgende stappen uit in de eigenschappen voor het **Spark**-activiteitvenster onderaan: 

   a. Ga naar het tabblad **HDI-cluster**.
   
   b. Selecteer **AzureHDInsightLinkedService** (gemaakt in de vorige procedure). 
        
   ![De gekoppelde HDInsight-service opgeven](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. Ga naar het tabblad **Script/Jar** en voer de volgende stappen uit: 

   a. Selecteer **AzureBlobStorage1** bij **Taakgekoppelde service**.
   
   b. Selecteer **In opslag bladeren**.

   ![Het Spark-script opgeven op het tabblad 'Script/Jar'](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Ga naar de map **adftutorial/spark/script**, selecteer **WordCount_Spark.py** en selecteer **Voltooien**.      

1. Selecteer op de werkbalk de knop **Valideren** om de pijplijn te valideren. Selecteer de **>>** (pijl-rechts) om het validatievenster te sluiten. 
    
   ![De knop Valideren](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. Selecteer **Alles publiceren**. De gebruikersinterface van Data Factory publiceert entiteiten (gekoppelde services en pijplijn) naar de Azure Data Factory-service. 
    
   ![De knop 'Alles publiceren'](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren
Selecteer op de werkbalk de optie **Trigger toevoegen** en selecteer vervolgens **Nu activeren**. 

![De knoppen Activeren en Nu activeren](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Ga naar het tabblad **Controleren**. Controleer of u een pijplijnuitvoering ziet. Het duurt ongeveer 20 minuten om een Spark-cluster te maken. 
   
1. Selecteer regelmatig **Vernieuwen** om de status van de pijplijnuitvoering te controleren. 

   ![Tabblad voor het controleren van pijplijnuitvoeringen met knop Vernieuwen](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. Selecteer **Uitvoering van activiteiten weergeven** in de kolom **Acties** om de uitvoering van activiteiten weer te geven die zijn gekoppeld aan de pijplijnuitvoering.

   ![Status pijplijnuitvoering](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   Als u wilt terugkeren naar de vorige weergave, selecteert u de koppeling **Alle pijplijnuitvoeringen** bovenaan.

   ![Uitvoering van activiteiten weergeven](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>De uitvoer controleren
Controleer of het uitvoerbestand is gemaakt in de map spark/outputfiles/wordcount van de container adftutorial. 

![Locatie van het uitvoerbestand](./media/tutorial-transform-data-spark-portal/verity-output.png)

Het bestand moet elk woord uit het invoertekstbestand bevatten, plus het aantal keren dat het woord in het bestand voorkomt. Bijvoorbeeld: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Volgende stappen
De pijplijn in dit voorbeeld transformeert gegevens met behulp van een Spark-activiteit en een gekoppelde HDInsight-service op aanvraag. U hebt geleerd hoe u: 

> [!div class="checklist"]
> * Een data factory maken. 
> * Een pijplijn maken die een Spark-activiteit gebruikt.
> * Een pijplijnuitvoering activeren.
> * De pijplijnuitvoering controleert.

Ga naar de volgende zelfstudie voor informatie over het transformeren van gegevens door een Hive-script uit te voeren in een Azure HDInsight-cluster in een virtueel netwerk: 

> [!div class="nextstepaction"]
> [Zelfstudie: gegevens transformeren met behulp van Hive in Azure Virtual Network](tutorial-transform-data-hive-virtual-network-portal.md).





