---
title: Op identiteit gebaseerde gegevenstoegang tot opslagservices in Azure
titleSuffix: Machine Learning
description: Meer informatie over het gebruik van op identiteit gebaseerde gegevenstoegang om verbinding te maken met opslagservices in Azure met Azure Machine Learning-gegevensopslag en de python Machine Learning-SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 8bc85e8991ebf0a5ba7418f63943b0515c9f264d
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884998"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Verbinding maken met opslag met behulp van op identiteit gebaseerde gegevenstoegang (preview)

>[!IMPORTANT]
> De functies in dit artikel zijn beschikbaar als preview-versie. Ze moeten worden beschouwd als [experimentele](/python/api/overview/azure/ml/#stable-vs-experimental) preview-functies die op elk moment kunnen worden gewijzigd.

In dit artikel leert u hoe u verbinding maakt met opslagservices in Azure met behulp van op identiteit gebaseerde gegevenstoegang en Azure Machine Learning-gegevensopslag via de [Azure Machine Learning SDK voor Python.](/python/api/overview/azure/ml/intro)  

Normaal gesproken gebruiken gegevensopslag op basis van referenties toegang om te bevestigen dat u toegang hebt tot de opslagservice. Ze bewaren verbindingsgegevens, zoals uw abonnements-id en tokenautorisatie, in de [sleutelkluis](https://azure.microsoft.com/services/key-vault/) die is gekoppeld aan de werkruimte. Wanneer u een gegevensopslag maakt die gebruikmaakt van op identiteit gebaseerde gegevenstoegang, wordt uw Azure-account[(Azure Active Directory-token)](../active-directory/fundamentals/active-directory-whatis.md)gebruikt om te bevestigen dat u toegang hebt tot de opslagservice. In dit scenario worden geen verificatiereferenties opgeslagen. Alleen de gegevens van het opslagaccount worden opgeslagen in de gegevensopslag. 

Zie Verbinding maken met opslagservices in Azure voor het maken van gegevensopslag die gebruikmaken van verificatie op basis [van referenties,](how-to-access-data.md)zoals toegangssleutels of service-principals.

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Gegevenstoegang op basis van identiteit in Azure Machine Learning

Er zijn twee scenario's waarin u op identiteit gebaseerde gegevenstoegang kunt toepassen in Azure Machine Learning. Deze scenario's zijn geschikt voor toegang op basis van identiteiten wanneer u met vertrouwelijke gegevens werkt en meer gedetailleerd toegangsbeheer nodig hebt:

- Toegang tot opslagservices
- Trainingsmodellen machine learning persoonlijke gegevens

### <a name="accessing-storage-services"></a>Toegang tot opslagservices

U kunt verbinding maken met opslagservices via op identiteit gebaseerde gegevenstoegang met Azure Machine Learning gegevensopslag of [Azure Machine Learning gegevenssets](how-to-create-register-datasets.md). 

Uw verificatiereferenties worden meestal bewaard in een gegevensopslag, die wordt gebruikt om ervoor te zorgen dat u toegang hebt tot de opslagservice. Wanneer deze referenties zijn geregistreerd via gegevensstores, kan elke gebruiker met de rol lezer van de werkruimte deze ophalen. Deze schaal van toegang kan voor sommige organisaties een beveiligingsrisico zijn. [Meer informatie over de rol Lezer van de werkruimte.](how-to-assign-roles.md#default-roles) 

Wanneer u op identiteit gebaseerde gegevenstoegang gebruikt, wordt Azure Machine Learning gevraagd om uw Azure Active Directory-token voor verificatie van gegevenstoegang in plaats van uw referenties in de gegevensstore te bewaren. Met deze benadering is gegevenstoegangsbeheer op opslagniveau mogelijk en blijven referenties vertrouwelijk. 

Hetzelfde gedrag is van toepassing wanneer u:

* [Maak een gegevensset rechtstreeks vanuit opslag-URL's.](#use-data-in-storage) 
* Interactief met gegevens werken via een Jupyter Notebook op uw lokale computer of [reken-exemplaar](concept-compute-instance.md).

> [!NOTE]
> Referenties die zijn opgeslagen via verificatie op basis van referenties, zijn abonnements-ID's, SAS-tokens (Shared Access Signature) en informatie over de opslagtoegangssleutel en service-principal, zoals client-ID's en tenant-ID's.

### <a name="model-training-on-private-data"></a>Modeltraining voor privégegevens

Bepaalde machine learning hebben betrekking op trainingsmodellen met persoonlijke gegevens. In dergelijke gevallen moeten gegevenswetenschappers trainingswerkstromen uitvoeren zonder dat ze worden blootgesteld aan de vertrouwelijke invoergegevens. In dit scenario wordt een beheerde identiteit van het trainingsrekenproces gebruikt voor verificatie van gegevenstoegang. Met deze benadering kunnen opslagbeheerders Storage Blob Data Reader toegang verlenen tot de beheerde identiteit die door de trainingsrekenkracht wordt gebruikt om de trainings job uit te voeren. De afzonderlijke gegevenswetenschappers hoeven geen toegang te krijgen. Zie Beheerde identiteit instellen op [een rekencluster voor meer informatie.](how-to-create-attach-compute-cluster.md#managed-identity)


## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

- Een Azure-opslagaccount met een ondersteund opslagtype. Deze opslagtypen worden ondersteund in de preview-versie: 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- De [Azure Machine Learning-SDK voor Python](/python/api/overview/azure/ml/install).

- Een Azure Machine Learning-werkruimte.
  
  Maak [een Azure Machine Learning werkruimte of](how-to-manage-workspace.md) gebruik een bestaande werkruimte via de Python [SDK.](how-to-manage-workspace.md#connect-to-a-workspace) 

## <a name="storage-access-permissions"></a>Toegangsmachtigingen voor opslag

Om ervoor te zorgen dat u veilig verbinding maakt met uw opslagservice in Azure, moet Azure Machine Learning toegang hebben tot de bijbehorende gegevensopslag.

Op identiteit gebaseerde gegevenstoegang ondersteunt alleen verbindingen met de volgende opslagservices:

* Azure Blob Storage
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

Als u toegang wilt krijgen tot deze opslagservices, moet u ten minste toegang hebben [tot Storage Blob Data Reader.](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Alleen eigenaren van opslagaccounts [kunnen uw toegangsniveau wijzigen via de Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).

Als u een model traint op een extern rekendoel, moet aan de rekenidentiteit ten minste de rol Lezer van opslagblobgegevens van de opslagservice worden verleend. Meer informatie over [het instellen van een beheerde identiteit op een rekencluster.](how-to-create-attach-compute-cluster.md#managed-identity)

## <a name="work-with-virtual-networks"></a>Werken met virtuele netwerken

Standaard kunnen Azure Machine Learning niet communiceren met een opslagaccount dat zich achter een firewall of in een virtueel netwerk bevinden.

U kunt opslagaccounts zo configureren dat alleen toegang vanuit specifieke virtuele netwerken wordt toegestaan. Deze configuratie vereist extra stappen om ervoor te zorgen dat gegevens niet buiten het netwerk worden gelekt. Dit gedrag is hetzelfde voor toegang tot gegevens op basis van referenties. Zie Scenario's voor virtuele netwerken [configureren voor meer informatie.](how-to-access-data.md#virtual-network) 

## <a name="create-and-register-datastores"></a>Gegevensstores maken en registreren

Wanneer u een opslagservice in Azure registreert als een gegevensopslag, maakt en registreert u die gegevensopslag automatisch in een specifieke werkruimte. Zie [Toegangsmachtigingen voor opslag](#storage-access-permissions) voor hulp bij vereiste machtigingstypen. Zie [Werken met virtuele netwerken voor](#work-with-virtual-networks) meer informatie over het maken van verbinding met gegevensopslag achter virtuele netwerken.

In de volgende code ziet u dat er geen verificatieparameters `sas_token` zijn, zoals `account_key` , , en `subscription_id` de service-principal `client_id` . Deze weglating geeft aan dat Azure Machine Learning op identiteit gebaseerde gegevenstoegang gebruikt voor verificatie. Het maken van gegevensstores gebeurt doorgaans interactief in een notebook of via de studio. Uw Azure Active Directory wordt dus gebruikt voor verificatie van gegevenstoegang.

> [!NOTE]
> Gegevensstorenamen mogen alleen bestaan uit kleine letters, cijfers en onderstrepingstekens. 

### <a name="azure-blob-container"></a>Azure Blob-container

Gebruik om een Azure Blob-container te registreren als een [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) gegevensopslag.

Met de volgende code maakt u de gegevensstore, registreert u deze `credentialless_blob` in de werkruimte en wijst u deze toe aan de variabele `ws` `blob_datastore` . Deze gegevensopslag heeft toegang tot de `my_container_name` blobcontainer in `my-account-name` het opslagaccount.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Gebruik [register_azure_data_lake() om](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) een gegevensstore te registreren die verbinding maakt met Azure Data Lake Storage Gen1.

Met de volgende code maakt u de gegevensstore, registreert u deze `credentialless_adls1` in de werkruimte en wijst u deze toe aan de variabele `workspace` `adls_dstore` . Deze gegevensstore heeft toegang tot `adls_storage` Azure Data Lake Storage account.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Gebruik [register_azure_data_lake_gen2() om](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) een gegevensstore te registreren die verbinding maakt met Azure Data Lake Storage Gen2.

Met de volgende code maakt u de gegevensstore, registreert u deze `credentialless_adls2` in de werkruimte en wijst u deze toe aan de variabele `ws` `adls2_dstore` . Dit gegevensopslag heeft toegang tot het bestandssysteem `tabular` in het `myadls2` opslagaccount.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Gegevens in opslag gebruiken

We raden u aan om Azure Machine Learning [gegevenssets te gebruiken](how-to-create-register-datasets.md) wanneer u met uw gegevens in de opslag werkt met Azure Machine Learning. 

Gegevenssets verpakken uw gegevens in een lazily geëvalueerd verbruiksobject voor machine learning taken zoals training. Met gegevenssets kunt [](how-to-train-with-datasets.md#mount-vs-download) u ook bestanden van elke indeling downloaden of aan een rekendoel toevoegen vanuit Azure-opslagservices, Azure Blob Storage en Azure Data Lake Storage aan een rekendoel.


Als u gegevenssets wilt maken met op identiteit gebaseerde gegevenstoegang, hebt u de volgende opties. Dit type gegevensset maakt gebruik van uw Azure Active Directory-token voor verificatie van gegevenstoegang. 

*  Referentiepaden van gegevensstores die ook gebruikmaken van op identiteit gebaseerde gegevenstoegang. 
<br>In het volgende voorbeeld `blob_datastore` bestaat al en wordt gebruikgemaakt van op identiteit gebaseerde gegevenstoegang.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Sla het maken van gegevensopslag over en maak gegevenssets rechtstreeks vanuit opslag-URL's. Deze functionaliteit ondersteunt momenteel alleen Azure-blobs en Azure Data Lake Storage Gen1 gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Wanneer u een trainings job indient die gebruik maakt van een gegevensset die is gemaakt met op identiteit gebaseerde gegevenstoegang, wordt de beheerde identiteit van de trainingsrekenset gebruikt voor verificatie van gegevenstoegang. Uw Azure Active Directory token wordt niet gebruikt. Voor dit scenario moet u ervoor zorgen dat aan de beheerde identiteit van de rekenkracht ten minste de rol Lezer van opslagblobgegevens van de opslagservice wordt verleend. Zie Beheerde identiteit instellen op rekenclusters [voor meer informatie.](how-to-create-attach-compute-cluster.md#managed-identity) 

## <a name="next-steps"></a>Volgende stappen

* [Een gegevensset Azure Machine Learning maken](how-to-create-register-datasets.md)
* [Trainen met gegevenssets](how-to-train-with-datasets.md)
* [Een gegevensstore maken met op sleutels gebaseerde gegevenstoegang](how-to-access-data.md)
