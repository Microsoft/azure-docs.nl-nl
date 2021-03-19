---
title: Diagnostische logboeken bewaken via Azure Monitor
description: In dit artikel wordt beschreven hoe u Diagnostische logboeken kunt routeren en weer geven via Azure Monitor.
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
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0719fede4147943e76ddfea1c5e77388c7c5cc9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609320"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Diagnostische logboeken van Media Services controleren

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Met [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview.md) kunt u metrische gegevens en Diagnostische logboeken bewaken die u helpen begrijpen hoe uw toepassingen worden uitgevoerd. Zie [Media Services metrische gegevens en Diagnostische logboeken controleren](monitor-media-services.md)voor een gedetailleerde beschrijving van deze functie en om te zien waarom u Azure Media Services metrische gegevens en Diagnostische logboeken wilt gebruiken.

In dit artikel wordt beschreven hoe u gegevens naar het opslag account rondstuurt en vervolgens de gegevens bekijkt.

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](../create-account-howto.md).
- Controleer de  [Monitor Media Services](monitor-media-services.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Gegevens naar het opslag account routeren met behulp van de portal

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Navigeer naar uw Media Services-account in en klik op **Diagnostische instellingen** onder **monitor**. Hier kunt u een lijst zien van alle resources in uw abonnement die bewakingsgegevens via Azure Monitor genereren.

    ![Scherm opname van de diagnostische instellingen in de sectie bewaking.](../media/media-services-diagnostic-logs/logs01.png)

1. Klik op **Diagnostische instelling toevoegen**.

   De diagnostische instelling van een resource is een definitie van *welke* bewakingsgegevens moeten worden doorgestuurd vanuit een bepaalde resource en *waar* die bewakingsgegevens naartoe moeten worden gestuurd.

1. Geef in de weergegeven sectie de instelling een **naam** en schakel het selectievakje in voor **Archiveren naar een opslagaccount**.

    Selecteer het opslag account waarnaar u logboeken wilt verzenden en druk op **OK**.
1. Schakel alle selectievakjes onder **Logboek** en **Metrische gegevens** in. Afhankelijk van het resourcetype is er mogelijk maar één van deze opties beschikbaar. Met deze selectievakjes bepaalt u welke categorieën beschikbare logboekgegevens en metrische gegevens voor een bepaald resourcetype worden verzonden naar de bestemming die u hebt geselecteerd (in dit geval: een opslagaccount).

   ![Sectie Diagnostische instellingen](../media/media-services-diagnostic-logs/logs02.png)
1. Stel de schuifregelaar **Bewaarperiode (dagen)** in op 30. Met deze schuifregelaar stelt u in hoeveel dagen u de bewakingsgegevens wilt bewaren in het opslagaccount. Gegevens die ouder zijn dan het aantal opgegeven dagen, worden automatisch verwijderd. Bij een bewaarperiode van nul dagen worden de gegevens voor onbepaalde tijd opgeslagen.
1. Klik op **Opslaan**.

Bewakingsgegevens uit uw resource worden nu doorgestuurd naar het opslagaccount.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Gegevens naar het opslag account routeren met behulp van de Azure CLI

Als u de opslag van Diagnostische logboeken in een opslag account wilt inschakelen, voert u de volgende `az monitor diagnostic-settings` Azure cli-opdracht uit:

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Bijvoorbeeld:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Gegevens in het opslag account weer geven met behulp van de portal

Als u de voorgaande stappen hebt gevolgd, worden gegevens nu doorgestuurd naar uw opslagaccount.

Wellicht moet u vijf minuten wachten voordat de gebeurtenis in het opslagaccount wordt weergegeven.

1. Navigeer in de portal naar de sectie **Opslagaccounts** vanuit de linker navigatiebalk.
1. Identificeer het opslagaccount dat u in de voorgaande sectie hebt gemaakt en klik erop.
1. Klik op **blobs** en vervolgens op de container met het label **Insights-logs-keydeliveryrequests**. Dit is de container waarin uw logboeken zich bevinden. Bewakings gegevens worden in containers onderverdeeld op Resource-ID en vervolgens op datum en tijd.
1. Ga naar het bestand PT1H.json door te klikken in de containers voor resource-id, datum en tijd. Klik op het bestand PT1H.json en op **Downloaden**.

 U kunt nu de JSON-gebeurtenis zien die in het opslagaccount werd opgeslagen.

### <a name="examples-of-pt1hjson"></a>Voor beelden van PT1H.jsop

#### <a name="clear-key-delivery-log"></a>Sleutel leverings logboek wissen

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine versleuteld sleutel leverings logboek

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="see-also"></a>Zie ook

* [Metrische gegevens van Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/data-platform.md)
* [Diagnostische logboeken Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)
* [Logboek gegevens van uw Azure-resources verzamelen en gebruiken](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)

## <a name="next-steps"></a>Volgende stappen

[Metrische gegevens bewaken](media-services-metrics-howto.md)
