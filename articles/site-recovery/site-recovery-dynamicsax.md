---
title: Herstel na nood gevallen van Dynamics AX met Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen voor Dynamics AX met Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: dfa3c108d00aeba9c7d42e96e7a40736a087a508
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133811"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Herstel na nood geval instellen voor een Dynamics AX-toepassing met meerdere lagen   




 Dynamics AX is een van de populairste ERP-oplossingen die door ondernemingen worden gebruikt voor het standaardiseren van processen op verschillende locaties, het beheren van resources en het vereenvoudigen van de naleving. Omdat de toepassing cruciaal is voor een organisatie, moet de toepassing in het geval van een nood situatie in de minimale tijd actief zijn.

Dynamics AX biedt vandaag geen mogelijkheden voor herstel na nood gevallen. Dynamics AX bestaat uit vele Server onderdelen, zoals Windows Application object Server, Azure Active Directory, Azure SQL Database, share Point server en Reporting Services. Het hand matig beheren van herstel na nood gevallen van elk van deze onderdelen is niet alleen duur, maar ook gevoelig voor fouten.

In dit artikel wordt uitgelegd hoe u een nood herstel oplossing voor uw Dynamics AX-toepassing kunt maken met behulp van [Azure site Recovery](site-recovery-overview.md). Het behandelt ook geplande/ongeplande testfailover met een herstel plan met één klik, ondersteunde configuraties en vereisten.



## <a name="prerequisites"></a>Vereisten

Voor het implementeren van herstel na nood gevallen voor de Dynamics AX-toepassing met behulp van Site Recovery zijn de volgende vereisten vereist:

• Stel een on-premises Dynamics AX-implementatie in.

• Maak een Site Recovery kluis in een Azure-abonnement.

• Als Azure uw herstel site is, voert u het hulp programma Azure virtual machine Readiness Assessment uit op de Vm's. Ze moeten compatibel zijn met de Azure Virtual Machines-en Site Recovery-Services.

## <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery

Voor het maken van dit artikel hebben we virtuele VMware-machines gebruikt met Dynamics AX 2012 R3 op Windows Server 2012 R2 Enter prise. Omdat Site Recovery replicatie neutraal is, verwachten we de aanbevelingen die hier worden gegeven voor de volgende scenario's.

### <a name="source-and-target"></a>Bron en doel

**Scenario** | **Op een secundaire site** | **Naar Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysieke server** | Ja | Ja

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Herstel na nood gevallen van de Dynamics AX-toepassing inschakelen met behulp van Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Uw Dynamics AX-toepassing beveiligen
Als u de volledige replicatie en het herstel van de toepassing wilt inschakelen, moet elk onderdeel van Dynamics AX worden beveiligd.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Active Directory-en DNS-replicatie instellen

Active Directory is vereist op de site voor herstel na nood gevallen voor de werking van de Dynamics AX-toepassing. We raden de volgende twee keuzes aan op basis van de complexiteit van de on-premises omgeving van de klant.

**Optie 1**

De klant heeft een klein aantal toepassingen en één domein controller voor de hele on-premises site en de plannen om de hele site samen te laten mislukken. We raden u aan Site Recovery replicatie te gebruiken om de computer van de domein controller te repliceren naar een secundaire site (van toepassing op de scenario's van site-naar-site en site-naar-Azure).

**Optie 2**

De klant beschikt over een groot aantal toepassingen en voert een Active Directory forest en abonnementen uit om een aantal toepassingen tegelijk uit te voeren. U wordt aangeraden een extra domein controller in te stellen op de nood herstel site (een secundaire site of in Azure).

 Zie [een domein controller beschikbaar maken op een nood herstel site](site-recovery-active-directory.md)voor meer informatie. Voor de rest van dit document wordt ervan uitgegaan dat er een domein controller beschikbaar is op de site voor herstel na nood gevallen.

### <a name="2-set-up-sql-server-replication"></a>2. SQL Server replicatie instellen
Zie [toepassingen repliceren met SQL Server en Azure site Recovery](site-recovery-sql.md)voor technische richt lijnen over de aanbevolen optie voor het beveiligen van de SQL-laag.

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Schakel de beveiliging voor de virtuele machines van de Dynamics AX-client en de Application object server in
Voer relevante Site Recovery configuratie uit op basis van of de Vm's op [Hyper-V](./hyper-v-azure-tutorial.md) of [VMware](./vmware-azure-tutorial.md)zijn geïmplementeerd.

> [!TIP]
> We raden u aan de crash-consistente frequentie in te stellen op 15 minuten.
>

In de volgende moment opname ziet u de beveiligings status van de Vm's van Dynamics-onderdelen in een scenario met beveiliging van VMware site-naar-Azure.

![Beveiligde items](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. netwerken configureren
**De compute-en netwerk instellingen voor de virtuele machine configureren**

Configureer voor de virtuele machines van de Dynamics AX-client en Application object Server netwerk instellingen in Site Recovery zodat de VM-netwerken na een failover aan het juiste netwerk voor herstel na nood geval worden gekoppeld. Zorg ervoor dat het nood herstel netwerk voor deze lagen routeerbaar is naar de SQL-laag.

U kunt de virtuele machine in de gerepliceerde items selecteren om de netwerk instellingen te configureren, zoals wordt weer gegeven in de volgende moment opname:

* Voor Application object Server-servers selecteert u de juiste beschikbaarheidsset.

* Als u een statisch IP-adres gebruikt, geeft u het IP-adres op dat u wilt dat de virtuele machine in het tekstvak **doel-IP** moet worden uitgevoerd.

    ![Netwerkinstellingen](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. een herstel plan maken

U kunt een herstel plan maken in Site Recovery om het failoverproces te automatiseren. Voeg een app-laag en een weblaag toe in het herstel plan. Volg deze in verschillende groepen, zodat de front-end wordt afgesloten vóór de app-laag.

1. Selecteer de Site Recovery kluis in uw abonnement en selecteer de tegel **herstel plannen** .

2. Selecteer **+ herstel plan** en geef een naam op.

3. Selecteer de **bron** en het **doel**. Het doel kan Azure of een secundaire site zijn. Als u Azure kiest, moet u het implementatie model opgeven.

    ![Herstelplan maken](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selecteer de Application object Server en de client-Vm's voor het herstel plan en selecteer de ✓.

    ![Items selecteren](./media/site-recovery-dynamics-ax/selectvms.png)

    Voor beeld van herstel plan:

    ![Details herstelplan](./media/site-recovery-dynamics-ax/recoveryplan.png)

U kunt het herstel plan voor de Dynamics AX-toepassing aanpassen door de volgende stappen toe te voegen. In de vorige moment opname wordt het volledige herstel plan weer gegeven nadat u alle stappen hebt toegevoegd.


* **Stappen voor SQL Server failover**: Zie [Replication Applications with SQL Server and Azure site Recovery](site-recovery-sql.md)(Engelstalig) voor informatie over de specifieke herstel stappen voor SQL Server.

* **Failovergroep 1**: failover van de Application object Server vm's.
Zorg ervoor dat het geselecteerde herstel punt zo dicht mogelijk bij de data base-PIT ligt, maar niet vóór het.

* **Script**: add Load Balancer (alleen E-A).
Voeg een script toe (via Azure Automation) nadat de VM-groep van de toepassings object Server een load balancer toevoegen. U kunt een script gebruiken om deze taak uit te voeren. Zie [een Load Balancer toevoegen voor herstel na nood gevallen](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)voor meerdere lagen voor meer informatie.

* **Failover-groep 2**: failover van de Dynamics AX-client-vm's. Failover van de virtuele machines van de weblaag als onderdeel van het herstel plan.


### <a name="perform-a-test-failover"></a>Een testfailover uitvoeren

Zie de aanvullende hand leiding ' Active Directory nood herstel oplossing ' voor meer informatie over het Active Directory tijdens de testfailover.

Zie [toepassingen repliceren met SQL Server en Azure site Recovery](site-recovery-sql.md)voor meer informatie die specifiek is voor SQL Server tijdens een testfailover.

1. Ga naar de Azure Portal en selecteer uw Site Recovery kluis.

2. Selecteer het herstel plan dat voor Dynamics AX is gemaakt.

3. Selecteer **Failover testen**.

4. Selecteer het virtuele netwerk om het proces van de testfailover te starten.

5. Nadat de secundaire omgeving is ingesteld, kunt u uw validaties uitvoeren.

6. Nadat de validaties zijn voltooid, selecteert u de **validaties voltooid** en wordt de testfailover gereinigd.

Zie [failover testen naar Azure in site Recovery](site-recovery-test-failover-to-azure.md)voor meer informatie over het uitvoeren van een testfailover.

### <a name="perform-a-failover"></a>Een failover uitvoeren

1. Ga naar de Azure Portal en selecteer uw Site Recovery kluis.

2. Selecteer het herstel plan dat voor Dynamics AX is gemaakt.

3. Selecteer **failover** en selecteer **failover**.

4. Selecteer het doelnet werk en selecteer **✓** om het failoverproces te starten.

Zie [failover in site Recovery](site-recovery-failover.md)voor meer informatie over het uitvoeren van een failover.

### <a name="perform-a-failback"></a>Failback uitvoeren

Zie [toepassingen repliceren met SQL Server en Azure site Recovery](site-recovery-sql.md)voor overwegingen die specifiek zijn voor SQL Server tijdens het failback.

1. Ga naar de Azure Portal en selecteer uw Site Recovery kluis.

2. Selecteer het herstel plan dat voor Dynamics AX is gemaakt.

3. Selecteer **failover** en selecteer **failover**.

4. Selecteer **richting wijzigen**.

5. Selecteer de juiste opties: gegevens synchronisatie en VM maken.

6. Selecteer **✓** om het failbackproces te starten.


Zie voor meer informatie over het uitvoeren van een failback [failback VMware-vm's van Azure naar on-premises](./vmware-azure-failback.md).

## <a name="summary"></a>Samenvatting
U kunt met behulp van Site Recovery een volledig geautomatiseerd plan voor herstel na nood gevallen maken voor uw Dynamics AX-toepassing. In het geval van een onderbreking kunt u de failover binnen enkele seconden initiëren en de toepassing in een paar minuten actief maken en uitvoeren.

## <a name="next-steps"></a>Volgende stappen
Zie [welke workloads kan ik beveiligen?](site-recovery-workload.md)voor meer informatie over het beveiligen van zakelijke workloads met site Recovery.
