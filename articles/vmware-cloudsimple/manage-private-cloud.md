---
title: Azure VMware-oplossing beheren via CloudSimple Private Cloud
description: Beschrijft de mogelijkheden die beschikbaar zijn voor het beheren van uw CloudSimple persoonlijke cloud resources en-activiteit
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f2f66c2e1e2e8aa596393d4c69a757138ab5a91
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895203"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Persoonlijke cloud resources en-activiteiten beheren

Persoonlijke Clouds worden beheerd vanuit de CloudSimple-Portal.  Controleer de status, beschik bare resources, activiteit op de privécloud en andere instellingen in de CloudSimple-Portal.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

Toegang tot de [CloudSimple-Portal](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>De lijst met persoonlijke Clouds weer geven

Op het tabblad **persoonlijke Clouds** op de pagina **resources** wordt een lijst weer gegeven met alle persoonlijke Clouds in uw abonnement. De informatie bevat de naam, het aantal vSphere-clusters, de locatie, de huidige status van de privécloud en de resource gegevens.

![Pagina Persoonlijke Cloud](media/manage-private-cloud.png)

Selecteer een Privécloud voor meer informatie en acties.

## <a name="private-cloud-summary"></a>Samen vatting van privécloud

Een uitgebreid overzicht van de geselecteerde Privécloud weer geven.  Overzichts pagina bevat de DNS-servers die zijn geïmplementeerd in de Privécloud.  U kunt DNS-door sturen van lokale DNS-servers naar uw particuliere cloud-DNS-servers instellen.  Voor meer informatie over DNS-door sturen raadpleegt [u DNS configureren voor naam omzetting voor Private Cloud vCenter vanuit on-premises](./on-premises-dns-setup.md).

![Samen vatting van privécloud](media/private-cloud-summary.png)

### <a name="available-actions"></a>Beschikbare acties

* [Start de vSphere-client](./vcenter-access.md). Toegang tot de vCenter voor deze Privécloud.
* [Inkoop knooppunten](create-nodes.md). Voeg knoop punten toe aan deze Privécloud.
* [Uitvouwen](expand-private-cloud.md). Voeg knoop punten toe aan deze Privécloud.
* **Vernieuwen**. De gegevens op deze pagina bijwerken.
* **Delete**. U kunt de Privécloud op elk gewenst moment verwijderen. **Voordat u verwijdert, moet u ervoor zorgen dat u een back-up hebt gemaakt van alle systemen en gegevens.** Wanneer u een Privécloud verwijdert, worden alle Vm's, de configuratie van de vCenter en de gegevens verwijderd. Klik op **verwijderen** in het gedeelte samen vatting van de geselecteerde privécloud. Na het verwijderen worden alle gegevens van de Privécloud gewist in een beveiligd, uiterst compatibel verwijderings proces.
* [Wijzig de vSphere-bevoegdheden](escalate-private-cloud-privileges.md).  Escaleren uw bevoegdheden op deze Privécloud.

## <a name="private-cloud-vlanssubnets"></a>Particuliere cloud-VLAN'S/-subnetten

Bekijk de lijst met gedefinieerde VLAN'S/subnetten voor de geselecteerde Privécloud.  De lijst bevat de beheer-VLAN'S/-subnetten die zijn gemaakt tijdens het maken van de privécloud.

![Privécloud-VLAN'S/subnetten](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Beschikbare acties

* [Vlan's/subnetten toevoegen](./create-vlan-subnet.md). Voeg een VLAN/subset toe aan deze Privécloud.

Een VLAN/subnet selecteren voor de volgende acties
* Een [firewall tabel toevoegen](./firewall.md). Een firewall tabel koppelen aan deze Privécloud.
* **Bewerken**
* **Verwijderen** (alleen door de gebruiker gedefinieerde Vlan's/subnetten)

## <a name="private-cloud-activity"></a>Activiteit privécloud

Bekijk de volgende informatie voor de geselecteerde Privécloud.  De gegevens van de activiteit zijn een gefilterde lijst met alle activiteiten voor de geselecteerde Privécloud.  Op deze pagina worden Maxi maal 25 recente activiteiten weer gegeven.

* Recente waarschuwingen
* Recente gebeurtenissen
* Recente taken
* Recente controle

![Privécloud-activiteit](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Cloud racks

Cloud racks zijn de bouw stenen van uw Privécloud. Elk rek biedt een capaciteits eenheid. CloudSimple configureert automatisch Cloud racks op basis van uw selecties bij het maken of uitbreiden van een Privécloud.  Bekijk de volledige lijst met Cloud racks, met inbegrip van de privécloud die aan hen is toegewezen.

![Particuliere cloud-Cloud racks](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere-beheernetwerk

Een lijst met VMware-beheer bronnen en virtuele machines die momenteel zijn geconfigureerd in de Privécloud. De informatie omvat de software versie, de Fully Qualified Domain Name (FQDN) en het IP-adres van de bronnen.

![Private Cloud-vSphere Management-netwerk](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [persoonlijke Clouds](cloudsimple-private-cloud.md)
