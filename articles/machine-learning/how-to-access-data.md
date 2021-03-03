---
title: Verbinding maken met Storage services in azure
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van data stores om veilig verbinding te maken met Azure Storage-services tijdens de training met Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/03/2020
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 0bc247e473ea96f2f9301eeaebb543b3317c84c7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659661"
---
# <a name="connect-to-storage-services-on-azure"></a>Verbinding maken met Storage services in azure

In dit artikel leert u hoe u verbinding kunt maken met de services voor gegevens opslag in azure met Azure Machine Learning gegevens opslag en de [Azure machine learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

Data stores maken een beveiligde verbinding met uw opslag service op Azure zonder dat u uw verificatie referenties en de integriteit van de oorspronkelijke gegevens bron risico. Er worden verbindings gegevens opgeslagen, zoals uw abonnements-ID en Token autorisatie in uw [Key Vault](https://azure.microsoft.com/services/key-vault/) dat is gekoppeld aan de werk ruimte, zodat u veilig toegang kunt krijgen tot uw opslag zonder dat u ze in uw scripts hoeft te coderen. U kunt gegevens opslag maken die verbinding maakt met [deze Azure Storage-oplossingen](#matrix).

Zie het artikel over [beveiligde toegang](concept-data.md#data-workflow) als u wilt weten waar gegevens opslag in de werk stroom van Azure machine learning van de gehele Data Access passen.

Zie de [Azure machine learning Studio gebruiken om gegevens opslag te maken en te registreren](how-to-connect-data-ui.md#create-datastores)voor een lage code-ervaring.

>[!TIP]
> In dit artikel wordt ervan uitgegaan dat u verbinding wilt maken met uw opslag service met verificatie referenties op basis van referenties, zoals een service-principal of een SAS-token (Shared Access Signature). Als er referenties zijn geregistreerd bij data stores, kunnen alle gebruikers met de rol van werkruimte *lezer* deze referenties ophalen. [Meer informatie over de rol van werkruimte *lezer* .](how-to-assign-roles.md#default-roles) <br><br>Als dit een probleem is, leert u hoe u [verbinding kunt maken met opslag Services met toegang op basis van identiteiten](how-to-identity-based-data-access.md). <br><br>Deze mogelijkheid is een [experimentele](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) preview-functie en kan op elk gewenst moment worden gewijzigd. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- Een Azure-opslag account met een [ondersteund opslag type](#matrix).

- De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

- Een Azure Machine Learning-werkruimte.
  
  [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md) of gebruik een bestaand item via de PYTHON-SDK. 

    Importeer de `Workspace` `Datastore` -en-klasse en laad uw abonnements gegevens uit het bestand `config.json` met behulp van de functie `from_config()` . Hiermee zoekt u standaard naar het JSON-bestand in de huidige map, maar u kunt ook een para meter Path opgeven om naar het bestand te verwijzen met `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Wanneer u een werk ruimte maakt, worden een Azure Blob-container en een Azure-bestands share automatisch geregistreerd als gegevens opslag in de werk ruimte. Ze hebben respectievelijk de naam `workspaceblobstore` en `workspacefilestore` . De `workspaceblobstore` wordt gebruikt voor het opslaan van werk ruimte-artefacten en uw machine learning-experiment-Logboeken. Het is ook ingesteld als de **standaard gegevens opslag** en kan niet worden verwijderd uit de werk ruimte. De `workspacefilestore` wordt gebruikt voor het opslaan van notitie blokken en R-scripts die zijn geautoriseerd via [Compute-instantie](./concept-compute-instance.md#accessing-files).
    
    > [!NOTE]
    > Azure Machine Learning Designer wordt automatisch een gegevens archief met de naam **azureml_globaldatasets** gemaakt wanneer u een voor beeld opent in de start pagina van de ontwerp functie. Dit gegevens archief bevat alleen voorbeeld gegevens sets. Gebruik deze gegevens opslag **niet** voor vertrouwelijke gegevens toegang.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Ondersteunde service typen voor gegevens opslag

Data stores ondersteunen momenteel het opslaan van verbindings gegevens naar de opslag services die in de volgende matrix worden weer gegeven. 

> [!TIP]
> **Voor niet-ondersteunde opslag oplossingen** en voor het opslaan van de kosten voor de uitvoer van gegevens tijdens ml experimenten, [verplaatst u uw gegevens](#move) naar een ondersteunde Azure Storage-oplossing. 

| Opslag &nbsp; type | Verificatie &nbsp; type | [Azure &nbsp; machine &nbsp; Learning Studio](https://ml.azure.com/) | [Azure &nbsp; machine &nbsp; Learning &nbsp; python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) |  [Azure &nbsp; machine &nbsp; Learning cli](reference-azure-machine-learning-cli.md) | [&nbsp;Rest API voor Azure machine &nbsp; Learning &nbsp;](/rest/api/azureml/) | VS-code
---|---|---|---|---|---|---
[Azure &nbsp; BLOB- &nbsp; opslag](../storage/blobs/storage-blobs-overview.md)| Accountsleutel <br> SAS-token | ✓ | ✓ | ✓ |✓ |✓
[Azure- &nbsp; bestands &nbsp; share](../storage/files/storage-files-introduction.md)| Accountsleutel <br> SAS-token | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; Data Lake &nbsp; Storage gen &nbsp; 1](../data-lake-store/index.yml)| Service-principal| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Lake &nbsp; Storage gen &nbsp; 2](../storage/blobs/data-lake-storage-introduction.md)| Service-principal| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; SQL &nbsp; Data Base](../azure-sql/database/sql-database-paas-overview.md)| SQL-verificatie <br>Service-principal| ✓ | ✓ | ✓ |✓|
[Azure- &nbsp; postgresql](../postgresql/overview.md) | SQL-verificatie| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Base &nbsp; for &nbsp; mysql](../mysql/overview.md) | SQL-verificatie|  | ✓* | ✓* |✓*|
[Databricks- &nbsp; bestands &nbsp; systeem](/azure/databricks/data/databricks-file-system)| Geen verificatie | | ✓** | ✓ ** |✓** |

\* MySQL wordt alleen ondersteund voor pijplijn [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py)<br />
\*\* Databricks wordt alleen ondersteund voor pijplijn [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?preserve-view=true&view=azure-ml-py)


### <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

We raden u aan een gegevens opslag voor een [Azure Blob-container](../storage/blobs/storage-blobs-introduction.md)te maken. Zowel Standard-als Premium-opslag zijn beschikbaar voor blobs. Hoewel Premium-opslag duurder is, kunnen de snellere doorvoer snelheden de snelheid van uw trainings uitvoeringen verbeteren, met name als u traint voor een grote gegevensset. Voor informatie over de kosten van opslag accounts raadpleegt u de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) is gebaseerd op Azure Blob-opslag en ontworpen voor enter prise Big data-analyses. De toevoeging van een [hiërarchische naamruimte](../storage/blobs/data-lake-storage-namespace.md) aan Blob Storage vormt een fundamenteel onderdeel van Data Lake Storage Gen2. Met de hiërarchische naamruimte worden objecten/bestanden georganiseerd in een hiërarchie met mappen voor efficiënte toegang tot de gegevens.

## <a name="storage-access-and-permissions"></a>Toegang tot en machtigingen voor opslag

Om ervoor te zorgen dat u veilig verbinding maakt met uw Azure Storage-service, moet u voor Azure Machine Learning toegang hebben tot de bijbehorende gegevens opslag container. Deze toegang is afhankelijk van de verificatie referenties die worden gebruikt voor het registreren van de gegevens opslag. 

### <a name="virtual-network"></a>Virtueel netwerk 

Azure Machine Learning kan standaard niet communiceren met een opslag account dat zich achter een firewall of binnen een virtueel netwerk bevindt. Als uw gegevens opslag account zich in een **virtueel netwerk** bevindt, moet u extra configuratie stappen uitvoeren om ervoor te zorgen dat Azure machine learning toegang heeft tot uw gegevens. 

> [!NOTE]
> Deze richt lijnen zijn ook van toepassing op gegevens opslag die is [gemaakt met op identiteit gebaseerde gegevens toegang (preview-versie)](how-to-identity-based-data-access.md). 

**Voor PYTHON SDK-gebruikers** moet het Compute-doel zich in hetzelfde virtuele netwerk en subnet van de opslag bevinden om toegang te krijgen tot uw gegevens via uw trainings script op een compute-doel.  

**Voor Azure machine learning Studio-gebruikers** zijn verschillende functies afhankelijk van de mogelijkheid om gegevens te lezen uit een gegevensset. zoals gegevensset-previews, profielen en automatische machine learning. Om ervoor te zorgen dat deze functies werken met opslag achter virtuele netwerken, gebruikt u een door een [werk ruimte beheerde identiteit in de Studio](how-to-enable-studio-virtual-network.md) om Azure machine learning toegang te geven tot het opslag account van buiten het virtuele netwerk. 

Azure Machine Learning kan aanvragen ontvangen van clients die zich buiten het virtuele netwerk bevinden. Om ervoor te zorgen dat de entiteit die gegevens aanvraagt van de service veilig is, stelt u een [persoonlijke Azure-koppeling in voor uw werk ruimte](how-to-configure-private-link.md).

### <a name="access-validation"></a>Toegangs validatie

**Als onderdeel van het proces voor het maken en registreren van de eerste Data Store**, Azure machine learning automatisch gevalideerd of de onderliggende opslag service bestaat en de door de gebruiker opgegeven principal (gebruikers naam, Service-Principal of SAS-token) heeft toegang tot de opgegeven opslag.

**Nadat Data Store is gemaakt**, wordt deze validatie alleen uitgevoerd voor methoden waarvoor toegang tot de onderliggende opslag container is vereist. **niet** elke keer dat de objecten gegevens opslag worden opgehaald. Validatie vindt bijvoorbeeld plaats als u bestanden wilt downloaden uit uw gegevens archief. maar als u de standaard gegevens opslag gewoon wilt wijzigen, vindt validatie niet plaats.

Als u uw toegang tot de onderliggende opslag service wilt verifiëren, kunt u uw account sleutel, SAS-tokens (Shared Access signatures) of een Service-Principal opgeven in de overeenkomstige `register_azure_*()` methode van het gegevens opslag type dat u wilt maken. De [opslag type matrix](#matrix) bevat de ondersteunde verificatie typen die overeenkomen met elk type gegevens opslag.

U vindt de account sleutel, het SAS-token en de gegevens van de Service-Principal op uw [Azure Portal](https://portal.azure.com).

* Als u van plan bent om een account sleutel of SAS-token voor verificatie te gebruiken, selecteert u **opslag accounts** in het linkerdeel venster en kiest u het opslag account dat u wilt registreren. 
  * De **overzichts** pagina bevat informatie zoals de account naam, container en naam van de bestands share. 
      1. Ga voor account sleutels naar **toegangs sleutels** in het deel venster **instellingen** . 
      1. Voor SAS-tokens gaat u naar de **hand tekeningen voor gedeelde toegang** in het deel venster **instellingen** .

* Als u van plan bent om een service-principal te gebruiken voor verificatie, gaat u naar uw **app-registraties** en selecteert u welke app u wilt gebruiken. 
    * De bijbehorende **overzichts** pagina bevat de vereiste informatie, zoals Tenant-id en client-id.

> [!IMPORTANT]
> * Als u de toegangs sleutels voor een Azure Storage account (account sleutel of SAS-token) wilt wijzigen, moet u ervoor zorgen dat u de nieuwe referenties synchroniseert met uw werk ruimte en de gegevens opslag die ermee zijn verbonden. Meer informatie over [het synchroniseren van uw bijgewerkte referenties](how-to-change-storage-access-key.md). 
### <a name="permissions"></a>Machtigingen

Voor Azure Blob-container en Azure Data Lake gen 2-opslag, moet u ervoor zorgen dat uw verificatie referenties toegang hebben tot **BLOB-gegevens lezer voor opslag** . Meer informatie over [Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Een SAS-token voor het account is standaard ingesteld op geen machtigingen. 
* Voor **Lees toegang** voor gegevens moet uw verificatie referenties mini maal lijst-en lees machtigingen voor containers en objecten hebben. 

* Voor het **schrijven** van gegevens is het schrijven en toevoegen van machtigingen ook vereist.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Gegevens opslag maken en registreren

Wanneer u een Azure Storage-oplossing registreert als een gegevens opslag, maakt en registreert u die gegevens opslag automatisch aan een specifieke werk ruimte. Raadpleeg de sectie [machtigingen voor opslag toegang &](#storage-access-and-permissions) voor hulp bij scenario's voor virtuele netwerken en waar u de vereiste verificatie referenties kunt vinden. 

In deze sectie vindt u voor beelden van het maken en registreren van een gegevens opslag via de python-SDK voor de volgende opslag typen. De para meters die in deze voor beelden worden opgegeven, zijn de **vereiste para meters** voor het maken en registreren van een gegevens opslag.

* [Azure Blob-container](#azure-blob-container)
* [Azure-bestandsshare](#azure-file-share)
* [Azure Data Lake Storage generatie 2](#azure-data-lake-storage-generation-2)

 Zie de [documentatie van de betreffende `register_azure_*` methoden](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=truemethods)voor het maken van gegevens opslag voor andere ondersteunde opslag Services.

Zie [verbinding maken met gegevens met Azure machine learning Studio](how-to-connect-data-ui.md)als u de voor keur geeft aan een lage code-ervaring.
>[!IMPORTANT]
> Als u de registratie ongedaan maakt en een gegevens archief met dezelfde naam opnieuw registreert, en dit mislukt, is het mogelijk dat de Azure Key Vault voor uw werk ruimte niet voor het voorlopig verwijderen is ingeschakeld. Voorlopig verwijderen is standaard ingeschakeld voor het sleutel kluis-exemplaar dat is gemaakt door uw werk ruimte, maar kan niet worden ingeschakeld als u een bestaande sleutel kluis hebt gebruikt of een werk ruimte hebt gemaakt vóór oktober 2020. Zie voor meer informatie over het inschakelen van soft voorlopig [verwijderen inschakelen voor een bestaande sleutel kluis]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault).

> [!NOTE]
> De naam van het gegevens archief mag alleen bestaan uit kleine letters, cijfers en onderstrepings tekens. 

### <a name="azure-blob-container"></a>Azure Blob-container

Als u een Azure Blob-container wilt registreren als gegevens opslag, gebruikt u [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Met de volgende code wordt het gegevens archief gemaakt en geregistreerd `blob_datastore_name` in de `ws` werk ruimte. Deze Data Store heeft toegang tot de `my-container-name` BLOB-container op het `my-account-name` opslag account met behulp van de gegeven toegangs sleutel voor het account. Raadpleeg de sectie [machtigingen voor opslag toegang &](#storage-access-and-permissions) voor hulp bij scenario's voor virtuele netwerken en waar u de vereiste verificatie referenties kunt vinden. 

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

### <a name="azure-file-share"></a>Azure-bestandsshare

Als u een Azure-bestands share wilt registreren als gegevens opslag, gebruikt u [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

Met de volgende code wordt het gegevens archief gemaakt en geregistreerd `file_datastore_name` in de `ws` werk ruimte. Deze Data Store heeft toegang tot de `my-fileshare-name` Bestands share op het `my-account-name` opslag account met behulp van de gegeven toegangs sleutel voor het account. Raadpleeg de sectie [machtigingen voor opslag toegang &](#storage-access-and-permissions) voor hulp bij scenario's voor virtuele netwerken en waar u de vereiste verificatie referenties kunt vinden. 

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generatie 2

Gebruik [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) voor het registreren van een Azure data Lake Storage Generation 2 (ADLS gen 2) om het opslaan van referenties die zijn verbonden met een Azure DataLake gen 2-opslag met [Service-Principal-machtigingen](../active-directory/develop/howto-create-service-principal-portal.md).  

Als u de Service-Principal wilt gebruiken, moet u [uw toepassing registreren](../active-directory/develop/app-objects-and-service-principals.md) en de Service Principal Data Access verlenen via op rollen gebaseerd toegangs beheer (Azure RBAC) of Access Control Lists (ACL). Meer informatie over [toegangs beheer dat is ingesteld voor ADLS gen 2](../storage/blobs/data-lake-storage-access-control-model.md). 

Met de volgende code wordt het gegevens archief gemaakt en geregistreerd `adlsgen2_datastore_name` in de `ws` werk ruimte. Deze Data Store heeft toegang tot het bestands systeem `test` in het `account_name` opslag account met behulp van de gegeven referenties voor de Service-Principal. Raadpleeg de sectie [machtigingen voor opslag toegang &](#storage-access-and-permissions) voor hulp bij scenario's voor virtuele netwerken en waar u de vereiste verificatie referenties kunt vinden. 

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```



## <a name="create-datastores-with-other-azure-tools"></a>Gegevens opslag maken met andere Azure-hulpprogram ma's
Naast het maken van gegevens opslag met de python-SDK en de Studio, kunt u ook Azure Resource Manager sjablonen of de Azure Machine Learning VS code-extensie gebruiken. 

<a name="arm"></a>
### <a name="azure-resource-manager"></a>Azure Resource Manager

Er zijn een aantal sjablonen op [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) die kunnen worden gebruikt voor het maken van gegevens opslag.

Zie [een Azure Resource Manager sjabloon gebruiken om een werk ruimte voor Azure machine learning te maken](how-to-create-workspace-template.md)voor meer informatie over het gebruik van deze sjablonen.

### <a name="vs-code-extension"></a>VS Code-extensie

Als u de gegevens opslag wilt maken en beheren met behulp van de Azure Machine Learning VS code-uitbrei ding, gaat u naar de [hand leiding voor het bron beheer over de VS-code](how-to-manage-resources-vscode.md#datastores) voor meer informatie.
<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Gegevens in uw gegevens opslag gebruiken

Nadat u een gegevens opslag hebt gemaakt, [maakt u een Azure machine learning gegevensset](how-to-create-register-datasets.md) om te communiceren met uw gegevens. Met gegevens sets kunt u in een vertraagd geëvalueerd voor machine learning-taken, zoals training. 

Met gegevens sets kunt u bestanden van elke indeling [downloaden of koppelen](how-to-train-with-datasets.md#mount-vs-download) van Azure Storage-services voor model trainingen op een reken doel. Meer [informatie over het trainen van ml-modellen met gegevens sets](how-to-train-with-datasets.md).

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Gegevens opslag ophalen uit uw werk ruimte

Als u een specifieke gegevens opslag die in de huidige werk ruimte is geregistreerd wilt ophalen, gebruikt u de [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-workspace--datastore-name-) statische methode voor de `Datastore` klasse:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Als u de lijst met gegevens opslag die is geregistreerd met een bepaalde werk ruimte wilt ophalen, kunt u de [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedatastores) eigenschap gebruiken voor een werkruimte object:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Als u de standaard gegevens opslag van de werk ruimte wilt ophalen, gebruikt u deze regel:

```Python
datastore = ws.get_default_datastore()
```
U kunt ook de standaard gegevens opslag wijzigen met de volgende code. Deze mogelijkheid wordt alleen ondersteund via de SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>Toegang tot gegevens tijdens de Score

Azure Machine Learning biedt verschillende manieren om uw modellen te gebruiken voor het scoren van punten. Sommige van deze methoden bieden geen toegang tot gegevens opslag. Gebruik de volgende tabel om inzicht te krijgen in de methoden waarmee u toegang hebt tot gegevens opslag in de Score:

| Methode | Toegang tot Data Store | Beschrijving |
| ----- | :-----: | ----- |
| [Batchvoorspelling](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | Doe asynchroon voorspellingen op grote hoeveelheden gegevens. |
| [Webservice](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen als een webservice. |
| [Module Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen om apparaten te IoT Edge. |

In situaties waarin de SDK geen toegang biedt tot gegevens opslag, kunt u mogelijk aangepaste code maken met behulp van de relevante Azure SDK om toegang te krijgen tot de data. De [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python) is bijvoorbeeld een client bibliotheek die u kunt gebruiken om toegang te krijgen tot gegevens die zijn opgeslagen in blobs of bestanden.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Gegevens verplaatsen naar ondersteunde Azure Storage-oplossingen

Azure Machine Learning ondersteunt het openen van gegevens vanuit Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. Als u niet-ondersteunde opslag gebruikt, raden we u aan om uw gegevens te verplaatsen naar ondersteunde Azure-opslag oplossingen door gebruik te maken van [Azure Data Factory en deze stappen uit te voeren](../data-factory/quickstart-create-data-factory-copy-data-tool.md). Het verplaatsen van gegevens naar ondersteunde opslag kan u helpen bij het opslaan van de kosten voor de uitvoer van gegevens tijdens machine learning experimenten. 

Azure Data Factory biedt een efficiënte en robuuste gegevens overdracht met meer dan 80 vooraf ontwikkelde connectors zonder extra kosten. Deze connectors zijn onder andere Azure Data Services, on-premises gegevens bronnen, Amazon S3 en Redshift en Google BigQuery.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md)
* [Een model trainen](how-to-set-up-training-targets.md)
* [Een model implementeren](how-to-deploy-and-where.md)