---
title: Een WAF configureren
description: Meer informatie over het configureren van een Web Application Firewall (WAF) voor uw App Service Environment, met Azure Application Gateway of een WAF van derden.
author: ccompy
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 56d931f2346e5a0b615d3f11dce3b06396e586b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588714"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Een Web Application Firewall (WAF) voor App Service Environment configureren
## <a name="overview"></a>Overzicht

Web Application Firewalls (WAF) helpen met het beveiligen van uw webtoepassingen door inkomend webverkeer te inspecteren om SQL-injecties, Cross-Site Scripting, malware-uploads en DDoS-toepassingen en andere aanvallen te blokkeren. Ze inspecteren ook de antwoorden van de back-end-webservers om gegevensverlies te voorkomen (DLP). In combinatie met de isolatie en aanvullende schalen geleverd door App Service Environments, biedt dit een ideale omgeving om bedrijfskritieke webtoepassingen te hosten die bestand moeten zijn tegen schadelijke aanvragen en grote hoeveelheden verkeer. Azure biedt mogelijkheden met WAF met de [Application Gateway](../../application-gateway/overview.md).  Voor informatie over het integreren van uw App Service Environment met een toepassingsgateway, kunt u het document [Uw ILD ASE integreren met een Application Gateway](./integrate-with-application-gateway.md) lezen.

Naast de Azure Application Gateway zijn er meerdere opties, zoals de [Barracuda WAF voor Azure](https://www.barracuda.com/programs/azure), beschikbaar in de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf?tab=PlansAndPrice). De rest van dit document richt zich op het integreren van uw App Service Environment met een Barracuda WAF-apparaat.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Instellen
Voor dit document wordt de App Service Environment achter meerdere exemplaren met gelijke taakverdeling van de Barracuda WAF zodanig geconfigureerd dat alleen het verkeer vanaf de WAF de App Service Environment kan bereiken en deze niet toegankelijk is vanaf de DMZ. We hebben ook Azure Traffic Manager voor de Barracuda WAF-exemplaren geplaatst om de taken gelijk te verdelen over Azure-datacenters en -regio's. Een diagram op hoog niveau van de installatie ziet eruit als in de volgende afbeelding:

![Diagram van een optionele Azure Traffic Manager die verbinding maakt met exemplaren van Web Application Firewall, die verbinding maakt met een netwerktoegangsbeheerlijst zodat alleen verkeer van de firewall wordt toegestaan in een App Service Environment met Web, API en Mobile App voor twee regio's.][Architecture] 

> [!NOTE]
> Dankzij de introductie van [ILB-ondersteuning voor App Service Environment](app-service-environment-with-internal-load-balancer.md), kunt u de ASE zodanig configureren dat deze niet toegankelijk is vanaf de DMZ en alleen beschikbaar is voor het particuliere netwerk. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Uw App Service Environment configureren
Raadpleeg voor het configureren van een App Service Environment [onze documentatie](app-service-web-how-to-create-an-app-service-environment.md) over dit onderwerp. Als u een App Service Environment gemaakt hebt, kunt u in deze omgeving Web-apps, API-apps en [Mobiele apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) maken die allemaal worden beveiligd achter de WAF die we in de volgende sectie configureren.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Uw Barracuda WAF Cloud Service configureren
Barracuda heeft een [gedetailleerd artikel](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) over het implementeren van de WAF op een virtuele machine in Azure. Maar omdat we redundantie willen en geen Single Point of Failure willen introduceren, dient u ten minste twee instanties van virtuele WAF-machines te implementeren in dezelfde cloudservice wanneer u deze instructies volgt.

### <a name="adding-endpoints-to-cloud-service"></a>Eindpunten toevoegen in de cloudservice
Wanneer u 2 of meer WAF VM-exemplaren in uw cloudservice hebt, kunt u [Azure Portal](https://portal.azure.com/) gebruiken om HTTP en HTTPS-eindpunten toe te voegen die worden gebruikt door uw toepassing, zoals wordt weergegeven in de volgende afbeelding:

![Eindpunt configureren][ConfigureEndpoint]

Als uw toepassingen andere eindpunten gebruiken, zorg er dan voor dat deze ook aan deze lijst zijn toegevoegd. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Barracuda WAF via het Beheerportal configureren
Barracuda WAF maakt gebruik van TCP-poort 8000 voor configuratie via het Beheerportal. Als u meerdere exemplaren van de virtuele WAF-machines hebt, moet u hier de stappen herhalen voor elk VM-exemplaar. 

> [!NOTE]
> Wanneer u klaar bent met de WAF-configuratie, moet u het TCP/8000-eindpunt uit uw WAF-VM's verwijderen om uw WAF veilig te houden.
> 
> 

Voeg het beheereindpunt toe zoals weergegeven in de volgende afbeelding om uw Barracuda WAF te configureren.

![Beheereindpunt toevoegen][AddManagementEndpoint]

Een browser gebruiken om te bladeren naar het beheerseindpunt op uw cloudservice. Als uw cloudservice test.cloudapp.net heet, zou u dit eindpunt openen door naar `http://test.cloudapp.net:8000` te bladeren. U ziet een aanmeldingspagina zoals de volgende afbeelding waarop u kunt aanmelden met de referenties die u hebt opgegeven in de installatiefase van de WAF-VM.

![Aanmeldingspagina voor beheer][ManagementLoginPage]

Zodra u zich aanmeldt, moet u een dashboard kunnen zien zoals in de volgende afbeelding, waarin de basisstatistieken over de beveiliging van WAF te zien zijn.

![Dashboard voor beheer][ManagementDashboard]

Klik op het tabblad **Services** om uw WAF te configureren voor services die deze beveiligt. Zie voor meer informatie over het configureren van uw Barracuda WAF [hun documentatie](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259884/configure-the-barracuda-web-application-firewall-from-the-web-interface/). In het volgende voorbeeld wordt een App Service-app geconfigureerd voor verkeer op HTTP en HTTPS.

![Beheerservices toevoegen][ManagementAddServices]

> [!NOTE]
> Afhankelijk van hoe uw toepassingen zijn geconfigureerd en welke functies worden gebruikt in uw App Service Environment, moet u verkeer voor andere TCP-poorten dan 80 en 443 doorsturen, bijvoorbeeld als u IP TLS hebt ingesteld voor een App Service-app. Zie voor een lijst met netwerkpoorten die worden gebruikt in App Service Environments, [Documentatie voor inkomend verkeer beheren](app-service-app-service-environment-control-inbound-traffic.md), in de sectie Netwerkpoorten.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configuratie van Microsoft Azure Traffic Manager (OPTIONEEL)
Als uw toepassing beschikbaar in meerdere regio's, kunt u het best zorgen voor gelijke taakverdeling achter [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Om dit te doen, kunt u een eindpunt toevoegen in de [Azure Portal](https://portal.azure.com) met de naam van de cloudservice voor uw WAF in het Traffic Manager-profiel, zoals wordt weergegeven in de volgende afbeelding. 

![Traffic Manager-eindpunt][TrafficManagerEndpoint]

Als voor uw toepassing verificatie is vereist, zorg er dan voor dat u een resource hebt die geen verificatie voor Traffic Manager vereist om te pingen voor de beschikbaarheid van uw toepassing. U kunt de URL configureren op de **Configuratie** pagina in de [Azure Portal](https://portal.azure.com), zoals wordt weergegeven in de volgende afbeelding:

![Traffic Manager configureren][ConfigureTrafficManager]

Om Traffic Manager-pings naar uw toepassing door te verbinden vanaf uw WAF, moet u Website Translations op uw WAF Barracuda instellen voor het doorsturen van verkeer naar uw toepassing, zoals wordt weergegeven in het volgende voorbeeld:

![Website Translations][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Verkeer naar App Service Environment beveiligen met behulp van Netwerkbeveiligingsgroepen (NSG)
Volg de [Documentatie van binnenkomend verkeer beheren](app-service-app-service-environment-control-inbound-traffic.md) voor meer informatie over het beperken van verkeer tot uw App Service Environment vanaf de WAF met behulp van alleen het VIP-adres van uw cloudservice. Hier volgt een PowerShell-voorbeeldopdracht voor het uitvoeren van deze taak voor TCP-poort 80.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
```

Vervang de SourceAddressPrefix door het virtuele IP-adres (VIP) van uw WAF-cloudservice.

> [!NOTE]
> Het VIP van uw cloudservice wordt gewijzigd wanneer u de cloudservice verwijdert en opnieuw aanmaakt. Zorg ervoor dat u het IP-adres in de netwerk-resourcegroep bijwerkt nadat u dit doet. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
