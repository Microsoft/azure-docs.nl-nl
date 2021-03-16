---
title: 'Zelfstudie: Formuliergegevens bulksgewijs extraheren met behulp van Azure Data Factory - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Stel Azure Data Factory activiteiten in om de training te activeren en uit te voeren op een grote achterstand van documenten.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: lajanuar
ms.openlocfilehash: 0c009a87a5834997cdc489efc75ebb16f9459754
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467099"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>Zelf studie: formulier gegevens bulksgewijs extra heren met behulp van Azure Data Factory

In deze zelfstudie wordt beschreven hoe u met Azure-services een grote batch met formulieren in digitale media kunt opnemen. In de zelf studie ziet u hoe u de gegevens opname van een Azure data Lake of documenten kunt automatiseren in een Azure-SQL database. Met een paar klikken kunt u snel modellen trainen en nieuwe documenten verwerken.

## <a name="business-need"></a>Zakelijke behoefte

De meeste organisaties zijn nu op de hoogte van de waarde van de gegevens die ze hebben in verschillende indelingen (PDF, afbeeldingen, Video's). Ze zijn op zoek naar de best practices en de meeste rendabele manieren om deze activa te digitaliseren.

Daarnaast hebben onze klanten vaak verschillende soorten formulieren die van hun clients en klanten afkomstig zijn. In tegens telling tot de [Quick](./quickstarts/client-library.md)starts ziet u in deze zelf studie hoe u een model automatisch kunt trainen met nieuwe en verschillende soorten formulieren door gebruik te maken van een door Meta gegevensgestuurde benadering. Als u geen bestaand model voor het opgegeven formulier type hebt, maakt het systeem er een en geeft u de model-ID. 

Door de gegevens uit formulieren te extraheren en deze te combineren met bestaande operationele systemen en datawarehouses, kunnen bedrijven inzicht krijgen in en waarde leveren voor hun klanten en zakelijke gebruikers.

Azure Form Recognizer helpt organisaties hun gegevens te gebruiken, processen te automatiseren (factuur betalingen, belasting verwerking, enzovoort), Bespaar geld en tijd en profiteer van een betere nauw keurigheid van de gegevens.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel Azure Data Lake in om uw formulieren op te slaan.
> * Een Azure-Data Base gebruiken om een parametrization-tabel te maken.
> * Gebruik Azure Key Vault om gevoelige referenties op te slaan.
> * Train uw formulier Recognizer-model in een Azure Databricks-notebook.
> * Pak uw formulier gegevens uit met behulp van een Databricks-notebook.
> * Automatiseer formulier training en-extractie met behulp van Azure Data Factory.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/).
* Nadat u uw Azure-abonnement hebt <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" gemaakt, maakt u een formulier Recognizer resource "  target="_blank"> een formulier herkenner maken </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de resource is geïmplementeerd, selecteert u **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Form Recognizer-API. Verderop in deze Quick start gaat u uw sleutel en eind punt in uw code plakken.
    * U kunt de gratis prijs categorie (F0) gebruiken om de service te proberen. U kunt later een upgrade uitvoeren naar een betaalde laag voor productie.
* Een set van minimaal vijf formulieren van hetzelfde type. Idealiter is deze werkstroom bedoeld voor grote verzamelingen documenten. Zie [een trainings gegevensset maken](./build-training-data-set.md) voor tips en opties voor het samen stellen van uw trainings gegevensset. Voor deze zelf studie kunt u de bestanden in de map Train van de voor [beeld-gegevensset](https://go.microsoft.com/fwlink/?linkid=2128080)gebruiken.


## <a name="project-architecture"></a>Projectarchitectuur 

Dit project is een set Azure Data Factory-pijp lijnen voor het activeren van python-notebooks die gegevens uit documenten in een Azure Data Lake Storage-account trainen, analyseren en ophalen.

De REST API voor het herkennen van formulieren vereist sommige para meters als invoer. Uit veiligheids overwegingen worden sommige van deze para meters opgeslagen in een Azure-sleutel kluis. Andere, minder gevoelige para meters, zoals de naam van de opslag-blob, worden opgeslagen in een parameterisering-tabel in een Azure-SQL database.

Voor elk type formulier dat wordt geanalyseerd, vullen gegevens technici of gegevens wetenschappers een rij van de parameter tabel in. Vervolgens gebruiken ze Azure Data Factory om de lijst met gedetecteerde formulier typen te herhalen en de relevante para meters door te geven aan een Databricks-notebook om de formulier Recognizer-modellen te trainen of opnieuw te trainen. Hier kan ook een Azure-functie worden gebruikt.

De Azure Databricks-notebook gebruikt vervolgens de getrainde modellen om formulier gegevens te extra heren. De gegevens worden geëxporteerd naar een Azure-SQL database.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="Diagram waarin de project architectuur wordt weer gegeven.":::


## <a name="set-up-azure-data-lake"></a>Azure Data Lake instellen

Uw achterstand van formulieren is mogelijk in uw on-premises omgeving of op een FTP-server (s). In deze zelf studie worden formulieren in een Azure Data Lake Storage Gen2-account gebruikt. U kunt uw bestanden daar overzetten met behulp van Azure Data Factory, Azure Storage Explorer of AzCopy. De gegevens sets training en Score kunnen zich in verschillende containers bevinden, maar de trainings gegevens sets voor alle formulier typen moeten zich in dezelfde container bevinden. (Ze kunnen zich in verschillende mappen bevinden.)

Als u een nieuwe data Lake wilt maken, volgt u de instructies in [een opslag account maken voor gebruik met Azure data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

## <a name="create-a-parameterization-table"></a>Een parametriseringstabel maken

Vervolgens maken we een meta gegevens tabel in een Azure-SQL database. Deze tabel bevat de niet-gevoelige gegevens die nodig zijn voor de REST API van Form Recognizer. Wanneer er een nieuw type formulier in de gegevensset is, wordt er een nieuwe record in deze tabel ingevoegd en worden de trainings-en Score pijplijnen geactiveerd. (Deze pijp lijnen worden later geïmplementeerd.)

Deze velden worden gebruikt in de tabel:

* **form_description**. Niet vereist als onderdeel van de training. Dit veld bevat een beschrijving van het type formulieren waarmee het model wordt getraind (bijvoorbeeld ' client A Forms, ' "Hotel B Forms ').
* **training_container_name**. De naam van de container van het opslag account waar de trainings gegevensset is opgeslagen. Dit kan dezelfde container zijn als **scoring_container_name**.
* **training_blob_root_folder**. De map in het opslag account waar de bestanden worden opgeslagen voor de training van het model.
* **scoring_container_name**. De naam van de container van het opslag account waar de bestanden zijn opgeslagen waarvan we de sleutel/waarde-paren willen extra heren. Dit kan dezelfde container zijn als **training_container_name**.
* **scoring_input_blob_folder**. De map in het opslag account waar de bestanden worden opgeslagen waaruit de gegevens moeten worden opgehaald.
* **model_id**. De ID van het model dat we willen opnieuw trainen. Voor de eerste uitvoering moet de waarde worden ingesteld op-1, waardoor de training notebook een nieuw aangepast model maakt om te trainen. De Oefen notitieblok retourneert de nieuwe model-ID naar het Azure Data Factory-exemplaar. Door gebruik te maken van een opgeslagen procedure-activiteit wordt deze waarde in de Azure-SQL database bijgewerkt.

  Wanneer u een nieuw formulier type wilt opnemen, moet u de model-ID hand matig opnieuw instellen op-1 voordat u het model traint.

* **FILE_TYPE**. Ondersteunde formulier typen zijn `application/pdf` , `image/jpeg` , en `image/png` `image/tif` .

  Als u formulieren in andere bestands typen hebt, moet u deze waarde wijzigen en **model_id** wanneer u een nieuw formulier type traint.
* **form_batch_group_id**. In de loop van de tijd hebt u mogelijk meerdere formulier typen die u met hetzelfde model traint. In het veld **form_batch_group_id** kunt u alle formulier typen opgeven die zijn getraind met een specifiek model.

### <a name="create-the-table"></a>De tabel maken


[Maak een Azure-SQL database](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)en voer dit SQL-script uit in de [query-editor](../../azure-sql/database/connect-query-portal.md) om de vereiste tabel te maken:


```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Voer dit script uit om de procedure te maken die **model_id** na het trainen automatisch bijwerkt.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Azure Key Vault gebruiken om gevoelige referenties op te slaan

Uit veiligheidsoverwegingen willen we bepaalde gevoelige gegevens niet in de parametriseringstabel in de Azure SQL-database opslaan. Gevoelige parameters worden opgeslagen als Azure Key Vault-geheimen.

### <a name="create-an-azure-key-vault"></a>Een Azure-sleutel kluis maken

[Maak een Key Vault-resource](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Ga vervolgens naar de Key Vault resource nadat deze is gemaakt en selecteer in de sectie **instellingen** de optie **geheimen** om de para meters toe te voegen.

Er wordt een nieuw venster weergegeven. Selecteer **genereren/importeren**. Voer de naam van de para meter en de waarde in en selecteer vervolgens **maken**. Voltooi deze stappen voor de volgende para meters:

* **CognitiveServiceEndpoint**. De eind punt-URL van de API voor formulier herkenning.
* **CognitiveServiceSubscriptionKey**. De toegangs sleutel voor de formulier Recognizer-service. 
* **StorageAccountName**. Het opslag account waarin de gegevensset van de training en de formulieren waarvan u de sleutel/waarde-paren wilt extra heren, worden opgeslagen. Als deze items zich in verschillende accounts bevinden, voert u elke account naam in als een afzonderlijk geheim. Houd er rekening mee dat de trainings gegevens sets zich in dezelfde container voor alle formulier typen moeten bevinden. Ze kunnen zich in verschillende mappen bevinden.
* **StorageAccountSasKey**. De Shared Access Signature (SAS) van het opslag account. Als u de SAS-URL wilt ophalen, gaat u naar uw opslag resource. Ga op het tabblad **Storage Explorer** naar uw container, klik met de rechter muisknop en selecteer **hand tekening voor gedeelde toegang ophalen**. Het is belangrijk dat u de SAS voor uw container ophaalt, niet voor het opslagaccount zelf. Zorg ervoor dat de machtigingen **lezen** en **lijst** zijn geselecteerd en selecteer vervolgens **maken**. Kopieer vervolgens de waarde in de sectie **URL**. Dit formulier moet de volgende indeling hebben: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Uw Form Recognizer-model trainen in een Databricks-notebook

U gebruikt Azure Databricks voor het opslaan en uitvoeren van de Python-code die samenwerkt met de Form Recognizer-service.

### <a name="create-a-notebook-in-databricks"></a>Een notitieblok maken in Databricks

[Maak een Azure Databricks-resource](https://ms.portal.azure.com/#create/Microsoft.Databricks) in Azure Portal. Ga naar de resource nadat deze is gemaakt en open de werk ruimte.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Een geheim bereik maken dat door Azure Key Vault wordt ondersteund


Als u wilt verwijzen naar de geheimen in de Azure-sleutel kluis die u hierboven hebt gemaakt, moet u een geheim bereik maken in Databricks. Volg de onderstaande stappen: [een geheim voor Azure Key Vault-back-ups maken](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Een Databricks-cluster maken

Een cluster is een verzameling Databricks-rekenresources. Om een cluster te maken:

1. Selecteer de knop **clusters** in het linkerdeel venster.
1. Selecteer op de pagina **clusters** de optie **cluster maken**.
1. Geef op de pagina **cluster maken** een cluster naam op en selecteer vervolgens **7,2 (scala 2,12, Spark 3.0.0)** in de lijst **Databricks runtime versie** .
1. Selecteer **Cluster maken**.

### <a name="write-a-settings-notebook"></a>Een notebook voor instellingen schrijven

U bent nu klaar om python-notebooks toe te voegen. Maak eerst een notitie blok met de naam **instellingen**. Met dit notitie blok worden de waarden in de parameterisering-tabel toegewezen aan de variabelen in het script. De waarden worden later door Azure Data Factory door gegeven als para meters. Er worden ook waarden van de geheimen in de sleutel kluis aan variabelen toegewezen. 

1. Selecteer de **werkruimte** knop om het **instellingen** notitieblok te maken. Op het tabblad Nieuw selecteert u de vervolg keuzelijst en selecteert u **maken** en vervolgens **notitie blok**.
1. Voer in het pop-upvenster een naam in voor het notitie blok en selecteer vervolgens **python** als standaard taal. Selecteer uw Databricks-cluster en selecteer vervolgens **maken**.
1. In de eerste notebook-cel haalt u de para meters op die worden door gegeven door Azure Data Factory:

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. In de tweede cel haalt u geheimen op uit Key Vault en wijst u deze toe aan variabelen:

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Een trainingsnotebook schrijven

Nu u de notebook **Instellingen** hebt gemaakt, kunt u een notebook maken om het model te trainen. Zoals hierboven vermeld, gebruiken we bestanden die zijn opgeslagen in een map in een Azure Data Lake Storage Gen2-account (**training_blob_root_folder**). De mapnaam is doorgegeven als een variabele. Elke set formulier typen bevindt zich in dezelfde map. Net als we de parameter tabel herhalen, wordt het model door middel van alle formulier typen door lopen. 

1. Maak een notitie blok met de naam **TrainFormRecognizer**. 
1. Voer in de eerste cel het **instellingen** notitieblok uit:

    ```python
    %run "./Settings"
    ```

1. Wijs in de volgende cel variabelen toe vanuit het bestand Instellingen en train het model dynamisch voor elk type formulier, waarbij u de code in de [REST quickstart](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20) (Engelstalig) toepast.

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. De laatste stap in het trainings proces is het verkrijgen van het trainings resultaat in een JSON-indeling:

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-by-using-a-notebook"></a>Formulier gegevens extra heren met behulp van een notitie blok

### <a name="mount-the-azure-data-lake-storage"></a>Azure Data Lake-opslag koppelen

De volgende stap is het beoordelen van de verschillende formulieren die wij hebben met behulp van het getrainde model. We koppelen het Azure Data Lake Storage-account in Databricks en verwijzen naar de koppeling tijdens het opname proces.

Zoals we in de trainings fase hebben gedaan, gebruiken we Azure Data Factory om het uitpakken van de sleutel-waardeparen van de formulieren aan te roepen. De formulieren in de mappen die zijn opgegeven in de parameter tabel worden door lopen.

1. Maak het notitie blok om het opslag account in Databricks te koppelen. Noem het **MountDataLake**. 
1. U moet eerst de notebook **Instellingen** aanroepen:

    ```python
    %run "./Settings"
    ```

1. In de tweede cel definieert u de variabelen voor de gevoelige para meters, die we ophalen uit onze Key Vault geheimen:

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Probeer het opslag account te ontkoppelen als dit eerder is gekoppeld:

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Het opslag account koppelen:


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > We hebben alleen het opslag account training gekoppeld. In dit geval zijn de trainings bestanden en de bestanden die we willen extra sleutel/waarde-paren uit hetzelfde opslag account. Als uw opslag accounts voor scores en trainingen niet overeenkomen, moet u beide opslag accounts koppelen. 

### <a name="write-the-scoring-notebook"></a>De notebook voor scores schrijven

We kunnen nu een score notitie blok maken. We doen iets dat vergelijkbaar is met wat we hebben gedaan in de Oefen notitieblok: we gebruiken bestanden die zijn opgeslagen in mappen in het Azure Data Lake Storage-account dat we zojuist hebben gekoppeld. De mapnaam wordt doorgegeven als een variabele. Alle formulieren in de opgegeven map worden herhaald en de sleutel/waarde-paren worden uitgepakt. 

1. Maak een notitie blok en noem het **ScoreFormRecognizer**. 
1. De **instellingen** en **MountDataLake** -notebooks uitvoeren:

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Voeg deze code toe, waarmee de [analyse](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) -API wordt aangeroepen:

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. In de volgende cel worden de resultaten van de extractie van sleutel/waarde-paar opgehaald. In deze cel komt de uitvoer van het resultaat terecht. We willen het resultaat in JSON-indeling zodat we het verder kunnen verwerken in Azure SQL Database of Azure Cosmos DB. Daarom schrijven we het resultaat naar een. JSON-bestand. De naam van het uitvoer bestand is de naam van het gescoorde bestand dat is samengevoegd met ' _output.jsop '. Het bestand wordt opgeslagen in dezelfde map als het bronbestand.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Voer de procedure voor het schrijven van bestanden in een nieuwe cel uit:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>Trainings-en Score automatisering automatiseren met behulp van Azure Data Factory

De enige resterende stap is het instellen van de Azure Data Factory-service om de trainings-en Score processen te automatiseren. Volg eerst de stappen onder [Een data factory maken](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory). Nadat u de Azure Data Factory resource hebt gemaakt, moet u drie pijp lijnen maken: één voor training en twee voor het scoren. (Verderop wordt uitgelegd.)

### <a name="training-pipeline"></a>Trainingspijplijn

De eerste activiteit in de trainingspijplijn is een zoekopdracht om de waarden in de parametriseringtabel in de Azure SQL-database te kunnen lezen en retourneren. Alle trainings gegevens sets bevinden zich in hetzelfde opslag account en dezelfde container (maar mogelijk andere mappen). Daarom blijven de standaard kenmerk waarde **eerste rij alleen** in de instellingen voor de opzoek activiteit. Voor elk type formulier waarmee het model wordt getraind, wordt het model getraind met behulp van alle bestanden in **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Scherm opname waarin een trainings pijplijn wordt weer gegeven in Data Factory.":::

De opgeslagen procedure heeft twee para meters: **model_id** en **form_batch_group_id**. De code voor het retour neren van de model-ID van de Databricks-notebook is `dbutils.notebook.exit(model_id)` . De code voor het lezen van de code in de activiteit opgeslagen procedure in Data Factory is `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Scorepijplijnen

Als u de sleutel/waarde-paren wilt extra heren, worden alle mappen in de tabel parameterisering gescand. Voor elke map worden de sleutel-waardeparen geëxtraheerd van alle bestanden in het bestand. Azure Data Factory biedt momenteel geen ondersteuning voor geneste ForEach-lussen. In plaats daarvan maken we twee pijp lijnen. De eerste pijplijn voert de zoekopdracht vanuit de parametriseringstabel uit en geeft de lijst met mappen als een parameter aan de tweede pijplijn door.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="Scherm opname van de eerste score pijplijn in Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="Scherm afbeelding met de details van de eerste score pijp lijn in Data Factory.":::

De tweede pijp lijn gebruikt een GetMeta-activiteit om de lijst met bestanden in de map op te halen en deze door te geven als een para meter voor de Score Databricks-notebook.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="Scherm opname van de tweede score pijplijn in Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="Scherm afbeelding met de details van de tweede score pijp lijn in Data Factory.":::

### <a name="specify-a-degree-of-parallelism"></a>Niveau van parallelle uitvoering opgeven

In zowel trainingen als Score pijp lijnen kunt u de mate van parallelle uitvoering opgeven, zodat u meerdere formulieren tegelijk kunt verwerken.

De mate van parallelle uitvoering instellen in de Azure Data Factory-pijp lijn:

1. Selecteer de **foreach** -activiteit.
1. Wis het **sequentiële** vak.
1. Stel de mate van parallelle uitvoering in in het vak **batch-aantal** . U wordt aangeraden een maximum aantal batches van vijftien voor de score in te stellen.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Scherm opname van de parallelle configuratie voor de Score activiteit in Azure Data Factory.":::

## <a name="how-to-use-the-pipeline"></a>De pijp lijn gebruiken

U hebt nu een geautomatiseerde pijplijn om uw achterstand aan formulieren te digitaliseren en er een analyse op uit te voeren. Wanneer u nieuwe formulieren van een bekend type toevoegt aan een bestaande opslagmap, voert u de Score pijplijnen opnieuw uit. Alle uitvoer bestanden worden bijgewerkt, inclusief uitvoer bestanden voor de nieuwe formulieren. 

Als u nieuwe formulieren van een nieuw type toevoegt, moet u tevens een gegevensset voor training naar de bijbehorende container uploaden. Vervolgens voegt u een nieuwe rij in de tabel parameterisering toe, waarbij u de locaties van de nieuwe documenten en de bijbehorende trainings gegevensset opgeeft. Voer een waarde van-1 in voor **model_ID** om aan te geven dat een nieuw model voor de formulieren moet worden getraind. Voer vervolgens de trainings pijplijn uit in Azure Data Factory. De pijp lijn leest uit de tabel, traint een model en overschrijft de model-ID in de tabel. U kunt vervolgens de Score pijplijnen aanroepen om te beginnen met het schrijven van de uitvoer bestanden.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie stelt u Azure Data Factory pijp lijnen in om de training te activeren en de formulier herkennings modellen uit te voeren en een grote achterstand van bestanden te maken. Bekijk vervolgens de Form Recognizer-API om te zien wat u er nog meer mee kunt doen.

* [Form Recognizer REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync) (Engelstalig)
