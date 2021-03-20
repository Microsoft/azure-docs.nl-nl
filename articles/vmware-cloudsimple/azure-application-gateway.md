---
title: Azure-toepassing gateway gebruiken met virtuele VMware-machines
description: Hierin wordt beschreven hoe u de Azure Application Gateway gebruikt voor het beheren van inkomend webverkeer voor webservers die worden uitgevoerd in virtuele VMware-machines, de CloudSimple-Privécloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d14fdc4e2b2b1dfca8194077acc0453808611946
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897940"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Azure-toepassing gateway gebruiken met virtuele VMware-machines in de CloudSimple-Privécloud

U kunt de Azure-toepassing-gateway gebruiken voor het beheren van inkomend webverkeer voor uw webservers die worden uitgevoerd in virtuele VMware-machines in uw CloudSimple-Privécloud.

Door gebruik te maken van Azure-toepassing gateway in een open bare, persoonlijke hybride implementatie, kunt u webverkeer naar uw toepassingen beheren, een veilige front-end-en offloading van TLS-verwerking bieden voor de services die worden uitgevoerd in de VMware-omgeving. Azure-toepassing gateway routeert inkomend webverkeer naar back-end-pool instanties die zich in VMware-omgevingen bevinden op basis van geconfigureerde regels en status controles.

Voor deze Azure-toepassing Gateway-oplossing moet u het volgende doen:

* U dient een Azure-abonnement te hebben.
* Een virtueel Azure-netwerk en een subnet binnen het virtuele netwerk maken en configureren.
* Maak en configureer NSG regels en vergelijkt uw vNet met behulp van ExpressRoute voor uw CloudSimple-Privécloud.
* Maak & Configureer uw Privécloud.
* Maak & uw Azure-toepassing gateway te configureren.

## <a name="azure-application-gateway-deployment-scenario"></a>Implementatie scenario Azure-toepassing gateway

In dit scenario wordt de Azure-toepassing-gateway uitgevoerd in uw virtuele Azure-netwerk. Het virtuele netwerk is met uw Privécloud verbonden via een ExpressRoute-circuit. Alle subnetten in de Privécloud zijn IP bereikbaar via de subnetten van het virtuele netwerk.

![Azure-load balancer in het virtuele Azure-netwerk](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>De oplossing implementeren

Het implementatie proces bestaat uit de volgende taken:

1. [Controleren of aan de vereisten wordt voldaan](#1-verify-prerequisites)
2. [Verbinding maken tussen uw virtuele Azure-verbinding en de Privécloud](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Een Azure Application Gateway implementeren](#3-deploy-an-azure-application-gateway)
4. [Een VM-groep voor een webserver maken en configureren in uw Privécloud](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Controleer de vereisten

Controleer of aan deze vereisten wordt voldaan:

* Er is al een Azure Resource Manager en een virtueel netwerk gemaakt.
* Er is al een toegewezen subnet (voor Application Gateway) binnen uw virtuele Azure-netwerk gemaakt.
* Er is al een CloudSimple-Privécloud gemaakt.
* Er is geen IP-conflict tussen IP-subnetten in het virtuele netwerk en subnetten in de Privécloud.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Verbind uw virtuele Azure-netwerk met uw Privécloud

Volg dit proces om uw virtuele Azure-netwerk te verbinden met uw Privécloud.

1. [Kopieer de ExpressRoute-peering-informatie in de CloudSimple-Portal](virtual-network-connection.md).

2. [Configureer een virtuele netwerk gateway voor uw virtuele Azure-netwerk](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Koppel uw virtuele netwerk aan het CloudSimple ExpressRoute-circuit](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Gebruik de peering-informatie die u hebt gekopieerd om uw virtuele netwerk te koppelen aan het ExpressRoute-circuit](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. een Azure Application Gateway implementeren

De gedetailleerde instructies hiervoor zijn beschikbaar in [een toepassings gateway maken met op pad gebaseerde routerings regels met behulp van de Azure Portal](../application-gateway/create-url-route-portal.md). Hier volgt een overzicht van de vereiste stappen:

1. Maak een virtueel netwerk in uw abonnement en resource groep.
2. Maak een subnet (dat moet worden gebruikt als het toegewezen subnet) in het virtuele netwerk.
3. Een standaard Application Gateway maken (optioneel WAF inschakelen): Klik vanuit de Azure Portal-start pagina op **resource**  >  **netwerken**  >  **Application Gateway** in de linkerbovenhoek van het venster. Selecteer de standaard-SKU en-grootte en geef een Azure-abonnement, resource groep en locatie-informatie op. Als dat nodig is, maakt u een nieuw openbaar IP-adres voor deze toepassings gateway en geeft u informatie op over het virtuele netwerk en het toegewezen subnet voor de toepassings gateway.
4. Voeg een back-end-groep met virtuele machines toe en voeg deze toe aan uw toepassings gateway.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. een virtuele-machine groep voor een webserver maken en configureren in uw Privécloud

In vCenter maakt u Vm's met het besturings systeem en de webserver van uw keuze (zoals Windows/IIS of Linux/Apache). Kies een subnet/VLAN dat is ingesteld voor de weblaag in uw Privécloud. Controleer of ten minste één vNIC van de webserver-Vm's zich op het subnet van de weblaag bevindt.
