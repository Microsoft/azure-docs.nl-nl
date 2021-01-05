---
title: Virtuele Azure-netwerk verbinden met CloudSimple met behulp van ExpressRoute-Azure VMware-oplossing door CloudSimple
description: Hierin wordt beschreven hoe u peering-informatie kunt verkrijgen voor een verbinding tussen het virtuele Azure-netwerk en uw CloudSimple-omgeving
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3fedfbe55fd8ea3d2b4cc910df631e40bc74e210
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899062"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Virtueel Azure-netwerk verbinden met CloudSimple met behulp van ExpressRoute

U kunt uw particuliere cloud netwerk uitbreiden naar uw virtuele Azure-netwerk en Azure-resources. Met een ExpressRoute-verbinding kunt u vanuit uw Privécloud toegang krijgen tot resources die worden uitgevoerd in uw Azure-abonnement.

## <a name="request-authorization-key"></a>Aanvraag autorisatie sleutel

Een autorisatie sleutel is vereist voor de ExpressRoute-verbinding tussen uw Privécloud en het virtuele Azure-netwerk. Als u een sleutel wilt verkrijgen, moet u een ticket indienen met <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">ondersteuning</a>.  Gebruik de volgende informatie in de aanvraag:

* Probleem type: **technisch**
* Abonnement: **Selecteer het abonnement waarin de CloudSimple-service is geïmplementeerd**
* Service: **VMware-oplossing per CloudSimple**
* Probleem type: **service aanvraag**
* Subtype van het probleem: **autorisatie sleutel voor Azure VNET-verbinding**
* Subject: **aanvraag voor autorisatie sleutel voor Azure VNET-verbinding**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Informatie over peering ophalen van CloudSimple Portal

Als u de verbinding wilt instellen, moet u een verbinding tot stand brengen tussen een virtueel Azure-netwerk en uw CloudSimple-omgeving.  Als onderdeel van de procedure moet u de URI en autorisatie sleutel van het peer circuit opgeven. Haal de URI en autorisatie sleutel op uit de [CloudSimple-Portal](access-cloudsimple-portal.md).  Selecteer **netwerk** in het menu aan de zijkant en selecteer vervolgens **Azure-netwerk verbinding**. Of selecteer **account** in het menu aan de zijkant en selecteer vervolgens **Azure-netwerk verbinding**.

Kopieer de URI van het peer circuit en voor de autorisatie sleutel voor elk van de regio's met behulp van het *Kopieer* pictogram. Voor elke CloudSimple-regio die u wilt verbinden:

1. Klik op **kopiëren** om de URI te kopiëren. Plak deze in een bestand waar het kan worden toegevoegd aan de Azure Portal.  
2. Klik op **kopiëren** om de autorisatie sleutel te kopiëren en deze ook in het bestand te plakken.

Kopieer de autorisatie sleutel en de URI van het peer circuit met de status **beschikbaar** .  De **gebruikte** status geeft aan dat de sleutel al is gebruikt voor het maken van een virtuele netwerk verbinding.

![Virtual Network verbindings pagina](media/virtual-network-connection.png)

Zie [uw CloudSimple Private Cloud Environment verbinden met het virtuele Azure-netwerk met behulp van ExpressRoute](azure-expressroute-connection.md)voor meer informatie over het instellen van het virtuele netwerk van Azure op CloudSimple.

## <a name="next-steps"></a>Volgende stappen

* [Virtuele Azure-netwerk verbinding met Privécloud](azure-expressroute-connection.md)
* [Verbinding maken met een on-premises netwerk met behulp van Azure ExpressRoute](on-premises-connection.md)
