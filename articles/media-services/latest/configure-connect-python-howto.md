---
title: Verbinding maken met Azure Media Services v3 API-python
description: In dit artikel wordt beschreven hoe u verbinding maakt met Media Services v3 API met python.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 24e2ba4027dc818256dc9572f697fe7ec5a5a56b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960687"
---
# <a name="connect-to-media-services-v3-api---python"></a>Verbinding maken met Media Services v3 API-python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt beschreven hoe u verbinding maakt met de Azure Media Services v3 python SDK met behulp van de aanmeldings methode voor de Service-Principal.

## <a name="prerequisites"></a>Vereisten

- Python downloaden via [python.org](https://www.python.org/downloads/)
- Zorg ervoor dat u de `PATH` omgevings variabele instelt
- [Een Azure Media Services-account maken](./account-create-how-to.md). Vergeet niet welke namen u gebruikt voor de resourcegroep en het Media Services-account.
- Volg de stappen in het onderwerp [Access api's](./access-api-howto.md) om de Service-Principal-verificatie methode te selecteren. Noteer de abonnements-ID ( `SubscriptionId` ), de client-id van de toepassing () `AadClientId` , de verificatie sleutel ( `AadSecret` ) en de Tenant-id ( `AadTenantId` ) die u nodig hebt in de volgende stappen.

> [!IMPORTANT]
> Bekijk [naamconventies](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>De modules installeren

Als u wilt werken met Azure Media Services met behulp van python, moet u deze modules installeren.

* De `azure-mgmt-resource` module, waaronder Azure-modules voor Active Directory.
* De `azure-mgmt-media` module, die de Media Services entiteiten bevat.

    Zorg ervoor dat u [de nieuwste versie van de Media Services SDK voor python](https://pypi.org/project/azure-mgmt-media/)krijgt.

Open een opdracht regel programma en gebruik de volgende opdrachten om de modules te installeren.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Verbinding maken met de python-client

1. Een bestand met een `.py` extensie maken
1. Open het bestand in uw favoriete editor
1. Voeg de volgende code toe aan het bestand. Met de code worden de vereiste modules geïmporteerd en wordt het Active Directory-referentie object gemaakt dat u nodig hebt om verbinding te maken met Media Services.

      Stel de waarden van de variabelen in op de waarden die u hebt ontvangen van [Access-api's](./access-api-howto.md). Werk de `ACCOUNT_NAME` `RESOUCE_GROUP_NAME` variabelen en bij naar de naam van het Media Services account en de namen van de resource groepen die worden gebruikt bij het maken van deze resources.

      ```
      import adal
      from azure.mgmt.media import AzureMediaServices
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

      RESOURCE = "https://management.core.windows.net/"
      # Tenant ID for your Azure Subscription (AadTenantId)
      TENANT_ID = "00000000-0000-0000-000000000000"
      # Your Service Principal App ID (AadClientId)
      CLIENT = "00000000-0000-0000-000000000000"
      # Your Service Principal Password (AadSecret)
      KEY = "00000000-0000-0000-000000000000"
      # Your Azure Subscription ID (SubscriptionId)
      SUBSCRIPTION_ID = "00000000-0000-0000-000000000000"
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = "amsaccount"
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = "amsResourceGroup"

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + "/" + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials, RESOURCE, CLIENT, KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print(client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Het bestand uitvoeren

## <a name="next-steps"></a>Volgende stappen

- [Python-SDK](https://aka.ms/ams-v3-python-sdk)gebruiken.
- Raadpleeg de [Python-naslagdocumentatie](/python/api/overview/azure/mediaservices/management) van Media Services.
