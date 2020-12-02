---
title: Een AVS-evaluatie met Azure Migrate server evaluatie maken | Microsoft Docs
description: Hierin wordt beschreven hoe u een AVS-evaluatie maakt met het hulp programma voor het evalueren van Azure Migrate-servers
author: rashi-ms
ms.service: azure-migrate
ms.topic: article
ms.date: 06/26/2020
ms.author: rajosh
ms.openlocfilehash: f9fdff54cbd795c6aeda1708dcae8d00022a6a43
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483501"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Een evaluatie van een Azure VMware-oplossing (AVS) maken

In dit artikel wordt beschreven hoe u een evaluatie van een Azure VMware-oplossing (AVS) maakt voor on-premises VMware-Vm's met Azure Migrate: Server evaluatie.

[Azure migrate](migrate-services-overview.md) helpt u bij het migreren naar Azure. Azure Migrate biedt een gecentraliseerde hub voor het bijhouden van detectie, beoordeling en migratie van on-premises infra structuur, toepassingen en gegevens naar Azure. De hub biedt Azure-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV-aanbiedingen (Independent Software Vendor) van derden.

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](./create-manage-projects.md) .
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) .
- Als u een beoordeling wilt maken, moet u een Azure Migrate apparaat voor [VMware](how-to-set-up-appliance-vmware.md)instellen, dat de on-premises machines detecteert en de meta gegevens en prestaties naar Azure migrate verzendt: Server Assessment. [Meer informatie](migrate-appliance.md).
- U kunt [de meta gegevens van de server ook importeren](./tutorial-discover-import.md) in CSV-indeling (door komma's gescheiden waarden).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Overzicht van evaluatie van Azure VMware-oplossing (AVS)

Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate: Server-evaluatie.

**Evaluatietype** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. <br/><br/> U kunt uw on-premises [virtuele VMware-machines](how-to-set-up-appliance-vmware.md), [virtuele Hyper-V-machines](how-to-set-up-appliance-hyper-v.md)en [fysieke servers](how-to-set-up-appliance-physical.md) voor migratie naar Azure evalueren met dit beoordelings type. [Meer informatie](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> De evaluatie versie van Azure VMware Solution (AVS) is momenteel in Preview en kan alleen worden gemaakt voor virtuele VMware-machines.


Er zijn twee soorten grootte criteria die u kunt gebruiken om de evaluaties van Azure VMware-oplossingen (AVS) te maken:

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens van on-premises Vm's. | **Aanbevolen knooppunt grootte**: op basis van het CPU-en geheugen gebruik gegevens, samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen knooppunt grootte**: op basis van de on-PREMISes VM-grootte samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Een evaluatie van de Azure VMware-oplossing (AVS) uitvoeren

Voer de evaluatie van de Azure VMware-oplossing (AVS) als volgt uit:

1. Bekijk de [best practices](best-practices-assessment.md) voor het maken van evaluaties.

2. Op het tabblad **Servers** in de tegel **Azure Migrate: Serverevaluatie**, klikt u op **Evalueren**.

    ![Scherm afbeelding toont Azure Migrate servers met beoordeling geselecteerd onder evaluatie hulpprogramma's.](./media/how-to-create-assessment/assess.png)

3. In **servers beoordelen** selecteert u het beoordelings type als ' Azure VMware-oplossing (AVS) ', selecteert u de detectie bron en geeft u de naam van de evaluatie op.

    ![Basisprincipes evaluatie](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Eigenschappen van AVS-evaluatie](./media/how-to-create-avs-assessment/avs-view-all.png)

5. Klik op **volgende** om **machines te selecteren om te evalueren**. In **Een groep selecteren of maken** selecteert u **Nieuwe maken** en geeft u een groepsnaam op. Een groep verzamelt een of meer VM's voor evaluatie.

6. Selecteer in **Machines toevoegen aan de groep** de VM's die aan de groep moeten worden toegevoegd.

7. Klik op **volgende** en **Controleren en evaluatie maken** om de details van de evaluatie te controleren.

8. Klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    ![Een AVS-evaluatie maken](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Servers** > **Azure Migrate: Serverevaluatie** > **Evaluaties**.

10. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Een evaluatie van een Azure VMware-oplossing (AVS) controleren

Een evaluatie van de Azure VMware-oplossing (AVS) beschrijft:

- **Gereedheid voor Azure VMware-oplossing (AVS)**: of de on-premises vm's geschikt zijn voor migratie naar de Azure VMware-oplossing (AVS).
- **Aantal AVS-knoop punten**: het geschatte aantal AVS-knoop punten dat is vereist voor het uitvoeren van de vm's.
- **Gebruik over AVS-knoop punten**: geprojecteerde CPU, geheugen en opslag gebruik op alle knoop punten.
- **Schatting van maandelijkse kosten**: de geschatte maandelijkse kosten voor alle knoop punten van de Azure VMware-oplossing (AVS) die de on-premises vm's uitvoeren.


### <a name="view-an-assessment"></a>Een evaluatie weergeven

1. Klik in **Migratiedoelen** >  **Servers**, op **Evaluaties** in **Azure Migrate: Serverevaluatie**.

2. Klik in **Evaluaties** op een evaluatie om deze te openen.

    ![Overzicht van AVS-evaluatie](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>Gereedheid voor de Azure VMware-oplossing (AVS) controleren

1. Controleer in **Azure Readiness** of de vm's gereed zijn voor migratie naar AVS.

2. Controleer de VM-status:
    - **Gereed voor AVS**: de machine kan worden gemigreerd naar Azure (AVS) zonder wijzigingen. Deze wordt gestart in AVS met volledige AVS-ondersteuning.
    - **Klaar met voor waarden**: de virtuele machine heeft mogelijk compatibiliteits problemen met de huidige vSphere-versie en vereist mogelijk VMware-hulpprogram ma's en of andere instellingen voordat de volledige functionaliteit van de virtuele machine kan worden bereikt in AVS.
    - **Niet gereed voor AVS**: de VM wordt niet gestart in AVS. Als de on-premises VMware-VM bijvoorbeeld een extern apparaat heeft dat is aangesloten, zoals een cd-rom, mislukt de bewerking van VMotion (als VMware VMotion) wordt gebruikt.
    - **Gereedheid onbekend**: Azure migrate kan de gereedheid van de machine niet bepalen vanwege onvoldoende meta gegevens die zijn verzameld uit de on-premises omgeving.

3. Bekijk het voorgestelde hulp programma:
    - **VMware HCX of ENTER prise**: voor VMware-machines is de VMware Hybrid Cloud extension (HCX)-oplossing het aanbevolen migratie programma voor het migreren van uw on-premises werk belasting naar uw Azure VMware-oplossing (AVS) Private Cloud. [Meer informatie](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Onbekend**: Voor machines die zijn geïmporteerd via een CSV-bestand, is het standaardhulpprogramma voor migratie onbekend. Voor VMware-machines wordt echter aanbevolen om de VMWare HCX-oplossing (Hybrid Cloud Extension) te gebruiken. 

4. Klik op een **AVS-gereedheids** status. U kunt details van de VM-gereedheid weergeven en inzoomen op de details van de VM, inclusief berekenings-, opslag- en netwerkinstellingen.



### <a name="review-cost-details"></a>Gedetailleerde kosten beoordelen

In deze weer gave worden de geschatte kosten van het uitvoeren van Vm's in de Azure VMware-oplossing (AVS) weer gegeven.

1. Controleer de maandelijkse totale kosten. De kosten worden geaggregeerd voor alle VM's in de geëvalueerde groep. 

    - Kosten ramingen zijn gebaseerd op het aantal AVS-knoop punten dat is vereist bij het overwegen van de resource vereisten van alle virtuele machines in totaal.
    - Naarmate de prijzen voor de Azure VMware-oplossing (AVS) per knoop punt zijn, heeft de totale kosten geen reken kosten en de distributie van de opslag kosten.
    - De schatting van de kosten is voor het uitvoeren van de on-premises virtuele machines in AVS. Azure Migrate Serverevaluatie houdt geen rekening met PaaS-of SaaS-kosten.
    
2. U kunt de schatting van de maandelijkse opslagkosten controleren. In deze weergave worden geaggregeerde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven.

3. U kunt inzoomen om de details voor specifieke VM's te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u evaluaties op basis van prestaties uitvoert, wordt er een betrouwbaarheidsclassificatie aan de evaluatie toegewezen.

![Betrouwbaarheidsclassificatie](./media/how-to-create-assessment/confidence-rating.png)

- Er wordt een classificatie van 1 ster (laagst) tot 5 sterren (hoogst) toegekend.
- De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die de evaluatie geeft.
- De betrouwbaarheidsclassificatie is op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.
- Voor de grootte van op basis van prestaties heeft AVS-evaluaties in Server evaluatie de gebruiks gegevens voor CPU-en VM-geheugen nodig. De volgende prestatie gegevens worden verzameld, maar niet gebruikt in aanbevelingen voor het aanpassen van de AVS-evaluaties:
  - De schijf-IOPS en doorvoer gegevens voor elke schijf die aan de VM is gekoppeld.
  - De netwerk-I/O voor het afhandelen van de grootte van de prestaties voor elke netwerk adapter die is gekoppeld aan een virtuele machine.

De betrouwbaarheidsclassificatie voor een evaluatie zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md) over prestatie gegevens 


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van [afhankelijkheids toewijzing](how-to-create-group-machine-dependencies.md) voor het maken van groepen met hoge betrouw baarheid.
- Meer [informatie](concepts-azure-vmware-solution-assessment-calculation.md) over hoe AVS-evaluaties worden berekend.