---
title: Een evaluatie van een Azure-VM maken met Azure Migrate server beoordeling | Microsoft Docs
description: Hierin wordt beschreven hoe u een evaluatie van een Azure-VM maakt met het hulp programma voor het evalueren van Azure Migrate-servers
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: cc7101c61db8f0863c3a16b1c17f04188f9bee4e
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754297"
---
# <a name="create-an-azure-vm-assessment"></a>Een Azure VM-evaluatie maken

In dit artikel wordt beschreven hoe u een Azure VM-evaluatie maakt voor on-premises virtuele VMware-machines of virtuele Hyper-V-machines met Azure Migrate: Server evaluatie.

[Azure migrate](migrate-services-overview.md) helpt u bij het migreren naar Azure. Azure Migrate biedt een gecentraliseerde hub voor het bijhouden van detectie, beoordeling en migratie van on-premises infra structuur, toepassingen en gegevens naar Azure. De hub biedt Azure-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV-aanbiedingen (Independent Software Vendor) van derden. 

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](./create-manage-projects.md) .
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) .
- Als u een beoordeling wilt maken, moet u een Azure Migrate apparaat instellen voor [VMware](how-to-set-up-appliance-vmware.md) of [Hyper-V](how-to-set-up-appliance-hyper-v.md). Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie. [Meer informatie](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Overzicht van de evaluatie van Azure VM
Er zijn twee soorten grootte criteria die u kunt gebruiken om een Azure VM-evaluatie te maken met behulp van Azure Migrate: Server evaluatie.

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte**: Gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf)** : Op basis van de IOPS en doorvoer van de on-premises schijven.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen VM-grootte**: Op basis van de on-premises VM-grootte<br/><br> **Aanbevolen schijftype**: Op basis van de instelling voor het opslagtype die u voor de evaluatie selecteert.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een evaluatie uit:

1. Bekijk de [best practices](best-practices-assessment.md) voor het maken van evaluaties.
2. Op het tabblad **Servers** in de tegel **Azure Migrate: Serverevaluatie**, klikt u op **Evalueren**.

    ![Scherm afbeelding toont Azure Migrate servers met beoordeling geselecteerd onder evaluatie hulpprogramma's.](./media/how-to-create-assessment/assess.png)

3. Selecteer bij **Servers evalueren** het evaluatietype 'Azure VM', kies de detectiebron en geef de evaluatienaam op.

    ![Basisprincipes evaluatie](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Evaluatie-eigenschappen](./media/how-to-create-assessment//view-all.png)

5. Klik op **volgende** om **machines te selecteren om te evalueren**. In **Een groep selecteren of maken** selecteert u **Nieuwe maken** en geeft u een groepsnaam op. Een groep verzamelt een of meer VM's voor evaluatie.
6. Selecteer in **Machines toevoegen aan de groep** de VM's die aan de groep moeten worden toegevoegd.
7. Klik op **volgende** en **Controleren en evaluatie maken** om de details van de evaluatie te controleren.
8. Klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    ![Een evaluatie maken](./media/how-to-create-assessment//assessment-create.png)

9. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Servers** > **Azure Migrate: Serverevaluatie** > **Evaluaties**.
10. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.



## <a name="review-an-azure-vm-assessment"></a>Een Azure VM-evaluatie controleren

Een Azure VM-evaluatie beschrijft:

- **Azure-gereedheid**: Hiermee wordt aangegeven of VM's geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: De geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

### <a name="view-an-azure-vm-assessment"></a>Een Azure VM-evaluatie weer geven

1. Klik in **Migratiedoelen** >  **Servers**, op **Evaluaties** in **Azure Migrate: Serverevaluatie**.
2. Klik in **Evaluaties** op een evaluatie om deze te openen.

    ![Evaluatie-overzicht](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid beoordelen

1. Controleer in **Azure-gereedheid** of de VM's gereed zijn voor migratie naar Azure.
2. Controleer de VM-status:
    - **Gereed voor Azure**: Azure Migrate raadt een VM-grootte en schattingen voor de kosten aan voor VM's in de evaluatie.
    - **Gereed met voorwaarden**: Geeft problemen en voorgesteld herstel weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgesteld herstel weer.
    - **Gereedheid onbekend**: Wordt gebruikt wanneer Azure Migrate de gereedheid niet kan evalueren door problemen met de beschikbaarheid van gegevens.

3. Klik op een **Azure-gereedheid**-status. U kunt details van de VM-gereedheid weergeven en inzoomen op de details van de VM, inclusief berekenings-, opslag- en netwerkinstellingen.



### <a name="review-cost-details"></a>Gedetailleerde kosten beoordelen

Deze weergave toont de geschatte berekenings- en opslagkosten om virtuele machines in Azure uit te voeren.

1. Controleer de maandelijkse berekenings- en opslagkosten. De kosten worden geaggregeerd voor alle VM's in de geëvalueerde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte-aanbevelingen voor een machine en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor berekening en opslag worden weergegeven.
    - De schatting van de kosten is voor het uitvoeren van de on-premises VM's als IaaS-VM's. Azure Migrate Serverevaluatie houdt geen rekening met PaaS-of SaaS-kosten.

2. U kunt de schatting van de maandelijkse opslagkosten controleren. In deze weergave worden geaggregeerde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven.
3. U kunt inzoomen om de details voor specifieke VM's te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u evaluaties op basis van prestaties uitvoert, wordt er een betrouwbaarheidsclassificatie aan de evaluatie toegewezen.

![Betrouwbaarheidsclassificatie](./media/how-to-create-assessment/confidence-rating.png)

- Er wordt een classificatie van 1 ster (laagst) tot 5 sterren (hoogst) toegekend.
- De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die de evaluatie geeft.
- De betrouwbaarheidsclassificatie is op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.

De betrouwbaarheidsclassificatie voor een evaluatie zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren




## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van [afhankelijkheids toewijzing](how-to-create-group-machine-dependencies.md) voor het maken van groepen met hoge betrouw baarheid.
- [Meer informatie](concepts-assessment-calculation.md) over hoe evaluaties worden berekend.