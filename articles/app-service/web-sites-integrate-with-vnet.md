---
title: App integreren met Azure Virtual Network
description: Integreer de app in Azure App Service met virtuele Azure-netwerken.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 42391a073d7cb1d7e6850e298c2be32d550bb813
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832064"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Uw app integreren met een virtueel Azure-netwerk

In dit artikel wordt de functie Azure App Service VNet-integratie beschreven en wordt beschreven hoe u deze kunt instellen met apps in [Azure App Service](./overview.md). Met [Azure Virtual Network][VNETOverview] (VNets) kunt u veel van uw Azure-resources in een niet-internet routeerbaar netwerk plaatsen. Met de functie VNet-integratie hebben uw apps toegang tot resources in of via een VNet. Met VNet-integratie kunnen uw apps niet privé worden gebruikt.

Azure App Service heeft twee variaties op de functie VNet-integratie:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet-integratie inschakelen

1. Ga naar de **netwerk gebruikersinterface** in de App Service portal. Selecteer **onder VNet-integratie** **de optie Klik hier om te configureren.**

1. Selecteer **VNet toevoegen.**

   ![VNet-integratie selecteren][1]

1. De vervolgkeuzelijst bevat alle Azure Resource Manager virtuele netwerken in uw abonnement in dezelfde regio. Daaronder staat een lijst met de Resource Manager virtuele netwerken in alle andere regio's. Selecteer het VNet dat u wilt integreren.

   ![Selecteer het VNet][2]

   * Als het VNet zich in dezelfde regio, maakt u een nieuw subnet of selecteert u een leeg vooraf bestaande subnet.
   * Als u een VNet in een andere regio wilt selecteren, moet u een VNet-gateway hebben ingericht met punt-naar-site ingeschakeld.
   * Als u wilt integreren met een  klassiek VNet, selecteert u in plaats Virtual Network de vervolgkeuzelijst Klik hier om verbinding te maken met **een klassiek VNet.** Selecteer het klassieke virtuele netwerk dat u wilt gebruiken. Het doel-VNet moet al een Virtual Network zijn ingericht met punt-naar-site ingeschakeld.

    ![Klassiek VNet selecteren][3]

Tijdens de integratie wordt uw app opnieuw opgestart. Wanneer de integratie is voltooid, ziet u details over het VNet waarin u bent geïntegreerd.

## <a name="regional-vnet-integration"></a>Regionale VNet-integratie

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Hoe regionale VNet-integratie werkt

Apps in App Service worden gehost op werkrollen. De basic- en hogere prijsplannen zijn speciale hostingplannen waarbij er geen workloads van andere klanten op dezelfde werkmedewerkers worden uitgevoerd. Regionale VNet-integratie werkt door virtuele interfaces te integreren met adressen in het gedelegeerde subnet. Omdat het van-adres zich in uw VNet heeft, heeft het toegang tot de meeste dingen in of via uw VNet, zoals een VM in uw VNet zou doen. De netwerk-implementatie is anders dan het uitvoeren van een VM in uw VNet. Daarom zijn sommige netwerkfuncties nog niet beschikbaar voor deze functie.

![Hoe regionale VNet-integratie werkt][5]

Wanneer regionale VNet-integratie is ingeschakeld, worden via uw app uitgaande oproepen naar internet uitgevoerd via dezelfde kanalen als normaal. De uitgaande adressen die worden vermeld in de portal met app-eigenschappen zijn de adressen die nog steeds door uw app worden gebruikt. Wat er voor uw app verandert, zijn de aanroepen naar met service-eindpunt beveiligde services, of RFC 1918-adressen gaan naar uw VNet. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer naar uw VNet worden verzonden.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` wordt momenteel niet ondersteund in Windows-containers.
> 

De functie ondersteunt slechts één virtuele interface per worker. Eén virtuele interface per werker betekent één regionale VNet-integratie per App Service plan. Alle apps in hetzelfde App Service kunnen dezelfde VNet-integratie gebruiken. Als u een app nodig hebt om verbinding te maken met een extra VNet, moet u een ander App Service maken. De virtuele interface die wordt gebruikt, is geen resource waar klanten directe toegang tot hebben.

Vanwege de aard van de werking van deze technologie, wordt het verkeer dat wordt gebruikt met VNet-integratie niet in Azure Network Watcher- of NSG-stroomlogboeken.

## <a name="gateway-required-vnet-integration"></a>Gateway-vereiste VNet-integratie

Gateway-vereiste VNet-integratie ondersteunt het maken van verbinding met een VNet in een andere regio of met een klassiek virtueel netwerk. Gateway-vereiste VNet-integratie:

* Hiermee kan een app verbinding maken met slechts één VNet tegelijk.
* Hiermee kunnen maximaal vijf VNets worden geïntegreerd binnen een App Service abonnement.
* Hiermee kan hetzelfde VNet worden gebruikt door meerdere apps in een App Service-abonnement zonder dat dit van invloed is op het totale aantal dat door een App Service worden gebruikt. Als u zes apps hebt die hetzelfde VNet gebruiken in hetzelfde App Service-abonnement, telt dat als één VNet dat wordt gebruikt.
* Ondersteunt een SLA van 99,9% vanwege de SLA op de gateway.
* Hiermee kunnen uw apps de DNS gebruiken waarmee het VNet is geconfigureerd.
* Vereist een Virtual Network op route gebaseerde gateway die is geconfigureerd met een SSTP-punt-naar-site-VPN voordat deze kan worden verbonden met een app.

U kunt gateway-vereiste VNet-integratie niet gebruiken:

* Met een VNet dat is verbonden met Azure ExpressRoute.
* Vanuit een Linux-app.
* Vanuit een [Windows-container.](quickstart-custom-container.md)
* Om toegang te krijgen tot beveiligde resources voor service-eindpunten.
* Met een naast elkaar bestaande gateway die ondersteuning biedt voor ExpressRoute en punt-naar-site- of site-naar-site-VPN's.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Een gateway instellen in uw virtuele Azure-netwerk ###

Een gateway maken:

1. [Maak een gatewaysubnet][creategatewaysubnet] in uw VNet.

1. [Maak de VPN-gateway][creategateway]. Selecteer een op route gebaseerd VPN-type.

1. [Stel de punt-naar-site-adressen in.][setp2saddresses] Als de gateway niet in de basis-SKU staat, moet IKEV2 worden uitgeschakeld in de punt-naar-site-configuratie en moet SSTP worden geselecteerd. De punt-naar-site-adresruimte moet zich in de RFC 1918-adresblokken 10.0.0.0/8, 172.16.0.0/12 en 192.168.0.0/16.

Als u de gateway maakt voor gebruik met App Service VNet-integratie, hoeft u geen certificaat te uploaden. Het maken van de gateway kan 30 minuten duren. U kunt uw app pas integreren met uw VNet als de gateway is ingericht.

### <a name="how-gateway-required-vnet-integration-works"></a>Hoe gateway-vereiste VNet-integratie werkt

Gateway-vereiste VNet-integratie is gebaseerd op punt-naar-site-VPN-technologie. Punt-naar-site-VPN's beperken de netwerktoegang tot de virtuele machine die als host voor de app wordt gebruikt. Apps kunnen alleen verkeer naar internet verzenden via hybride verbindingen of via VNet-integratie. Wanneer uw app is geconfigureerd met de portal voor het gebruik van gateway-vereiste VNet-integratie, wordt namens u een complexe onderhandeling beheerd om certificaten aan de gateway- en toepassingszijde te maken en toe te wijzen. Het resultaat is dat de werksters die worden gebruikt om uw apps te hosten, rechtstreeks verbinding kunnen maken met de gateway van het virtuele netwerk in het geselecteerde VNet.

![Hoe gateway-vereiste VNet-integratie werkt][6]

### <a name="access-on-premises-resources"></a>Toegang tot on-premises resources

Apps hebben toegang tot on-premises resources door te integreren met VNets die site-naar-site-verbindingen hebben. Als u gateway-vereiste VNet-integratie gebruikt, moet u uw on-premises VPN-gatewayroutes bijwerken met uw punt-naar-site-adresblokken. Wanneer de site-naar-site-VPN voor het eerst is ingesteld, moeten de scripts die worden gebruikt om deze te configureren, routes correct instellen. Als u de punt-naar-site-adressen toevoegt nadat u uw site-naar-site-VPN hebt gemaakt, moet u de routes handmatig bijwerken. Meer informatie over hoe u dit doet, verschilt per gateway en wordt hier niet beschreven. U kunt BGP niet configureren met een site-naar-site-VPN-verbinding.

Er is geen aanvullende configuratie vereist om de regionale VNet-integratiefunctie via uw VNet naar on-premises resources te bereiken. U hoeft alleen uw VNet te verbinden met on-premises resources met behulp van ExpressRoute of een site-naar-site-VPN.

> [!NOTE]
> De gateway-vereiste VNet-integratiefunctie integreert geen app met een VNet met een ExpressRoute-gateway. Zelfs als de ExpressRoute-gateway is geconfigureerd in [de naast elkaar][VPNERCoex]bestaansmodus, werkt de VNet-integratie niet. Als u toegang nodig hebt tot resources via een ExpressRoute-verbinding, gebruikt u de regionale VNet-integratiefunctie of een [App Service Environment][ASE], die wordt uitgevoerd in uw VNet.
>
>

### <a name="peering"></a>Peering

Als u peering gebruikt met de regionale VNet-integratie, hoeft u geen aanvullende configuratie uit te laten.

Als u gateway-vereiste VNet-integratie met peering gebruikt, moet u een aantal extra items configureren. Peering configureren voor gebruik met uw app:

1. Voeg een peeringverbinding toe aan het VNet waarmee uw app verbinding maakt. Wanneer u de peeringverbinding toevoegt, schakel dan **Toegang tot virtueel netwerk** toestaan in en **selecteert u Doorgestuurd** verkeer toestaan en **Gateway-doorvoer toestaan.**
1. Voeg een peeringverbinding toe aan het VNet dat wordt gekoppeld aan het VNet dat u hebt verbonden. Wanneer u de peeringverbinding toevoegt aan  het doel-VNet, schakel dan Toegang tot virtueel netwerk toestaan in en selecteert u **Doorgestuurd** verkeer toestaan en **Externe gateways toestaan.**
1. Ga naar de **App Service**  >  **gebruikersinterface**  >  **netwerk-VNet-integratie** plannen in de portal. Selecteer het VNet waarmee uw app verbinding maakt. Voeg in de sectie Routering het adresbereik toe van het VNet dat is gekoppeld aan het VNet waarmee uw app is verbonden.

## <a name="manage-vnet-integration"></a>VNet-integratie beheren

Verbinding maken en de verbinding met een VNet verbreken is op app-niveau. Bewerkingen die van invloed kunnen zijn op VNet-integratie in meerdere apps, staan op App Service niveau van het abonnement. Via de app > **De integratieportal**  >  **voor netwerk-VNets** vindt u meer informatie over uw VNet. Vergelijkbare informatie vindt u op het niveau van App Service plan in de App Service  >    >  **Netwerk-VNet-integratieportal.**

De enige bewerking die u in de app-weergave van uw VNet-integratie-exemplaar kunt uitvoeren, is de verbinding van uw app verbreken met het VNet waarmee deze momenteel is verbonden. Als u de verbinding van uw app met een VNet wilt verbreken, selecteert u **Verbinding verbreken.** Uw app wordt opnieuw opgestart wanneer u de verbinding met een VNet verbreekt. Als u de verbinding verbreekt, verandert uw VNet niet. Het subnet of de gateway wordt niet verwijderd. Als u vervolgens uw VNet wilt verwijderen, verbreekt u eerst de verbinding van uw app met het VNet en verwijdert u de resources in het VNet, zoals gateways.

In App Service plan VNet Integration UI ziet u alle VNet-integraties die worden gebruikt door de apps in uw App Service abonnement. Als u de details van elk VNet wilt bekijken, selecteert u het VNet waarin u geïnteresseerd bent. Er zijn twee acties die u hier kunt uitvoeren voor gateway-vereiste VNet-integratie:

* **Synchronisatienetwerk:** de synchronisatienetwerkbewerking wordt alleen gebruikt voor de gatewayafhankelijke VNet-integratiefunctie. Het uitvoeren van een synchronisatienetwerkbewerking zorgt ervoor dat uw certificaten en netwerkgegevens zijn gesynchroniseerd. Als u de DNS van uw VNet toevoegt of wijzigt, voert u een synchronisatienetwerkbewerking uit. Met deze bewerking worden alle apps die gebruikmaken van dit VNet opnieuw gestart. Deze bewerking werkt niet als u een app en een vnet gebruikt die tot verschillende abonnementen behoren.
* **Routes toevoegen:** het toevoegen van routes zorgt voor uitgaand verkeer naar uw VNet.

Het privé-IP-adres dat aan het exemplaar is toegewezen, wordt beschikbaar gemaakt via de omgevingsvariabele **, WEBSITE_PRIVATE_IP**. De gebruikersinterface van de Kudu-console toont ook de lijst met omgevingsvariabelen die beschikbaar zijn voor de web-app. Dit IP-adres wordt toegewezen vanuit het adresbereik van het geïntegreerde subnet. Voor regionale VNet-integratie is de waarde van WEBSITE_PRIVATE_IP een IP-adres uit het adresbereik van het gedelegeerde subnet en voor gateway-vereiste VNet-integratie is de waarde een IP-adres uit het adresbereik van de punt-naar-site-adresgroep die is geconfigureerd op de Virtual Network-gateway. Dit is het IP-adres dat door de web-app wordt gebruikt om verbinding te maken met de resources via de Virtual Network. 

> [!NOTE]
> De waarde van WEBSITE_PRIVATE_IP wordt gewijzigd. Het is echter een IP-adres binnen het adresbereik van het integratiesubnet of het punt-naar-site-adresbereik, dus u moet toegang vanaf het hele adresbereik toestaan.
>

### <a name="gateway-required-vnet-integration-routing"></a>Gateway-vereiste VNet-integratieroutering
De routes die in uw VNet zijn gedefinieerd, worden gebruikt om verkeer vanuit uw app naar uw VNet te leiden. Als u extra uitgaand verkeer naar het VNet wilt verzenden, voegt u deze adresblokken hier toe. Deze mogelijkheid werkt alleen met VNet-integratie die vereist is voor de gateway. Routetabellen hebben geen invloed op uw app-verkeer wanneer u gateway-vereiste VNet-integratie gebruikt zoals dat bij regionale VNet-integratie gebeurt.

### <a name="gateway-required-vnet-integration-certificates"></a>Gateway-vereiste VNet-integratiecertificaten
Wanneer gateway-vereiste VNet-integratie is ingeschakeld, is er een vereiste uitwisseling van certificaten om de beveiliging van de verbinding te garanderen. Naast de certificaten zijn de DNS-configuratie, routes en andere vergelijkbare zaken die het netwerk beschrijven.

Als certificaten of netwerkgegevens zijn gewijzigd, selecteert u **Netwerk synchroniseren.** Wanneer u Netwerk **synchroniseren selecteert,** veroorzaakt u een korte storing in de connectiviteit tussen uw app en uw VNet. Hoewel uw app niet opnieuw wordt opgestart, kan het verlies van connectiviteit ertoe leiden dat uw site niet goed werkt.

## <a name="pricing-details"></a>Prijsdetails
De regionale VNet-integratiefunctie brengt geen extra kosten met zich mee voor gebruik buiten App Service kosten voor de prijscategorie van het abonnement.

Er zijn drie kosten verbonden aan het gebruik van de gateway-vereiste VNet-integratiefunctie:

* **App Service:uw** apps moeten zich in een Standard-, Premium-, PremiumV2- of PremiumV3-abonnement App Service. Zie Prijzen voor App Service [meer informatie over deze kosten.][ASPricing]
* **Kosten voor gegevensoverdracht:** er worden kosten in rekening brengen voor gegevens die zijn uitgegaan, zelfs als het VNet zich in hetzelfde datacenter bevindt. Deze kosten worden beschreven in [Prijsinformatie voor gegevensoverdracht.][DataPricing]
* **Kosten voor** VPN-gateway: er zijn kosten verbonden aan de gateway van het virtuele netwerk die vereist is voor de punt-naar-site-VPN. Zie Prijzen voor [VPN Gateway voor meer informatie.][VNETPricing]

## <a name="troubleshooting"></a>Problemen oplossen

> [!NOTE]
> VNET-integratie wordt niet ondersteund voor Docker Compose-scenario's in App Service.
> Azure Functions toegangsbeperkingen worden genegeerd als er een privé-eindpunt aanwezig is.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI-ondersteuning is beschikbaar voor regionale VNet-integratie. Installeer de Azure CLI voor toegang [tot de volgende opdrachten.][installCLI]

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

PowerShell-ondersteuning voor regionale VNet-integratie is ook beschikbaar, maar u moet een algemene resource maken met een eigenschaps array van de resourceID van het subnet

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


Voor gateway-vereiste VNet-integratie kunt u App Service met een virtueel Azure-netwerk met behulp van PowerShell. Zie Connect an app in Azure App Service to an Azure virtual network (Een app in een netwerk verbinden met een virtueel [Azure-netwerk) voor een kant-en-klaar script.](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)


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
