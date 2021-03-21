---
title: App integreren met Azure Virtual Network
description: App integreren in Azure App Service met Azure Virtual Networks.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4666f03e529c568177ff56a2db84e226bc906132
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182907"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Uw app integreren met een virtueel Azure-netwerk

In dit artikel wordt de functie Azure App Service VNet-integratie beschreven en wordt uitgelegd hoe u deze kunt instellen met apps in [Azure app service](./overview.md). Met [Azure Virtual Network][VNETOverview] (VNets) kunt u veel van uw Azure-resources in een niet-Internet routeerbaar netwerk plaatsen. Met de VNet-integratie functie kunnen uw apps toegang krijgen tot bronnen in of via een VNet. Met VNet-integratie kunnen uw apps niet privé worden geopend.

Azure App Service heeft twee varianten van de VNet-integratie functie:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet-integratie inschakelen

1. Ga naar de gebruikers interface van het **netwerk** in de app Service Portal. Selecteer onder **VNet-integratie** de optie **Klik hier om te configureren**.

1. Selecteer **VNet toevoegen**.

   ![VNet-integratie selecteren][1]

1. De vervolg keuzelijst bevat alle Azure Resource Manager virtuele netwerken in uw abonnement in dezelfde regio. Hieronder vindt u een lijst met de virtuele netwerken van resource manager in alle andere regio's. Selecteer het VNet dat u wilt integreren met.

   ![Het VNet selecteren][2]

   * Als het VNet zich in dezelfde regio bevindt, maakt u een nieuw subnet of selecteert u een leeg, bestaand subnet.
   * Als u een VNet in een andere regio wilt selecteren, moet u een VNet-gateway hebben ingericht met Point-to-site ingeschakeld.
   * Als u wilt integreren met een klassiek VNet, selecteert u in plaats van de **Virtual Network** vervolg keuzelijst, **klikt u hier om verbinding te maken met een klassiek vnet**. Selecteer het klassieke virtuele netwerk dat u wilt. Het doel-VNet moet al een Virtual Network gateway hebben ingericht met behulp van punt-naar-site ingeschakeld.

    ![Klassiek VNet selecteren][3]

Tijdens de integratie wordt uw app opnieuw opgestart. Wanneer de integratie is voltooid, ziet u de details van het VNet dat u hebt geïntegreerd.

## <a name="regional-vnet-integration"></a>Regionale VNet-integratie

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Hoe regionale VNet-integratie werkt

Apps in App Service worden gehost op werk rollen. De basis-en hogere prijs plannen zijn toegewezen hosting plannen waarbij er geen werk belastingen van andere klanten op dezelfde werk nemers worden uitgevoerd. Regionale VNet-integratie werkt door virtuele interfaces te koppelen aan adressen in het overgedragen subnet. Omdat het van-adres zich in uw VNet bevindt, kan het toegang krijgen tot de meeste zaken in of via uw VNet, zoals een virtuele machine in uw VNet. De netwerk implementatie verschilt van het uitvoeren van een virtuele machine in uw VNet. Daarom zijn sommige netwerk functies nog niet beschikbaar voor deze functie.

![Hoe regionale VNet-integratie werkt][5]

Wanneer regionale VNet-integratie is ingeschakeld, maakt uw app uitgaande oproepen naar het Internet via dezelfde kanalen als normaal. De uitgaande adressen die worden vermeld in de app-eigenschappen Portal zijn de adressen die nog steeds worden gebruikt door uw app. Welke wijzigingen voor uw app zijn de aanroepen van beveiligde services voor service-eind punten of RFC 1918-adressen gaan naar uw VNet. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer naar uw VNet worden verzonden.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` wordt momenteel niet ondersteund in Windows-containers.
> 

De functie ondersteunt slechts één virtuele interface per werk nemer. Eén virtuele interface per werk nemer betekent één regionale VNet-integratie per App Service plan. Alle apps in hetzelfde App Service-abonnement kunnen gebruikmaken van dezelfde VNet-integratie. Als u een app nodig hebt om verbinding te maken met een aanvullend VNet, moet u een ander App Service plan aanmaken. De virtuele interface wordt gebruikt, is geen resource waarmee klanten rechtstreeks toegang hebben tot.

Vanwege de aard van de werking van deze technologie, wordt het verkeer dat wordt gebruikt met VNet-integratie niet weer gegeven in azure Network Watcher-of NSG-stroom Logboeken.

## <a name="gateway-required-vnet-integration"></a>Gateway-vereiste VNet-integratie

Gateway-vereiste VNet-integratie biedt ondersteuning voor het verbinden met een VNet in een andere regio of op een klassiek virtueel netwerk. Gateway-vereiste VNet-integratie:

* Hiermee kan een app verbinding maken met slechts één VNet per keer.
* Hiermee kunnen Maxi maal vijf VNets worden geïntegreerd in een App Service plan.
* Hiermee kan hetzelfde VNet door meerdere apps in een App Service plan worden gebruikt zonder dat dit van invloed is op het totale aantal dat kan worden gebruikt door een App Service plan. Als u zes apps hebt die hetzelfde VNet gebruiken in hetzelfde App Service-abonnement, telt dat op als één VNet dat wordt gebruikt.
* Ondersteunt een SLA van 99,9% door de SLA op de gateway.
* Hiermee kunnen uw apps de DNS gebruiken waarvoor het VNet is geconfigureerd.
* Vereist een Virtual Network op route gebaseerde gateway die is geconfigureerd met een SSTP-punt-naar-site-VPN voordat deze kan worden verbonden met een app.

U kunt Gateway-vereiste VNet-integratie niet gebruiken:

* Met een VNet dat is verbonden met Azure ExpressRoute.
* Vanuit een Linux-app.
* Vanuit een [Windows-container](quickstart-custom-container.md).
* Om toegang te krijgen tot beveiligde bronnen van service-eind punten.
* Met een gateway voor samen werking die zowel ExpressRoute als Point-to-site-of site-naar-site-Vpn's ondersteunt.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Een gateway instellen in uw virtuele Azure-netwerk ###

Een gateway maken:

1. [Maak een gateway-subnet][creategatewaysubnet] in uw VNet.

1. [Maak de VPN-gateway][creategateway]. Selecteer een op route gebaseerd VPN-type.

1. [Stel de punt-naar-site-adressen][setp2saddresses]in. Als de gateway zich niet in de basis-SKU bevindt, moet IKEV2 worden uitgeschakeld in de punt-naar-site-configuratie en moet SSTP worden geselecteerd. De punt-naar-site-adres ruimte moet in de RFC 1918-adres blokken 10.0.0.0/8, 172.16.0.0/12 en 192.168.0.0/16 staan.

Als u de gateway maakt voor gebruik met App Service VNet-integratie, hoeft u geen certificaat te uploaden. Het maken van de gateway kan 30 minuten duren. U kunt uw app pas met uw VNet integreren nadat de gateway is ingericht.

### <a name="how-gateway-required-vnet-integration-works"></a>Hoe gateway-vereiste VNet-integratie werkt

Gateway-vereiste VNet-integratie is gebouwd op basis van punt-naar-site-VPN-technologie. Punt-naar-site-Vpn's beperken netwerk toegang tot de virtuele machine die als host fungeert voor de app. Apps worden beperkt tot het verzenden van verkeer naar Internet via Hybride verbindingen of via VNet-integratie. Als uw app is geconfigureerd met de portal voor het gebruik van Gateway-vereiste VNet-integratie, wordt namens u een complexe onderhandeling beheerd om certificaten te maken en toe te wijzen aan de gateway en aan de kant van de toepassing. Het resultaat is dat de werk nemers die worden gebruikt voor het hosten van uw apps, rechtstreeks verbinding kunnen maken met de virtuele netwerk gateway in het geselecteerde VNet.

![Hoe gateway-vereiste VNet-integratie werkt][6]

### <a name="access-on-premises-resources"></a>Toegang tot on-premises resources

Apps kunnen toegang krijgen tot on-premises resources door te integreren met VNets met site-naar-site-verbindingen. Als u Gateway-vereiste VNet-integratie gebruikt, werkt u de on-premises VPN-gateway routes bij met uw punt-naar-site-adres blokken. Wanneer de site-naar-site-VPN voor het eerst wordt ingesteld, moeten de scripts die worden gebruikt voor het configureren van de server routes op de juiste wijze instellen. Als u de punt-naar-site-adressen toevoegt nadat u uw site-naar-site-VPN hebt gemaakt, moet u de routes hand matig bijwerken. Meer informatie over hoe u dit doet, verschilt per gateway en wordt hier niet beschreven. U kunt BGP niet configureren met een site-naar-site-VPN-verbinding.

Er is geen aanvullende configuratie vereist voor de functie Regional VNet-integratie voor het bereiken van uw VNet naar on-premises resources. U hoeft alleen maar uw VNet te verbinden met on-premises resources met behulp van ExpressRoute of een site-naar-site-VPN.

> [!NOTE]
> De gateway vereiste VNet-integratie functie integreert geen app met een VNet met een ExpressRoute-gateway. Zelfs als de ExpressRoute-gateway in de [modus][VPNERCoex]voor samen werking is geconfigureerd, werkt de VNet-integratie niet. Als u toegang nodig hebt tot bronnen via een ExpressRoute-verbinding, gebruikt u de functie voor regionale VNet-integratie of een [app service Environment][ASE], die wordt uitgevoerd in uw VNet.
>
>

### <a name="peering"></a>Peering

Als u peering met de regionale VNet-integratie gebruikt, hoeft u geen aanvullende configuratie uit te voeren.

Als u Gateway-vereiste VNet-integratie met peering gebruikt, moet u enkele extra items configureren. Peering configureren voor gebruik met uw app:

1. Een peering-verbinding toevoegen op het VNet waarmee uw app verbinding maakt. Wanneer u de peering-verbinding toevoegt, schakelt u **toegang tot virtueel netwerk toestaan** in en selecteert u **doorgestuurd verkeer toestaan** en **Gateway doorvoer** toestaan.
1. Voeg een peering-verbinding toe op het VNet dat wordt gekoppeld aan het VNet waarmee u verbinding hebt. Wanneer u de peering-verbinding op het doel-VNet toevoegt, schakelt u **toegang tot virtueel netwerk toestaan** in en selecteert u **doorgestuurd verkeer toestaan** en **externe gateways toestaan**.
1. Ga naar de **app service plan**  >  **Network**  >  **VNet Integration** UI in de portal. Selecteer het VNet waarmee uw app verbinding maakt. Voeg onder de sectie route ring het adres bereik toe van het VNet dat is gekoppeld aan het VNet waarmee uw app is verbonden.

## <a name="manage-vnet-integration"></a>VNet-integratie beheren

Het verbinden en verbreken van de verbinding met een VNet bevindt zich op een app-niveau. Bewerkingen die van invloed kunnen zijn op de VNet-integratie tussen meerdere apps, bevinden zich op het niveau van de App Service plan. Vanuit de app > **Network**  >  **VNet-integratie** Portal kunt u meer informatie krijgen over uw VNet. U kunt vergelijk bare gegevens bekijken op het niveau van de app service plan in het **app service plan**  >  **Network**  >  **VNet Integration** Portal.

De enige bewerking die u kunt uitvoeren in de app-weer gave van uw VNet-integratie-exemplaar is het verbreken van de verbinding tussen uw app en het VNet waarmee momenteel verbinding is gemaakt. Als u uw app wilt loskoppelen van een VNet, selecteert u **verbinding verbreken**. Uw app wordt opnieuw gestart wanneer u de verbinding met een VNet verbreekt. Als u de verbinding verbreekt, wordt uw VNet niet gewijzigd. Het subnet of de gateway wordt niet verwijderd. Als u uw VNet vervolgens wilt verwijderen, moet u eerst uw app loskoppelen van het VNet en de resources hierin verwijderen, zoals gateways.

In de gebruikers interface van het App Service plan VNet-integratie ziet u alle VNet-integraties die worden gebruikt door de apps in uw App Service-abonnement. Als u de details van elk VNet wilt bekijken, selecteert u het VNet waarin u bent geïnteresseerd. Er zijn twee acties die u hier kunt uitvoeren voor gateway-vereiste VNet-integratie:

* **Netwerk synchroniseren**: de bewerking netwerk synchroniseren wordt alleen gebruikt voor de gateway-afhankelijke VNet-integratie functie. Wanneer u een synchronisatie netwerk uitvoert, zorgt u ervoor dat uw certificaten en netwerk gegevens synchroon zijn. Als u de DNS van uw VNet toevoegt of wijzigt, moet u een synchronisatie netwerk bewerking uitvoeren. Met deze bewerking worden alle apps die gebruikmaken van dit VNet, opnieuw gestart. Deze bewerking werkt niet als u een app en een vnet gebruikt dat deel uitmaakt van verschillende abonnementen.
* **Routes toevoegen**: door routes toe te voegen, wordt uitgaand verkeer naar uw VNet gestuurd.

Het privé-IP-adres dat is toegewezen aan het exemplaar, wordt weer gegeven via de omgevings variabele, **WEBSITE_PRIVATE_IP**. De gebruikers interface van de kudu-console toont ook de lijst met omgevings variabelen die beschikbaar zijn voor de web-app. Dit IP-adres wordt toegewezen vanuit het adressen bereik van het geïntegreerde subnet. Voor regionale VNet-integratie is de waarde van WEBSITE_PRIVATE_IP een IP-adres van het bereik van het overgedragen subnet en voor de gateway vereiste VNet-integratie is de waarde een IP-adres van het bereik van de punt-naar-site-adressen groep die is geconfigureerd op de Virtual Network gateway. Dit is het IP-adres dat door de web-app wordt gebruikt om via de Virtual Network verbinding te maken met de resources. 

> [!NOTE]
> De waarde van WEBSITE_PRIVATE_IP is gebonden aan de wijziging. Het is echter een IP binnen het adres bereik van het subnet Integration of het punt-naar-site-adres bereik, zodat u toegang tot het hele adres bereik moet toestaan.
>

### <a name="gateway-required-vnet-integration-routing"></a>Gateway-vereiste VNet-integratie routering
De routes die in uw VNet zijn gedefinieerd, worden gebruikt voor het omleiden van verkeer naar uw VNet vanuit uw app. Als u extra uitgaand verkeer naar het VNet wilt verzenden, voegt u deze adres blokken hier toe. Deze functie werkt alleen met Gateway-vereiste VNet-integratie. Route tabellen hebben geen invloed op uw app-verkeer wanneer u Gateway-vereiste VNet-integratie gebruikt op de manier die met regionale VNet-integratie.

### <a name="gateway-required-vnet-integration-certificates"></a>Gateway-vereiste VNet-integratie certificaten
Als gateway-vereiste VNet-integratie is ingeschakeld, is er een vereiste uitwisseling van certificaten vereist om de beveiliging van de verbinding te garanderen. Naast de certificaten zijn de DNS-configuratie, routes en andere vergelijk bare dingen die het netwerk beschrijven.

Als certificaten of netwerk gegevens zijn gewijzigd, selecteert u **netwerk synchroniseren**. Wanneer u **netwerk synchroniseren** selecteert, zorgt u voor een korte onderbreking in de connectiviteit tussen uw app en uw VNet. Als uw app niet opnieuw wordt gestart, kan het verlies van de connectiviteit ertoe leiden dat uw site niet goed werkt.

## <a name="pricing-details"></a>Prijsdetails
De regionale VNet-integratie functie heeft geen extra kosten voor gebruik buiten de kosten categorie van het App Service plan.

Drie kosten zijn gerelateerd aan het gebruik van de gateway vereiste VNet-integratie functie:

* **Kosten categorie prijzen app service plannen**: uw apps moeten een Standard-, Premium-, PremiumV2-of app service PremiumV3-abonnement hebben. Zie voor meer informatie over deze kosten [app service prijzen][ASPricing].
* **Kosten voor gegevens overdracht**: er zijn kosten verbonden aan het afrekenen van gegevens, zelfs als het VNet zich in hetzelfde Data Center bevindt. Deze kosten worden beschreven in [gegevensoverdracht prijs informatie][DataPricing].
* **Kosten voor VPN-gateway**: er zijn kosten verbonden aan de virtuele netwerk gateway die is vereist voor het punt-naar-site-VPN. Zie [prijzen van VPN gateway][VNETPricing]voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

> [!NOTE]
> VNET-integratie wordt niet ondersteund voor docker-scenario's in App Service.
> Azure Functions toegangs beperkingen worden genegeerd als er een persoonlijk eind punt aanwezig is.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI-ondersteuning is beschikbaar voor regionale VNet-integratie. [Installeer de Azure cli][installCLI]om toegang te krijgen tot de volgende opdrachten.

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

De Power Shell-ondersteuning voor de regionale VNet-integratie is ook beschikbaar, maar u moet een algemene resource maken met een eigenschaps matrix van het subnet resourceID

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


Voor gateway-vereiste VNet-integratie kunt u App Service integreren met een virtueel Azure-netwerk met behulp van Power shell. Zie [een app in azure app service verbinden met een virtueel Azure-netwerk](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)voor een kant-en-klaar script.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
