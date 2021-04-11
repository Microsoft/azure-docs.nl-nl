---
title: Typen aanvallen Azure DDoS Protection standaard oplossingen
description: Meer informatie over soorten aanvallen Azure DDoS Protection Standard wordt beschermd.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8eac365fc5146f315db8f09a67960aa892fa1c85
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106051"
---
# <a name="types-of-ddos-attacks-overview"></a>Overzicht van typen DDoS-aanvallen

DDoS Protection Standard kan de volgende typen aanvallen beperken:

- **Volumetrische aanvallen**: deze aanvallen flooden de netwerklaag met een aanzienlijke hoeveelheid schijnbaar betrouwbaar verkeer. Ze omvatten UDP-flooden, versterking van stromen en andere vervalste pakket stromen. DDoS Protection Standard verkleint deze potentiële multi-Gigabyte-aanvallen door ze te absorberen en te reinigen, met de wereld wijde schaal van Azure automatisch.
- **Protocol aanvallen**: deze aanvallen genereren een doel dat niet toegankelijk is door een zwakte te misbruiken in de Layer 3-en Layer 4-protocol stack. Ze omvatten SYN-flood-aanvallen, reflectie-aanvallen en andere protocol aanvallen. DDoS Protection Standard verkleint deze aanvallen, onderscheidt zich van schadelijk en betrouwbaar verkeer door interactie met de client en het blok keren van schadelijk verkeer. 
- **Resource (Application) Layer-aanvallen**: deze aanvallen richten op webtoepassingen, om de overdracht van gegevens tussen hosts te verstoren. Ze omvatten HTTP-protocol schendingen, SQL-injectie, cross-site scripting en andere Layer 7-aanvallen. Gebruik een firewall voor webtoepassingen, zoals de Azure [Application Gateway Web Application firewall](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), en DDoS Protection standaard om bescherming tegen deze aanvallen te bieden. Er zijn ook Web Application Firewall aanbiedingen van derden beschikbaar in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

DDoS Protection Standard beveiligt bronnen in een virtueel netwerk, met inbegrip van open bare IP-adressen die zijn gekoppeld aan virtuele machines, load balancers en toepassings gateways. Wanneer de Application Gateway Web Application Firewall, of een Web Application Firewall van derden die in een virtueel netwerk met een openbaar IP-adres is geïmplementeerd, kan DDoS Protection Standard de mogelijkheid bieden om een volledige laag 3 te maken voor de beperking van laag 7.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een DDoS-beschermings plan](manage-ddos-protection.md).