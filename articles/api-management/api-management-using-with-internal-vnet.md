---
title: Azure-API Management gebruiken met interne virtuele netwerken
titleSuffix: Azure API Management
description: Meer informatie over het instellen en configureren van Azure API Management in een intern virtueel netwerk
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.openlocfilehash: 4298b291e5d183c31d30a548751599aeb3746c47
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534615"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Azure API Management-service gebruiken met een intern virtueel netwerk
Met Azure Virtual Networks kan Azure API Management api's beheren die niet toegankelijk zijn op internet. Er zijn een aantal VPN-technologieën beschikbaar om de verbinding te maken. API Management kunnen worden geïmplementeerd in twee hoofdmodi binnen een virtueel netwerk:
* Extern
* Intern

Wanneer API Management in de modus voor intern virtueel netwerk implementeert, zijn alle service-eindpunten (de proxygateway, de ontwikkelaarsportal, direct beheer en Git) alleen zichtbaar in een virtueel netwerk waar u de toegang tot controleert. Geen van de service-eindpunten zijn geregistreerd op de openbare DNS-server.

> [!NOTE]
> Omdat er geen DNS-vermeldingen voor de service-eindpunten zijn, zijn deze eindpunten pas toegankelijk als [DNS is geconfigureerd](#apim-dns-configuration) voor het virtuele netwerk.

Als API Management in de interne modus gebruikt, kunt u de volgende scenario's bereiken:

* Maak API's die worden gehost in uw privé datacenter veilig toegankelijk voor derden buiten het datacenter door gebruik te maken van site-naar-site- of Azure ExpressRoute VPN-verbindingen.
* Schakel hybride cloudscenario's in door uw cloud-API's en on-premises API's via een gemeenschappelijke gateway weer te bieden.
* Beheer uw API's die worden gehost op meerdere geografische locaties met behulp van één gateway-eindpunt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

Als u de stappen wilt uitvoeren die in dit artikel worden beschreven, hebt u het volgende nodig:

+ **Een actief Azure-abonnement.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Een Azure API Management-instantie.** Zie Een Azure API Management maken [voor meer informatie.](get-started-create-service-instance.md)

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Wanneer een API Management-service wordt geïmplementeerd in een virtueel netwerk, wordt een lijst met [poorten](./api-management-using-with-vnet.md#required-ports) gebruikt en moet deze worden geopend. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Een API Management maken in een intern virtueel netwerk
De API Management-service in een intern virtueel netwerk wordt gehost achter een interne load balancer Basic-SKU als de service is gemaakt met client-API-versie 2020-12-01. Voor een service die is gemaakt met clients met API-versie 2021-01-01-preview en een openbaar IP-adres uit het abonnement van de klant, wordt deze gehost achter een interne load balancer Standard-SKU. Zie voor meer informatie [Azure Load Balancer SKU's.](../load-balancer/skus.md)

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Een virtuele netwerkverbinding inschakelen met behulp van de Azure Portal

1. Blader naar uw Azure API Management-exemplaar in [Azure Portal](https://portal.azure.com/).
1. Selecteer **Virtueel netwerk.**
1. Configureer **het toegangstype** Intern. Zie [VNET-connectiviteit](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal)inschakelen met behulp van de Azure Portal voor gedetailleerde Azure Portal.

    ![Menu voor het instellen van een Azure-API Management in een intern virtueel netwerk][api-management-using-internal-vnet-menu]

4. Selecteer **Opslaan**.

Nadat de implementatie is geslaagd, ziet u  het persoonlijke virtuele IP-adres en het openbare virtuele IP-adres van uw API Management-service op de overzichtsblade.  Het **privé** virtuele IP-adres is een IP-adres met load balanced vanuit het API Management gedelegeerd subnet waarover , en eindpunten `gateway` toegankelijk `portal` `management` `scm` zijn. Het **openbare** virtuele IP-adres wordt alleen gebruikt voor het verkeer van de besturingsvlak naar het eindpunt via poort 3443 en kan worden vergrendeld met de  `management` [apiManagement-servicetag.][ServiceTags]

![API Management dashboard configureren met een intern virtueel netwerk][api-management-internal-vnet-dashboard]

> [!NOTE]
> De testconsole die beschikbaar is in Azure Portal werkt niet voor de interne **VNET** geïmplementeerde service, omdat de gateway-URL niet is geregistreerd op de openbare DNS. Gebruik in plaats daarvan de testconsole in de **ontwikkelaarsportal.**

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Implementeer API Management in Virtual Network

U kunt ook virtuele netwerkconnectiviteit inschakelen met behulp van de volgende methoden.


### <a name="api-version-2020-12-01"></a>API-versie 2020-12-01

* Azure Resource Manager [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-internal-vnet)

     [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell- maken of bijwerken [van](/powershell/module/az.apimanagement/new-azapimanagement) een API Management-exemplaar in een virtueel netwerk [](/powershell/module/az.apimanagement/update-azapimanagementregion)

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-configuratie
Wanneer API Management zich in de modus extern virtueel netwerk, wordt de DNS beheerd door Azure. Voor de modus intern virtueel netwerk moet u uw eigen DNS beheren. Het configureren Azure DNS privézone en het koppelen ervan aan het virtuele netwerk API Management service is geïmplementeerd in is de aanbevolen optie. Meer informatie over [het instellen van een privézone in Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> API Management-service luistert niet naar aanvragen die afkomstig zijn van IP-adressen. De service reageert alleen op aanvragen op de hostnaam die is geconfigureerd op de service-eindpunten. Deze eindpunten omvatten gateway, de Azure Portal en de ontwikkelaarsportal, het eindpunt voor direct beheer en Git.

### <a name="access-on-default-host-names"></a>Toegang op standaardhostnamen
Wanneer u bijvoorbeeld een API Management service met de naam contosointernalvnet maakt, worden de volgende service-eindpunten standaard geconfigureerd:

   * Gateway of proxy: contosointernalvnet.azure-api.net

   * De ontwikkelaarsportal: contosointernalvnet.portal.azure-api.net

   * De nieuwe ontwikkelaarsportal: contosointernalvnet.developer.azure-api.net

   * Eindpunt voor direct beheer: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Voor toegang tot API Management service-eindpunten kunt u een virtuele machine maken in een subnet dat is verbonden met het virtuele netwerk waarin API Management is geïmplementeerd. Ervan uitgaande dat het interne virtuele IP-adres voor uw service 10.1.0.5 is, kunt u het hosts-bestand, %SystemDrive%\drivers\etc\hosts, als volgt in kaart brengen:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

U hebt vervolgens toegang tot alle service-eindpunten vanaf de virtuele machine die u hebt gemaakt.
Als u een aangepaste DNS-server in een virtueel netwerk gebruikt, kunt u ook A DNS-records maken en vanaf elke locatie in uw virtuele netwerk toegang krijgen tot deze eindpunten.

### <a name="access-on-custom-domain-names"></a>Toegang tot aangepaste domeinnamen

1. Als u geen toegang wilt tot de API Management-service met de standaardhostnamen, kunt u aangepaste domeinnamen instellen voor al uw service-eindpunten, zoals wordt weergegeven in de volgende afbeelding:

   ![Een aangepast domein instellen voor API Management][api-management-custom-domain-name]

2. Vervolgens kunt u records in uw DNS-server maken voor toegang tot de eindpunten die alleen toegankelijk zijn vanuit uw virtuele netwerk.

## <a name="routing"></a><a name="routing"></a> Routering

* Een *privé-IP-adres* met load balanceding uit het subnetbereik wordt gereserveerd en gebruikt voor toegang tot de API Management-service-eindpunten vanuit het virtuele netwerk. Dit  privé-IP-adres vindt u op de blade Overzicht voor de service in Azure Portal. Dit adres moet worden geregistreerd bij de DNS-servers die worden gebruikt door het virtuele netwerk.
* Een openbaar *IP-adres* (VIP) met load balanced wordt ook gereserveerd voor toegang tot het beheerservice-eindpunt via poort 3443. Dit *openbare* IP-adres vindt u op de blade Overzicht voor de service in Azure Portal. Het *openbare* IP-adres wordt alleen gebruikt voor verkeer van het besturingsvlak naar het eindpunt via poort 3443 en kan worden vergrendeld met de `management` [apiManagement-servicetag.][ServiceTags]
* IP-adressen uit het SUBNET IP-bereik (DIP) worden toegewezen aan elke virtuele machine in de service en worden gebruikt voor toegang tot resources in het virtuele netwerk. Er wordt een openbaar IP-adres (VIP) gebruikt voor toegang tot resources buiten het virtuele netwerk. Als IP-beperkingslijsten worden gebruikt om resources binnen het virtuele netwerk te beveiligen, moet het volledige bereik voor het subnet waarin de API Management-service is geïmplementeerd, worden opgegeven om toegang vanuit de service te verlenen of te beperken.
* De openbare en privé IP-adressen met load balanced zijn te vinden op de blade Overzicht in de Azure Portal.
* De IP-adressen die zijn toegewezen voor openbare en persoonlijke toegang, kunnen worden gewijzigd als de service wordt verwijderd en vervolgens weer wordt toegevoegd aan het virtuele netwerk. Als dit gebeurt, kan het nodig zijn om DNS-registraties, routeringsregels en IP-beperkingslijsten in het virtuele netwerk bij te werken.

## <a name="related-content"></a><a name="related-content"> </a>Gerelateerde inhoud
Lees de volgende artikelen voor meer informatie:
* [Veelvoorkomende problemen met de netwerkconfiguratie tijdens het instellen van Azure API Management in een virtueel netwerk][Common network configuration problems]
* [Veelgestelde vragen over virtuele netwerken](../virtual-network/virtual-networks-faq.md)
* [Een record maken in DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
