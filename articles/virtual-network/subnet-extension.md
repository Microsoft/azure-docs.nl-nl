---
title: Subnetextensie in Azure | Microsoft Docs
description: Meer informatie over subnetextensie in Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: e39859e3cc4d28ac4b1456fcdaec65ecfb9c7f31
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728208"
---
# <a name="subnet-extension"></a>Subnetextensie
Workloadmigratie naar de openbare cloud vereist een zorgvuldige planning en coördinatie. Een van de belangrijkste overwegingen is de mogelijkheid om uw IP-adressen te behouden. Dit kan belangrijk zijn met name als uw toepassingen afhankelijk zijn van IP-adressen of als u nalevingsvereisten hebt voor het gebruik van specifieke IP-adressen. Azure Virtual Network dit probleem voor u oplossen doordat u VNets en subnetten kunt maken met behulp van een IP-adresbereik van uw keuze.

Migraties kunnen lastig worden wanneer de bovenstaande vereiste is gekoppeld aan een extra vereiste om sommige toepassingen on-premises te houden. In een dergelijke situatie moet u de toepassingen splitsen tussen Azure en on-premises, zonder de IP-adressen aan beide zijden opnieuw te nummeren. Daarnaast moet u toestaan dat de toepassingen communiceren alsof ze zich in hetzelfde netwerk.

Een oplossing voor het bovenstaande probleem is subnetextensie. Door een netwerk uit te breiden, kunnen toepassingen over hetzelfde broadcast-domein praten wanneer ze op verschillende fysieke locaties bestaan, waardoor het niet meer nodig is om uw netwerktopologie opnieuw te bouwen. 

Hoewel het uitbreiden van uw netwerk in het algemeen geen goede gewoonte is, kunnen onderstaande gebruiksgevallen dit noodzakelijk maken.

- **Gefaseerd migreren:** het meest voorkomende scenario is dat u de migratie wilt fasen. U wilt eerst enkele toepassingen en na een periode de rest van de toepassingen migreren naar Azure.
- **Latentie:** lage latentievereisten kunnen een andere reden zijn voor u om sommige toepassingen on-premises te houden om ervoor te zorgen dat ze zich zo dicht mogelijk bij uw datacenter bevinden.
- **Naleving:** een andere use-case is dat u mogelijk nalevingsvereisten hebt om sommige van uw toepassingen on-premises te houden.
 
> [!NOTE] 
> U moet uw subnetten alleen uitbreiden als dat nodig is. In de gevallen waarin u uw subnetten uitbreidt, moet u proberen er een tussenliggende stap van te maken. Na een periode moet u proberen toepassingen opnieuw te nummeren in uw on-premises netwerk en deze te migreren naar Azure.

In de volgende sectie wordt besproken hoe u uw subnetten kunt uitbreiden naar Azure.


## <a name="extend-your-subnet-to-azure"></a>Uw subnet uitbreiden naar Azure
 U kunt uw on-premises subnetten uitbreiden naar Azure met behulp van een laag-3-overlaynetwerkoplossing. De meeste oplossingen gebruiken een overlaytechnologie zoals VXLAN om het laag-2-netwerk uit te breiden met behulp van een laag-3-overlaynetwerk. In het onderstaande diagram ziet u een ge generaliseerde oplossing. In deze oplossing bestaat hetzelfde subnet aan beide zijden, Azure en on-premises. 

![Voorbeeld van subnetextensie](./media/subnet-extension/subnet-extension.png)

De IP-adressen van het subnet worden toegewezen aan VM's in Azure en on-premises. Zowel Azure als on-premises hebben een NVA ingevoegd in hun netwerken. Wanneer een VM in Azure probeert te praten met een VM in een on-premises netwerk, legt de Azure NVA het pakket vast, kapselt het in en verzendt het via VPN/Express Route naar het on-premises netwerk. Het pakket wordt door de on-premises NVA ontvangen, ontkapseld en doorgestuurd naar de beoogde ontvanger in het netwerk. Het retourverkeer maakt gebruik van een vergelijkbaar pad en dezelfde logica.

In het bovenstaande voorbeeld communiceren de Azure NVA en de on-premises NVA en worden ip-adressen achter elkaar beschreven. Complexere netwerken kunnen ook een toewijzingsservice hebben, die de toewijzing tussen de NPO's en de IP-adressen erachter onderhoudt. Wanneer een NVA een pakket ontvangt, vraagt deze de toewijzingsservice om het adres van de NVA met het doel-IP-adres erachter te vinden.

In de volgende sectie vindt u meer informatie over subnetextensieoplossingen die we in Azure hebben getest.

## <a name="next-steps"></a>Volgende stappen 
[Breid uw on-premises subnetten uit naar Azure met behulp van Azure Extended Network.](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-extended-network)