---
title: Azure NetApp Files met Azure VMware Solution
description: Gebruik Azure NetApp Files virtuele Azure VMware Solution's om gegevens te migreren en te synchroniseren tussen on-premises servers, Azure VMware Solution-VM's en cloudinfrastructuren.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 09b9ba2ff6b95e12558b1ac49e401ce6dede4942
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870981"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files met Azure VMware Solution

In dit artikel worden de stappen beschreven voor het integreren Azure NetApp Files met Azure VMware Solution workloads op basis van uw bedrijf. Het gastbesturingssysteem wordt uitgevoerd op virtuele machines (VM's) die toegang hebben tot Azure NetApp Files volumes. 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files overzicht

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) is een Azure-service voor migratie en het uitvoeren van de meest veeleisende bestandsworkloads in de cloud: databases, SAP en high performance computing-toepassingen, zonder codewijzigingen.

### <a name="features"></a>Functies
(Services waar Azure NetApp Files worden gebruikt.)

- **Active Directory-verbindingen:** Azure NetApp Files ondersteunt [Active Directory Domain Services en Azure Active Directory Domain Services.](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use)

- **ShareProtocol:** Azure NetApp Files ondersteunt Server Message Block protocollen (SMB) en Network File System (NFS). Deze ondersteuning betekent dat de volumes kunnen worden bevestigd op de Linux-client en kunnen worden kaarten op de Windows-client.

- **Azure VMware Solution:** Azure NetApp Files-shares kunnen worden bevestigd vanaf VM's die zijn gemaakt in de Azure VMware Solution omgeving.

Azure NetApp Files is beschikbaar in veel Azure-regio's en ondersteunt replicatie tussen regio's. Zie Storage hierarchy of Azure NetApp Files Azure NetApp Files (Opslaghiërarchie van de Azure NetApp Files) voor meer informatie over Azure NetApp Files [configuratiemethoden.](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)

## <a name="reference-architecture"></a>Referentiearchitectuur

Het volgende diagram illustreert een verbinding via Azure ExpressRoute met een Azure VMware Solution privécloud. De Azure VMware Solution heeft toegang tot de Azure NetApp Files share die is Azure VMware Solution VM's.

![Diagram met NetApp Files voor Azure VMware Solution architectuur.](media/net-app-files/net-app-files-topology.png)

In dit artikel worden instructies beschreven voor het instellen, testen en controleren van het volume Azure NetApp Files als een bestands share voor Azure VMware Solution VM's. In dit scenario hebben we het NFS-protocol gebruikt. Azure NetApp Files en Azure VMware Solution worden gemaakt in dezelfde Azure-regio.

## <a name="prerequisites"></a>Vereisten 

> [!div class="checklist"]
> * Azure-abonnement met Azure NetApp Files ingeschakeld
> * Subnet voor Azure NetApp Files
> * Linux-VM op Azure VMware Solution
> * Windows-VM's op Azure VMware Solution

## <a name="regions-supported"></a>Ondersteunde regio's

Een lijst met ondersteunde regio's vindt u op [Azure-producten per regio.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)

## <a name="verify-pre-configured-azure-netapp-files"></a>Vooraf geconfigureerde Azure NetApp Files 

Volg de stapsgewijse instructies in de volgende artikelen om volumes te maken en Azure NetApp Files te Azure VMware Solution VM's.

- [Een NetApp-account maken](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Een capaciteitspool instellen](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Een SMB-volume maken voor Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Een NFS-volume maken voor Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Een subnet delegeren aan Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

De volgende stappen omvatten verificatie van de vooraf geconfigureerde Azure NetApp Files gemaakt in Azure op Azure NetApp Files Premium-serviceniveau.

1. Selecteer in Azure Portal onder **OPSLAG** de **optie Azure NetApp Files.** Er wordt een lijst met de geconfigureerde Azure NetApp Files weergegeven. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Schermopname van de lijst met vooraf geconfigureerde Azure NetApp Files."::: 

2. Selecteer een geconfigureerd NetApp Files-account om de instellingen te bekijken. Selecteer bijvoorbeeld **Contoso-anf2.** 

3. Selecteer **Capaciteitspools** om de geconfigureerde pool te controleren. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Schermopname met opties voor het weergeven van capaciteitspools en volumes van een geconfigureerd NetApp Files-account.":::

    De pagina Capaciteitspools wordt geopend met de capaciteit en het serviceniveau. In dit voorbeeld is de opslaggroep geconfigureerd als 4 TiB met een Premium-serviceniveau.

4. Selecteer **Volumes om** volumes weer te zien die zijn gemaakt onder de capaciteitspool. (Zie de voorgaande schermopname.)

5. Selecteer een volume om de configuratie ervan weer te geven.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Schermopname van volumes die zijn gemaakt onder de capaciteitspool.":::

    Er wordt een venster geopend met de configuratiedetails van het volume.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Schermopname met configuratiedetails van een volume.":::

    U kunt zien dat anfvolume een grootte van 200 GiB heeft en zich in de capaciteitspool anfpool1. Deze wordt geëxporteerd als een NFS-bestands share via 10.22.3.4:/ANFVOLUME. Er is één privé-IP-adres van het Azure Virtual Network (VNet) gemaakt voor Azure NetApp Files en het NFS-pad dat op de virtueleM moet worden bevestigd.

    Zie Prestatieoverwegingen Azure NetApp Files Azure NetApp Files voor meer informatie over de volumeprestaties op grootte of [quotum.](../azure-netapp-files/azure-netapp-files-performance-considerations.md) 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Vooraf geconfigureerde toewijzing van Azure VMware Solution VM-share controleren

Als u uw Azure NetApp Files-share toegankelijk wilt maken voor Azure VMware Solution virtuele Azure VMware Solution, moet u SMB- en NFS-sharetoewijzing begrijpen. Pas na het configureren van de SMB- of NFS-volumes, kunt u deze als hier beschreven.

- SMB-share: maak een Active Directory-verbinding voordat u een SMB-volume implementeert. De opgegeven domeincontrollers moeten toegankelijk zijn voor het gedelegeerde subnet van Azure NetApp Files voor een geslaagde verbinding. Zodra de Active Directory is geconfigureerd in het Azure NetApp Files account, wordt het weergegeven als een selecteerbaar item tijdens het maken van SMB-volumes.

- NFS-share: Azure NetApp Files bij het maken van de volumes met behulp van NFS of dual protocol (NFS en SMB). Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. NFS kan worden bevestigd aan de Linux-server met behulp van de opdrachtregels of /etc/fstab vermeldingen.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Use Cases of Azure NetApp Files with Azure VMware Solution

Hier volgen slechts enkele aantrekkelijke Azure NetApp Files use cases. 
- Horizon-profielbeheer
- Citrix-profielbeheer
- Extern bureaublad-services profielbeheer
- Bestands shares op Azure VMware Solution

## <a name="next-steps"></a>Volgende stappen

Nu u de integratie van uw Azure NetApp Files met Azure VMware Solution werkbelastingen hebt behandeld, kunt u het volgende leren:

- [Resourcebeperkingen voor Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Richtlijnen voor Azure NetApp Files-netwerkplanning](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Replicatie tussen regio's van Azure NetApp Files volumes](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Veelgestelde vragen over Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md)
