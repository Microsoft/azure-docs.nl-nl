---
title: Python-service-naar-service-verificatie-Data Lake Storage Gen1
description: Meer informatie over service-to-service-verificatie met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory met behulp van python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: fe987070438c689e899dee18edaf4f708555ab11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106753"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Service-naar-service verificatie met Azure Data Lake Storage Gen1 met behulp van python
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK gebruiken](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-service-to-service-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

In dit artikel leert u hoe u de python-SDK kunt gebruiken om service-naar-service-verificatie met Azure Data Lake Storage Gen1 uit te voeren. Zie voor verificatie door eind gebruikers met Data Lake Storage Gen1 met behulp van python [verificatie van eind gebruikers met data Lake Storage gen1 met behulp van python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Vereisten

* **Python**. U kunt Python [hier](https://www.python.org/downloads/) downloaden. In dit artikel wordt Python 3.6.2 gebruikt.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure Active Directory Web-toepassing maken**. U moet de stappen in [service-to-service-verificatie met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md)hebben voltooid.

## <a name="install-the-modules"></a>De modules installeren

Als u wilt werken met Data Lake Storage Gen1 met behulp van python, moet u drie modules installeren.

* Module `azure-mgmt-resource`, die Azure-modules bevat voor Active Directory enzovoort.
* De `azure-mgmt-datalake-store` module, die de data Lake Storage gen1 account beheer bewerkingen bevat. Zie voor meer informatie over deze module, de [referentie voor Azure data Lake Storage gen1 management module](/python/api/azure-mgmt-datalake-store/).
* De `azure-datalake-store` module, die de data Lake Storage gen1 bestandssysteem bewerkingen bevat. Zie voor meer informatie over deze module [Azure-datalake-Store module Reference](/python/api/azure-datalake-store/azure.datalake.store.core/).

Gebruik de volgende opdrachten om de modules te installeren.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Gebruik de gewenste IDE om een nieuwe Python-toepassing te maken, bijvoorbeeld **mysample.py**.

2. Voeg het volgende codefragment toe om de vereiste modules te importeren

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Sla de wijzigingen in mysample.py op.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Service-naar-serviceverificatie met clientgeheim voor accountbeheer

Gebruik dit fragment om te verifiëren met Azure AD voor account beheer bewerkingen op Data Lake Storage Gen1, zoals het maken van een Data Lake Storage Gen1 account, het verwijderen van een Data Lake Storage Gen1 account, enzovoort. Het volgende code fragment kan worden gebruikt voor het niet-interactief verifiëren van uw toepassing, met behulp van het client geheim voor een toepassing/Service-Principal van een bestaande Azure AD-toepassing voor web-apps.

```python
authority_host_uri = 'https://login.microsoftonline.com'
tenant = '<TENANT>'
authority_uri = authority_host_uri + '/' + tenant
RESOURCE = 'https://management.core.windows.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

context = adal.AuthenticationContext(authority_uri, api_version=None)
mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)
```

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Service-naar-serviceverificatie met clientgeheim voor bestandssysteembewerkingen

Gebruik het volgende code fragment om te verifiëren met Azure AD voor bestandssysteem bewerkingen op Data Lake Storage Gen1, zoals het maken van een map, het uploaden van een bestand, enzovoort. Het volgende code fragment kan worden gebruikt voor het niet-interactief verifiëren van uw toepassing, met behulp van het client geheim voor een toepassing/Service-Principal. Gebruik dit met een bestaande Azure AD-toepassing voor webtoepassingen.

```python
tenant = '<TENANT>'
RESOURCE = 'https://datalake.azure.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

adlCreds = lib.auth(tenant_id = tenant,
                client_secret = client_secret,
                client_id = client_id,
                resource = RESOURCE)
```

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u service-naar-service-verificatie kunt gebruiken om te verifiëren met Data Lake Storage Gen1 met behulp van python. U kunt nu de volgende artikelen bekijken over het gebruik van python om met Data Lake Storage Gen1 te werken.

* [Account beheer bewerkingen op Data Lake Storage Gen1 met behulp van python](data-lake-store-get-started-python.md)
* [Gegevens bewerkingen op Data Lake Storage Gen1 met behulp van python](data-lake-store-data-operations-python.md)