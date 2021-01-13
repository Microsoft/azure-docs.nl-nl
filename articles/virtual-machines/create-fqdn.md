---
title: FQDN maken voor een virtuele machine in de Azure Portal
description: Meer informatie over het maken van een FQDN-naam (Fully Qualified Domain Name) voor een virtuele machine in de Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132061"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Een Fully Qualified Domain Name maken in de Azure Portal voor een Linux-VM

Wanneer u een virtuele machine (VM) maakt in de [Azure Portal](https://portal.azure.com), wordt er automatisch een open bare IP-resource voor de virtuele machine gemaakt. U gebruikt dit IP-adres om op afstand toegang te krijgen tot de virtuele machine. Hoewel de portal geen [Fully Qualified Domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)of FQDN maakt, kunt u één keer toevoegen wanneer de virtuele machine is gemaakt. In dit artikel worden de stappen beschreven voor het maken van een DNS-naam of FQDN. 

## <a name="create-a-fqdn"></a>Een FQDN maken
In dit artikel wordt ervan uitgegaan dat u al een virtuele machine hebt gemaakt. Als dat nodig is, kunt u een virtuele [Linux](./linux/quick-create-portal.md) -of [Windows](./windows/quick-create-portal.md) -machine maken in de portal. Volg deze stappen als uw virtuele machine actief is:


1. Selecteer uw virtuele machine in de portal. 
1. Selecteer in het linkermenu **configuratie**
1. Onder **DNS-naam label** voert u het voor voegsel in dat u wilt gebruiken.
1. Selecteer **Opslaan** bovenaan de pagina.
1. Ga terug naar de Blade overzicht van de virtuele machine door **overzicht** te selecteren in het menu links. 
1. Controleer of de *DNS-naam* correct wordt weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

U kunt DNS ook beheren met [Azure DNS zones](../dns/dns-getstarted-portal.md).

