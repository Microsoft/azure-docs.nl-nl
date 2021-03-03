---
title: 'Zelfstudie: Formuliergegevens bulksgewijs extraheren met behulp van Azure Data Factory - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Stel Azure Data Factory-activiteiten in om het trainen en uitvoeren van de Form Recognizer-modellen te activeren om een grote achterstand aan documenten te digitaliseren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 6faa612f55b4114b4242c48d43aae9aac8c56582
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699994"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>Zelfstudie: formuliergegevens bulksgewijs extraheren met behulp van Azure Data Factory

In deze zelfstudie wordt beschreven hoe u met Azure-services een grote batch met formulieren in digitale media kunt opnemen. In deze zelfstudie ziet u hoe u de gegevensopname van een Azure Data Lake met documenten kunt automatiseren in een Azure SQL-database. Met een paar klikken kunt u snel modellen trainen en nieuwe documenten verwerken.

## <a name="business-need"></a>Zakelijke behoefte

De meeste organisaties zijn zich bewust hoe waardevol de gegevens zijn die ze in verschillende indelingen hebben opgeslagen (PDF, afbeeldingen, video's). Ze zijn op zoek naar de best practices en de meeste rendabele manieren om deze activa te digitaliseren.

Daarnaast hebben onze klanten vaak verschillende soorten formulieren die afkomstig zijn van hun clients en klanten. In tegenstelling tot de [quickstarts](./quickstarts/client-library.md) ziet u in deze zelfstudie hoe u automatisch een model met nieuwe en verschillende soorten formulieren kunt trainen met behulp van een benadering op basis van metagegevens. Als u geen bestaand model voor het specifieke formuliertype hebt, wordt er een voor u gemaakt en krijgt u de model-id. 

Door de gegevens uit formulieren te extraheren en deze te combineren met bestaande operationele systemen en datawarehouses, kunnen bedrijven inzicht krijgen in en waarde leveren voor hun klanten en zakelijke gebruikers.

Met Azure Form Recognizer kunnen organisaties hun gegevens ten nutte maken, processen automatiseren (factuurbetalingen, belastingverwerking, enzovoort), geld en tijd besparen en profiteren van een betere gegevensnauwkeurigheid.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure Data Lake instellen om uw formulieren op te slaan
> * Een Azure-database gebruiken om een parametriseringstabel te maken
> * Azure Key Vault gebruiken om gevoelige referenties op te slaan
> * Uw Form Recognizer-model trainen in een Databricks-notebook
> * Uw formuliergegevens extraheren met behulp van een Databricks-notebook
> * Het trainen en extraheren van formulieren automatiseren met Azure Data Factory

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Wanneer u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Een Form Recognizer-resource maken"  target="_blank">een Form Recognizer-resource maken <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Form Recognizer-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Een set van minimaal vijf formulieren van hetzelfde type. Idealiter is deze werkstroom bedoeld voor grote verzamelingen documenten. Zie [Een set met trainingsgegevens samenstellen](./build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze zelfstudie kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2128080) gebruiken.

## <a name="project-architecture"></a>Projectarchitectuur 

In dit project wordt een set Azure Data Factory-pijplijnen in stand gehouden voor het activeren van Python-notebooks waarmee gegevens uit documenten in een Azure Data Lake Storage-account worden getraind, geanalyseerd en opgehaald.

Voor de REST-API van Form Recognizer zijn enkele parameters als invoer vereist. Uit veiligheidsoverwegingen worden sommige van deze parameters opgeslagen in een Azure-sleutelkluis, terwijl andere, minder gevoelige parameters, zoals de mapnaam van de opslag-blob, worden opgeslagen in een parametriseringstabel in een Azure SQL database.

Voor het type formulier dat wordt geanalyseerd, vullen gegevensanalisten of gegevenswetenschappers een rij in van de parametertabel. Vervolgens gebruiken ze Azure Data Factory om de lijst met gedetecteerde formuliertypen te herhalen en de relevante parameters door te geven aan een Databricks-notebook om de Form Recognizer-modellen (opnieuw) te trainen. Hier kan ook een Azure-functie worden gebruikt.

De Azure Databricks-notebook gebruikt vervolgens de getrainde modellen om formuliergegevens op te halen en deze gegevens naar een Azure SQL-database te exporteren.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="projectarchitectuur":::


## <a name="set-up-azure-data-lake"></a>Azure Data Lake instellen

Uw achterstand aan formulieren bevindt zich mogelijk in uw on-premises omgeving of op een (S)FTP-server. In deze zelfstudie wordt gebruikgemaakt van formulieren in een Azure Data Lake Gen 2-opslagaccount. U kunt uw bestanden daarnaar overzetten met Azure Data Factory, Azure Storage Explorer of AzCopy. De gegevenssets voor training en scores kunnen zich in verschillende containers bevinden, maar de gegevenssets voor training voor alle formuliertypen moeten zich in dezelfde container bevinden (hoewel ze in verschillende mappen mogen zitten).

Volg de instructies in [Een opslagaccount maken voor gebruik met Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md) als u een nieuwe data lake wilt maken.

## <a name="create-a-parameterization-table"></a>Een parametriseringstabel maken

U gaat vervolgens een tabel met metagegevens maken in een Azure SQL-database. Deze tabel bevat de niet-gevoelige gegevens die nodig zijn voor de REST API van Form Recognizer. Wanneer de gegevensset een nieuw type formulier bevat, wordt er een nieuwe record in deze tabel ingevoegd en worden de trainings- en scorepijplijn geactiveerd (en later geïmplementeerd).

De volgende velden worden in de tabel gebruikt:

* **form_description**: Dit veld is niet vereist als onderdeel van de training. Het bevat een beschrijving van het type formulieren dat voor het model wordt getraind (bijvoorbeeld client A-formulieren, Hotel B-formulieren).
* **training_container_name**: Dit veld is de naam van de container voor het opslagaccount waarin de gegevensset voor de training is opgeslagen. Dit kan dezelfde container zijn als **scoring_container_name**.
* **training_blob_root_folder**: De map in het opslagaccount waar de bestanden voor de training van het model worden opgeslagen.
* **scoring_container_name**: Dit veld is de naam van de container voor het opslagaccount waar de bestanden zijn opgeslagen waarvan de sleutelwaardeparen moeten worden geëxtraheerd. Dit kan dezelfde container zijn als **training_container_name**.
* **scoring_input_blob_folder**: De map in het opslagaccount waar de bestanden worden opgeslagen waaruit de gegevens moeten worden geëxtraheerd.
* **model_id**: De id van het model dat opnieuw moet worden getraind. Voor de eerste uitvoering moet de waarde worden ingesteld op -1, waardoor de trainingsnotebook een nieuw aangepast model maakt om te trainen. De trainingsnotebook retourneert de zojuist gemaakte model-id naar de instantie van Azure Data Factory en met behulp van een opgeslagen procedureactiviteit wordt deze waarde in de Azure SQL-database bijgewerkt.

  Wanneer u een nieuw formuliertype wilt opnemen, moet u de model-id handmatig opnieuw instellen op -1 voordat u het model gaat trainen.

* **file_type**: De ondersteunde formuliertypen zijn `application/pdf`, `image/jpeg`, `image/png` en `image/tif`.

  Als u formulieren van verschillende bestandstypen hebt, moet u deze waarde en de id **model_id** bij het trainen van een nieuw formuliertype.
* **form_batch_group_id**: In de loop van de tijd hebt u mogelijk meerdere formuliertypen die u voor hetzelfde model traint. Met **form_batch_group_id** kunt u alle formuliertypen opgeven die met behulp van een specifiek model zijn getraind.

### <a name="create-the-table"></a>De tabel maken

[Maak een Azure SQL-database](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase) en voer vervolgens het volgende SQL-script in de [query-editor](../../azure-sql/database/connect-query-portal.md) uit om de benodigde tabel te maken.

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

Voer het volgende script uit om de procedure te maken voor het automatisch bijwerken van **model_id** zodra deze is getraind.

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

### <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken

[Maak een Key Vault-resource](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Ga vervolgens naar de Key Vault-resource nadat deze is gemaakt en selecteer in de sectie **Instellingen** **geheimen** om de parameters toe te voegen.

Er wordt een nieuw venster geopend. Selecteer **Genereren/importeren**. Voer de naam van de parameter en de bijbehorende waarde in en klik op Maken. Doe dit voor de volgende parameters:

* **CognitiveServiceEndpoint**: De eindpunt-URL van de API voor Form Recognizer.
* **CognitiveServiceSubscriptionKey**: De toegangssleutel voor de Form Recognizer-service. 
* **StorageAccountName**: Het opslagaccount waarin de gegevensset voor de training en de formulieren waaruit de sleutel-waardeparen moeten worden geëxtraheerd, worden opgeslagen. Als deze zich in verschillende accounts bevinden, voert u elke accountnaam in als een afzonderlijk geheim. De gegevenssets voor de training moeten zich in dezelfde container voor alle formuliertypen bevinden, maar ze mogen zich wel in verschillende mappen bevinden.
* **StorageAccountSasKey**: de handtekening voor gedeelde toegang (Shared Access Signature, SAS) van het opslagaccount. Als u de SAS-URL wilt ophalen, gaat u naar uw opslagresource en selecteert u het tabblad **Storage Explorer**. Ga naar de container, klik er met de rechtermuisknop op en selecteer **Shared Access Signature ophalen**. Het is belangrijk dat u de SAS voor uw container ophaalt, niet voor het opslagaccount zelf. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL**. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Uw Form Recognizer-model trainen in een Databricks-notebook

U gebruikt Azure Databricks voor het opslaan en uitvoeren van de Python-code die samenwerkt met de Form Recognizer-service.

### <a name="create-a-notebook-in-databricks"></a>Een notitieblok maken in Databricks

[Maak een Azure Databricks-resource](https://ms.portal.azure.com/#create/Microsoft.Databricks) in Azure Portal. Ga naar de resource nadat deze is gemaakt en start de werkruimte.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Een geheim bereik maken dat door Azure Key Vault wordt ondersteund

Als u wilt verwijzen naar de geheimen in de Azure-sleutelkluis die hierboven zijn gemaakt, moet u een geheim bereik maken in Databricks. Volg de stappen onder [Een geheim bereik maken dat door Azure Key Vault wordt ondersteund](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Een Databricks-cluster maken

Een cluster is een verzameling Databricks-rekenresources. Om een cluster te maken:

1. Klik in de zijbalk op de knop **Clusters**.
1. Klik op de pagina **Clusters** op **Cluster maken**.
1. Geef op de pagina **Cluster maken** een clusternaam op en selecteer **7.2 (Scala 2.12, Spark 3.0.0)** in de vervolgkeuzelijst van de Databricks Runtime-versie.
1. Klik op **Cluster maken**.

### <a name="write-a-settings-notebook"></a>Een notebook voor instellingen schrijven

U bent nu klaar om Python-notebooks toe te voegen. Maak eerst een notebook met de naam **Instellingen**. Met dit notebook worden de waarden in de parametriseringstabel toegewezen aan variabelen in het script. De waarden worden later door Azure Data Factory doorgegeven als parameters. Er worden ook waarden van de geheimen in de sleutelkluis aan variabelen toegewezen. 

1. Als u de notebook **Instellingen** wilt maken, klikt u op de knop **werkruimte**, klikt u op het tabblad op de vervolgkeuzelijst en selecteert u **maken** en vervolgens **notebook**.
1. In het pop-upvenster voert u de naam in die u aan de notebook wilt geven en selecteert u **Python** als standaardtaal. Selecteer uw Databricks-cluster en selecteer **Maken**.
1. In de eerste cel van de notebook worden de parameters opgehaald die door Azure Data Factory zijn doorgegeven.

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

1. In de tweede cel worden geheimen uit Key Vault opgehaald en aan variabelen toegewezen.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Een trainingsnotebook schrijven

Nu u de notebook **Instellingen** hebt gemaakt, kunt u een notebook maken om het model te trainen. Zoals hierboven vermeld, worden er bestanden gebruikt die zijn opgeslagen in een map in een Azure Data Lake Gen 2-opslagaccount (**training_blob_root_folder**). De mapnaam is doorgegeven als een variabele. Elke set formuliertypen bevindt zich in dezelfde map, en als de tabel met parameters wordt doorlopen, wordt het model getraind door gebruik te maken van alle formuliertypen. 

1. Maak een nieuw notebook met de naam **TrainFormRecognizer**. 
1. Voer in de eerste cel het notebook Instellingen uit:

    ```python
    %run "./Settings"
    ```

1. Wijs in de volgende cel variabelen toe vanuit het bestand **Instellingen** en train het model dynamisch voor elk type formulier, waarbij u de code in de [REST quickstart](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20) (Engelstalig) toepast.

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
        # Request headers
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
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
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
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. De laatste stap in het trainingsproces bestaat uit het ophalen van het trainingsresultaat in een JSON-indeling.

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

## <a name="extract-form-data-using-a-notebook"></a>Formuliergegevens extraheren met behulp van een notebook

### <a name="mount-the-azure-data-lake-storage"></a>Azure Data Lake-opslag koppelen

In de volgende stap krijgen de verschillende formulieren een score met behulp van het getrainde model. Het Azure Data Lake-opslagaccount wordt in Databricks gekoppeld. Tijdens het opnameproces wordt naar de koppeling verwezen.

Net als in de trainingsfase wordt Azure Data Factory gebruikt om het extraheren van de sleutel-waardeparen van de formulieren aan te roepen. De formulieren worden doorlopen in de mappen die in de parametertabel zijn opgegeven.

1. Nu wordt de notebook gemaakt om het opslagaccount in Databricks te koppelen. De notebook krijgt de naam **MountDataLake**. 
1. U moet eerst de notebook **Instellingen** aanroepen:

    ```python
    %run "./Settings"
    ```

1. In de tweede cel worden variabelen gedefinieerd voor de gevoelige parameters. Deze worden opgehaald uit de Key Vault-geheimen.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Vervolgens wordt het opslagaccount ontkoppelt, mocht dit eerder zijn gekoppeld.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Ten slotte wordt het opslagaccount gekoppeld.


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
    > Het opslagaccount voor de training is alleen in dit geval gekoppeld. De trainingsbestanden en de bestanden waarvan de sleutel-waardeparen moeten worden geëxtraheerd, bevinden zich in hetzelfde opslagaccount. Als uw opslagaccounts voor scores en trainingen niet overeenkomen, moet u beide opslagaccounts hier koppelen. 

### <a name="write-the-scoring-notebook"></a>De notebook voor scores schrijven

Nu kunt u een scorenotebook maken. Net als de trainingsnotebook worden de bestanden gebruikt die zijn opgeslagen in mappen in het Azure Data Lake-opslagaccount dat zojuist is gekoppeld. De mapnaam wordt doorgegeven als een variabele. Alle formulieren in de opgegeven map worden doorlopen en de sleutel-waardeparen worden eruit geëxtraheerd. 

1. Maak een nieuwe notebook en geef deze de naam **ScoreFormRecognizer**. 
1. Voer de notebooks **Instellingen** en **MountDataLake** uit.

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Voeg vervolgens de volgende code toe. Hiermee wordt de [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)-API aangeroepen.

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
        # Request headers
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

1. In de volgende cel worden de resultaten van de extractie van de sleutel-waardeparen opgehaald. In deze cel komt de uitvoer van het resultaat terecht. Het resultaat wordt naar een JSON-bestand geschreven omdat het resultaat in de Azure SQL-database of Cosmos DB moet worden verwerkt. De naam van het uitvoerbestand krijgt de naam van het bestand met scores, waaraan _output.json is toegevoegd. Het bestand wordt opgeslagen in dezelfde map als het bronbestand.

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

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>Trainen en scores geven automatiseren met Azure Data Factory

De resterende stap bestaat uit het instellen van de Azure Data Factory-service (ADF) om de trainings- en scoreprocessen te automatiseren. Volg eerst de stappen onder [Een data factory maken](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory). Nadat u de ADF-resource hebt gemaakt, moet u drie pijplijnen maken: één voor de training en twee voor het toewijzen van scores (zie hieronder).

### <a name="training-pipeline"></a>Trainingspijplijn

De eerste activiteit in de trainingspijplijn is een zoekopdracht om de waarden in de parametriseringtabel in de Azure SQL-database te kunnen lezen en retourneren. Aangezien alle gegevenssets voor de training zich in hetzelfde opslagaccount en dezelfde container bevinden (maar mogelijk in verschillende mappen), laten we de standaardwaarde van het kenmerk **Alleen eerste rij** in de instellingen voor de opzoekactiviteit ongewijzigd. Voor elk type formulier waarmee het model wordt getraind, wordt het model getraind met behulp van alle bestanden in **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="trainingspijplijn in data factory":::

De opgeslagen procedure maakt gebruik van twee parameters: **model_id** en **form_batch_group_id**. De code voor het retourneren van de model-id van de Databricks-notebook is `dbutils.notebook.exit(model_id)`; de code voor het lezen van de code in de opgeslagen procedureactiviteit in de data factory is `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`.

### <a name="scoring-pipelines"></a>Scorepijplijnen

Als u de sleutel-waardeparen wilt extraheren, worden alle mappen in de parametriseringstabel gescand en worden voor elke map de sleutel-waardeparen van alle bestanden erin geëxtraheerd. Vanaf heden ondersteunt ADF geen geneste ForEach-lussen meer. In plaats daarvan worden twee pijplijnen gemaakt. De eerste pijplijn voert de zoekopdracht vanuit de parametriseringstabel uit en geeft de lijst met mappen als een parameter aan de tweede pijplijn door.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="eerste scorepijplijn in data factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="eerste scorepijplijn in data factory, details":::

De tweede pijplijn maakt gebruik van een GetMeta-activiteit om de lijst met bestanden in de map op te halen en deze door te geven als een parameter voor de Databricks-scorenotebook.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="tweede scorepijplijn in data factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="tweede scorepijplijn in data factory, details":::

### <a name="specify-a-degree-of-parallelism"></a>Niveau van parallelle uitvoering opgeven

In beide pijplijnen, die voor de training en die voor de score, kunt u het niveau van parallelle uitvoering opgeven om meerdere formulieren tegelijkertijd te kunnen verwerken.

Het niveau van parallelle uitvoering instellen in de ADF-pijplijn:

* Selecteer de ForEach-activiteit.
* Schakel het selectievakje **Sequentieel** uit.
* Stel het niveau van parallelle uitvoering in het tekstvak **Aantal batches** in. U wordt aangeraden een maximum aantal batches van vijftien voor de score in te stellen.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="configuratie voor parallelle uitvoering voor scoreactiviteit in ADF":::

## <a name="how-to-use"></a>Gebruik

U hebt nu een geautomatiseerde pijplijn om uw achterstand aan formulieren te digitaliseren en er een analyse op uit te voeren. Wanneer u nieuwe formulieren van een bekend type aan een bestaande opslagmap toevoegt, voert u de scorepijplijnen gewoon opnieuw uit. Alle uitvoerbestanden worden vervolgens bijgewerkt, inclusief uitvoerbestanden voor de nieuwe formulieren. 

Als u nieuwe formulieren van een nieuw type toevoegt, moet u tevens een gegevensset voor training naar de bijbehorende container uploaden. Voeg vervolgens een nieuwe rij aan de parametriseringstabel toe, waarbij u de locaties van de nieuwe documenten en de bijbehorende gegevensset voor training invoert. Voer een waarde van -1 in voor **model_ID** om aan te geven dat er een nieuw model voor deze formulieren moet worden getraind. Voer vervolgens de trainingspijplijn uit in ADF. De tabel wordt uitgelezen, er wordt een model getraind en de model-id in de tabel wordt overschreven. Vervolgens kunt u de scorepijplijnen aanroepen om met het schrijven van de uitvoerbestanden te beginnen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Data Factory-pijplijnen ingesteld om het trainen en uitvoeren van Form Recognizer-modellen te activeren voor het digitaliseren van een grote achterstand aan documenten. Bekijk vervolgens de Form Recognizer-API om te zien wat u er nog meer mee kunt doen.

* [Form Recognizer REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) (Engelstalig)