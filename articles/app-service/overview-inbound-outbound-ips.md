---
title: Binnenkomende/uitgaande IP-adressen
description: Meer informatie over hoe binnenkomende en uitgaande IP-adressen worden gebruikt in Azure App Service, wanneer ze veranderen en hoe u de adressen voor uw app kunt vinden.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1dda487d23c9f955aea8e35d16e5a560a890a173
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834476"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Binnenkomende en uitgaande IP-adressen in Azure App Service

[Azure App Service](overview.md) is een service met meerdere tenants, met uitzondering [van App Service Environments.](environment/intro.md) Apps die zich niet in een App Service -omgeving (niet in de [geïsoleerde laag](https://azure.microsoft.com/pricing/details/app-service/)) delen de netwerkinfrastructuur met andere apps. Als gevolg hiervan kunnen de binnenkomende en uitgaande IP-adressen van een app anders zijn en kunnen ze zelfs in bepaalde situaties veranderen.

[App Service omgevingen](environment/intro.md) maken gebruik van toegewezen netwerkinfrastructuren, zodat apps die worden uitgevoerd in een App Service-omgeving statische, toegewezen IP-adressen krijgen voor binnenkomende en uitgaande verbindingen.

## <a name="how-ip-addresses-work-in-app-service"></a>Hoe IP-adressen werken in App Service

Een App Service-app wordt uitgevoerd in een App Service-plan en App Service-plannen worden geïmplementeerd in een van de implementatie-eenheden in de Azure-infrastructuur (intern een webruimte genoemd). Aan elke implementatie-eenheid wordt een set virtuele IP-adressen toegewezen, die één openbaar binnenkomende IP-adres en een set uitgaande [IP-adressen bevat.](#find-outbound-ips) Alle App Service in dezelfde implementatie-eenheid en app-exemplaren die erop worden uitgevoerd, delen dezelfde set virtuele IP-adressen. Voor een App Service Environment (een App Service-abonnement [in](https://azure.microsoft.com/pricing/details/app-service/)de isolated-laag) is het App Service-plan de implementatie-eenheid zelf, zodat de virtuele IP-adressen er als gevolg hiervan aan worden toegewezen.

Omdat het u niet is toegestaan om een App Service-plan te verplaatsen tussen implementatie-eenheden, blijven de virtuele IP-adressen die zijn toegewezen aan uw app meestal hetzelfde, maar er zijn uitzonderingen.

## <a name="when-inbound-ip-changes"></a>Wanneer het inkomende IP-adres wordt gewijzigd

Ongeacht het aantal uitgeschaalde exemplaren heeft elke app één binnenkomende IP-adres. Het binnenkomende IP-adres kan veranderen wanneer u een van de volgende acties uit te voeren:

- Verwijder een app en maak deze opnieuw in een andere resourcegroep (de implementatie-eenheid kan worden gewijzigd).
- Verwijder de laatste app in een combinatie van _resourcegroep_ en regio en maak deze opnieuw (implementatie-eenheid kan veranderen).
- Verwijder een bestaande TLS/SSL-binding op basis van IP, zoals tijdens het verlengen van het certificaat [(zie Certificaat vernieuwen).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>Het binnenkomende IP-adres zoeken

Voer de volgende opdracht uit in een lokale terminal:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Een statisch binnenkomende IP-adres krijgen

Soms wilt u een toegewezen, statisch IP-adres voor uw app. Als u een statisch ip-adres voor binnenkomende gegevens wilt, moet u [een aangepast domein beveiligen.](configure-ssl-bindings.md#secure-a-custom-domain) Als u niet daadwerkelijk TLS-functionaliteit nodig hebt om uw app te beveiligen, kunt u zelfs een zelf-ondertekend certificaat voor deze binding uploaden. In een IP-gebaseerde TLS-binding is het certificaat gebonden aan het IP-adres zelf, dus App Service een statisch IP-adres om dit mogelijk te maken. 

## <a name="when-outbound-ips-change"></a>Wanneer uitgaande IP's worden gewijzigd

Ongeacht het aantal uitschaalbare exemplaren heeft elke app op een bepaald moment een vast aantal uitgaande IP-adressen. Elke uitgaande verbinding van de App Service-app, zoals een back-enddatabase, gebruikt een van de uitgaande IP-adressen als het ip-adres van de oorsprong. Het IP-adres dat moet worden gebruikt, wordt willekeurig geselecteerd tijdens runtime, dus uw back-endservice moet de firewall openen voor alle uitgaande IP-adressen voor uw app.

De set uitgaande IP-adressen voor uw app wordt gewijzigd wanneer u een van de volgende acties uit te voeren:

- Verwijder een app en maak deze opnieuw in een andere resourcegroep (de implementatie-eenheid kan worden gewijzigd).
- Verwijder de laatste app in een combinatie van _resourcegroep_ en regio en maak deze opnieuw (de implementatie-eenheid kan worden gewijzigd).
- Schaal uw app tussen de lagere lagen **(Basic,** **Standard** en **Premium)** en de **Premium V2-laag** (IP-adressen kunnen worden toegevoegd aan of afgetrokken van de set).

U vindt de set met alle mogelijke uitgaande IP-adressen die uw app kan gebruiken, ongeacht de prijscategorie, door te zoeken naar de eigenschap of in het veld Extra uitgaande IP-adressen op de blade Eigenschappen in de `possibleOutboundIpAddresses` Azure Portal.   Zie [Uitgaande IP's zoeken.](#find-outbound-ips)

## <a name="find-outbound-ips"></a>Uitgaande IP's zoeken

Als u de uitgaande IP-adressen wilt vinden die momenteel door uw app worden gebruikt in de Azure Portal, klikt u **op** Eigenschappen in de linkernavigatiebalk van uw app. Ze worden weergegeven in het **veld Uitgaande IP-adressen.**

U kunt dezelfde informatie vinden door de volgende opdracht uit te voeren in [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Als u _alle mogelijke_ uitgaande IP-adressen voor uw app wilt vinden, ongeacht de prijscategorie, klikt u **op** Eigenschappen in het linkernavigatievenster van uw app. Ze worden vermeld in het **veld Extra uitgaande IP-adressen.**

U kunt dezelfde informatie vinden door de volgende opdracht uit te voeren in [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beperken van inkomende verkeer per bron-IP-adressen.

> [!div class="nextstepaction"]
> [Beperkingen voor statische IP-adressen](app-service-ip-restrictions.md)
