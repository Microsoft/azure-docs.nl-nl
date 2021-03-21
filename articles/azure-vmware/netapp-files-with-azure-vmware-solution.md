---
title: Azure NetApp Files met Azure VMware-oplossing
description: Gebruik Azure NetApp Files met virtuele machines van Azure VMware-oplossingen voor het migreren en synchroniseren van gegevens over on-premises servers, virtuele machines met Azure VMware-oplossingen en Cloud infrastructuren.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 2f2e8fdeb777e7e4b2b4e89c1bb36b51c3083257
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575435"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files met Azure VMware-oplossing

In dit artikel wordt uitgelegd hoe u Azure NetApp Files integreert met Azure VMware-workloads op basis van oplossingen. Het gast besturingssysteem wordt uitgevoerd in virtuele machines (Vm's) die toegang hebben tot Azure NetApp Files volumes. 

## <a name="azure-netapp-files-overview"></a>Overzicht van Azure NetApp Files

[Azure NetApp files](../azure-netapp-files/azure-netapp-files-introduction.md) is een Azure-service voor migratie en het uitvoeren van de meest veeleisende bedrijfs bestanden-workloads in de Cloud: data bases, SAP en high performance computing-toepassingen, zonder code wijzigingen.

### <a name="features"></a>Functies
(Services waarbij Azure NetApp Files worden gebruikt.)

- **Active Directory verbindingen**: Azure NetApp Files ondersteunt [Active Directory Domain Services en Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use).

- **Share protocol**: Azure NetApp files ondersteunt Server Message Block (SMB) en Network File System (NFS)-protocollen. Deze ondersteuning betekent dat de volumes kunnen worden gekoppeld op de Linux-client en kunnen worden toegewezen op Windows-clients.

- **Azure VMware-oplossing**: Azure NetApp files-shares kunnen worden gekoppeld van vm's die zijn gemaakt in de Azure VMware-oplossings omgeving.

Azure NetApp Files is beschikbaar in veel Azure-regio's en ondersteunt replicatie tussen regio's. Zie [opslag hiërarchie van Azure NetApp files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)voor meer informatie over Azure NetApp files configuratie methoden.

## <a name="reference-architecture"></a>Referentiearchitectuur

In het volgende diagram ziet u een verbinding via Azure ExpressRoute naar een privécloud van Azure VMware-oplossing. De Azure VMware-oplossings omgeving heeft toegang tot de Azure NetApp Files-share die is gekoppeld aan Vm's van Azure VMware-oplossingen.

![Diagram met NetApp-bestanden voor Azure VMware-oplossings architectuur.](media/net-app-files/net-app-files-topology.png)

Dit artikel bevat instructies voor het instellen, testen en controleren van het Azure NetApp Files volume als een bestands share voor virtuele machines van Azure VMware-oplossingen. In dit scenario hebben we het NFS-protocol gebruikt. Azure NetApp Files en Azure VMware-oplossing worden in dezelfde Azure-regio gemaakt.

## <a name="prerequisites"></a>Vereisten 

> [!div class="checklist"]
> * Azure-abonnement met Azure NetApp Files ingeschakeld
> * Subnet voor Azure NetApp Files
> * Linux-VM in azure VMware-oplossing
> * Windows-Vm's in azure VMware-oplossing

## <a name="regions-supported"></a>Regio's worden ondersteund

Een lijst met ondersteunde regio's vindt u op [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all).

## <a name="verify-pre-configured-azure-netapp-files"></a>Vooraf geconfigureerde Azure NetApp Files controleren 

Volg de stapsgewijze instructies in de volgende artikelen voor het maken en koppelen van Azure NetApp Files volumes op virtuele machines met Azure VMware-oplossingen.

- [Een NetApp-account maken](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Een capaciteitspool instellen](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Een SMB-volume maken voor Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Een NFS-volume maken voor Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Een subnet delegeren aan Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

De volgende stappen omvatten het verifiëren van de vooraf geconfigureerde Azure NetApp Files die in azure zijn gemaakt op Azure NetApp Files Premium-service niveau.

1. Selecteer **Azure NetApp files** in de Azure portal onder **opslag**. Er wordt een lijst weer gegeven met uw geconfigureerde Azure NetApp Files. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Scherm afbeelding met een lijst met vooraf geconfigureerde Azure NetApp Files."::: 

2. Selecteer een geconfigureerde NetApp-account om de instellingen ervan weer te geven. Selecteer bijvoorbeeld **Contoso-anf2**. 

3. Selecteer de **capaciteits groepen** om de geconfigureerde groep te controleren. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Scherm opname met opties voor het weer geven van capaciteits Pools en volumes van een geconfigureerde NetApp-bestanden account.":::

    De pagina capaciteits Pools wordt geopend met de capaciteit en het service niveau. In dit voor beeld wordt de opslag groep geconfigureerd als 4 TiB met een Premium-service niveau.

4. Selecteer **volumes** om de volumes weer te geven die zijn gemaakt onder de capaciteits groep. (Zie de vorige scherm afbeelding.)

5. Selecteer een volume om de configuratie ervan weer te geven.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Scherm afbeelding met volumes die zijn gemaakt onder de capaciteits groep.":::

    Er wordt een venster geopend waarin de configuratie details van het volume worden weer gegeven.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Scherm afbeelding met configuratie details van een volume.":::

    U kunt zien dat anfvolume een grootte van 200 GiB heeft en zich in de anfpool1 voor de capaciteits groep bevindt. Deze wordt geëxporteerd als een NFS-bestands share via 10.22.3.4:/ANFVOLUME. Er is één privé-IP-adres van de Azure Virtual Network (VNet) gemaakt voor Azure NetApp Files en het NFS-pad dat moet worden gekoppeld aan de virtuele machine.

    Zie [prestatie overwegingen voor Azure NetApp files](../azure-netapp-files/azure-netapp-files-performance-considerations.md)als u meer wilt weten over de prestaties van Azure NetApp files volumes op grootte of quotum. 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>De vooraf geconfigureerde Azure VMware-oplossing voor VM-share toewijzing controleren

Als u uw Azure NetApp Files share toegankelijk wilt maken voor uw Azure VMware-oplossings-VM, moet u de toewijzing van SMB-en NFS-shares begrijpen. Pas nadat u de SMB-of NFS-volumes hebt geconfigureerd, kunt u deze koppelen zoals hier wordt beschreven.

- SMB-share: een Active Directory verbinding maken voordat een SMB-volume wordt geïmplementeerd. De opgegeven domein controllers moeten toegankelijk zijn voor het gedelegeerde subnet van Azure NetApp Files voor een geslaagde verbinding. Zodra de Active Directory is geconfigureerd binnen het Azure NetApp Files account, wordt deze weer gegeven als een selecteerbaar item tijdens het maken van SMB-volumes.

- NFS-share: Azure NetApp Files draagt bij aan het maken van de volumes met NFS of Dual Protocol (NFS en SMB). Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. NFS kan worden gekoppeld aan de Linux-server met behulp van de opdracht regels of bestand/etc/fstab vermeldingen.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Gebruiks voorbeelden van Azure NetApp Files met de Azure VMware-oplossing

Hier volgen slechts enkele aantrekkelijke Azure NetApp Files use cases. 
- Horizon Profiel beheer
- Citrix-Profiel beheer
- Profiel beheer Extern bureaublad-services
- Bestands shares op de Azure VMware-oplossing

## <a name="next-steps"></a>Volgende stappen

Nu u de integratie van Azure NetApp Files met uw Azure VMware-oplossings workloads hebt besproken, kunt u het volgende weten:

- [Resource limieten voor Azure NetApp files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits).
- [Richt lijnen voor het plannen van Azure NetApp files netwerk](../azure-netapp-files/azure-netapp-files-network-topologies.md).
- [Replicatie tussen regio's van Azure NetApp files volumes](../azure-netapp-files/cross-region-replication-introduction.md). 
- [Veelgestelde vragen over Azure NetApp files](../azure-netapp-files/azure-netapp-files-faqs.md).
