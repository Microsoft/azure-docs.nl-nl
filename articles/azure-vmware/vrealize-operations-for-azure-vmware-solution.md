---
title: VRealize-bewerkingen voor Azure VMware-oplossing instellen
description: Meer informatie over het instellen van vRealize-bewerkingen voor uw persoonlijke cloud van Azure VMware-oplossingen.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 3d2617f893c34fde6a154f9f93b5bda520c5a91a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738426"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>VRealize-bewerkingen voor Azure VMware-oplossing instellen


vRealize Operations Manager is een operations management-platform waarmee VMware-infrastructuur beheerders systeem bronnen kunnen bewaken. Deze systeem bronnen kunnen op toepassings niveau of op infrastructuur niveau (zowel fysieke als virtuele) objecten zijn. De meeste VMware-beheerders hebben vRealize-bewerkingen gebruikt voor het bewaken en beheren van de onderdelen van de VMware Private Cloud – vCenter, ESXi, NSX-T, vSAN en VMware HCX.  Elke ingerichte Azure VMware-oplossing privécloud bevat een speciale vCenter-, NSX-T-, vSAN-en HCX-implementatie. 

Controleer eerst [voordat u](#before-you-begin) aan de slag gaat en voordat u aan de [vereisten](#prerequisites) voldoet. Vervolgens begeleiden u de twee typische implementatie topologieën:

> [!div class="checklist"]
> * [On-premises vRealize-bewerkingen Azure VMware-oplossings implementatie beheren](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [vRealize-bewerkingen die worden uitgevoerd op de implementatie van de Azure VMware-oplossing](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Voordat u begint
* Raadpleeg de [product documentatie voor vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) voor meer informatie over het implementeren van VRealize-bewerkingen. 
* Bekijk de [reeks eenvoudige zelfstudies](tutorial-network-checklist.md) over Azure VMware Solution Software Defined Datacenter (SDDC).
* Bekijk eventueel de product documentatie voor [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) voor de on-premises VRealize-bewerkingen de implementatie optie Azure VMware-oplossing beheren. 


## <a name="prerequisites"></a>Vereisten
* [vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) geïnstalleerd.
* Een VPN-of Azure-ExpressRoute die is geconfigureerd tussen on-premises en Azure VMware Solution SDDC.
* Er is een Azure VMware-oplossings privécloud geïmplementeerd in Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>On-premises vRealize-bewerkingen Azure VMware-oplossings implementatie beheren
De meeste klanten hebben een bestaande on-premises implementatie van vRealize-bewerkingen voor het beheren van een of meer on-premises vCenter-domeinen. Wanneer ze een privécloud van Azure VMware-oplossing inrichten, verbinden ze hun on-premises omgeving met hun privécloud met behulp van een Azure ExpressRoute of een Layer 3 VPN-oplossing.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="On-premises vRealize-bewerkingen Azure VMware-oplossings implementatie beheren" border="false":::

Als u de vRealize-bewerkingen wilt uitbreiden naar de privécloud van Azure VMware-oplossing, maakt u een [instantie van een adapter voor de persoonlijke cloud resources](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html). Het verzamelt gegevens uit de privécloud van de Azure VMware-oplossing en brengt deze over in on-premises vRealize-bewerkingen. De on-premises vRealize Operations Manager-exemplaar kunnen rechtstreeks verbinding maken met de vCenter-en NSX-T-manager in de Azure VMware-oplossing. U kunt desgewenst een externe Collector van vRealize-bewerkingen implementeren in de privécloud van Azure VMware-oplossing. De Collector comprimeert en versleutelt de gegevens die zijn verzameld uit de privécloud voordat deze via het ExpressRoute-of VPN-netwerk worden verzonden naar de vRealize-Operations Manager die on-premises wordt uitgevoerd. 

> [!TIP]
> Raadpleeg de [documentatie van VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) voor stapsgewijze hand leiding voor het installeren van vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>vRealize-bewerkingen die worden uitgevoerd op de implementatie van de Azure VMware-oplossing

Een andere optie is het implementeren van een exemplaar van vRealize Operations Manager op een vSphere-cluster in de privécloud. 

>[!IMPORTANT]
>Deze optie wordt momenteel niet ondersteund door VMware.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize-bewerkingen die worden uitgevoerd op de Azure VMware-oplossing" border="false":::

Zodra het exemplaar is geïmplementeerd, kunt u vRealize-bewerkingen configureren voor het verzamelen van gegevens van vCenter, ESXi, NSX-T, vSAN en HCX. 



## <a name="known-limitations"></a>Bekende beperkingen

- De **cloudadmin \@ vSphere. local** user in azure VMware-oplossing heeft [beperkte bevoegdheden](concepts-role-based-access-control.md).  Virtuele machines (Vm's) in azure VMware-oplossing bieden geen ondersteuning voor het verzamelen van gast geheugen met behulp van VMware-hulpprogram ma's.  Actief en verbruikt geheugen gebruik blijft in dit geval werken.
- De optimalisatie van de werk belasting voor zakelijke doel stellingen op basis van een host werkt niet, omdat Azure VMware-oplossingen cluster configuraties beheren, met inbegrip van DRS-instellingen.
- Optimalisatie van de werk belasting voor de cross-cluster plaatsing binnen de SDDC met behulp van de op het cluster gebaseerde bedrijfs intentie wordt volledig ondersteund met vRealize Operations Manager 8,0 en hoger. De optimalisatie van werk belastingen is echter niet op de hoogte van resource groepen en plaatst de Vm's op het cluster niveau. Een gebruiker kan deze hand matig corrigeren in de Azure VMware-oplossing vCenter Server-interface.
- U kunt zich niet aanmelden bij vRealize Operations Manager met uw Azure VMware-oplossing vCenter Server referenties. 
- De Azure VMware-oplossing biedt geen ondersteuning voor de vRealize Operations Manager-invoeg toepassing.

Wanneer u de Azure VMware Solution vCenter verbindt met vRealize Operations Manager een vCenter Server Cloud-account gebruikt, wordt er een waarschuwing weer gegeven:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Het maken van het exemplaar van de waarschuwing is voltooid":::

De waarschuwing treedt op omdat de **cloudadmin \@ vSphere. local** gebruiker in azure VMware-oplossing niet voldoende rechten heeft om alle vCenter Server acties uit te voeren die vereist zijn voor de registratie. De bevoegdheden zijn echter voldoende voor de gegevens verzameling van het adapter exemplaar, zoals hieronder wordt weer gegeven:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Adapter exemplaar voor het uitvoeren van gegevens verzameling":::

Zie bevoegdheden die vereist zijn [voor het configureren van een vCenter-adapter instantie](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)voor meer informatie.

<!-- LINKS - external -->


<!-- LINKS - internal -->




