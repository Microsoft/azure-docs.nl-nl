---
title: Op identiteit gebaseerde gegevens toegang tot opslag Services op Azure
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van gegevens toegang op basis van een identiteit om verbinding te maken met opslag Services in Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: dbfb4ea729b8360c7065d75cb3efbaf42b82c0da
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662457"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Verbinding maken met opslag met op identiteit gebaseerde gegevens toegang (preview-versie)

>[!IMPORTANT]
> De functies die in dit artikel worden gepresenteerd, zijn beschikbaar in de preview-versie en moeten worden beschouwd als [experimentele](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) preview-onderdelen die op elk gewenst moment kunnen worden gewijzigd.

In dit artikel leert u hoe u verbinding kunt maken met opslag Services op Azure met op identiteit gebaseerde gegevens toegang en Azure Machine Learning data stores via de [Azure machine learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).  

Normaal gesp roken gebruiken gegevens toegangs rechten op basis van referenties om te bevestigen dat u gemachtigd bent om toegang te krijgen tot de opslag service. Ze houden verbindings gegevens, zoals uw abonnements-ID en Token autorisatie, op in uw [Key Vault](https://azure.microsoft.com/services/key-vault/) dat is gekoppeld aan de werk ruimte. Wanneer u een gegevens opslag maakt die gebruikmaakt van op identiteit gebaseerde toegang tot Data Services, wordt uw Azure-aanmelding ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)) gebruikt om te bevestigen dat u gemachtigd bent om toegang te krijgen tot de opslag service. In dit scenario worden er geen verificatie referenties opgeslagen en worden alleen de gegevens van het opslag account opgeslagen in het gegevens archief. 

Zie [verbinding maken met Storage services op Azure](how-to-access-data.md)als u gegevens opslag wilt maken die verificatie op basis van referenties gebruiken, zoals toegangs sleutels of service-principals.

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Toegang tot gegevens op basis van identiteiten in Azure Machine Learning

Er zijn twee gebieden voor het Toep assen van toegang tot gegevens op basis van identiteiten in Azure Machine Learning. Met name bij het werken met vertrouwelijke gegevens en meer nauw keuriger beheer van gegevens toegang nodig is. 

1. Opslag Services openen.
1. Trainings machine learning modellen met persoonlijke gegevens.

### <a name="accessing-storage-services"></a>Opslag Services openen

U kunt verbinding maken met opslag Services via toegang tot de identiteit op basis van gegevens met Azure Machine Learning data stores of [Azure machine learning gegevens sets](how-to-create-register-datasets.md). 

Uw verificatie referenties worden meestal opgeslagen in een gegevens opslag, die wordt gebruikt om ervoor te zorgen dat u gemachtigd bent om toegang te krijgen tot de opslag service. Wanneer deze referenties bij data stores zijn geregistreerd, kan elke gebruiker met de rol van werkruimte *lezer* deze ophalen, wat een beveiligings probleem voor sommige organisaties kan zijn. Meer [informatie over de rol van de werkruimte *lezer*](how-to-assign-roles.md#default-roles). 

Wanneer u toegang tot gegevens op basis van een identiteit gebruikt, vraagt Azure Machine Learning u om uw Azure Active Directory token voor verificatie van gegevens toegang, in plaats van uw referenties in het gegevens archief te bewaren. Hiermee wordt het beheer van gegevens toegang op het opslag niveau en de vertrouwelijkheid van referenties gehandhaafd. 

Hetzelfde gedrag geldt wanneer u,

* [Maak rechtstreeks vanuit opslag-url's een gegevensset](#use-data-in-storage). 
* Werk met gegevens interactief met behulp van een Jupyter-notebook op uw lokale computer of [reken instantie](concept-compute-instance.md).

> [!NOTE]
> De referenties die zijn opgeslagen met verificatie op basis van referenties zijn onder andere: abonnements-ID, SAS-tokens (Shared Access Signature), toegangs sleutels voor opslag en Service-Principal-informatie, zoals client-ID en Tenant-ID.

### <a name="model-training-on-private-data"></a>Model training over privé gegevens

Bepaalde machine learning scenario's hebben betrekking op trainings modellen met persoonlijke gegevens. In dergelijke gevallen moeten gegevens wetenschappers trainings werk stromen uitvoeren zonder bloot stelling aan de vertrouwelijke invoer gegevens. In dit scenario wordt een beheerde identiteit van de trainings Compute gebruikt voor de verificatie van gegevens toegang. Op deze manier kunnen opslag beheerders toegang tot de **gegevens lezer** van de opslag-BLOB verlenen aan de beheerde identiteit die de trainings Compute gebruikt om de trainings taak uit te voeren, in plaats van de afzonderlijke gegevens wetenschappers. Meer informatie over het [instellen van een beheerde identiteit op een compute](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- Een Azure-opslag account met een ondersteund opslag type. De volgende opslag typen worden ondersteund in de preview-versie. 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake gen 1](../data-lake-store/index.yml)
    - [Azure Data Lake gen 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md) (Azure SQL-database)

- De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

- Een Azure Machine Learning-werkruimte.
  
  [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md) of gebruik een [bestaand item via de python-SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Toegangs machtigingen voor opslag

Om ervoor te zorgen dat u veilig verbinding maakt met uw opslag service op Azure, moet u voor Azure Machine Learning toegang hebben tot de bijbehorende gegevens opslag.

Toegang tot gegevens op basis van identiteiten ondersteunt alleen verbindingen met de volgende opslag Services:

* Azure Blob Storage
* Azure Data Lake generatie 1
* Azure Data Lake generatie 2
* Azure SQL-database

Voor toegang tot deze opslag Services moet u Mini maal toegang hebben tot de **opslag-BLOB-gegevens lezer** . Meer informatie over [Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Alleen eigen aars van opslag accounts kunnen [uw toegangs niveau wijzigen via de Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).

Als u een model op een extern Compute-doel uitoefent, moet de compute-identiteit worden verleend met ten minste de rol van **BLOB-gegevens lezer** van de opslag van de opslag service. Meer informatie over het [instellen van een beheerde identiteit op Compute](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Werken met virtuele netwerken

Azure Machine Learning kan standaard niet communiceren met een opslag account dat zich achter een firewall of binnen een virtueel netwerk bevindt.

Opslag accounts kunnen zodanig worden geconfigureerd dat ze alleen toegang toestaan vanuit specifieke virtuele netwerken, waarvoor extra configuraties nodig zijn om ervoor te zorgen dat gegevens niet buiten het netwerk worden gelekt. Dit gedrag is hetzelfde voor op referenties gebaseerde gegevens toegang, Zie [welke configuraties nodig zijn en hoe u deze kunt Toep assen voor scenario's voor virtuele netwerken](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Gegevens opslag maken en registreren

Wanneer u een opslag service in azure registreert als een Data Store, maakt en registreert u die gegevens archief automatisch aan een specifieke werk ruimte. Bekijk deze secties: [toegangs machtigingen voor opslag](#storage-access-permissions) voor hulp bij de vereiste machtigings typen en [werk samen met het virtuele netwerk](#work-with-virtual-networks) voor meer informatie over het maken van verbinding met gegevens opslag achter virtuele netwerken.

In de volgende code ziet u het ontbreken van verificatie parameters, zoals `sas_token` , `account_key` , `subscription_id` of Service-Principal `client_id` . Dit niet-wegge laten, geeft aan dat Azure Machine Learning gegevens toegang op basis van de identiteit wilt gebruiken voor verificatie. Omdat het maken van gegevens opslag doorgaans interactief plaatsvindt in een notebook of via Studio, wordt uw Azure Active Directory-token gebruikt voor verificatie van gegevens toegang.

> [!NOTE]
> Namen van gegevens opslag mogen alleen bestaan uit kleine letters, cijfers en onderstrepings tekens. 

### <a name="azure-blob-container"></a>Azure Blob-container

Als u een Azure Blob-container wilt registreren als gegevens opslag, gebruikt u [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

De volgende code maakt en registreert de `credentialless_blob` gegevens opslag voor de `ws` werk ruimte en wijst deze toe aan de variabele `blob_datastore` . Deze Data Store heeft toegang tot de `my_container_name` BLOB-container op het `my-account-name` opslag account.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage generatie 1

Gebruik [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) om een gegevens opslag te registreren die is verbonden met een Azure DataLake Generation 1-opslag. Azure data Lake Storage

De volgende code maakt en registreert de `credentialless_adls1` gegevens opslag voor de `workspace` werk ruimte en wijst deze toe aan de variabele `adls_dstore` . Deze Data Store heeft toegang tot het `adls_storage` Azure data Lake Store Storage-account.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generatie 2

Gebruik [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) om een gegevens opslag te registreren die is verbonden met een Azure DataLake gen 2-opslag. Azure data Lake Storage

De volgende code maakt en registreert de `credentialless_adls2` gegevens opslag voor de `ws` werk ruimte en wijst deze toe aan de variabele `adls2_dstore` . Deze Data Store heeft toegang tot het bestands systeem `tabular` in het `myadls2` opslag account.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Gegevens in de opslag gebruiken

[Azure machine learning gegevens sets](how-to-create-register-datasets.md) zijn de aanbevolen manier om met behulp van de-Azure machine learning te communiceren met uw gegevensopslag ruimte. 

Met gegevens sets kunt u in een vertraagd geëvalueerd voor machine learning-taken, zoals training. Met gegevens sets kunt u ook bestanden van elke indeling [downloaden of koppelen](how-to-train-with-datasets.md#mount-vs-download) van Azure Storage-services, zoals Azure Blob Storage en Azure data meren, naar een reken doel.


U hebt de volgende opties **om gegevens sets te maken met toegang op basis van identiteiten**. Dit type gegevensset wordt gemaakt met uw Azure Active Directory-token voor verificatie van gegevens toegang. 

*  Referentie paden van data stores die ook toegang tot gegevens op basis van identiteiten gebruiken. 
<br>In het volgende voor beeld `blob_datastore` is eerder gemaakt met behulp van op identiteit gebaseerde gegevens toegang.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Sla het maken van Data Store over en maak rechtstreeks gegevens sets van opslag-url's. Momenteel ondersteunt deze functionaliteit alleen Azure-blobs en Azure Data Lake Storage generaties 1 en 2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**Wanneer u echter een opleidings taak indient die gebruikmaakt van een gegevensset die is gemaakt met toegang tot gegevens op basis van identiteit**, wordt de beheerde identiteit van de trainings Compute gebruikt voor verificatie van gegevens toegang, in plaats van uw Azure Active Directory-token. Voor dit scenario moet u ervoor zorgen dat de beheerde identiteit van de compute wordt verleend met ten minste de rol van **BLOB-gegevens lezer voor opslag** van de opslag service. Meer informatie over het [instellen van een beheerde identiteit op Compute](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md).
* [Train met gegevens sets](how-to-train-with-datasets.md).
* [Maak een gegevens opslag met toegang op basis van sleutels](how-to-access-data.md).
