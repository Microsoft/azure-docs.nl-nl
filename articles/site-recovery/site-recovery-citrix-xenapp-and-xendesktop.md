---
title: XenDesktop/XenApp-herstel na nood gevallen met Azure Site Recovery instellen met behulp van Citrix
description: In dit artikel wordt beschreven hoe u met behulp van Azure Site Recovery herstel na nood gevallen kunt instellen voor Citrix XenDesktop-en XenApp-implementaties.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 90d54a8ded99dd8ab43aed688036add6aede20ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134839"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>herstel na nood geval instellen voor een Citrix XenApp-en XenDesktop-implementatie met meerdere lagen



Citrix XenDesktop is een Desktop Virtualization-oplossing die Desk tops en toepassingen levert als een OnDemand-service aan elke gebruiker, waar dan ook. Met FlexCast Delivery Technology kunnen XenDesktop snel en veilig toepassingen en Desk tops leveren aan gebruikers.
Op dit moment biedt Citrix XenApp geen mogelijkheden voor herstel na nood gevallen.

Een goede oplossing voor herstel na nood gevallen, moet het mogelijk maken dat herstel plannen worden gemodelleerd rond de bovenstaande complexe toepassings architecturen. Daarnaast kunnen ze aangepaste stappen toevoegen voor het afhandelen van toepassings toewijzingen tussen de verschillende lagen, waardoor het mogelijk is om een oplossing met één muis klik te bieden in het geval van een nood situatie met een lagere RTO.

Dit document bevat stapsgewijze richt lijnen voor het bouwen van een oplossing voor herstel na nood gevallen voor uw on-premises Citrix XenApp-implementaties op Hyper-V en VMware vSphere platforms. In dit document wordt ook beschreven hoe u een testfailover (nood herstel analyse) en een niet-geplande failover naar Azure uitvoert met behulp van herstel plannen, de ondersteunde configuraties en vereisten.


## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende weten:

1. [Een virtuele machine repliceren naar Azure](./vmware-azure-tutorial.md)
1. [Een herstel netwerk ontwerpen](./concepts-on-premises-to-azure-networking.md)
1. [Een testfailover naar Azure uitvoeren](site-recovery-test-failover-to-azure.md)
1. [Een failover naar Azure uitvoeren](site-recovery-failover.md)
1. [Een domein controller repliceren](site-recovery-active-directory.md)
1. [SQL server repliceren](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Implementatie patronen

Een Citrix XenApp-en XenDesktop-Farm hebben doorgaans het volgende implementatie patroon:

**Implementatie patroon**

Citrix XenApp-en XenDesktop-implementatie met AD DNS-server, SQL database server, Citrix Delivery controller, winkel server, XenApp Master (VDA), Citrix XenApp-licentie server

![Implementatie patroon 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery

Voor het doel van dit artikel zijn Citrix-implementaties op virtuele VMware-machines die worden beheerd door vSphere 6,0/System Center VMM 2012 R2, gebruikt voor het instellen van DR.

### <a name="source-and-target"></a>Bron en doel

**Scenario** | **Op een secundaire site** | **Naar Azure**
--- | --- | ---
**Hyper-V** | Niet binnen het bereik | Yes
**VMware** | Niet binnen het bereik | Yes
**Fysieke server** | Niet binnen het bereik | Yes

### <a name="versions"></a>Versies
Klanten kunnen XenApp-onderdelen implementeren als Virtual Machines die worden uitgevoerd op Hyper-V of VMware of als fysieke servers. Azure Site Recovery kunt zowel fysieke als virtuele implementaties beveiligen naar Azure.
Omdat XenApp 7,7 of hoger wordt ondersteund in azure, kunnen alleen implementaties met deze versies naar Azure worden gemigreerd voor herstel na nood geval of migratie.

### <a name="things-to-keep-in-mind"></a>Zaken om rekening mee te houden

1. De beveiliging en het herstel van on-premises implementaties met behulp van Server besturingssysteem computers voor het leveren van XenApp gepubliceerde apps en XenApp gepubliceerde Bureau bladen worden ondersteund.

2. De beveiliging en het herstel van on-premises implementaties met desktop besturingssystemen om Desktop VDI te bieden voor virtuele Bureau bladen van clients, waaronder Windows 10, wordt niet ondersteund. Dit komt doordat Site Recovery het herstellen van machines met Desktop OS'es niet ondersteunt.  Ook zijn sommige besturings systemen van virtueel bureau blad van de client (bijvoorbeeld Windows 7) worden nog niet ondersteund voor licentie verlening in Azure. [Meer informatie](https://azure.microsoft.com/pricing/licensing-faq/) over licentieverlening voor clientbureaubladen en serverdesktops in Azure.

3.  Azure Site Recovery kunt bestaande on-premises MCS-of PVS-klonen niet repliceren en beveiligen.
U moet deze klonen opnieuw maken met behulp van Azure RM-inrichting van de bezorgings controller.

4. NetScaler kan niet worden beveiligd met Azure Site Recovery als NetScaler is gebaseerd op FreeBSD en Azure Site Recovery biedt geen ondersteuning voor de beveiliging van FreeBSD-besturings systeem. U moet een nieuw NetScaler-apparaat implementeren en configureren vanaf de Azure-markt plaats na een failover naar Azure.


## <a name="replicating-virtual-machines"></a>Virtuele machines repliceren

De volgende onderdelen van de Citrix XenApp-implementatie moeten worden beveiligd om replicatie en herstel mogelijk te maken.

* Beveiliging van AD DNS-server
* Beveiliging van SQL database server
* Beveiliging van Citrix Delivery controller
* Beveiliging van de winkel server.
* Beveiliging van XenApp-Master (VDA)
* Beveiliging van Citrix XenApp-licentie server


**AD DNS-Server replicatie**

Raadpleeg [Active Directory en DNS beveiligen met Azure site Recovery](site-recovery-active-directory.md) over de richt lijnen voor het repliceren en configureren van een domein controller in Azure.

**Replicatie van SQL database server**

Raadpleeg [SQL Server met SQL Server herstel na nood gevallen en Azure site Recovery](site-recovery-sql.md) voor gedetailleerde technische richt lijnen voor de aanbevolen opties voor het beveiligen van SQL-servers.

Volg [deze richt lijnen](./vmware-azure-tutorial.md) om te beginnen met het repliceren van de andere virtuele machines van het onderdeel naar Azure.

![Beveiliging van XenApp-onderdelen](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Reken-en netwerk instellingen**

Nadat de computers zijn beveiligd (status wordt weer gegeven als ' beveiligd ' onder gerepliceerde items), moeten de berekenings-en netwerk instellingen worden geconfigureerd.
In Compute en Network > compute properties kunt u de naam en doel grootte van de Azure-VM opgeven.
Wijzig indien nodig de naam om te voldoen aan de Azure-vereisten. U kunt ook informatie weer geven en toevoegen over het doelnet werken, het subnet en het IP-adres dat wordt toegewezen aan de Azure VM.

Houd rekening met het volgende:

* U kunt het doel-IP-adres instellen. Als u geen adres opgeeft, maakt de machine waarvoor een failover is uitgevoerd, gebruik van DHCP. Als u een adres instelt dat niet beschikbaar is op failover, werkt de failover niet. Hetzelfde doel-IP-adres kan worden gebruikt voor een testfailover als het adres beschikbaar is in het testfailovernetwerk.

* Voor de AD/DNS-server, die het on-premises adres behoudt, kunt u hetzelfde adres opgeven als de DNS-server voor het virtuele Azure-netwerk.

Het aantal netwerkadapters wordt bepaald door de grootte die u voor de virtuele doelmachine opgeeft. Dat werkt als volgt:

*   Als het aantal netwerkadapters op de bronmachine kleiner is dan of gelijk is aan het aantal adapters dat is toegestaan voor de grootte van de doelmachine, heeft het doel hetzelfde aantal adapters als de bron.
*   Als het aantal adapters voor de virtuele bronmachine meer is dan is toegestaan voor de doelgrootte, wordt het maximum voor de doelgrootte gebruikt.
* Als een bronmachine bijvoorbeeld twee netwerkadapters heeft en met de grootte van de doelmachine vier netwerkadapters worden ondersteund, heeft de doelcomputer twee adapters. Als de bronmachine twee adapters heeft, maar de grootte van de doelmachine slechts één adapter ondersteunt, heeft de doelmachine één adapter.
*   Als de virtuele machine meerdere netwerk adapters heeft, worden deze allemaal verbonden met hetzelfde netwerk.
*   Als de virtuele machine meerdere netwerk adapters heeft, wordt de eerste die in de lijst wordt weer gegeven, de standaard netwerk adapter in de virtuele machine van Azure.


## <a name="creating-a-recovery-plan"></a>Een herstel plan maken

Nadat de replicatie is ingeschakeld voor de virtuele machines van het XenApp-onderdeel, is de volgende stap het maken van een herstel plan.
In een herstel plan worden virtuele machines gegroepeerd met vergelijk bare vereisten voor failover en herstel.  

**Stappen voor het maken van een herstel plan**

1. Voeg de virtuele machines van het XenApp-onderdeel toe in het herstel plan.
2. Klik op herstel plannen-> en herstel plan. Geef een intuïtieve naam op voor het herstel plan.
3. Voor virtuele VMware-machines: Selecteer bron als VMware-proces server, doel als Microsoft Azure en implementatie model als Resource Manager en klik op items selecteren.
4. Voor virtuele Hyper-V-machines: Selecteer bron als VMM-server, doel als Microsoft Azure en implementatie model als Resource Manager, en klik op items selecteren en selecteer vervolgens de virtuele machines met XenApp-implementatie.

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failover-groepen

Herstel plannen kunnen worden aangepast om failover-groepen toe te voegen voor specifieke opstart volgorde, scripts of hand matige acties. De volgende groepen moeten worden toegevoegd aan het herstel plan.

1. Failover-Group1: AD DNS
2. Failover-group2: SQL Server Vm's
2. Failover-Group3: VDA master image VM
3. Failover-Group4: bezorgings controller en winkel server-Vm's


### <a name="adding-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstel plan

Scripts kunnen worden uitgevoerd vóór of na een specifieke groep in een herstel plan. Hand matige acties kunnen ook worden opgenomen en uitgevoerd tijdens failover.

Het aangepaste herstel plan ziet er als volgt uit:

1. Failover-Group1: AD DNS
2. Failover-group2: SQL Server Vm's
3. Failover-Group3: VDA master image VM

   >[!NOTE]     
   >Stap 4, 6 en 7 met hand matige of script acties zijn alleen van toepassing op een on-premises XenApp->omgeving met MCS/PVS-catalogi.

4. Hand matig of script actie van groep 3: de virtuele machine van het hoofd VDA afsluiten.
De hoofd-VDA-VM bij een failover naar Azure wordt uitgevoerd. Als u nieuwe MCS-catalogussen wilt maken met behulp van Azure-hosting, moet de hoofd-VDA-VM de status gestopt (de toegewezen) hebben. Sluit de VM af vanaf Azure Portal.

5. Failover-Group4: bezorgings controller en winkel server-Vm's
6. Group3 hand matig of script actie 1:

    ***Azure RM-hostrecord toevoegen***

    Maak verbinding met Azure-host op de computer van de bezorgings controller om nieuwe MCS-catalogussen in te richten in Azure. Volg de stappen die in dit [artikel](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)worden beschreven.

7. Group3 hand matig of script actie 2:

    ***MCS-catalogi opnieuw maken in azure***

    De bestaande MCS-of PVS-klonen op de primaire site worden niet gerepliceerd naar Azure. U moet deze klonen opnieuw maken met het gerepliceerde hoofd VDA en Azure Provisioning van de bezorgings controller. Volg de stappen in dit [artikel](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) voor het maken van MCS-catalogussen in Azure.

![Herstel plan voor XenApp-onderdelen](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >U kunt scripts op [locatie](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) gebruiken om de DNS bij te werken met de nieuwe IP-adressen van de failover->virtuele machines of om een Load Balancer te koppelen aan de virtuele machine waarvoor een failover is uitgevoerd, indien nodig.


## <a name="doing-a-test-failover"></a>Een testfailover uitvoeren

Volg [deze richt lijnen](site-recovery-test-failover-to-azure.md) om een testfailover uit te voeren.

![Herstelplan](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Een failover uitvoeren

Volg [deze richt lijnen](site-recovery-failover.md) wanneer u een failover uitvoert.

## <a name="next-steps"></a>Volgende stappen

In dit artikel vindt u [meer informatie](https://aka.ms/citrix-xenapp-xendesktop-with-asr) over het repliceren van Citrix XenApp-en XenDesktop-implementaties. Bekijk de richt lijnen voor het [repliceren van andere toepassingen](site-recovery-workload.md) met site Recovery.
