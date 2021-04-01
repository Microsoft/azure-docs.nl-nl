---
title: Verificatie door eind gebruikers-python met Data Lake Storage Gen1-Azure
description: Meer informatie over het bezorgen van de verificatie van eind gebruikers met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory met python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: c7ee58dec4e8bc04eb07695378e0d4dc973b6705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109082"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Verificatie door eind gebruikers met Azure Data Lake Storage Gen1 met behulp van python
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK gebruiken](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-end-user-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-end-user-authenticate-rest-api.md)
>
>

In dit artikel leert u hoe u de python-SDK kunt gebruiken om verificatie door eind gebruikers uit te voeren met Azure Data Lake Storage Gen1. Verificatie van eind gebruikers kan verder worden onderverdeeld in twee categorieën:

* Verificatie door eind gebruikers zonder multi-factor Authentication
* Verificatie door eind gebruikers met multi-factor Authentication

Beide opties worden in dit artikel besproken. Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van python](data-lake-store-service-to-service-authenticate-python.md)voor service-naar-service verificatie met data Lake Storage gen1 met python.

## <a name="prerequisites"></a>Vereisten

* **Python**. U kunt Python [hier](https://www.python.org/downloads/) downloaden. In dit artikel wordt Python 3.6.2 gebruikt.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory systeem eigen toepassing**. U moet de stappen in de [verificatie van eind gebruikers hebben voltooid met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>De modules installeren

Als u wilt werken met Data Lake Storage Gen1 met behulp van python, moet u drie modules installeren.

* Module `azure-mgmt-resource`, die Azure-modules bevat voor Active Directory enzovoort.
* De `azure-mgmt-datalake-store` module, die de Azure data Lake Storage gen1 account beheer bewerkingen bevat. Zie voor meer informatie over deze module, de [referentie voor Azure data Lake Storage gen1 management module](/python/api/azure-mgmt-datalake-store/).
* De `azure-datalake-store` module, die de Azure data Lake Storage gen1 bestandssysteem bewerkingen bevat. Zie voor meer informatie over deze module [Azure-datalake-Store module Reference](/python/api/azure-datalake-store/azure.datalake.store.core/).

Gebruik de volgende opdrachten om de modules te installeren.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Maak in de IDE van uw keuze een nieuwe python-toepassing, bijvoorbeeld **mysample.py**.

2. Voeg het volgende codefragment toe om de vereiste modules te importeren

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Sla de wijzigingen in mysample.py op.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Verificatie door eind gebruikers met multi-factor Authentication

### <a name="for-account-management"></a>Voor account beheer

Gebruik het volgende code fragment om te verifiëren met Azure AD voor account beheer bewerkingen op een Data Lake Storage Gen1-account. Het volgende fragment kan worden gebruikt om een toepassing te verifiëren met Multi-Factor Authentication. Geef de waarden hieronder op voor een bestaande **systeem eigen** Azure AD-toepassing.

```python
authority_host_url = "https://login.microsoftonline.com"
tenant = "FILL-IN-HERE"
authority_url = authority_host_url + '/' + tenant
client_id = 'FILL-IN-HERE'
redirect = 'urn:ietf:wg:oauth:2.0:oob'
RESOURCE = 'https://management.core.windows.net/'

context = adal.AuthenticationContext(authority_url)
code = context.acquire_user_code(RESOURCE, client_id)
print(code['message'])
mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)
```

### <a name="for-filesystem-operations"></a>Voor bestandssysteem bewerkingen

Gebruik dit om te verifiëren met Azure AD voor bestandssysteem bewerkingen op een Data Lake Storage Gen1-account. Het volgende fragment kan worden gebruikt om een toepassing te verifiëren met Multi-Factor Authentication. Geef de waarden hieronder op voor een bestaande **systeem eigen** Azure AD-toepassing.

```console
adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')
```

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Verificatie door eind gebruikers zonder multi-factor Authentication

Dit is afgeschaft. Zie [Azure-verificatie met behulp van PYTHON SDK](/azure/python/python-sdk-azure-authenticate)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u verificatie van eind gebruikers kunt gebruiken om te verifiëren met Azure Data Lake Storage Gen1 met python. U kunt nu de volgende artikelen bekijken over het gebruik van python om met Azure Data Lake Storage Gen1 te werken.

* [Account beheer bewerkingen op Data Lake Storage Gen1 met behulp van python](data-lake-store-get-started-python.md)
* [Gegevens bewerkingen op Data Lake Storage Gen1 met behulp van python](data-lake-store-data-operations-python.md)