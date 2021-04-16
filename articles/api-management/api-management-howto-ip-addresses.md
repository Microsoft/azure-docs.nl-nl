---
title: IP-adressen van Azure API Management-service | Microsoft Docs
description: Meer informatie over het ophalen van de IP-adressen van een Azure API Management-service en wanneer deze worden gewijzigd.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 5939292b6e810634723fada17521bb227764b989
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534032"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-adressen van Azure API Management

In dit artikel wordt beschreven hoe u de IP-adressen van Azure API Management ophalen. IP-adressen kunnen openbaar of privé zijn als de service zich in een virtueel netwerk.

U kunt IP-adressen gebruiken om firewallregels te maken, het binnenkomende verkeer naar de back-endservices te filteren of het uitgaande verkeer te beperken.

## <a name="ip-addresses-of-api-management-service"></a>IP-adressen van API Management service

Elk API Management service-exemplaar in de categorie Developer, Basic, Standard of Premium heeft openbare IP-adressen die alleen voor dat service-exemplaar zijn (ze worden niet gedeeld met andere resources). 

U kunt de IP-adressen ophalen uit het overzichtsdashboard van uw resource in de Azure Portal.

![API Management IP-adres](media/api-management-howto-ip-addresses/public-ip.png)

U kunt ze ook programmatisch ophalen met de volgende API-aanroep:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Openbare IP-adressen maken deel uit van het antwoord:

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

In [implementaties met meerdere regio's](api-management-howto-deploy-multi-region.md)heeft elke regionale implementatie één openbaar IP-adres.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-adressen van API Management-service in VNet

Als uw API Management zich in een virtueel netwerk, heeft deze twee typen IP-adressen: openbaar en privé.

Openbare IP-adressen worden gebruikt voor interne communicatie op poort - voor het beheren van configuratie `3443` (bijvoorbeeld via Azure Resource Manager). In de externe VNet-configuratie worden ze ook gebruikt voor runtime-API-verkeer. Wanneer een aanvraag wordt verzonden van API Management naar een openbare (internet gerichte) back-end, is een openbaar IP-adres zichtbaar als de oorsprong van de aanvraag.

Persoonlijke VIP-adressen (virtueel IP-adres), die alleen beschikbaar zijn **in** de interne [VNet-modus,](api-management-using-with-internal-vnet.md)worden gebruikt om vanuit het netwerk verbinding te maken met API Management-eindpunten: gateways, de ontwikkelaarsportal en het beheervlak voor directe API-toegang. U kunt ze gebruiken voor het instellen van DNS-records in het netwerk.

U ziet adressen van beide typen in de Azure Portal en in het antwoord van de API-aanroep:

![API Management in ip-adres van VNet](media/api-management-howto-ip-addresses/vnet-ip.png)


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

API Management maakt gebruik van een openbaar IP-adres voor verbindingen buiten het VNet en een privé-IP-adres voor verbindingen binnen het VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-adressen van verbruikslaag API Management service

Als uw API Management service een verbruikslaagservice is, heeft deze geen toegewezen IP-adres. Service van de verbruikslaag wordt uitgevoerd op een gedeelde infrastructuur en zonder een deterministisch IP-adres. 

Voor verkeersbeperkingsdoeleinden kunt u het bereik van IP-adressen van Azure-datacenters gebruiken. Raadpleeg het [Azure Functions voor nauwkeurige](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) stappen.

## <a name="changes-to-the-ip-addresses"></a>Wijzigingen in de IP-adressen

In de laag Developer, Basic, Standard en Premium van API Management zijn de openbare IP-adressen (VIP) statisch voor de levensduur van een service, met de volgende uitzonderingen:

* De service wordt verwijderd en vervolgens opnieuw gemaakt.
* Het serviceabonnement [wordt opgeschort of](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) [gewaarschuwd](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (bijvoorbeeld voor niet-vooruitbetaling) en wordt vervolgens opnieuw in gebruik genomen.
* Azure Virtual Network wordt toegevoegd aan of verwijderd uit de service.
* API Management-service wordt overgeschakeld tussen de implementatiemodus Extern en Intern VNet.

In [implementaties met meerdere regio's](api-management-howto-deploy-multi-region.md)verandert het regionale IP-adres als een regio wordt vrijgemaakt en vervolgens opnieuw wordt hersteld. Het regionale IP-adres verandert ook wanneer u beschikbaarheidszones in- of [verwijdert.](zone-redundancy.md)
