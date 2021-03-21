---
title: Privé-eind punten gebruiken voor Azure-app configuratie
description: Uw app-configuratie archief beveiligen met behulp van privé-eind punten
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 6cadadfb3623d05dd3ae3851acd5eaca13860023
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929840"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Privé-eind punten gebruiken voor Azure-app configuratie

U kunt [privé-eind punten](../private-link/private-endpoint-overview.md) voor Azure-app configuratie gebruiken om clients in een virtueel netwerk (VNet) toe te staan om veilig toegang te krijgen tot gegevens via een [privé-koppeling](../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de VNet-adres ruimte voor uw app-configuratie opslag. Netwerk verkeer tussen de clients in het VNet en de app-configuratie opslag gaat over op het VNet met behulp van een persoonlijke koppeling in het micro soft-backbone-netwerk, waardoor de bloot stelling aan het open bare Internet wordt geëlimineerd.

Met persoonlijke eind punten voor uw app-configuratie archief kunt u het volgende doen:
- Beveilig de configuratie gegevens van uw toepassing door de firewall zodanig te configureren dat alle verbindingen met de app-configuratie op het open bare eind punt worden geblokkeerd.
- Verg root de beveiliging van het virtuele netwerk (VNet) zodat gegevens niet van het VNet te zien zijn.
- Maak een beveiligde verbinding met het app-configuratie archief vanuit on-premises netwerken die verbinding maken met het VNet met behulp van [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [expressroutes waaraan](../expressroute/expressroute-locations.md) met privé-peering.

## <a name="conceptual-overview"></a>Conceptueel overzicht

Een persoonlijk eind punt is een speciale netwerk interface voor een Azure-service in uw [Virtual Network](../virtual-network/virtual-networks-overview.md) (VNet). Wanneer u een persoonlijk eind punt maakt voor uw app-configuratie archief, biedt het een beveiligde verbinding tussen clients in uw VNet en uw configuratie-archief. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw VNet. Voor de verbinding tussen het persoonlijke eind punt en het configuratie archief wordt een beveiligde persoonlijke koppeling gebruikt.

Toepassingen in het VNet kunnen verbinding maken met de configuratie opslag via het persoonlijke eind punt **met behulp van dezelfde verbindings reeksen en autorisatie mechanismen die ze anders zouden gebruiken**. Privé-eind punten kunnen worden gebruikt met alle protocollen die worden ondersteund door de app-configuratie opslag.

Hoewel app-configuratie geen service-eind punten ondersteunt, kunnen privé-eind punten worden gemaakt in subnetten die gebruikmaken van [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md). Clients in een subnet kunnen veilig verbinding maken met een app-configuratie archief met behulp van het privé-eind punt terwijl service-eind punten worden gebruikt voor toegang tot anderen.  

Wanneer u een persoonlijk eind punt voor een service in uw VNet maakt, wordt er een aanvraag voor goed keuring verzonden naar de eigenaar van het service account. Als de gebruiker die het persoonlijke eind punt wil maken ook een eigenaar van het account is, wordt deze aanvraag voor toestemming automatisch goedgekeurd.

De eigenaar van het service account kan toestemming aanvragen en persoonlijke eind punten beheren via het `Private Endpoints` tabblad van de configuratie opslag in de [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints-for-app-configuration"></a>Persoonlijke eind punten voor de app-configuratie 

Wanneer u een persoonlijk eind punt maakt, moet u de app-configuratie opslag opgeven waarmee de verbinding tot stand wordt gebracht. Als u meerdere app-configuratie-exemplaren binnen een account hebt, hebt u voor elke opslag een apart persoonlijk eind punt nodig.

### <a name="connecting-to-private-endpoints"></a>Verbinding maken met privé-eind punten

Azure is afhankelijk van de DNS-omzetting om verbindingen van het VNet naar de configuratie opslag via een persoonlijke koppeling te routeren. U kunt snel verbindings reeksen vinden in de Azure portal door uw app-configuratie archief te selecteren en vervolgens **instellingen**  >  **toegangs sleutels** te selecteren.  

> [!IMPORTANT]
> Gebruik hetzelfde connection string om verbinding te maken met uw app-configuratie archief met behulp van persoonlijke eind punten zoals u zou gebruiken voor een openbaar eind punt. Maak geen verbinding met het archief met behulp van de `privatelink` subdomein-URL.

## <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privé-eind punten

Wanneer u een persoonlijk eind punt maakt, wordt de DNS CNAME-bron record voor het configuratie archief bijgewerkt naar een alias in een subdomein met het voor voegsel `privatelink` . Azure maakt ook een [persoonlijke DNS-zone](../dns/private-dns-overview.md) die overeenkomt met het `privatelink` subdomein, met de DNS a-bron records voor de privé-eind punten.

Wanneer u de eind punt-URL oplost vanuit het VNet dat als host fungeert voor het persoonlijke eind punt, wordt dit omgezet in het persoonlijke eind punt van de Store. Bij omzetting van buiten het VNet wordt de eind punt-URL omgezet naar het open bare eind punt. Wanneer u een persoonlijk eind punt maakt, wordt het open bare eind punt uitgeschakeld.

Als u een aangepaste DNS-server in uw netwerk gebruikt, moeten clients de Fully Qualified Domain Name (FQDN) voor het service-eind punt kunnen omzetten naar het IP-adres van het privé-eind punt. Configureer uw DNS-server voor het delegeren van het subdomein van uw privé-koppeling naar de privé-DNS-zone voor het VNet of configureer de A-records voor `AppConfigInstanceA.privatelink.azconfig.io` met het IP-adres van het privé-eind punt.

> [!TIP]
> Wanneer u een aangepaste of lokale DNS-server gebruikt, moet u de DNS-server zo configureren dat de archief naam in het subdomein wordt omgezet in `privatelink` het IP-adres van het privé-eind punt. U kunt dit doen door het `privatelink` subdomein te delegeren aan de privé-DNS-zone van het VNet of door de DNS-zone op de DNS-server te configureren en de DNS A-records toe te voegen.

## <a name="pricing"></a>Prijzen

Voor het inschakelen van privé-eind punten is een configuratie-archief van de [Standard](https://azure.microsoft.com/pricing/details/app-configuration/) -app vereist.  Zie [prijzen voor persoonlijke](https://azure.microsoft.com/pricing/details/private-link)koppelingen voor Azure voor meer informatie over de prijs informatie voor persoonlijke koppelingen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het maken van een persoonlijk eind punt voor uw app-configuratie archief:

- [Maak een persoonlijk eind punt met behulp van het privé koppelings centrum in de Azure Portal](../private-link/create-private-endpoint-portal.md)
- [Een persoonlijk eind punt maken met behulp van Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Een privé=eindpunt maken met Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Meer informatie over het configureren van uw DNS-server met persoonlijke eind punten:

- [Naamomzetting voor resources in virtuele Azure-netwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-configuratie voor privé-eind punten](../private-link/private-endpoint-overview.md#dns-configuration)