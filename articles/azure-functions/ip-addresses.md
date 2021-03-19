---
title: IP-adressen in Azure Functions
description: Meer informatie over het vinden van binnenkomende en uitgaande IP-adressen voor functie-apps en wat ertoe leidt dat deze worden gewijzigd.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 2c248756899459e17082bcab863a4e857b594909
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608228"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-adressen in Azure Functions

In dit artikel worden de volgende concepten beschreven met betrekking tot IP-adressen van functie-apps:

* Zoeken naar de IP-adressen die momenteel worden gebruikt door een functie-app.
* Omstandigheden die ertoe leiden dat de IP-adressen van de functie-app worden gewijzigd.
* Het beperken van de IP-adressen die toegang hebben tot een functie-app.
* Specifieke IP-adressen definiëren voor een functie-app.

IP-adressen zijn gekoppeld aan functie-apps, niet met afzonderlijke functies. Inkomende HTTP-aanvragen kunnen het inkomende IP-adres niet gebruiken voor het aanroepen van afzonderlijke functies. ze moeten de standaard domein naam (functionappname.azurewebsites.net) of een aangepaste domein naam gebruiken.

## <a name="function-app-inbound-ip-address"></a>Inkomend IP-adres van functie-app

Elke functie-app heeft één inkomend IP-adres. Het IP-adres zoeken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer naar de functie-app.
3. Selecteer onder **Alle instellingen** de optie **Eigenschappen**. Het inkomende IP-adres wordt weer gegeven onder **virtueel IP-adres**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Uitgaande IP-adressen van functie-app

Elke functie-app heeft een aantal beschik bare uitgaande IP-adressen. Elke uitgaande verbinding van een functie, zoals een back-enddatabase, gebruikt een van de beschik bare uitgaande IP-adressen als het IP-begin adres van de bron. U weet niet vooraf welk IP-adres een bepaalde verbinding zal gebruiken. Daarom moet uw back-end-service de firewall openen voor alle uitgaande IP-adressen van de functie-app.

De uitgaande IP-adressen vinden die beschikbaar zijn voor een functie-app:

1. Meld u aan bij de [Azure resource Explorer](https://resources.azure.com).
2. Selecteer **abonnementen > {Your Subscription} > providers > micro soft. Web >-sites**.
3. Zoek in het deel venster JSON de site met een `id` eigenschap die eindigt op de naam van uw functie-app.
4. Zie `outboundIpAddresses` en `possibleOutboundIpAddresses` . 

De set van `outboundIpAddresses` is momenteel beschikbaar voor de functie-app. De set van `possibleOutboundIpAddresses` bevat IP-adressen die alleen beschikbaar zijn als de functie-app wordt [geschaald naar andere prijs categorieën](#outbound-ip-address-changes).

Een alternatieve manier om de beschik bare uitgaande IP-adressen te vinden, is met behulp van de [Cloud shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> Wanneer een functie-app die wordt uitgevoerd op het [verbruiks plan](consumption-plan.md) of het [Premium-plan](functions-premium-plan.md) wordt geschaald, kan een nieuw bereik aan uitgaande IP-adressen worden toegewezen. Wanneer u een van deze abonnementen uitvoert, moet u mogelijk het hele Data Center toevoegen aan een acceptatie lijst.

## <a name="data-center-outbound-ip-addresses"></a>Uitgaande IP-adressen van data centers

Als u de uitgaande IP-adressen wilt toevoegen die door uw functie-apps worden gebruikt voor een allowlist, is een andere optie het toevoegen van de functie-apps Data Center (Azure Region) aan een allowlist. U kunt [een JSON-bestand downloaden met een lijst met IP-adressen voor alle Azure-data centers](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Zoek vervolgens het JSON-fragment dat van toepassing is op de regio waarin uw functie-app wordt uitgevoerd.

Zo kan het volgende JSON-fragment bijvoorbeeld het allowlist voor West-Europa zien:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Voor informatie over wanneer dit bestand wordt bijgewerkt en wanneer de IP-adressen worden gewijzigd, vouwt u de sectie **Details** van de [pagina Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=56519)uit.

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Wijzigingen in het inkomende IP-adres

Het inkomende IP-adres **kan** veranderen wanneer u:

- Verwijder een functie-app en maak deze opnieuw in een andere resource groep.
- Verwijder de laatste functie-app in een combi natie van resource groep en regio en maak deze opnieuw.
- Verwijder een TLS-binding, bijvoorbeeld tijdens het vernieuwen van het [certificaat](../app-service/configure-ssl-certificate.md#renew-certificate).

Wanneer uw functie-app wordt uitgevoerd in een [verbruiks abonnement](consumption-plan.md) of in een [Premium-abonnement](functions-premium-plan.md), kan het binnenkomende IP-adres ook worden gewijzigd, zelfs als u geen acties hebt uitgevoerd zoals [hierboven vermeld](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Wijzigingen in het uitgaande IP-adres

De set beschik bare uitgaande IP-adressen voor een functie-app kan veranderen wanneer u:

* Onderneem elke actie die het inkomende IP-adres kan wijzigen.
* Wijzig de prijs categorie van uw App Service abonnement. De lijst met alle mogelijke uitgaande IP-adressen die uw app kan gebruiken voor alle prijs categorieën, bevindt zich in de `possibleOutboundIPAddresses` eigenschap. Zie [uitgaande Ip's zoeken](#find-outbound-ip-addresses).

Wanneer uw functie-app wordt uitgevoerd in een [verbruiks abonnement](consumption-plan.md) of in een [Premium-abonnement](functions-premium-plan.md), kan het uitgaande IP-adres ook worden gewijzigd, zelfs als u geen acties hebt uitgevoerd zoals [hierboven vermeld](#inbound-ip-address-changes).

Gebruik de volgende procedure om de wijziging van een uitgaand IP-adres opzettelijk af te dwingen:

1. Schaal uw App Service plan omhoog of omlaag tussen de Standard-en Premium v2-prijs categorieën.

2. Wacht tien minuten.

3. Schaal terug naar de locatie waar u bent begonnen.

## <a name="ip-address-restrictions"></a>IP-adresbeperkingen

U kunt een lijst met IP-adressen die u wilt toestaan of weigeren van toegang tot een functie-app configureren. Zie [Azure app service static IP-beperkingen](../app-service/app-service-ip-restrictions.md)voor meer informatie.

## <a name="dedicated-ip-addresses"></a>Toegewezen IP-adressen

Er zijn verschillende strategieën om te ontdekken wanneer uw functie-app statische, specifieke IP-adressen nodig heeft. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>Virtuele netwerk gateway voor uitgaande vaste IP

U kunt het IP-adres van het uitgaande verkeer van uw functies beheren door gebruik te maken van een NAT-gateway van het virtuele netwerk om verkeer via een statisch openbaar IP-adres te sturen. U kunt deze topologie gebruiken wanneer u in een [Premium-abonnement](functions-premium-plan.md)wordt uitgevoerd. Voor meer informatie, Zie [zelf studie: Control Azure Functionsing van het uitgaande IP-adres met een NAT-gateway van het virtuele netwerk van Azure](functions-how-to-use-nat-gateway.md).

### <a name="app-service-environments"></a>App Service-omgevingen

Voor volledige controle over de IP-adressen, zowel binnenkomend als uitgaand, raden we [app service omgevingen](../app-service/environment/intro.md) aan (de [geïsoleerde tier](https://azure.microsoft.com/pricing/details/app-service/) of app service-abonnementen). Zie [app service Environment IP-adressen](../app-service/environment/network-info.md#ase-ip-addresses) en informatie [over het beheren van inkomend verkeer naar een app service Environment](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)voor meer informatie.

Als u wilt weten of uw functie-app wordt uitgevoerd in een App Service Environment:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer naar de functie-app.
3. Selecteer het tabblad **Overzicht**.
4. De laag App Service plan wordt weer gegeven onder **app service plan/prijs categorie**. De prijs Categorie App Service Environment is **geïsoleerd**.
 
Als alternatief kunt u het [Cloud shell](../cloud-shell/quickstart.md)gebruiken:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

De App Service Environment `sku` is `Isolated` .

## <a name="next-steps"></a>Volgende stappen

Een veelvoorkomende oorzaak van IP-wijzigingen is het wijzigen van de schaal van de app. Meer [informatie over het schalen van functie-apps](functions-scale.md).
