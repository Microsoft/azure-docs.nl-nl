---
title: REST-service-naar-service-verificatie-Data Lake Storage Gen1-Azure
description: Meer informatie over het uitvoeren van service-to-service-verificatie met Azure Data Lake Storage Gen1 en Azure Active Directory met behulp van de REST API.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4947bab58a6d23bcc3e3212a9e3e7dc0c4fa8578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106702"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Service-naar-service verificatie met Azure Data Lake Storage Gen1 met behulp van REST API
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK gebruiken](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-service-to-service-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

In dit artikel leert u hoe u met behulp van de REST API service-to-service-verificatie kunt uitvoeren met Azure Data Lake Storage Gen1. Voor verificatie door eind gebruikers met Data Lake Storage Gen1 met behulp van REST API, Zie [verificatie door eind gebruikers met Data Lake Storage Gen1 rest API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure Active Directory Web-toepassing maken**. U moet de stappen in [service-to-service-verificatie met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md)hebben voltooid.

## <a name="service-to-service-authentication"></a>Verificatie van service-tot-service

In dit scenario biedt de toepassing zijn eigen referenties om de bewerkingen uit te voeren. Hiervoor moet u een POST-aanvraag uitgeven, zoals in het volgende code fragment:

```console
curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
  -F grant_type=client_credentials \
  -F resource=https://management.core.windows.net/ \
  -F client_id=<CLIENT-ID> \
  -F client_secret=<AUTH-KEY>
```

De uitvoer van de aanvraag bevat een autorisatie token (aangegeven door `access-token` in de onderstaande uitvoer) dat u vervolgens doorgeeft aan uw rest API-aanroepen. Sla het verificatie token op in een tekst bestand. u hebt deze nodig wanneer REST-aanroepen naar Data Lake Storage Gen1 worden uitgevoerd.

```output
{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}
```

In dit artikel wordt de **niet-interactieve** benadering gebruikt. Zie [Service-naar-service-aanroepen met referenties](/previous-versions/azure/dn645543(v=azure.100)) voor meer informatie over niet-interactieve (service-naar-service) aanroepen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u service-naar-service-verificatie kunt gebruiken om te verifiëren met Data Lake Storage Gen1 met behulp van REST API. U kunt nu de volgende artikelen bekijken om te praten over het gebruik van de REST API om met Data Lake Storage Gen1 te werken.

* [Account beheer bewerkingen op Data Lake Storage Gen1 met behulp van REST API](data-lake-store-get-started-rest-api.md)
* [Gegevens bewerkingen op Data Lake Storage Gen1 met behulp van REST API](data-lake-store-data-operations-rest-api.md)