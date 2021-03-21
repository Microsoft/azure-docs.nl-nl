---
title: Inkomende/uitgaande IP-adressen
description: Meer informatie over hoe binnenkomende en uitgaande IP-adressen worden gebruikt in Azure App Service, wanneer ze worden gewijzigd en hoe u de adressen voor uw app kunt vinden.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 4237e51251a7ece05800aa7efa328a9c6cf65e76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591364"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Binnenkomende en uitgaande IP-adressen in Azure App Service

[Azure app service](overview.md) is een service met meerdere tenants, met uitzonde ring van [app service omgevingen](environment/intro.md). Apps die zich niet in een App Service omgeving bevinden (niet in de [geïsoleerde laag](https://azure.microsoft.com/pricing/details/app-service/)) delen netwerk infrastructuur met andere apps. Als gevolg hiervan kunnen de inkomende en uitgaande IP-adressen van een app afwijken en kunnen zelfs in bepaalde situaties worden gewijzigd.

[App service omgevingen](environment/intro.md) gebruiken specifieke netwerk infrastructuren, zodat apps die in een app service omgeving worden uitgevoerd, statische, specifieke IP-adressen voor binnenkomende en uitgaande verbindingen krijgen.

## <a name="how-ip-addresses-work-in-app-service"></a>Hoe IP-adressen werken in App Service

Een App Service-app wordt uitgevoerd in een App Service plan en App Service plannen worden geïmplementeerd in een van de implementatie-eenheden in de Azure-infra structuur (intern een webruimte genoemd). Elke implementatie-eenheid krijgt een set virtuele IP-adressen, die één openbaar inkomend IP-adres en een reeks [uitgaande IP-adressen](#find-outbound-ips)bevat. Alle App Service plannen in dezelfde implementatie-eenheid en app-exemplaren die hierin worden uitgevoerd, delen dezelfde set virtuele IP-adressen. Voor een App Service Environment (een App Service plan in een [geïsoleerde laag](https://azure.microsoft.com/pricing/details/app-service/)) is het app service plan zelf de implementatie-eenheid, zodat de virtuele IP-adressen hiervoor kunnen worden toegewezen.

Omdat u niet gemachtigd bent om een App Service plan tussen implementatie-eenheden te verplaatsen, blijven de virtuele IP-adressen die zijn toegewezen aan uw app meestal hetzelfde, maar zijn er uitzonde ringen.

## <a name="when-inbound-ip-changes"></a>Wanneer inkomende IP-wijzigingen

Ongeacht het aantal uitgeschaalde instanties heeft elke app één inkomend IP-adres. Het binnenkomende IP-adres kan veranderen wanneer u een van de volgende acties uitvoert:

- Een app verwijderen en opnieuw maken in een andere resource groep (implementatie-eenheid kan worden gewijzigd).
- Verwijder de laatste app in een combi natie van resource groep _en_ regio en maak deze opnieuw (implementatie-eenheid kan worden gewijzigd).
- Een bestaande TLS/SSL-binding op basis van IP verwijderen, zoals tijdens het vernieuwen van een certificaat (Zie [certificaat vernieuwen](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Het inkomende IP-adres zoeken

Voer de volgende opdracht uit in een lokale terminal:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Een statisch inkomend IP-adres ophalen

Soms wilt u mogelijk een toegewezen, statisch IP-adres voor uw app. Als u een statisch inkomend IP-adres wilt ophalen, moet u [een aangepast domein beveiligen](configure-ssl-bindings.md#secure-a-custom-domain). Als u geen TLS-functionaliteit nodig hebt om uw app te beveiligen, kunt u zelfs een zelfondertekend certificaat voor deze binding uploaden. In een TLS-binding op basis van IP is het certificaat gebonden aan het IP-adres zelf, dus App Service een statisch IP-adres in te stellen om het uit te voeren. 

## <a name="when-outbound-ips-change"></a>Wanneer uitgaande Ip's worden gewijzigd

Ongeacht het aantal uitgeschaalde instanties heeft elke app op elk gewenst moment een bepaald aantal uitgaande IP-adressen. Elke uitgaande verbinding van de App Service-app, zoals een back-enddatabase, gebruikt een van de uitgaande IP-adressen als het IP-bron adres. Het IP-adres dat moet worden gebruikt, is wille keurig geselecteerd tijdens runtime. Daarom moet uw back-end-service zijn firewall openen voor alle uitgaande IP-adressen voor uw app.

De set met uitgaande IP-adressen voor uw app wordt gewijzigd wanneer u een van de volgende acties uitvoert:

- Een app verwijderen en opnieuw maken in een andere resource groep (implementatie-eenheid kan worden gewijzigd).
- Verwijder de laatste app in een combi natie van resource groep _en_ regio en maak deze opnieuw (implementatie-eenheid kan worden gewijzigd).
- Schaal uw app tussen de lagere lagen (**Basic**, **Standard** en **Premium**) en de **Premium v2** -laag (IP-adressen kunnen worden toegevoegd aan of afgetrokken van de set).

U kunt de set met alle mogelijke uitgaande IP-adressen die uw app kan gebruiken, ongeacht de prijs categorieën, door te zoeken naar de `possibleOutboundIpAddresses` eigenschap of in het veld **extra uitgaande IP-adressen** op de Blade **Eigenschappen** in de Azure Portal. Zie [uitgaande Ip's zoeken](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Uitgaande Ip's zoeken

Als u de uitgaande IP-adressen wilt vinden die momenteel worden gebruikt door uw app in de Azure Portal, klikt u op **Eigenschappen** in de linkernavigatiebalk van de app. Deze worden weer gegeven in het veld **uitgaande IP-adressen** .

U kunt dezelfde informatie vinden door de volgende opdracht uit te voeren in de [Cloud shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Klik op **Eigenschappen** in de linkernavigatiebalk van uw app om _alle_ mogelijke uitgaande IP-adressen voor uw app te vinden, ongeacht de prijs categorie. Deze worden weer gegeven in het veld **extra uitgaande IP-adressen** .

U kunt dezelfde informatie vinden door de volgende opdracht uit te voeren in de [Cloud shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beperken van inkomend verkeer op bron-IP-adressen.

> [!div class="nextstepaction"]
> [Beperkingen voor statische IP-adressen](app-service-ip-restrictions.md)
