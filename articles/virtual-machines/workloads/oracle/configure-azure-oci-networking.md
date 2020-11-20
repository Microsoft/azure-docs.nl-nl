---
title: Verbinding maken met Azure ExpressRoute met Oracle Cloud Infrastructure | Microsoft Docs
description: Verbinding maken met Azure ExpressRoute met de FastConnect van Oracle Cloud Infrastructure (OCI) om cross-Cloud Oracle-toepassings oplossingen in te scha kelen
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 03/16/2020
ms.author: rogardle
ms.reviewer: cynthn
ms.openlocfilehash: 2717ba307cac82465e0c5df996ef3b668af5e7d4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963705"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Een directe interverbinding tussen Azure en Oracle Cloud Infrastructure instellen  

Voor het maken van een [geïntegreerde multi-Cloud-ervaring](oracle-oci-overview.md)bieden micro soft en Oracle direct interconnectie tussen Azure en Oracle Cloud Infrastructure (OCI) via [ExpressRoute](../../../expressroute/expressroute-introduction.md) en [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Via de ExpressRoute-en FastConnect-verbinding kunnen klanten met een lage latentie, een hoge door Voer en een particuliere directe connectiviteit tussen de twee Clouds profiteren.

> [!IMPORTANT]
> Oracle zal deze toepassingen certificeren om uit te voeren in azure wanneer de Azure/Oracle-Cloud Interconnect-oplossing wordt gebruikt door mei 2020.
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * People
> * Retail toepassingen van Oracle
> * Financieel beheer van Oracle Hyperion

De volgende afbeelding toont een overzicht op hoog niveau van de interverbinding:

![Netwerk verbinding tussen de Cloud](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Vereisten

* Als u verbinding wilt maken tussen Azure en OCI, hebt u een actief Azure-abonnement en een actieve OCI pacht nodig.

* Connectiviteit is alleen mogelijk wanneer een Azure ExpressRoute-peering locatie zich in de buurt van of op dezelfde peering-locatie bevindt als de OCI FastConnect. Zie de [Beschik baarheid van regio's](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Directe connectiviteit tussen ExpressRoute en FastConnect configureren

1. Maak een standaard ExpressRoute-circuit op uw Azure-abonnement onder een resource groep. 
    * Kies bij het maken van de ExpressRoute **Oracle Cloud FastConnect** als service provider. Zie de [Stapsgewijze hand leiding](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)voor het maken van een ExpressRoute-circuit.
    * Een Azure ExpressRoute-circuit biedt gedetailleerde bandbreedte opties, terwijl FastConnect 1, 2, 5 of 10 Gbps ondersteunt. Daarom is het raadzaam een van deze overeenkomende bandbreedte opties te kiezen onder ExpressRoute.

    ![ExpressRoute-circuit maken](media/configure-azure-oci-networking/exr-create-new.png)
1. Noteer uw ExpressRoute- **Service sleutel**. U moet de sleutel opgeven tijdens het configureren van uw FastConnect-circuit.

    ![ExpressRoute-service sleutel](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Er worden kosten in rekening gebracht voor ExpressRoute zodra het ExpressRoute-circuit is ingericht (zelfs als de status van de **provider** **niet is ingericht**).

1. Reserveren twee privé-IP-adres ruimten van/30 die elkaar niet overlappen met uw virtuele Azure-netwerk of de IP-adres ruimte van het virtuele Cloud netwerk. We verwijzen naar de eerste IP-adres ruimte als primaire adres ruimte en de tweede IP-adres ruimte als secundaire adres ruimte. Noteer de adressen die u nodig hebt bij het configureren van uw FastConnect-circuit.
1. Maak een dynamische routerings gateway (DRG). U hebt dit nodig bij het maken van uw FastConnect-circuit. Zie de documentatie voor [dynamische routerings gateway](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) voor meer informatie.
1. Maak een FastConnect-circuit onder uw Oracle-Tenant. Raadpleeg de [Oracle-documentatie](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)voor meer informatie.
  
    * Onder FastConnect-configuratie selecteert u **Microsoft Azure: ExpressRoute** als provider.
    * Selecteer de dynamische routerings gateway die u in de vorige stap hebt ingericht.
    * Selecteer de band breedte die moet worden ingericht. Voor optimale prestaties moet de band breedte overeenkomen met de band breedte die is geselecteerd bij het maken van het ExpressRoute-circuit.
    * Plak de ExpressRoute-service sleutel in **Provider service sleutel**.
    * Gebruik de eerste/30 persoonlijke IP-adres ruimte gehaald uit in een vorige stap voor het **primaire BGP IP-adres** en de tweede/30 privé IP-adres ruimte voor het **secundaire BGP IP-** adres.
        * Wijs het eerste bebruikbaar adres van de twee bereiken voor het IP-adres van de Oracle BGP (primair en secundair) en het tweede adres toe aan het BGP-IP-adres van de klant (vanuit een FastConnect-perspectief). Het eerste bebruikbaarde IP-adres is het tweede IP-adres in de/30-adres ruimte (het eerste IP-adres is gereserveerd door micro soft).
    * Klik op **Create**.
1. Voltooi het koppelen van de FastConnect aan het virtuele Cloud netwerk onder uw Oracle-Tenant via de dynamische routerings gateway via de route tabel.
1. Ga naar Azure en zorg ervoor dat de **provider status** voor uw ExpressRoute-circuit is gewijzigd in **ingericht** en dat een peering van het type **Azure private** is ingericht. Dit is een vereiste voor de volgende stappen.

    ![Status van ExpressRoute-provider](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klik op **persoonlijke Azure** -peering. De peering-Details worden automatisch geconfigureerd op basis van de informatie die u hebt ingevoerd bij het instellen van uw FastConnect-circuit.

    ![Instellingen voor privé-peering](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Virtueel netwerk verbinden met ExpressRoute

1. Maak een virtueel netwerk en een virtuele netwerk gateway, als u dat nog niet hebt gedaan. Zie de [Stapsgewijze hand leiding](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)voor meer informatie.
1. Stel de verbinding tussen de gateway van het virtuele netwerk en het ExpressRoute-circuit in door het [terraform-script](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) uit te voeren of door de Power shell-opdracht uit te voeren om [ExpressRoute FastPath te configureren](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Nadat u de netwerk configuratie hebt voltooid, kunt u de geldigheid van uw configuratie controleren door te klikken op **ARP-records ophalen** en **route tabel ophalen** onder de Blade ExpressRoute persoonlijke peering in de Azure Portal.

## <a name="automation"></a>Automation

Micro soft heeft terraform-scripts gemaakt om de automatische implementatie van de netwerk verbinding in te scha kelen. De terraform-scripts moeten bij Azure worden geverifieerd voordat ze kunnen worden uitgevoerd, omdat ze voldoende machtigingen nodig hebben voor het Azure-abonnement. Verificatie kan worden uitgevoerd met behulp van een [Azure Active Directory Service-Principal](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) of met behulp van de Azure cli. Zie de [terraform-documentatie](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)voor meer informatie.

De terraform-scripts en gerelateerde documentatie voor het implementeren van de Inter-Connect vindt u in deze [github-opslag plaats](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Bewaking

Als u agents op beide Clouds installeert, kunt u gebruikmaken van Azure [Netwerkprestatiemeter (NPM)](../../../expressroute/how-to-npm.md) om de prestaties van het end-to-end netwerk te bewaken. NPM helpt u bij het eenvoudig identificeren van netwerk problemen en helpt ze te elimineren.

## <a name="delete-the-interconnect-link"></a>De Interconnect-koppeling verwijderen

Als u de Interconnect wilt verwijderen, moeten de volgende stappen worden gevolgd in de opgegeven volg orde. Als u dit niet doet, wordt het ExpressRoute-circuit ' failed state ' geretourneerd.

1. Verwijder de ExpressRoute-verbinding. Als u de verbinding wilt verwijderen, klikt u op het pictogram **verwijderen** op de pagina voor uw verbinding. Zie de [ExpressRoute-documentatie](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources)voor meer informatie.
1. Verwijder de Oracle-FastConnect uit de Oracle-Cloud console.
1. Zodra het Oracle FastConnect-circuit is verwijderd, kunt u het Azure ExpressRoute-circuit verwijderen.

Op dit moment is het verwijderen en het ongedaan maken van de inrichting voltooid.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Oracle-documentatie](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)voor meer informatie over de cross-Cloud verbinding tussen OCI en Azure.
* Gebruik [terraform-scripts](https://aka.ms/azureociinterconnecttf) om de infra structuur te implementeren voor de doel groep Oracle-toepassingen via Azure en configureer de netwerk verbinding. 
