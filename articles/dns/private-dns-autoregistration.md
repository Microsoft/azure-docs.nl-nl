---
title: Wat is de functie voor het autoregistreren van Azure DNS privé zones
description: Overzicht van de functie voor het registreren van Azure DNS persoonlijke zones
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 2e5f41c0e149c99b5524c439c59e72afe554c776
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783894"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Wat is de functie voor het autoregistreren van Azure DNS privé zones

De Azure DNS functie voor automatische registratie van particuliere zones neemt de knel punt in het beheer van DNS-records voor virtuele machines die zijn geïmplementeerd in een virtueel netwerk. Wanneer u [een virtueel netwerk koppelt](./private-dns-virtual-network-links.md) aan een privé-DNS-zone en automatische registratie inschakelt voor alle virtuele machines, worden de DNS-records voor de virtuele machines die zijn geïmplementeerd in het virtuele netwerk automatisch gemaakt in de privé-DNS-zone. Naast het door sturen van records (A-records) worden er ook automatisch reverse lookup-records (PTR-records) gemaakt voor de virtuele machines.
Als u meer virtuele machines aan het virtuele netwerk toevoegt, worden ook de DNS-records voor deze virtuele machines automatisch in de gekoppelde persoonlijke DNS-zone gemaakt.

Wanneer u een virtuele machine verwijdert, worden de DNS-records voor de virtuele machine automatisch verwijderd uit de privé-DNS-zone.

U kunt auto registratie inschakelen door de optie automatische registratie inschakelen te selecteren tijdens het maken van een virtuele netwerk koppeling.

![Automatische registratie inschakelen](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Beperkingen

* De functie voor het registreren van gegevens werkt alleen voor virtuele machines. Voor alle andere resources, zoals interne load balancers enz., kunt u DNS-records hand matig maken in de privé-DNS-zone die aan het virtuele netwerk is gekoppeld.
* DNS-records worden alleen automatisch gemaakt voor de NIC van de primaire virtuele machine. Als uw virtuele machines meer dan één NIC hebben, kunt u hand matig de DNS-records maken voor andere netwerk interfaces.
* DNS-records worden alleen automatisch gemaakt als de NIC van de primaire virtuele machine DHCP gebruikt. Als er statische IP-adressen zijn geconfigureerd (bijvoorbeeld om [meerdere IP-adressen in azure](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config)te gebruiken), maakt automatische registratie geen records voor die virtuele machine.
* De registratie van IPv6 (AAAA-records) wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privé zone in Azure DNS met behulp van [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure cli](./private-dns-getstarted-cli.md).

* Lees meer over een aantal veelvoorkomende [scenario's voor privézones](./private-dns-scenarios.md) die u kunt realiseren met privézones in Azure DNS.

* Zie [Veelgestelde vragen over Privé-DNS](./dns-faq-private.md) voor veelgestelde vragen en antwoorden over privézones in Azure DNS, waaronder specifiek gedrag dat u kunt verwachten voor bepaalde soorten bewerkingen.
