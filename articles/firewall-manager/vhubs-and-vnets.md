---
title: Wat zijn de opties voor de Azure Firewall Manager-architectuur?
description: Vergelijk en contrast met behulp van hub Virtual Network of beveiligde virtuele hub-architecturen met Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563145"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Wat zijn de opties voor de Azure Firewall Manager-architectuur?

Azure Firewall Manager kan beveiligings beheer bieden voor twee typen netwerk architectuur:

- **beveiligde virtuele hub**

   Een [Azure Virtual WAN-hub](../virtual-wan/virtual-wan-about.md#resources) is een door Microsoft beheerde resource waarmee u eenvoudig sterarchitecturen kunt maken. Wanneer beveiligings-en routerings beleidsregels aan een dergelijke hub zijn gekoppeld, wordt deze aangeduid als een *[beveiligde virtuele hub](secured-virtual-hub.md)*. 
- **hub-netwerk**

   Dit is een standaard virtueel Azure-netwerk dat u zelf maakt en beheert. Wanneer beveiligingsbeleidsregels met een dergelijke hub worden gekoppeld, wordt dit een *virtueel hubnetwerk* genoemd. Op dit ogenblik wordt alleen Azure Firewall-beleid ondersteund. U kunt virtuele spaaknetwerken met uw workloadservers en services via peering verbinden. U kunt ook firewalls beheren in zelfstandige virtuele netwerken die niet aan een spaak zijn gekoppeld.

## <a name="comparison"></a>Vergelijking

De volgende tabel vergelijkt deze twee architectuur opties en kan u helpen beslissen welke optie geschikt is voor de beveiligings vereisten van uw organisatie:


|  |**Virtueel hubnetwerk**|**Beveiligde virtuele hub**  |
|---------|---------|---------|
|**Onderliggende resource**     |Virtueel netwerk|Virtuele WAN-hub|
|**Hub & spoke**     |Maakt gebruik van peering op virtueel netwerk|Geautomatiseerd met hub Virtual Network-verbinding|
|**On-premises connectiviteit**     |VPN Gateway tot 10 Gbps en 30 S2S-verbindingen; ExpressRoute|Meer schaal bare VPN Gateway van 20 Gbps-en 1000 S2S-verbindingen; Express route|
|**Automatische filiaal connectiviteit met SDWAN**      |Niet ondersteund|Ondersteund|
|**Hubs per regio**     |Meerdere virtuele netwerken per regio|Eén virtuele hub per regio. Meerdere hubs mogelijk met meerdere virtuele Wan's|
|**Azure Firewall: meerdere open bare IP-adressen**      |Klant|Automatisch gegenereerd|
|**Azure Firewall Beschikbaarheidszones**     |Ondersteund|Nog niet beschikbaar|
|**Geavanceerde Internet beveiliging met beveiliging van derden als service partners**     |Door de klant tot stand gebrachte en beheerde VPN-verbinding met de partner service van de keuze|Geautomatiseerd via de stroom van de beveiligings partner en de partner beheer ervaring|
|**Gecentraliseerd route beheer voor het routeren van verkeer naar de hub**     |Door de gebruiker gedefinieerde route door de klant|Ondersteund met behulp van BGP|
|**Ondersteuning voor meerdere beveiligings providers**|Ondersteund met hand matig geconfigureerde geforceerde tunneling naar firewalls van derden|Automatische ondersteuning voor twee beveiligings providers: Azure Firewall voor het filteren van privé verkeer en van derden voor het filteren van Internet|
|**Web Application Firewall in Application Gateway** |Ondersteund in Virtual Network|Momenteel ondersteund in spoke-netwerk|
|**Virtueel netwerk apparaat**|Ondersteund in Virtual Network|Momenteel ondersteund in spoke-netwerk|
|**Azure DDoS Protection Standard-ondersteuning**|Ja|Nee|

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de implementatie van Azure Firewall Manager](deployment-overview.md) controleren
- Meer informatie over [beveiligde virtuele hubs](secured-virtual-hub.md).