---
title: Overzicht van het virtuele netwerk van Azure Microsoft Docs
description: Meer informatie over het tikken van een virtueel netwerk. Met het virtuele netwerk tikken kunt u een diepe kopie maken van het netwerk verkeer van de virtuele machine die kan worden gestreamd naar een pakket verzamelaar.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 8347dcd60dcdfbf03f52e25eb24744909c69465a
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804867"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP
> [!IMPORTANT]
> Het voor beeld van een virtueel netwerk Tik is momenteel in de wachtrij voor alle Azure-regio's. U kunt ons een e-mail sturen met <azurevnettap@microsoft.com> uw abonnements-id en u ontvangt een melding met toekomstige updates over de preview-versie. In de tijdelijke oplossing kunt u op agents gebaseerde of NVA oplossingen gebruiken die de zichtbaarheids functionaliteit van tikken/netwerk bieden via onze [pakket Broker-partner oplossingen](#virtual-network-tap-partner-solutions) die beschikbaar zijn in [Azure Marketplace-aanbiedingen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners).

Met het virtuele netwerk van Azure (Terminal Access Point) kunt u het netwerk verkeer van de virtuele machine continu streamen naar een netwerk pakket verzamelaar of een analyse programma. Het hulp programma Collector of Analytics wordt verschaft door een [virtuele netwerk apparaat](https://azure.microsoft.com/solutions/network-appliances/) -partner. Zie [partner oplossingen](#virtual-network-tap-partner-solutions)voor een lijst met partner oplossingen die zijn gevalideerd om te werken met Virtual Network tikken.
In de volgende afbeelding ziet u hoe virtuele netwerk tikken werkt. U kunt een TAP-configuratie toevoegen aan een [netwerk interface](virtual-network-network-interface.md) die is gekoppeld aan een virtuele machine die is geïmplementeerd in uw virtuele netwerk. De bestemming is een IP-adres van een virtueel netwerk in hetzelfde virtuele netwerk als de bewaakte netwerk interface of een gekoppeld [virtueel](virtual-network-peering-overview.md) netwerk. De Collector-oplossing voor Virtual Network TIKT kan worden geïmplementeerd achter een interne Load Balancer van Azure voor hoge Beschik baarheid.
![Hoe Virtual Network TIKT werkt](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Vereisten

Voordat u een virtueel netwerk maakt, moet u een bevestigings bericht hebben ontvangen dat u in de preview hebt Inge schreven en dat er een of meer virtuele machines zijn gemaakt met [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) implementatie model en een partner oplossing voor het samen voegen van het tikken verkeer in dezelfde Azure-regio. Als u geen partner oplossing in uw virtuele netwerk hebt, raadpleegt u [partner oplossingen](#virtual-network-tap-partner-solutions) om er een te implementeren. U kunt hetzelfde virtuele netwerk tikken op resource om verkeer van meerdere netwerk interfaces in dezelfde of verschillende abonnementen samen te voegen. Als de bewaakte netwerk interfaces zich in verschillende abonnementen bevinden, moeten de abonnementen zijn gekoppeld aan dezelfde Azure Active Directory Tenant. Daarnaast kunnen de bewaakte netwerk interfaces en het bestemmings eindpunt voor het samen voegen van het TIKs verkeer zich in gekoppelde virtuele netwerken in dezelfde regio bevinden. Als u dit implementatie model gebruikt, moet u ervoor zorgen dat de [peering van het virtuele netwerk](virtual-network-peering-overview.md) is ingeschakeld voordat u tikt op het virtuele netwerk.

## <a name="permissions"></a>Machtigingen

De accounts die u gebruikt voor het Toep assen van TIKT op netwerk interfaces, moeten worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de nodige acties zijn toegewezen in de volgende tabel:

| Bewerking | Name |
|---|---|
| Micro soft. Network/virtualNetworkTaps/* | Vereist voor het maken, bijwerken, lezen en verwijderen van een virtueel netwerk Tik op resource |
| Micro soft. Network/networkInterfaces/lezen | Vereist voor het lezen van de netwerk interface bron waarop de Tik wordt geconfigureerd |
| Micro soft. Network/tapConfigurations/* | Vereist voor het maken, bijwerken, lezen en verwijderen van de TAP-configuratie op een netwerk interface |


## <a name="virtual-network-tap-partner-solutions"></a>Virtueel netwerk TIKT u op partner oplossingen

### <a name="network-packet-brokers"></a>Netwerkpakket Brokers

- [GigaVUE Cloud Suite voor Azure](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Big-Switch infrastructuur Big](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Beveiligings analyse, beheer van netwerk/toepassings prestaties

- [Wakker-beveiliging](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch-Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cyber beveiliging](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [Netfort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [® Platform voor RSA-netwitness](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een virtueel netwerk tikken](tutorial-tap-virtual-network-cli.md).
