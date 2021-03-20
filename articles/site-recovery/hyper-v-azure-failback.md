---
title: Failback van virtuele Hyper-V-machines van Azure met Azure Site Recovery
description: Failback van virtuele Hyper-V-machines naar een on-premises site vanuit Azure met Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: a31a28728dd0521262bd0518cc49a385f4314302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87416227"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Een failback uitvoeren voor Hyper-V-VM's

In dit artikel wordt beschreven hoe u back-ups maakt van virtuele Azure-machines die zijn gemaakt na een failover van virtuele Hyper-V-machines van een on-premises site naar Azure, met [Azure site Recovery](site-recovery-overview.md).

- U kunt geen back-ups van virtuele Hyper-V-machines uitvoeren vanuit Azure door een geplande failover uit te voeren van Azure naar de on-premises site. Als de richting van de failover van Azure naar on-premises plaatsvindt, wordt deze beschouwd als een failback.
- Omdat Azure een Maxi maal beschik bare omgeving is en virtuele machines altijd beschikbaar zijn, is failback vanuit Azure een geplande activiteit. U kunt een kleine downtime plannen zodat werk belastingen op de lokale computer opnieuw kunnen worden uitgevoerd. 
- Met geplande failback worden de Vm's in azure uitgeschakeld en worden de meest recente wijzigingen gedownload. Er wordt geen gegevens verlies verwacht.

## <a name="before-you-start"></a>Voordat u begint

1. [Bekijk de typen failback](failover-failback-overview.md#hyper-v-reprotectionfailback) die u kunt gebruiken: herstel naar de oorspronkelijke locatie en herstel naar een alternatieve locatie.
2. Zorg ervoor dat de virtuele machines van Azure gebruikmaken van een opslag account en geen beheerde schijven. Failback van virtuele Hyper-V-machines die zijn gerepliceerd met Managed disks, wordt niet ondersteund.
3. Controleer of de on-premises Hyper-V-host (of System Center VMM-server als u gebruikt met Site Recovery) wordt uitgevoerd en is verbonden met Azure. 
4. Zorg ervoor dat failover en door voeren zijn voltooid voor de Vm's. U hoeft geen specifieke Site Recovery onderdelen in te stellen voor failback van virtuele Hyper-V-machines vanuit Azure.
5. De tijd die nodig is om gegevens synchronisatie te volt ooien en de on-premises VM te starten, is afhankelijk van een aantal factoren. Als u het downloaden van gegevens wilt versnellen, kunt u de micro soft Recovery Services-agent zo configureren dat er meer threads worden gebruikt om de down load te parallelliseren. [Meer informatie](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Een failback uitvoeren naar de oorspronkelijke locatie

Als u virtuele Hyper-V-machines in azure wilt herstellen naar de oorspronkelijke on-premises VM, voert u de volgende geplande failover uit van Azure naar de on-premises site:

1. Selecteer de VM in de kluis > **Gerepliceerde items**. Klik met de rechter muisknop op de virtuele machine > **geplande failover**. Als u een herstel plan niet meer wilt herstellen, selecteert u de naam van het plan en klikt u op **failover**  >  **geplande** failover.
2. Kies in **geplande failover bevestigen** de bron-en doel locatie. Let op de richting van de failover. Als de failover van de primaire naar verwachting werkt en alle virtuele machines zich op de secundaire locatie bevinden, is dit alleen ter informatie.
3. Selecteer een optie bij **gegevens synchronisatie**:
    - **Gegevens synchroniseren vóór de failover (alleen Delta wijzigingen synchroniseren)**: met deze optie wordt de uitval tijd voor virtuele machines geminimaliseerd wanneer deze worden gesynchroniseerd zonder dat ze worden afgesloten.
        - **Fase 1**: Hiermee wordt een moment opname van de virtuele machine van Azure gemaakt en gekopieerd naar de on-premises Hyper-V-host. De machine blijft actief in Azure.
        - **Fase 2**: Hiermee wordt de virtuele machine van Azure afgesloten, zodat er geen nieuwe wijzigingen worden uitgevoerd. De laatste set Delta wijzigingen wordt overgedragen naar de on-premises server en de on-premises VM wordt gestart.
    - **Gegevens alleen synchroniseren tijdens failover (volledige down load)**: deze optie is sneller omdat we ervan uit te gaan dat het grootste deel van de schijf is gewijzigd en u niet wilt dat er tijd meters worden berekend. Met deze optie worden geen controlesom berekeningen uitgevoerd.
        - Hiermee wordt de schijf gedownload. 
        - We raden u aan deze optie te gebruiken als u Azure al een tijdje (een maand of meer) hebt uitgevoerd of als de on-premises virtuele machine is verwijderd.

4. Alleen voor VMM: als gegevens versleuteling is ingeschakeld voor de Cloud, selecteert u in **versleutelings sleutel** het certificaat dat is uitgegeven tijdens het inschakelen van gegevens versleuteling tijdens de installatie van de provider op de VMM-server.
5. Start de failover. U kunt de voortgang van de failover volgen op het tabblad **Taken**.
6. Als u de optie hebt geselecteerd voor het synchroniseren van de gegevens vóór de failover, nadat de initiële gegevens synchronisatie is voltooid en u klaar bent om de virtuele machines in azure uit te scha kelen, klikt u op **taken** > taak naam > **failover volt ooien**. Er gebeurt nu het volgende:
    - De Azure-machine wordt afgesloten.
    - Brengt de laatste wijzigingen over naar de on-premises VM.
    - Start de on-premises VM.
7. U kunt zich nu aanmelden bij de on-premises VM-machine om te controleren of deze beschikbaar is zoals verwacht.
8. De virtuele machine heeft de status in behandeling door voeren. Klik op **door voeren** om de failover door te voeren.
9. Als u de failback wilt volt ooien, klikt u op **achterwaartse replicatie** om de on-premises VM opnieuw te repliceren naar Azure.



## <a name="fail-back-to-an-alternate-location"></a>Failback naar een alternatieve locatie 

Als volgt een failback naar een alternatieve locatie:

1. Als u nieuwe hardware wilt instellen, installeert u een [ondersteunde versie van Windows](hyper-v-azure-support-matrix.md#replicated-vms)en de Hyper-V-rol op de computer.
2. Maak een virtuele netwerk switch met dezelfde naam als die van de oorspronkelijke server.
3. Selecteer in beveiligings groep **beveiligde items**  >    >  \<ProtectionGroupName>  ->  \<VirtualMachineName> de virtuele machine waarvoor u een failback wilt uitvoeren en selecteer vervolgens **geplande failover**.
4. In **geplande failover bevestigen** s kiest **u on-premises virtuele machine maken als deze niet bestaat**.
5. In **hostnaam** selecteert u de nieuwe Hyper-V-hostserver waarop u de virtuele machine wilt plaatsen.
6. Bij het **synchroniseren van gegevens** wordt u aangeraden om de optie voor het synchroniseren van de gegevens voor de failover te selecteren. Hiermee wordt de uitval tijd voor Vm's geminimaliseerd wanneer deze worden gesynchroniseerd zonder dat ze worden afgesloten. Dit doet het volgende:
    - **Fase 1**: er wordt een moment opname gemaakt van de virtuele machine van Azure en gekopieerd naar de on-premises Hyper-V-host. De machine blijft actief in Azure.
    - **Fase 2**: Hiermee wordt de virtuele machine van Azure afgesloten, zodat er geen nieuwe wijzigingen worden uitgevoerd. De laatste set wijzigingen wordt overgedragen naar de on-premises server en de on-premises virtuele machine wordt gestart.
    
7. Klik op het vinkje om de failover (failback) te starten.
8. Wanneer de initiële synchronisatie is voltooid en u klaar bent om de Azure VM af te sluiten, klikt u op **taken**  >  \<planned failover job>  >  **voltooid failover**. Hiermee wordt de Azure-machine afgesloten, worden de meest recente wijzigingen overgedragen naar de on-premises VM en wordt de computer gestart.
9. U kunt zich aanmelden bij de on-premises VM om te controleren of alles werkt zoals verwacht.
10. Klik op **door voeren** om de failover te volt ooien. Door voeren worden de virtuele machine en schijven van Azure verwijderd en wordt de on-premises VM voor bereid om opnieuw te worden beveiligd.
10. Klik op **achterwaartse replicatie** om te beginnen met het repliceren van de on-premises VM naar Azure. Alleen de Delta wijzigingen sinds de VM is uitgeschakeld in Azure worden gerepliceerd.

    > [!NOTE]
    > Als u de failback-taak tijdens de gegevens synchronisatie annuleert, wordt de status van de on-premises VM beschadigd. Dit komt doordat gegevens synchronisatie de nieuwste gegevens kopieert van Azure VM-schijven naar de on-premises gegevens schijven en totdat de synchronisatie is voltooid, de schijf gegevens mogelijk niet consistent zijn. Als de on-premises VM wordt gestart nadat de gegevens synchronisatie is geannuleerd, wordt deze mogelijk niet opgestart. In dit geval voert u de failover opnieuw uit om de gegevens synchronisatie te volt ooien.


## <a name="next-steps"></a>Volgende stappen
Wanneer de on-premises VM wordt gerepliceerd naar Azure, kunt u indien nodig [nog een failover](site-recovery-failover.md) naar Azure uitvoeren.
