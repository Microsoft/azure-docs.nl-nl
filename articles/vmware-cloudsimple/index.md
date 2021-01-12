---
title: Azure VMware Solution by CloudSimple
description: Meer informatie over Azure VMware Solution by CloudSimple, inclusief een overzicht, quickstarts, concepten, zelfstudies en handleidingen.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.custom: seo-azure-migrate
keywords: vms-ondersteuning, azure vmware-oplossing door cloudsimple, cloudsimple azure, hulpprogramma's voor vms, vmware-documentatie
ms.openlocfilehash: a3514a011f940b82702984737d0a93c5da402b55
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108378"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple

Welkom bij de portal voor complete hulp met Azure VMware Solution by CloudSimple.
Op deze documentatiesite vindt u meer informatie over de volgende onderwerpen:

## <a name="overview"></a>Overzicht

Meer informatie over Azure VMware Solution by CloudSimple

* Meer informatie over de functies, voordelen en gebruiksscenario's in [Wat is de Azure VMware Solution by CloudSimple?](cloudsimple-vmware-solutions-overview.md)
* Meer informatie over de [belangrijkste concepten voor beheer](key-concepts.md)

## <a name="quickstart"></a>Snelstartgids

Meer informatie over hoe u aan de slag kunt met de oplossing

* Meer informatie over [het initialiseren van de service en het kopen van capaciteit](quickstart-create-cloudsimple-service.md)
* Meer informatie over het maken van een nieuwe VMware-omgeving in [Een privécloudomgeving configureren](quickstart-create-private-cloud.md)
* Meer informatie over hoe u het beheer van VMware en Azure kunt samenbrengen in het artikel [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)

## <a name="concepts"></a>Concepten

Meer informatie over de volgende concepten

* Een [CloudSimple-service](cloudsimple-service.md) (ook wel 'Azure VMware Solution by CloudSimple - service' genoemd). Deze resource moet eenmaal per regio worden gemaakt.
* Koop capaciteit voor uw omgeving door een of meer [CloudSimple-knooppuntresources](cloudsimple-node.md) te maken. Deze resources worden ook wel 'Azure VMware Solution by CloudSimple - node' genoemd.
* Initialiseer en configureer uw VMware-omgeving met behulp van [privéclouds](cloudsimple-private-cloud.md).
* Breng uw beheer samen met [Virtuele CloudSimple-machines](cloudsimple-virtual-machines.md) (ook wel 'Azure VMware Solution by CloudSimple - virtuele machine' genoemd).
* Ontwerp het onderliggende netwerk met behulp van [VLAN's/subnetten](cloudsimple-vlans-subnets.md).
* Segmenteer en beveilig uw onderliggende netwerk met behulp van de resource [Firewall-tabel](cloudsimple-firewall-tables.md).
* Krijg beveiligde toegang tot uw VMware-omgevingen via het WAN met behulp van [VPN-gateways](cloudsimple-vpn-gateways.md).
* Schakel openbare toegang voor workloads in met behulp van [openbare IP-adressen](cloudsimple-public-ip-address.md).
* Maak verbinding met virtuele Azure-netwerken en on-premises netwerken met behulp van een [Azure-netwerkverbinding.](cloudsimple-azure-network-connection.md)
* Configureer doelen voor waarschuwingse-mails met behulp van [Accountbeheer](cloudsimple-account.md).
* Bekijk logboeken van gebruikers-en systeemactiviteiten met behulp van weergaven van [Activiteitenbeheer](cloudsimple-activity.md).
* Krijg inzicht in de verschillende [VMware-onderdelen](vmware-components.md).

## <a name="tutorials"></a>Zelfstudies

Leer hoe u algemene taken kunt uitvoeren, zoals:

* [Een CloudSimple-service maken](create-cloudsimple-service.md), eenmaal per regio waar u VMware-omgevingen wilt implementeren.
* Kernfunctionaliteit van de service beheren in de [CloudSimple-portal](access-cloudsimple-portal.md).
* Capaciteit inschakelen en facturering optimaliseren voor uw infrastructuur door [CloudSimple-knooppunten te kopen](create-nodes.md).
* Configuraties voor VMware-omgevingen beheren met behulp van privéclouds. U kunt privéclouds [maken](create-private-cloud.md), [beheren](manage-private-cloud.md), [uitbreiden](expand-private-cloud.md) of [verkleinen](shrink-private-cloud.md).
* Gecombineerd beheer inschakelen door [Azure-abonnementen toe te wijzen](azure-subscription-mapping.md).
* Gebruikers- en systeemactiviteiten bewaken met behulp van de [activiteitenpagina's](monitor-activity.md).
* Netwerken voor uw omgevingen configureren door [subnetten te maken en beheren](create-vlan-subnet.md).
* Uw omgeving segmenteren en beveiligen met behulp van [firewall-tabellen en -regels](firewall.md).
* Binnenkomende internettoegang inschakelen voor workloads door [openbare IP-adressen toe te wijzen](public-ips.md).
* Connectiviteit van uw interne netwerken of clientwerkstations inschakelen door [een VPN in te stellen](vpn-gateway.md).
* Communicatie van uw [on-premises omgevingen](on-premises-connection.md) en [Azure Virtual Networks](virtual-network-connection.md) inschakelen.
* Waarschuwingsdoelen configureren en de totale aangeschafte capaciteit weergeven in de [accountsamenvatting](account.md)
* [Gebruikers](users.md) weergeven die de CloudSimple-portal hebben geopend.
* Virtuele VMware-machines beheren vanuit de Azure-portal:
    * [Virtuele machines maken](azure-create-vm.md) in de Azure-portal.
    * [Virtuele machines beheren](azure-manage-vm.md) die u hebt gemaakt.

## <a name="how-to-guides"></a>Handleidingen

Deze handleidingen beschrijven oplossingen voor doelstellingen, zoals:

* [Uw omgeving beveiligen](private-cloud-secure.md)
* Hulpprogramma's van derden installeren, aanvullende gebruikers inschakelen en externe verificatiebronnen gebruiken in vSphere met behulp van [escalatie van bevoegdheden](escalate-privileges.md).
* De toegang tot verschillende VMware-services configureren met behulp van [on-premises DNS](on-premises-dns-setup.md).
* Naam- en adrestoewijzing inschakelen voor uw workloads door [workload-DNS en -DHCP te configureren](dns-dhcp-setup.md).
* Inzicht krijgen in hoe de service de beveiliging en functionaliteit van uw platform waarborgt met behulp van [service-updates en -upgrades](vmware-components.md#updates-and-upgrades).
* TCO van back-ups verlagen door een voorbeeldarchitectuur voor back-ups te maken met behulp van [back-upsoftware van derden, zoals Veeam](backup-workloads-veeam.md).
* Een beveiligde omgeving maken door versleuteling in rust in te schakelen met behulp van [KMS-versleutelingssoftware van derden](vsan-encryption.md).
* Beheer van Azure Active Directory (Azure AD) uitbreiden naar VMware door de [Azure AD-identiteitsbron](azure-ad.md) te configureren.
