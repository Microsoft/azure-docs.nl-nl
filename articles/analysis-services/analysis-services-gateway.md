---
title: On-premises gegevens gateway voor Azure Analysis Services | Microsoft Docs
description: Een on-premises gateway is nodig als uw Analysis Services-server in azure verbinding maakt met on-premises gegevens bronnen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6ca96f76287482a445d8a9a1cdc441333b36efbd
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739600"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Verbinding maken met on-premises gegevens bronnen met on-premises gegevens gateway

De on-premises gegevens gateway biedt veilige gegevens overdracht tussen on-premises gegevens bronnen en uw Azure Analysis Services servers in de Cloud. Naast het werken met meerdere Azure Analysis Services servers in dezelfde regio, werkt de meest recente versie van de gateway ook met Azure Logic Apps, Power BI, Power apps en automatische energie. Hoewel de gateway die u installeert hetzelfde is voor al deze services, Azure Analysis Services en Logic Apps enkele extra stappen hebben.

De informatie die hier wordt gegeven, is specifiek voor de manier waarop Azure Analysis Services werkt met de on-premises gegevens gateway. Zie [Wat is een on-premises gegevens gateway?](/data-integration/gateway/service-gateway-onprem)voor meer informatie over de gateway in het algemeen en hoe deze werkt met andere services.

Voor Azure Analysis Services is het voorbereiden van de installatie met de gateway de eerste keer een proces met vier delen:

- **Setup downloaden en uitvoeren** : met deze stap installeert u een gateway service op een computer in uw organisatie. U kunt zich ook aanmelden bij Azure met behulp van een account in de Azure AD [van uw Tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Accounts van Azure B2B (gast) worden niet ondersteund.

- **Uw gateway registreren** : in deze stap geeft u een naam en herstel sleutel op voor uw gateway en selecteert u een regio, waarbij u uw gateway registreert bij de gateway-Cloud service. Uw gateway resource kan in elke regio worden geregistreerd, maar het wordt aanbevolen dat deze zich in dezelfde regio bevindt als uw Analysis Services-servers. 

- **Een gateway bron maken in azure** : in deze stap maakt u een gateway bron in Azure.

- **De gateway bron verbinden met servers** : Zodra u een gateway resource hebt, kunt u aan de slag gaan met het verbinden van de servers. U kunt meerdere servers en andere resources verbinden, mits deze zich in dezelfde regio bevinden.

## <a name="installing"></a>Installeren

Wanneer u voor een Azure Analysis Services omgeving installeert, is het belang rijk dat u de stappen volgt die worden beschreven in de [on-premises gegevens gateway installeren en configureren voor Azure Analysis Services](analysis-services-gateway-install.md). Dit artikel is specifiek voor Azure Analysis Services. Het bevat aanvullende stappen die vereist zijn voor het instellen van een on-premises gegevens gateway resource in Azure en het verbinden van uw Azure Analysis Services-server met de resource.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Verbinding maken met een gateway bron in een ander abonnement

Het is raadzaam om uw Azure-gateway resource te maken in hetzelfde abonnement als uw server. U kunt uw servers echter configureren om verbinding te maken met een gateway bron in een ander abonnement. Het maken van verbinding met een gateway bron in een ander abonnement wordt niet ondersteund bij het configureren van bestaande server instellingen of het maken van een nieuwe server in de portal, maar kan worden geconfigureerd met behulp van Power shell. Zie [verbinding maken tussen gateway bron en server](analysis-services-gateway-install.md#connect-gateway-resource-to-server)voor meer informatie.

## <a name="ports-and-communication-settings"></a>Poorten en communicatie-instellingen

De gateway maakt een uitgaande verbinding naar Azure Service Bus. De gateway communiceert via uitgaande poorten: TCP 443 (standaard), 5671, 5672, 9350 t/m 9354.  De gateway vereist geen inkomende poorten.

Mogelijk moet u IP-adressen voor uw gegevens regio in uw firewall toevoegen. U kunt de lijst met IP-adressen van Microsoft Azure-datacenters [hier](https://www.microsoft.com/download/details.aspx?id=56519) downloaden. Deze lijst wordt wekelijks bijgewerkt. De adressen in de lijst met IP-adressen van Azure-datacenters worden vermeld in de CIDR-notatie. Zie [klasseloze Inter-Domain route ring](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)voor meer informatie.

Hieronder vindt u een volledig gekwalificeerde domein naam die wordt gebruikt door de gateway.

| Domeinnamen | Uitgaande poorten | Beschrijving |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP wordt gebruikt om het installatiebestand te downloaden. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listeners op Service Bus Relay via TCP (vereist 443 voor het ophalen van tokens voor toegangsbeheer) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |80 |Gebruikt voor het testen van de internetverbinding als de gateway onbereikbaar is voor de Power BI-service. |
| *.microsoftonline-p.com |443 |Wordt gebruikt voor verificatie, afhankelijk van de configuratie. |
| dc.services.visualstudio.com    |443 |Wordt door AppInsights gebruikt voor het verzamelen van telemetrie. |

## <a name="next-steps"></a>Volgende stappen 

De volgende artikelen zijn opgenomen in de on-premises gegevens gateway algemene inhoud die van toepassing is op alle services die door de gateway worden ondersteund:

* [Veelgestelde vragen over on-premises gegevensgateways](/data-integration/gateway/service-gateway-onprem-faq)   
* [Gebruik de on-premises gegevensgateway-app](/data-integration/gateway/service-gateway-app)   
* [Beheer op tenantniveau](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Proxyinstellingen configureren](/data-integration/gateway/service-gateway-proxy)   
* [Communicatie-instellingen aanpassen](/data-integration/gateway/service-gateway-communication)   
* [Logboek bestanden configureren](/data-integration/gateway/service-gateway-log-files)   
* [Problemen oplossen](/data-integration/gateway/service-gateway-tshoot)
* [Gatewayprestaties bewaken en optimaliseren](/data-integration/gateway/service-gateway-performance)
