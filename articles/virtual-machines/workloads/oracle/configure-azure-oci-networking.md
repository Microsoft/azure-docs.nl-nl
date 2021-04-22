---
title: Verbinding Azure ExpressRoute met Oracle Cloud Infrastructure | Microsoft Docs
description: Verbinding Azure ExpressRoute Oracle Cloud Infrastructure (OCI) FastConnect om Oracle-toepassingsoplossingen in de cloud mogelijk te maken
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/16/2020
ms.author: rogardle
ms.openlocfilehash: 06f0eb1ef36e711c5297af936d704f442596fc1a
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887572"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Een directe verbinding tussen Azure en Oracle Cloud Infrastructure instellen  

Voor het maken van een [geïntegreerde multi-cloudervaring](oracle-oci-overview.md)bieden Microsoft en Oracle directe interconnectie tussen Azure en Oracle Cloud Infrastructure (OCI) via [ExpressRoute](../../../expressroute/expressroute-introduction.md) en [FastConnect.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) Via de ExpressRoute- en FastConnect-interconnectie kunnen klanten een lage latentie, hoge doorvoer en directe privéverbinding tussen de twee clouds ervaren.

> [!IMPORTANT]
> Oracle zal deze toepassingen certificeren om in Azure te worden uitgevoerd wanneer ze in mei 2020 gebruikmaken van de Azure-/Oracle Cloud-interconnect-oplossing.
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * Peoplesoft
> * Oracle Retail-toepassingen
> * Oracle Hyperion Financial Management

In de volgende afbeelding ziet u een overzicht op hoog niveau van de onderlinge verbinding:

![Netwerkverbinding tussen de cloud](https://user-images.githubusercontent.com/37556655/115093592-bced0180-9ecf-11eb-976d-9d4c7a1be2a8.png)

## <a name="prerequisites"></a>Vereisten

* Als u verbinding wilt maken tussen Azure en OCI, moet u een actief Azure-abonnement en een actieve OCI-tenancy hebben.

* Connectiviteit is alleen mogelijk wanneer een Azure ExpressRoute peeringlocatie zich in de nabijheid van of op dezelfde peeringlocatie bevindt als de OCI FastConnect. Zie [Beschikbaarheid in regio's.](oracle-oci-overview.md#region-availability)

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Directe connectiviteit tussen ExpressRoute en FastConnect configureren

1. Maak een standaard ExpressRoute-circuit in uw Azure-abonnement onder een resourcegroep. 
    * Kies tijdens het maken van de ExpressRoute **Oracle Cloud FastConnect** als de serviceprovider. Zie de stapsgewijse handleiding voor het maken van een [ExpressRoute-circuit.](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)
    * Een Azure ExpressRoute-circuit biedt gedetailleerde bandbreedteopties, terwijl FastConnect ondersteuning biedt voor 1, 2, 5 of 10 Gbps. Daarom is het raadzaam om een van deze overeenkomende bandbreedteopties te kiezen onder ExpressRoute.

    ![ExpressRoute-circuit maken](media/configure-azure-oci-networking/exr-create-new.png)
1. Noteer uw **ExpressRoute-servicesleutel.** U moet de sleutel verstrekken tijdens het configureren van uw FastConnect-circuit.

    ![ExpressRoute-servicesleutel](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Er worden kosten in rekening gebracht voor ExpressRoute zodra het ExpressRoute-circuit is ingericht (zelfs als de **providerstatus** **Niet ingericht is).**

1. Carve out twee privé IP-adresruimten van /30 elk die niet overlappen met uw virtuele Azure-netwerk of OCI virtueel cloudnetwerk IP-adresruimte. We verwijzen naar de eerste IP-adresruimte als primaire adresruimte en de tweede IP-adresruimte als secundaire adresruimte. Noteer de adressen die u nodig hebt bij het configureren van uw FastConnect-circuit.
1. Maak een dynamische routeringsgateway (DRG). U hebt deze nodig bij het maken van uw FastConnect-circuit. Zie de documentatie over gateway voor dynamische [routering voor meer](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) informatie.
1. Maak een FastConnect-circuit onder uw Oracle-tenant. Zie de Oracle-documentatie voor [meer informatie.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)
  
    * Selecteer onder FastConnect-configuratie **Microsoft Azure: ExpressRoute** als provider.
    * Selecteer de gateway voor dynamische routering die u in de vorige stap hebt ingericht.
    * Selecteer de bandbreedte die moet worden ingericht. Voor optimale prestaties moet de bandbreedte overeenkomen met de bandbreedte die is geselecteerd bij het maken van het ExpressRoute-circuit.
    * Plak **in Provider Service Key** de ExpressRoute-servicesleutel.
    * Gebruik de eerste /30 privé-IP-adresruimte die in een vorige stap is gebruikt voor het primaire **BGP-IP-adres** en de tweede /30 privé-IP-adresruimte voor het secundaire **BGP-IP-adres.**
        * Wijs het eerste gebruiksbare adres van de twee adresbereiken voor het IP-adres van Oracle BGP (primair en secundair) en het tweede adres toe aan het BGP-IP-adres van de klant (vanuit het perspectief van FastConnect). Het eerste gebruiksbare IP-adres is het tweede IP-adres in de /30-adresruimte (het eerste IP-adres is gereserveerd door Microsoft).
    * Klik op **Create**.
1. Voltooi het koppelen van FastConnect aan het virtuele cloudnetwerk onder uw Oracle-tenant via dynamische routeringsgateway met behulp van Routetabel.
1. Navigeer naar Azure en zorg ervoor dat de  **providerstatus** voor uw ExpressRoute-circuit is gewijzigd in Ingericht en dat een peering van het type **Azure-privé** is ingericht. Dit is een vereiste voor de volgende stappen.

    ![Status expressRoute-provider](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klik op persoonlijke **Azure-peering.** U ziet dat de peeringgegevens automatisch zijn geconfigureerd op basis van de gegevens die u hebt ingevoerd bij het instellen van uw FastConnect-circuit.

    ![Instellingen voor persoonlijke peering](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Virtueel netwerk verbinden met ExpressRoute

1. Maak een virtueel netwerk en een virtuele netwerkgateway als u dat nog niet hebt gedaan. Zie de stapsgewijse handleiding [voor meer informatie.](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
1. Stel de verbinding tussen de virtuele netwerkgateway en uw ExpressRoute-circuit in door het [Terraform-script](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) uit te voeren of door de PowerShell-opdracht uit te voeren om [ExpressRoute FastPath te configureren.](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)

Nadat u de netwerkconfiguratie hebt voltooid, kunt u de geldigheid van uw configuratie controleren door te klikken op **ARP-records en** **routetabel** op halen onder de blade Persoonlijke ExpressRoute-peering in de Azure Portal.

## <a name="automation"></a>Automation

Microsoft heeft Terraform-scripts gemaakt om geautomatiseerde implementatie van de netwerkverbinding mogelijk te maken. De Terraform-scripts moeten worden geverifieerd bij Azure voordat ze worden uitgevoerd, omdat ze voldoende machtigingen nodig hebben voor het Azure-abonnement. Verificatie kan worden uitgevoerd met behulp van [een Azure Active Directory service-principal](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) of met behulp van de Azure CLI. Zie de [Terraform-documentatie voor meer informatie.](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)

De Terraform-scripts en verwante documentatie voor het implementeren van de inter-connect vindt u in deze [GitHub-opslagplaats.](https://aka.ms/azureociinterconnecttf)

## <a name="monitoring"></a>Bewaking

Als u agents in beide clouds installeert, kunt u Azure [Netwerkprestatiemeter (NPM)](../../../expressroute/how-to-npm.md) gebruiken om de prestaties van het end-to-end-netwerk te bewaken. Met NPM kunt u gemakkelijk netwerkproblemen identificeren en deze elimineren.

## <a name="delete-the-interconnect-link"></a>De koppeling tussen verbindingen verwijderen

Als u de verbinding wilt verwijderen, moeten de volgende stappen worden gevolgd in de opgegeven volgorde. Als u dit niet doet, resulteert dit in een ExpressRoute-circuit met de status Mislukt.

1. Verwijder de ExpressRoute-verbinding. Verwijder de verbinding door te klikken op het **pictogram Verwijderen** op de pagina voor uw verbinding. Zie de [ExpressRoute-documentatie voor meer informatie.](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources)
1. Verwijder Oracle FastConnect uit de Oracle Cloud-console.
1. Zodra het Oracle FastConnect-circuit is verwijderd, kunt u het Azure ExpressRoute verwijderen.

Op dit moment is het verwijderen en deprovisioning voltooid.

## <a name="next-steps"></a>Volgende stappen

* Zie de Oracle-documentatie voor meer informatie over de cloudverbinding tussen OCI [en Azure.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)
* Gebruik [Terraform-scripts om](https://aka.ms/azureociinterconnecttf) infrastructuur te implementeren voor gerichte Oracle-toepassingen via Azure en de netwerkverbinding te configureren. 
