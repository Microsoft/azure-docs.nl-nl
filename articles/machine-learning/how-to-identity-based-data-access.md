---
title: Op identiteit gebaseerde gegevens toegang tot opslag Services op Azure
titleSuffix: Machine Learning
description: Meer informatie over het gebruik van gegevens toegang op basis van identiteit om verbinding te maken met opslag Services in azure met Azure Machine Learning gegevens opslag en de Machine Learning python-SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210649"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Verbinding maken met de opslag met behulp van op identiteit gebaseerde gegevens toegang (preview-versie)

>[!IMPORTANT]
> De functies die in dit artikel worden weer gegeven, zijn beschikbaar als preview-versie. Ze moeten worden beschouwd als [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functies die op elk gewenst moment kunnen worden gewijzigd.

In dit artikel leert u hoe u verbinding kunt maken met opslag Services in azure met behulp van identiteits gegevens toegang en Azure Machine Learning data stores via de [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro).  

Normaal gesp roken gebruiken gegevens toegangs rechten op basis van referenties om te bevestigen dat u gemachtigd bent om toegang te krijgen tot de opslag service. Ze houden verbindings gegevens, zoals uw abonnements-ID en Token autorisatie, op in de [sleutel kluis](https://azure.microsoft.com/services/key-vault/) die is gekoppeld aan de werk ruimte. Wanneer u een gegevens opslag maakt die gebruikmaakt van op identiteit gebaseerde toegang tot Data Services, wordt uw Azure-account ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)) gebruikt om te bevestigen dat u gemachtigd bent om toegang te krijgen tot de opslag service. In dit scenario worden er geen verificatie referenties opgeslagen. Alleen de gegevens van het opslag account worden opgeslagen in het gegevens archief. 

Zie [verbinding maken met Storage services op Azure](how-to-access-data.md)als u gegevens opslag wilt maken die gebruikmaken van verificatie op basis van referenties, zoals toegangs sleutels of service-principals.

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Toegang tot gegevens op basis van identiteiten in Azure Machine Learning

Er zijn twee scenario's waarin u toegang tot gegevens op basis van identiteiten kunt Toep assen in Azure Machine Learning. Deze scenario's zijn geschikt voor toegang op basis van identiteiten wanneer u met vertrouwelijke gegevens werkt en meer nauw keuriger gegevens toegangs beheer nodig hebt:

- Opslag Services openen
- machine learning modellen trainen met persoonlijke gegevens

### <a name="accessing-storage-services"></a>Opslag Services openen

U kunt verbinding maken met opslag Services via toegang tot de identiteit op basis van gegevens met Azure Machine Learning data stores of [Azure machine learning gegevens sets](how-to-create-register-datasets.md). 

Uw verificatie referenties worden meestal opgeslagen in een gegevens opslag, die wordt gebruikt om ervoor te zorgen dat u gemachtigd bent om toegang te krijgen tot de opslag service. Wanneer deze referenties worden geregistreerd via gegevens opslag, kunnen gebruikers met de rol van werkruimte lezer ze ophalen. Deze schaal van toegang kan voor sommige organisaties een beveiligings probleem zijn. [Meer informatie over de rol van de werkruimte lezer.](how-to-assign-roles.md#default-roles) 

Wanneer u toegang tot gegevens op basis van een identiteit gebruikt, vraagt Azure Machine Learning u om uw Azure Active Directory-token voor verificatie van gegevens toegang in plaats van uw referenties in het gegevens archief te bewaren. Op die manier wordt het beheer van gegevens toegang op het opslag niveau toegestaan en blijven referenties vertrouwelijk. 

Hetzelfde gedrag geldt wanneer u:

* [Maak rechtstreeks vanuit opslag-url's een gegevensset](#use-data-in-storage). 
* Werk samen met gegevens interactief via een Jupyter Notebook op uw lokale computer of [reken instantie](concept-compute-instance.md).

> [!NOTE]
> De referenties die zijn opgeslagen via verificatie op basis van referenties zijn abonnements-Id's, SAS-tokens (Shared Access Signature) en toegangs sleutel voor opslag en Service-Principal-informatie, zoals client-Id's en Tenant-Id's.

### <a name="model-training-on-private-data"></a>Model training over privé gegevens

Bepaalde machine learning scenario's hebben betrekking op trainings modellen met persoonlijke gegevens. In dergelijke gevallen moeten gegevens wetenschappers trainings werk stromen uitvoeren zonder dat ze worden blootgesteld aan de vertrouwelijke invoer gegevens. In dit scenario wordt een beheerde identiteit van de trainings Compute gebruikt voor de verificatie van gegevens toegang. Met deze aanpak kunnen opslag beheerders toegang krijgen tot de gegevens lezer van de opslag-BLOB voor de beheerde identiteit die de trainings Compute gebruikt om de trainings taak uit te voeren. De individuele gegevens wetenschappers hoeven geen toegang te krijgen. Zie [Managed Identity instellen op een berekenings cluster](how-to-create-attach-compute-cluster.md#managed-identity)voor meer informatie.


## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- Een Azure-opslag account met een ondersteund opslag type. Deze opslag typen worden ondersteund in Preview: 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/install).

- Een Azure Machine Learning-werkruimte.
  
  [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md) of gebruik een [bestaand item via de python-SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Toegangs machtigingen voor opslag

Om ervoor te zorgen dat u veilig verbinding maakt met uw opslag service op Azure, moet u voor Azure Machine Learning toegang hebben tot de bijbehorende gegevens opslag.

Gegevens toegang op basis van identiteiten ondersteunt verbindingen met alleen de volgende opslag Services:

* Azure Blob Storage
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

Voor toegang tot deze opslag Services moet u ten minste toegang hebben tot de [opslag BLOB-gegevens lezer](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Alleen eigen aars van opslag accounts kunnen [uw toegangs niveau wijzigen via de Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).

Als u een model op een extern Compute-doel wilt trainen, moet aan de compute-identiteit ten minste de rol van BLOB-gegevens lezer voor opslag van de opslag service worden verleend. Meer informatie over het [instellen van een beheerde identiteit op een berekenings cluster](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Werken met virtuele netwerken

Azure Machine Learning kan standaard niet communiceren met een opslag account dat zich achter een firewall of in een virtueel netwerk bevindt.

U kunt opslag accounts zodanig configureren dat ze alleen toegang hebben tot binnen specifieke virtuele netwerken. Deze configuratie vereist extra stappen om ervoor te zorgen dat gegevens niet buiten het netwerk worden gelekt. Dit gedrag is hetzelfde voor op referenties gebaseerde gegevens toegang. Zie [scenario's voor virtuele netwerken configureren](how-to-access-data.md#virtual-network)voor meer informatie. 

## <a name="create-and-register-datastores"></a>Gegevens opslag maken en registreren

Wanneer u een opslag service in azure registreert als een Data Store, maakt en registreert u die gegevens archief automatisch aan een specifieke werk ruimte. Zie [opslag toegangs machtigingen](#storage-access-permissions) voor hulp bij de vereiste machtigings typen. Zie [werken met virtuele netwerken](#work-with-virtual-networks) voor meer informatie over het maken van verbinding met gegevens opslag achter virtuele netwerken.

In de volgende code ziet u het ontbreken van verificatie parameters zoals `sas_token` , `account_key` , `subscription_id` en de Service-Principal `client_id` . Dit weglating geeft aan dat Azure Machine Learning toegang tot gegevens op basis van identiteiten gebruikt voor verificatie. Het maken van gegevens opslag gebeurt doorgaans interactief in een notebook of via Studio. Uw Azure Active Directory-token wordt dus gebruikt voor verificatie van gegevens toegang.

> [!NOTE]
> Namen van gegevens opslag mogen alleen bestaan uit kleine letters, cijfers en onderstrepings tekens. 

### <a name="azure-blob-container"></a>Azure Blob-container

Als u een Azure Blob-container wilt registreren als gegevens opslag, gebruikt u [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

De volgende code maakt de `credentialless_blob` gegevens opslag, registreert deze aan de `ws` werk ruimte en wijst deze toe aan de `blob_datastore` variabele. Deze Data Store heeft toegang tot de `my_container_name` BLOB-container op het `my-account-name` opslag account.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Gebruik [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) om een gegevens opslag te registreren die verbinding maakt met Azure data Lake Storage gen1.

De volgende code maakt de `credentialless_adls1` gegevens opslag, registreert deze aan de `workspace` werk ruimte en wijst deze toe aan de `adls_dstore` variabele. Deze Data Store heeft toegang tot het `adls_storage` Azure data Lake Storage-account.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Gebruik [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) om een gegevens opslag te registreren die verbinding maakt met Azure data Lake Storage Gen2.

De volgende code maakt de `credentialless_adls2` gegevens opslag, registreert deze aan de `ws` werk ruimte en wijst deze toe aan de `adls2_dstore` variabele. Deze Data Store heeft toegang tot het bestands systeem `tabular` in het `myadls2` opslag account.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Gegevens in de opslag gebruiken

U wordt aangeraden [Azure machine learning gegevens sets](how-to-create-register-datasets.md) te gebruiken wanneer u met uw gegevens in Storage communiceert met Azure machine learning. 

Gegevens sets worden verpakt in een vertraagd geëvalueerd voor het verbruikte object voor machine learning taken als training. Met gegevens sets kunt u ook bestanden van elke indeling [downloaden of koppelen](how-to-train-with-datasets.md#mount-vs-download) van Azure Storage-services zoals Azure Blob Storage en Azure data Lake Storage naar een reken doel.


U hebt de volgende opties om gegevens sets te maken met toegang op basis van identiteiten. Bij het maken van dit type gegevensset wordt uw Azure Active Directory-token gebruikt voor verificatie van gegevens toegang. 

*  Referentie paden van data stores die ook toegang tot gegevens op basis van identiteiten gebruiken. 
<br>In het volgende voor beeld `blob_datastore` bestaat al en maakt gebruik van identiteits gegevens toegang.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Sla het maken van Data Store over en maak rechtstreeks gegevens sets van opslag-Url's. Deze functionaliteit ondersteunt momenteel alleen Azure-blobs en Azure Data Lake Storage Gen1 en Gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Wanneer u een trainings taak indient die gebruikmaakt van een gegevensset die is gemaakt met gegevens toegang op basis van identiteit, wordt de beheerde identiteit van de trainings Compute gebruikt voor verificatie van gegevens toegang. Uw Azure Active Directory-token wordt niet gebruikt. Voor dit scenario moet u ervoor zorgen dat aan de beheerde identiteit van de compute wordt voldaan ten minste de rol van BLOB-gegevens lezer voor opslag van de opslag service. Zie [Managed Identity instellen op compute clusters](how-to-create-attach-compute-cluster.md#managed-identity)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Machine Learning-gegevensset maken](how-to-create-register-datasets.md)
* [Trainen met gegevenssets](how-to-train-with-datasets.md)
* [Een gegevens opslag maken met toegang op basis van sleutels](how-to-access-data.md)
