---
title: 'Python: bestandssysteem bewerkingen op Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Meer informatie over het gebruik van python SDK om met het Data Lake Storage Gen1-bestands systeem te werken.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 8ca455a802b180163579f67104a61f455dd54f94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109218"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Bestandssysteem bewerkingen op Azure Data Lake Storage Gen1 met behulp van python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

In dit artikel leert u hoe u python SDK gebruikt om bestandssysteem bewerkingen uit te voeren op Azure Data Lake Storage Gen1. Zie voor instructies over het uitvoeren van account beheer bewerkingen op Data Lake Storage Gen1 met behulp van python [account beheer bewerkingen op Data Lake Storage gen1 met behulp van python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Vereisten

* **Python**. U kunt Python [hier](https://www.python.org/downloads/) downloaden. In dit artikel wordt Python 3.6.2 gebruikt.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Azure data Lake Storage gen1-account**. Volg de instructies in aan [de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>De modules installeren

Als u wilt werken met Data Lake Storage Gen1 met behulp van python, moet u drie modules installeren.

* Module `azure-mgmt-resource`, die Azure-modules bevat voor Active Directory enzovoort.
* De `azure-mgmt-datalake-store` module, die de Azure data Lake Storage gen1 account beheer bewerkingen bevat. Zie voor meer informatie over deze module de [Naslag Gids voor Azure-beheer-datalake-Store-module](/python/api/azure-mgmt-datalake-store/).
* De `azure-datalake-store` module, die de Azure data Lake Storage gen1 bestandssysteem bewerkingen bevat. Zie de naslag informatie voor de [module File-System van Azure-datalake-Store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Gebruik de volgende opdrachten om de modules te installeren.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Gebruik de gewenste IDE om een nieuwe Python-toepassing te maken, bijvoorbeeld **mysample.py**.

2. Voeg de volgende regels toe om de vereiste modules te importeren.

   ```python
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Sla de wijzigingen in mysample.py op.

## <a name="authentication"></a>Verificatie

In deze sectie bespreken we de verschillende manieren om te verifiëren met Azure AD. De beschikbare opties zijn:

* Voor verificatie door eind gebruikers voor uw toepassing raadpleegt [u verificatie door eind gebruikers met data Lake Storage gen1 met behulp van python](data-lake-store-end-user-authenticate-python.md).
* Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van python](data-lake-store-service-to-service-authenticate-python.md)voor service-naar-service verificatie voor uw toepassing.

## <a name="create-filesystem-client"></a>Bestandssysteemclient maken

In het volgende code fragment maakt u eerst de client voor het Data Lake Storage Gen1-account. Er wordt gebruikgemaakt van het-client object om een Data Lake Storage Gen1-account te maken. Ten slotte maakt het fragment een clientobject voor het bestandssysteem.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'

## Create a filesystem client object
adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)
```

## <a name="create-a-directory"></a>Een map maken

```python
## Create a directory
adlsFileSystemClient.mkdir('/mysampledirectory')
```

## <a name="upload-a-file"></a>Een bestand uploaden

```python
## Upload a file
multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```


## <a name="download-a-file"></a>Een bestand downloaden

```python
## Download a file
multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```

## <a name="delete-a-directory"></a>Een map verwijderen

```python
## Delete a directory
adlsFileSystemClient.rm('/mysampledirectory', recursive=True)
```

## <a name="next-steps"></a>Volgende stappen
* [Account beheer bewerkingen op Data Lake Storage gen1 met behulp van python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Zie ook

* [Naslag informatie over Azure Data Lake Storage Gen1 python (bestands systeem)](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Open source Big Data-toepassingen die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)