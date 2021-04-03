---
title: IP-adressen van de Azure API Management-service | Microsoft Docs
description: Meer informatie over het ophalen van de IP-adressen van een Azure API Management-service en wanneer deze worden gewijzigd.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80047733"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-adressen van Azure API Management

In dit artikel wordt beschreven hoe u de IP-adressen van de Azure API Management-service ophaalt. IP-adressen kunnen openbaar of privé zijn als de service zich in een virtueel netwerk bevindt.

U kunt IP-adressen gebruiken om firewall regels te maken, het binnenkomende verkeer te filteren op de back-end-services of het uitgaande verkeer te beperken.

## <a name="ip-addresses-of-api-management-service"></a>IP-adressen van API Management-service

Elk exemplaar van de API Management service in de laag ontwikkelaar, Basic, Standard of Premium heeft open bare IP-adressen, die uitsluitend voor dat service-exemplaar zijn bedoeld (ze worden niet gedeeld met andere resources). 

U kunt de IP-adressen ophalen uit het overzichts Dashboard van uw resource in de Azure Portal.

![API Management IP-adres](media/api-management-howto-ip-addresses/public-ip.png)

U kunt deze ook programmatisch ophalen met de volgende API-aanroep:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Open bare IP-adressen zullen deel uitmaken van het antwoord:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

In [meerdere regionale implementaties](api-management-howto-deploy-multi-region.md)heeft elke regionale implementatie één openbaar IP-adres.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-adressen van API Management service in VNet

Als uw API Management-service zich in een virtueel netwerk bevindt, heeft deze twee typen IP-adressen: openbaar en privé.

Open bare IP-adressen worden gebruikt voor interne communicatie op poort `3443` -voor het beheren van de configuratie (bijvoorbeeld via Azure Resource Manager). In de externe VNet-configuratie worden ze ook gebruikt voor runtime-API-verkeer. Wanneer een aanvraag wordt verzonden van API Management naar een open bare back-end (Internet Facing), is een openbaar IP-adres zichtbaar als de oorsprong van de aanvraag.

Privé-IP-adressen (VIP), **alleen** beschikbaar in de [interne VNet-modus](api-management-using-with-internal-vnet.md), worden gebruikt om vanuit het netwerk verbinding te maken met API Management eind punten-gateways, de ontwikkelaars Portal en het beheer vlak voor directe API-toegang. U kunt deze gebruiken voor het instellen van DNS-records in het netwerk.

U ziet adressen van beide typen in de Azure Portal en in het antwoord van de API-aanroep:

![API Management in IP-adres van VNet](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management gebruikt een openbaar IP-adres voor verbindingen buiten het VNet en een privé-IP-adres voor verbindingen binnen het VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-adressen van API Management service voor verbruiks lagen

Als uw API Management-service een verbruiks tier-service is, heeft deze geen toegewezen IP-adres. De service verbruiks tier wordt uitgevoerd op een gedeelde infra structuur en zonder een deterministisch IP-adres. 

U kunt het bereik van IP-adressen van Azure-Data Centers gebruiken voor het beperken van verkeers doeleinden. Raadpleeg [het artikel over Azure functions documentatie voor een](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) nauw keurige procedure.

## <a name="changes-to-the-ip-addresses"></a>Wijzigingen in de IP-adressen

In de lagen ontwikkelaar, basis, standaard en Premium van API Management zijn de open bare IP-adressen (VIP) statisch voor de levens duur van een service, met de volgende uitzonde ringen:

* De service wordt verwijderd en vervolgens opnieuw gemaakt.
* Het service abonnement wordt [opgeschort](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) of [gewaarschuwd](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (bijvoorbeeld voor niet-betaling) en vervolgens opnieuw ingesteld.
* Azure Virtual Network wordt toegevoegd aan of verwijderd uit de service.
* API Management-service wordt overgeschakeld tussen de externe en interne VNet-implementatie modus.

In [meerdere regionale implementaties](api-management-howto-deploy-multi-region.md)verandert het regionale IP-adres als een regio leeg is en opnieuw wordt ingesteld.
