---
title: Privé-eindpunten gebruiken
titleSuffix: Azure SignalR Service
description: Overzicht van persoonlijke eind punten voor beveiligde toegang tot de Azure signalerings service van virtuele netwerken.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682627"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Privé-eind punten gebruiken voor de Azure signalerings service

U kunt [privé-eind punten](../private-link/private-endpoint-overview.md) voor uw Azure signalerings service gebruiken om clients op een virtueel netwerk (VNet) toe te staan om veilig toegang te krijgen tot gegevens via een [persoonlijke koppeling](../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de VNet-adres ruimte voor uw Azure signalerings service. Netwerk verkeer tussen de clients in de VNet en de Azure signalerings service gaat over een persoonlijke koppeling in het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen.

Door gebruik te maken van privé-eind punten voor uw Azure signalerings service kunt u:

- Beveilig uw Azure signalerings service met het netwerk toegangs beheer om alle verbindingen op het open bare eind punt voor de Azure signalerings service te blok keren.
- Verbeter de beveiliging van het virtuele netwerk (VNet) door u in staat te stellen exfiltration van gegevens van het VNet te blok keren.
- Maak veilig verbinding met Azure signalerings services van on-premises netwerken die verbinding maken met het VNet met behulp van [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [expressroutes waaraan](../expressroute/expressroute-locations.md) met privé-peering.

## <a name="conceptual-overview"></a>Conceptueel overzicht

![Overzicht van privé-eind punten voor de Azure signalerings service](media/howto-private-endpoints/private-endpoint-overview.png)

Een persoonlijk eind punt is een speciale netwerk interface voor een Azure-service in uw [Virtual Network](../virtual-network/virtual-networks-overview.md) (VNet). Wanneer u een persoonlijk eind punt maakt voor uw Azure signalerings service, biedt het een beveiligde verbinding tussen clients op uw VNet en uw service. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw VNet. De verbinding tussen het persoonlijke eind punt en de Azure signalerings service maakt gebruik van een beveiligde persoonlijke koppeling.

Toepassingen in het VNet kunnen naadloos verbinding maken met de Azure signalerings service via het persoonlijke eind punt, **met behulp van dezelfde verbindings reeksen en autorisatie mechanismen die ze anders zouden gebruiken**. Privé-eind punten kunnen worden gebruikt met alle protocollen die worden ondersteund door de Azure signalerings service, inclusief REST API.

Wanneer u in uw VNet een persoonlijk eind punt maakt voor een Azure signalerings service, wordt er een aanvraag voor goed keuring verzonden naar de eigenaar van de Azure signalerings service. Als de gebruiker die het persoonlijke eind punt wil maken ook een eigenaar is van de Azure signalerings service, wordt deze aanvraag voor toestemming automatisch goedgekeurd.

De eigenaar van de Azure signalerings service kan toestemming aanvragen en de persoonlijke eind punten beheren via het tabblad *privé-eind punten* voor de Azure signalerings service in de [Azure Portal](https://portal.azure.com).

> [!TIP]
> Als u de toegang tot uw Azure signalerings service via het persoonlijke eind punt alleen wilt beperken, [configureert u de netwerk Access Control](howto-network-access-control.md#managing-network-access-control) om toegang via het open bare eind punt te weigeren of te beheren.

### <a name="connecting-to-private-endpoints"></a>Verbinding maken met privé-eind punten

Clients op een VNet met behulp van het privé-eind punt moeten hetzelfde connection string gebruiken voor de Azure signalerings service, wanneer clients verbinding maken met het open bare eind punt. We vertrouwen op DNS-omzetting om de verbindingen van het VNet naar de Azure signalerings service via een persoonlijke koppeling automatisch te routeren.

> [!IMPORTANT]
> Gebruik hetzelfde connection string om verbinding te maken met de Azure signalerings service met behulp van privé-eind punten, zoals u anders zou gebruiken. Maak geen verbinding met de Azure signalerings service met behulp van de `privatelink` subdomein-URL.

Er wordt standaard een [privé-DNS-zone](../dns/private-dns-overview.md) gekoppeld aan het VNet met de vereiste updates voor de privé-eind punten. Als u echter uw eigen DNS-server gebruikt, moet u mogelijk aanvullende wijzigingen aanbrengen in uw DNS-configuratie. In de volgende sectie over [DNS-wijzigingen](#dns-changes-for-private-endpoints) worden de vereiste updates voor persoonlijke eind punten beschreven.

## <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privé-eind punten

Wanneer u een persoonlijk eind punt maakt, wordt de DNS CNAME-bron record voor uw Azure signalerings service bijgewerkt naar een alias in een subdomein met het voor voegsel `privatelink` . Standaard maken we ook een [privé-DNS-zone](../dns/private-dns-overview.md), die overeenkomt met het `privatelink` subdomein, met de DNS a-bron records voor de privé-eind punten.

Wanneer u de domein naam van de Azure signalerings service verhelpt van buiten het VNet met het persoonlijke eind punt, wordt deze omgezet in het open bare eind punt van de Azure signalerings service. Wanneer het is opgelost vanuit het VNet dat het persoonlijke eind punt host, wordt de domein naam omgezet in het IP-adres van het privé-eind punt.

Voor het bovenstaande voor beeld is de DNS-bron records voor de Azure signalerings service ' Foobar ', wanneer deze zijn omgezet van buiten de VNet die als host fungeert voor het persoonlijke eind punt:

| Naam                                                  | Type  | Waarde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

Zoals eerder vermeld, kunt u de toegang tot clients buiten het VNet via het open bare eind punt met behulp van het netwerk toegangs beheer weigeren of beheren.

De DNS-bron records voor ' Foobar ', wanneer deze zijn opgelost door een client in de VNet die als host fungeert voor het persoonlijke eind punt, zijn:

| Naam                                                  | Type  | Waarde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

Deze benadering maakt toegang tot de Azure signalerings service mogelijk **met dezelfde Connection String** voor clients op het VNet dat als host fungeert voor de privé-eind punten, evenals clients buiten het vnet.

Als u een aangepaste DNS-server in uw netwerk gebruikt, moeten clients de FQDN voor het Azure signalerings service-eind punt kunnen omzetten in het IP-adres van het privé-eind punt. U moet uw DNS-server zo configureren dat uw privé-koppelings subdomein wordt overgedragen aan de privé-DNS-zone voor het VNet, of dat u de A-records configureert `foobar.privatelink.service.signalr.net` met het IP-adres van het privé-eind punt.

> [!TIP]
> Wanneer u een aangepaste of lokale DNS-server gebruikt, moet u de DNS-server zo configureren dat de naam van de Azure signalerings service in het subdomein wordt omgezet in `privatelink` het IP-adres van het privé-eind punt. U kunt dit doen door het `privatelink` subdomein te delegeren aan de privé-DNS-zone van het VNet of door de DNS-zone op de DNS-server te configureren en de DNS A-records toe te voegen.

De aanbevolen DNS-zone naam voor privé-eind punten voor de Azure signalerings service is: `privatelink.service.signalr.net` .

Raadpleeg de volgende artikelen voor meer informatie over het configureren van uw eigen DNS-server voor de ondersteuning van persoonlijke eind punten:

- [Naamomzetting voor resources in virtuele Azure-netwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-configuratie voor privé-eind punten](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Maak een persoonlijk eind punt samen met een nieuwe Azure signalerings service in de Azure Portal

1. Wanneer u een nieuwe Azure signalerings service maakt, selecteert u het tabblad **netwerken** . Kies een **persoonlijk eind punt** als connectiviteits methode.

    ![Een Azure signalerings service maken-tabblad netwerken](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. Klik op **Add**. Vul het abonnement, de resource groep, de locatie en de naam voor het nieuwe persoonlijke eind punt in. Kies een virtueel netwerk en een subnet.

    ![De Azure signalerings service maken-persoonlijk eind punt toevoegen](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. Klik op **Controleren + maken**.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Een persoonlijk eind punt maken voor een bestaande Azure signalerings service in de Azure Portal

1. Ga naar de Azure signalerings service.

1. Klik op het menu instellingen met de naam **VPN-verbindingen**.

1. Klik bovenaan op de knop **+ persoonlijk eind punt** .

    ![Blade verbindingen met privé-eind punt](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Vul het abonnement, de resource groep, de resource naam en de regio voor het nieuwe persoonlijke eind punt in.
    
    ![Persoonlijk eind punt maken-basis beginselen](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Kies de doel service resource van Azure signalering.

    ![Persoonlijk eind punt maken-resource](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Virtueel netwerk van doel kiezen

    ![Persoonlijk eind punt maken-configuratie](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. Klik op **Controleren + maken**.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Een persoonlijk eind punt maken met behulp van Azure CLI

1. Aanmelden bij Azure CLI
    ```azurecli
    az login
    ```
1. Selecteer uw Azure-abonnement
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Een nieuwe brongroep maken
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Micro soft. SignalRService als provider registreren
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. Een nieuwe Azure signalerings service maken
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Een Virtual Network maken
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Een subnet toevoegen
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Virtueel netwerk-beleid uitschakelen
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. Een Privé-DNS-zone
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Privé-DNS zone koppelen aan Virtual Network
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Een privé-eindpunt maken (automatisch goedkeuren)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Een privé-eindpunt maken (handmatig goedkeuring aanvragen)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Verbindingsstatus weergeven
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Prijzen

Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link) voor meer informatie over prijzen.

## <a name="known-issues"></a>Bekende problemen

Denk aan de volgende bekende problemen met persoonlijke eind punten voor de Azure signalerings service

### <a name="free-tier"></a>Gratis laag

U kunt geen persoonlijk eind punt maken voor de Azure signalerings service van de gratis laag.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Toegangs beperkingen voor clients in VNets met privé-eind punten

Clients in VNets met bestaande persoonlijke eind punten geven gezichts beperkingen bij het openen van andere Azure signalerings service-exemplaren met persoonlijke eind punten. Stel dat een VNet N1 een persoonlijk eind punt heeft voor een exemplaar van de Azure signalerings service S1. Als de Azure signalerings service S2 een persoonlijk eind punt in een VNet N2 heeft, moeten clients in VNet N1 ook toegang krijgen tot de Azure signalerings service S2 met behulp van een persoonlijk eind punt. Als de Azure signalerings service S2 geen privé-eind punten heeft, hebben clients in VNet N1 toegang tot de Azure signalerings service in dat account zonder een persoonlijk eind punt.

Deze beperking is het gevolg van de DNS-wijzigingen die zijn aangebracht toen de Azure signalerings service S2 een persoonlijk eind punt maakt.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regels voor netwerkbeveiligingsgroepen voor subnetten met privé-eindpunten

Op dit moment kunt u geen NSG-regels ( [netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md) ) en door de gebruiker gedefinieerde routes configureren voor privé-eind punten. NSG-regels die worden toegepast op het subnet waarop het persoonlijke eind punt wordt gehost, worden toegepast op het persoonlijke eind punt. Een beperkte tijdelijke oplossing voor dit probleem is het implementeren van uw toegangs regels voor privé-eind punten op de bron-subnetten, hoewel deze benadering mogelijk een hogere beheer overhead nodig heeft.

## <a name="next-steps"></a>Volgende stappen

- [Netwerk Access Control configureren](howto-network-access-control.md)