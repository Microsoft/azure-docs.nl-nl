---
title: Virtuele machines voorbereiden voor een FCI
description: Bereid uw virtuele Azure-machines voor om ze te gebruiken met een FCI (failover cluster instance) en SQL Server op Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 1b8d88167dac6b2d0b1ba2afc90c443fd80b9e46
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223156"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Virtuele machines voorbereiden voor een FCI (SQL Server op Azure-Vm's)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u virtuele machines van Azure (Vm's) voorbereidt voor gebruik met een SQL Server-failovercluster (FCI). De configuratie-instellingen variëren, afhankelijk van de FCI-opslag oplossing, dus controleer of u de juiste configuratie kiest voor uw omgeving en uw bedrijf. 

Zie voor meer informatie een overzicht van [FCI met SQL Server op Azure vm's](failover-cluster-instance-overview.md) en [Aanbevolen procedures voor clusters](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Vereisten 

- Een Microsoft Azure-abonnement. Ga [gratis](https://azure.microsoft.com/free/)aan de slag. 
- Een Windows-domein op virtuele machines van Azure of een on-premises Data Center dat is uitgebreid naar Azure met koppeling van virtueel netwerk.
- Een account met machtigingen voor het maken van objecten op virtuele machines van Azure en in Active Directory.
- Een virtueel Azure-netwerk en subnet met voldoende IP-adres ruimte voor deze onderdelen:
   - Zowel virtuele machines
   - Het IP-adres van het Windows-failovercluster
   - Een IP-adres voor elke FCI
- DNS geconfigureerd op het Azure-netwerk, die verwijst naar de domein controllers.

## <a name="choose-an-fci-storage-option"></a>Een FCI-opslag optie kiezen

De configuratie-instellingen voor uw virtuele machine variëren, afhankelijk van de opslag optie die u wilt gebruiken voor uw SQL Server failover-cluster exemplaar. Voordat u de virtuele machine voorbereidt, controleert u de [beschik bare FCI-opslag opties](failover-cluster-instance-overview.md#storage) en kiest u de optie die het beste past bij uw omgeving en uw bedrijfs behoeften. Selecteer vervolgens zorgvuldig de juiste VM-configuratie opties in dit artikel op basis van uw opslag selectie. 

## <a name="configure-vm-availability"></a>VM-Beschik baarheid configureren 

Voor de functie failover cluster moeten virtuele machines in een [beschikbaarheidsset](../../../virtual-machines/linux/tutorial-availability-sets.md) of een [beschikbaarheids zone](../../../availability-zones/az-overview.md#availability-zones)worden geplaatst. Als u beschikbaarheids sets kiest, kunt u [proximity-plaatsings groepen](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) gebruiken om de vm's dichter te vinden. In feite zijn proximity-plaatsings groepen een vereiste voor het gebruik van gedeelde Azure-schijven. 

Selecteer zorgvuldig de optie voor de beschik baarheid van de VM die overeenkomt met de gewenste cluster configuratie: 

- **Gedeelde Azure-schijven**: de beschikbaarheids optie is afhankelijk van het gebruik van Premium Ssd's of UltraDisk:
   - Premium-SSD: [beschikbaarheidsset](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) in verschillende fout-en update domeinen voor Premium-ssd's die in een [proximity-plaatsings groep](../../../virtual-machines/windows/proximity-placement-groups-portal.md)zijn geplaatst.
   - Ultra disk: [beschikbaarheids zone](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) , maar de virtuele machines moeten in dezelfde beschikbaarheids zone worden geplaatst, waardoor de beschik baarheid van het cluster tot 99,9% wordt verminderd. 
- **Premium-bestands shares**: [beschikbaarheidsset](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) of [beschikbaarheids zone](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Opslagruimten direct**: [beschikbaarheidsset](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> U kunt de beschikbaarheidsset niet instellen of wijzigen nadat u een virtuele machine hebt gemaakt.

## <a name="create-the-virtual-machines"></a>De virtuele machines maken

Nadat u de beschik baarheid van uw VM hebt geconfigureerd, kunt u uw virtuele machines maken. U kunt ervoor kiezen om een installatie kopie van Azure Marketplace te gebruiken waarop SQL Server al is geïnstalleerd. Als u echter een installatie kopie kiest voor SQL Server op Azure-Vm's, moet u SQL Server verwijderen van de virtuele machine voordat u het failover-cluster exemplaar configureert. 

### <a name="considerations"></a>Overwegingen

Voor een Azure VM-gast-failovercluster wordt een enkele NIC per server (cluster knooppunt) en één subnet aanbevolen. Een Azure-netwerk maakt gebruikt van fysieke redundantie, waardoor extra NIC's en subnetten overbodig zijn voor een gastcluster voor een Azure IaaS-VM. Hoewel het clustervalidatierapport een waarschuwing zal bevatten dat de knooppunten alleen bereikbaar zijn in één netwerk, kan deze waarschuwing zonder problemen worden genegeerd in het geval van failover-gastclusters voor een Azure IaaS-VM.

Plaats beide virtuele machines:

- Als u in dezelfde Azure-resource groep als uw beschikbaarheidsset gebruikmaakt van beschikbaarheids sets.
- Op hetzelfde virtuele netwerk als uw domein controller of op een virtueel netwerk dat een geschikte verbinding met uw domein controller heeft.
- Op een subnet met voldoende IP-adres ruimte voor zowel virtuele machines als alle Failoverclusterinstanties die u uiteindelijk kunt gebruiken op het cluster.
- In de Azure Availability set of beschikbaarheids zone.

U kunt een virtuele machine van Azure maken met behulp van een installatie kopie [met](sql-vm-create-portal-quickstart.md) of [zonder](../../../virtual-machines/windows/quick-create-portal.md) SQL Server vooraf geïnstalleerd. Als u de installatie kopie voor SQL Server kiest, moet u de SQL Server instantie hand matig verwijderen voordat u het failovercluster installeert. 


## <a name="uninstall-sql-server"></a>SQL Server verwijderen

Als onderdeel van het proces voor het maken van de FCI installeert u SQL Server als een geclusterd exemplaar van het failovercluster. *Als u een virtuele machine hebt geïmplementeerd met een installatie kopie van Azure Marketplace zonder SQL Server, kunt u deze stap overs Laan.* Als u een installatie kopie hebt geïmplementeerd met SQL Server vooraf geïnstalleerd, moet u de registratie van de SQL Server VM ongedaan maken vanuit de SQL IaaS agent-extensie en vervolgens SQL Server verwijderen. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Registratie bij de SQL IaaS agent-extensie ongedaan maken

SQL Server VM-installatie kopieën van Azure Marketplace worden automatisch geregistreerd met de SQL IaaS agent-extensie. Voordat u het vooraf geïnstalleerde SQL Server exemplaar verwijdert, moet u eerst de [registratie van elke SQL Server virtuele machine uit de SQL IaaS agent-extensie ongedaan](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)maken. 

### <a name="uninstall-sql-server"></a>SQL Server verwijderen

Nadat u de registratie van de extensie ongedaan hebt gemaakt, kunt u SQL Server verwijderen. Volg deze stappen op elke virtuele machine: 

1. Maak verbinding met de virtuele machine met behulp van RDP.

   Wanneer u voor het eerst verbinding maakt met een virtuele machine met behulp van RDP, wordt u gevraagd of u wilt toestaan dat de PC kan worden gedetecteerd in het netwerk. Selecteer **Ja**.

1. Als u een van de op SQL Server gebaseerde installatie kopieën voor virtuele machines gebruikt, verwijdert u het SQL Server-exemplaar:

   1. Klik in **Program ma's en onderdelen** met de rechter muisknop op **Microsoft SQL Server 201_ (64-bits)** en selecteer **verwijderen/wijzigen**.
   1. Selecteer **Verwijderen**.
   1. Selecteer het standaard exemplaar.
   1. Verwijder alle functies onder **Data Base Engine-Services**. Verwijder niets onder **gedeelde onderdelen**. U ziet iets als de volgende scherm afbeelding:

      ![Kenmerken selecteren](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Selecteer **volgende** en selecteer vervolgens **verwijderen**.
   1. Nadat het exemplaar is verwijderd, start u de virtuele machine opnieuw op. 

## <a name="open-the-firewall"></a>De firewall openen 

Open op elke virtuele machine de Windows Firewall TCP-poort die SQL Server gebruikt. Dit is standaard poort 1433. Maar u kunt de SQL Server poort wijzigen op een VM-implementatie van Azure, dus open de poort die SQL Server gebruikt in uw omgeving. Deze poort wordt automatisch geopend op SQL Server installatie kopieën die vanuit Azure Marketplace worden geïmplementeerd. 

Als u een [Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md)gebruikt, moet u ook de poort openen die door de Health probe wordt gebruikt. Dit is standaard poort 59999. Maar dit kan elke TCP-poort zijn die u opgeeft wanneer u de load balancer maakt. 

Deze tabel bevat informatie over de poorten die u mogelijk moet openen, afhankelijk van uw FCI-configuratie: 

   | Doel | Poort | Notities
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Normale poort voor standaard exemplaren van SQL Server. Als u een installatie kopie uit de galerie hebt gebruikt, wordt deze poort automatisch geopend. </br> </br> **Gebruikt door**: alle FCI-configuraties. |
   | Statustest | TCP 59999 | Een open TCP-poort. Configureer de load balancer [Health probe](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) en het cluster om deze poort te gebruiken. </br> </br> **Gebruikt door**: FCI met Load Balancer. |
   | Bestandsshare | UDP 445 | Poort die door de bestands share service wordt gebruikt. </br> </br> **Gebruikt door**: FCI met Premium-bestands share. |

## <a name="join-the-domain"></a>Aan domein toevoegen

U moet ook uw virtuele machines toevoegen aan het domein. U kunt dit doen met behulp van een Quick Start- [sjabloon](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Opslag configuratie controleren

Virtuele machines die zijn gemaakt op basis van Azure Marketplace, worden geleverd met gekoppelde opslag. Als u van plan bent uw FCI-opslag te configureren met behulp van Premium-bestands shares of gedeelde Azure-schijven, kunt u de gekoppelde opslag verwijderen om kosten op te slaan, omdat lokale opslag niet wordt gebruikt voor het failover-cluster exemplaar. Het is echter mogelijk om de gekoppelde opslag voor Opslagruimten Direct FCI-oplossingen te gebruiken, zodat u deze in dit geval mogelijk niet kunt verwijderen. Controleer uw FCI-opslag oplossing om te bepalen of het verwijderen van gekoppelde opslag optimaal is voor het besparen van kosten. 


## <a name="next-steps"></a>Volgende stappen

Nu u uw virtuele-machine omgeving hebt voor bereid, kunt u uw failover-cluster exemplaar configureren. 

Kies een van de volgende hand leidingen voor het configureren van de FCI-omgeving die geschikt is voor uw bedrijf: 
- [FCI configureren met gedeelde Azure-schijven](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [FCI configureren met een Premium-bestands share](failover-cluster-instance-premium-file-share-manually-configure.md)
- [FCI configureren met Opslagruimten Direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Zie voor meer informatie een overzicht van [FCI met SQL Server op Azure-vm's](failover-cluster-instance-overview.md) en [ondersteunde HADR-configuraties](hadr-cluster-best-practices.md). 

Zie voor meer informatie: 
- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instanties van een SQL Server-failovercluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
