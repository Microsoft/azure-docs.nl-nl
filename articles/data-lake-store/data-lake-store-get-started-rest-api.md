---
title: Een Azure Data Lake Storage Gen1-account beheren met REST
description: Gebruik de REST API WebHDFS om account beheer bewerkingen uit te voeren op een Azure Data Lake Storage Gen1-account.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 13467a51b2a06dbc0ca0ec5eadd139fde8b82ad0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103489"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Account beheer bewerkingen op Azure Data Lake Storage Gen1 met behulp van REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

In dit artikel vindt u informatie over het uitvoeren van account beheer bewerkingen op Azure Data Lake Storage Gen1 met behulp van de REST API. Account beheer bewerkingen zijn onder andere het maken van een Data Lake Storage Gen1 account, het verwijderen van een Data Lake Storage Gen1 account, enzovoort. Zie voor instructies over het uitvoeren van bestandssysteem bewerkingen op Data Lake Storage Gen1 met behulp van REST API [bestandssysteem bewerkingen op Data Lake Storage gen1 met behulp van rest API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Vereisten
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **[krul](https://curl.haxx.se/)**. Dit artikel maakt gebruik van krul om te laten zien hoe u REST API aanroepen voor een Data Lake Storage Gen1 account maakt.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hoe verifieer ik met Azure Active Directory?
Er zijn twee benaderingen voor verificatie met Azure Active Directory.

* Voor verificatie door eind gebruikers voor uw toepassing (interactief), Zie [verificatie door eind gebruikers met data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md)voor service-naar-service verificatie voor uw toepassing (niet-interactief).


## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken
Deze bewerking is gebaseerd op de REST-API-aanroep die [hier](/rest/api/datalakestore/accounts/create) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht: Vervang door **\<yourstoragegen1name>** de naam van uw data Lake Storage gen1.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"
```

Vervang \<`REDACTED`\> in de bovenstaande opdracht door het verificatietoken dat u eerder hebt opgehaald. De nettolading van de aanvraag voor deze opdracht zit in het bestand **input.json** dat is opgegeven voor de parameter `-d` hierboven. De inhoud van het bestand input.json ziet er ongeveer als uit als het volgende codefragment:

```json
{
"location": "eastus2",
"tags": {
    "department": "finance"
    },
"properties": {}
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1 account verwijderen
Deze bewerking is gebaseerd op de REST-API-aanroep die [hier](/rest/api/datalakestore/accounts/delete) wordt gedefinieerd.

Gebruik de volgende krul opdracht om een Data Lake Storage Gen1 account te verwijderen. Vervang door **\<yourstoragegen1name>** de naam van uw data Lake Storage gen1-account.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview
```

Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

```output
HTTP/1.1 200 OK
...
...
```

## <a name="next-steps"></a>Volgende stappen
* [Bestandssysteem bewerkingen op Data Lake Storage gen1 met behulp van rest API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Zie ook
* [Naslag informatie over Azure Data Lake Storage Gen1 REST API](/rest/api/datalakestore/)
* [Open source Big Data-toepassingen die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)