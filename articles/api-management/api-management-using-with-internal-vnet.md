---
title: Azure API Management gebruiken met interne virtuele netwerken
titleSuffix: Azure API Management
description: Meer informatie over het instellen en configureren van Azure API Management op een intern virtueel netwerk
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 10154f496d76ce6b9eb19d610fdff8d7a4023c2d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565951"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Azure API Management-service gebruiken met een intern virtueel netwerk
Met Azure Virtual Networks kunnen Azure API Management Api's beheren die niet toegankelijk zijn via internet. Er zijn een aantal VPN-technologieën beschikbaar om verbinding te maken. API Management kan in twee hoofd modi in een virtueel netwerk worden geïmplementeerd:
* Extern
* Intern

Als API Management in de interne virtuele netwerk modus wordt geïmplementeerd, zijn alle service-eind punten (de proxy gateway, de ontwikkelaars Portal, direct beheer en Git) alleen zichtbaar in een virtueel netwerk waartoe u de toegang beheert. Geen van de service-eind punten is geregistreerd op de open bare DNS-server.

> [!NOTE]
> Omdat er geen DNS-vermeldingen voor de service-eind punten zijn, zijn deze eind punten pas toegankelijk als [DNS is geconfigureerd](#apim-dns-configuration) voor het virtuele netwerk.

Met API Management in de interne modus kunt u de volgende scenario's uitvoeren:

* Maak Api's die worden gehost in uw particuliere Data Center die veilig toegankelijk zijn voor derden buiten het bedrijfs netwerk met behulp van site-naar-site-of Azure ExpressRoute-VPN-verbindingen.
* Schakel hybride Cloud scenario's in door uw Cloud-Api's en on-premises Api's via een gemeen schappelijke gateway beschikbaar te stellen.
* Beheer uw Api's die worden gehost op meerdere geografische locaties door één gateway-eind punt te gebruiken.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

Voor het uitvoeren van de stappen die in dit artikel worden beschreven, hebt u het volgende nodig:

+ **Een actief Azure-abonnement**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Een Azure API Management-exemplaar**. Zie [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.
+ Wanneer een API Management-service in een virtueel netwerk wordt geïmplementeerd, wordt een [lijst met poorten](./api-management-using-with-vnet.md#required-ports) gebruikt en moet deze worden geopend. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Een API Management in een intern virtueel netwerk maken
De API Management-service in een intern virtueel netwerk wordt gehost achter een [interne Load Balancer (klassiek)](/previous-versions/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Dit is de enige optie die beschikbaar is en niet kan worden gewijzigd.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Een virtuele netwerk verbinding inschakelen met behulp van de Azure Portal

1. Blader naar uw Azure API Management-exemplaar in het [Azure Portal](https://portal.azure.com/).
2. Selecteer **virtueel netwerk**.
3. Configureer het API Management-exemplaar dat in het virtuele netwerk moet worden geïmplementeerd.

    ![Menu voor het instellen van een Azure-API Management in een intern virtueel netwerk][api-management-using-internal-vnet-menu]

4. Selecteer **Opslaan**.

Nadat de implementatie is voltooid, ziet u het **persoonlijke** virtuele IP-adres en het **open bare** virtuele IP-adres van uw API Management-service op de Blade overzicht. Het **persoonlijke** virtuele IP-adres is een IP-adres met taak verdeling vanuit het API Management overgedragen subnet waarover `gateway` , `portal` `management` en `scm` eind punten toegankelijk zijn. Het **open bare** virtuele IP-adres wordt **alleen** gebruikt voor controle vlak verkeer naar `management` het eind punt via poort 3443 en kan worden vergrendeld op de [ApiManagement][ServiceTags] servicetag.

![API Management dash board met een intern virtueel netwerk geconfigureerd][api-management-internal-vnet-dashboard]

> [!NOTE]
> De test console die beschikbaar is in azure Portal, werkt niet voor **intern** geïmplementeerde Services, omdat de gateway-URL niet is geregistreerd op de open bare DNS-server. U moet in plaats daarvan de test console gebruiken die is opgenomen in de **ontwikkelaars Portal**.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>API Management implementeren in Virtual Network

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

U kunt ook virtuele netwerk connectiviteit inschakelen met behulp van Power shell-cmdlets.

* Een API Management-service maken in een virtueel netwerk: gebruik de cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) om een Azure API Management-service in een virtueel netwerk te maken en configureer deze voor het gebruik van het interne virtuele netwerk type.

* Een bestaande implementatie van een API Management-service in een virtueel netwerk bijwerken: gebruik de cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) om een bestaande API Management service in een virtueel netwerk te verplaatsen en deze te configureren voor het gebruik van het interne virtuele-netwerk type.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-configuratie
Wanneer API Management zich in de modus Extern virtueel netwerk bevindt, wordt de DNS beheerd door Azure. Voor interne virtuele netwerk modus moet u uw eigen DNS beheren. Het configureren van een Azure DNS privé zone en koppelen aan het virtuele netwerk API Management service is geïmplementeerd in is de aanbevolen optie.  Klik [hier](../dns/private-dns-getstarted-portal.md) voor meer informatie over het instellen van een privé zone in azure DNS.

> [!NOTE]
> API Management-service luistert niet naar aanvragen die afkomstig zijn van IP-adressen. Deze reageert alleen op aanvragen voor de hostnaam die is geconfigureerd op de service-eind punten. Deze eind punten omvatten gateway, het Azure Portal en de ontwikkelaars Portal, het direct beheer eindpunt en git.

### <a name="access-on-default-host-names"></a>Toegang op standaard hostnamen
Wanneer u een API Management-service met de naam ' contosointernalvnet ' maakt, worden de volgende service-eind punten standaard geconfigureerd:

   * Gateway of proxy: contosointernalvnet.azure-api.net

   * De ontwikkelaars portal: contosointernalvnet.portal.azure-api.net

   * De nieuwe ontwikkelaars portal: contosointernalvnet.developer.azure-api.net

   * Direct beheer eindpunt: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Voor toegang tot deze API Management service-eind punten kunt u een virtuele machine maken in een subnet dat is verbonden met het virtuele netwerk waarin API Management wordt geïmplementeerd. Uitgaande van het interne virtuele IP-adres voor uw service is 10.1.0.5, kunt u het hosts-bestand als volgt toewijzen:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

U kunt vervolgens toegang krijgen tot alle service-eind punten van de virtuele machine die u hebt gemaakt.
Als u een aangepaste DNS-server in een virtueel netwerk gebruikt, kunt u ook een DNS-record maken en vanaf elke locatie in het virtuele netwerk toegang krijgen tot deze eind punten.

### <a name="access-on-custom-domain-names"></a>Toegang voor aangepaste domein namen

1. Als u geen toegang wilt krijgen tot de API Management-service met de standaardhostnaam, kunt u aangepaste domein namen instellen voor al uw service-eind punten, zoals wordt weer gegeven in de volgende afbeelding:

   ![Instellen van een aangepast domein voor API Management][api-management-custom-domain-name]

2. Vervolgens kunt u in uw DNS-server records maken voor toegang tot de eind punten die alleen toegankelijk zijn vanuit uw virtuele netwerk.

## <a name="routing"></a><a name="routing"></a> Route ring

* Een *privé* -IP-adres met gelijke taak verdeling van het subnet-bereik wordt gereserveerd en wordt gebruikt voor toegang tot de API Management service-eind punten vanuit het virtuele netwerk. Dit *privé* -IP-adres bevindt zich op de Blade overzicht voor de service in de Azure Portal. Dit adres moet worden geregistreerd bij de DNS-servers die worden gebruikt door het virtuele netwerk.
* Een *openbaar* IP-adres (VIP) met gelijke taak verdeling wordt ook gereserveerd om toegang te bieden tot het beheer service-eind punt via poort 3443. Dit *open bare* IP-adres bevindt zich op de Blade overzicht voor de service in de Azure Portal. Het *open bare* IP-adres wordt alleen gebruikt voor het beheer van het verkeer naar het `management` eind punt via poort 3443 en kan worden vergrendeld op de [ApiManagement][ServiceTags] servicetag.
* IP-adressen uit het subnet IP-bereik (DIP) worden toegewezen aan elke virtuele machine in de service en worden gebruikt voor toegang tot bronnen in het virtuele netwerk. Een openbaar IP-adres (VIP) wordt gebruikt voor toegang tot bronnen buiten het virtuele netwerk. Als er IP-beperkings lijsten worden gebruikt voor het beveiligen van resources binnen het virtuele netwerk, moet het hele bereik voor het subnet waarin de API Management-service is geïmplementeerd, worden opgegeven om de toegang tot de service te verlenen of te beperken.
* U kunt de open bare en privé-IP-adressen met gelijke taak verdeling vinden op de Blade overzicht in het Azure Portal.
* De IP-adressen die zijn toegewezen voor open bare en persoonlijke toegang, kunnen worden gewijzigd als de service wordt verwijderd uit en vervolgens weer wordt toegevoegd aan het virtuele netwerk. Als dit gebeurt, kan het nodig zijn om DNS-registraties, routerings regels en IP-beperkings lijsten in het virtuele netwerk bij te werken.

## <a name="related-content"></a><a name="related-content"> </a>Gerelateerde inhoud
Lees de volgende artikelen voor meer informatie:
* [Veelvoorkomende problemen met de netwerk configuratie tijdens het instellen van Azure-API Management in een virtueel netwerk][Common network configuration problems]
* [Veelgestelde vragen over virtuele netwerken](../virtual-network/virtual-networks-faq.md)
* [Een record maken in DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
