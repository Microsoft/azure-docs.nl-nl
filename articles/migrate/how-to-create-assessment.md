---
title: Een Azure VM-evaluatie maken met Azure Migrate hulp programma voor detectie en evaluatie | Microsoft Docs
description: Hierin wordt beschreven hoe u een evaluatie van een Azure-VM maakt met het hulp programma voor detectie en evaluatie van Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: f901fe23d2ff04e7ad9ba920dd90ebab8a39246c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786716"
---
# <a name="create-an-azure-vm-assessment"></a>Een Azure VM-evaluatie maken

In dit artikel wordt beschreven hoe u een evaluatie van een Azure-VM maakt voor on-premises servers in uw VMware-, Hyper-V-of fysieke/andere cloud omgeving met Azure Migrate: detectie en evaluatie.

[Azure migrate](migrate-services-overview.md) helpt u bij het migreren naar Azure. Azure Migrate biedt een gecentraliseerde hub voor het bijhouden van detectie, beoordeling en migratie van on-premises infra structuur, toepassingen en gegevens naar Azure. De hub biedt Azure-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV-aanbiedingen (Independent Software Vendor) van derden. 

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](./create-manage-projects.md) .
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het hulp programma Azure Migrate: detectie en evaluatie hebt [toegevoegd](how-to-assess.md) .
- Als u een beoordeling wilt maken, moet u een Azure Migrate apparaat instellen voor [VMware](how-to-set-up-appliance-vmware.md) of [Hyper-V](how-to-set-up-appliance-hyper-v.md). Het apparaat detecteert on-premises servers en stuurt meta gegevens en prestatie gegevens naar Azure Migrate: detectie en evaluatie. [Meer informatie](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Overzicht van de evaluatie van Azure VM
Er zijn twee soorten grootte criteria die u kunt gebruiken om een Azure VM-evaluatie te maken met behulp van Azure Migrate: detectie en evaluatie.

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte**: Gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf)** : Op basis van de IOPS en doorvoer van de on-premises schijven.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen VM-grootte**: Op basis van de on-premises VM-grootte<br/><br> **Aanbevolen schijftype**: Op basis van de instelling voor het opslagtype die u voor de evaluatie selecteert.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een evaluatie uit:

1. Klik op de pagina **overzicht** > **Windows, Linux en SQL Server** op **servers evalueren en migreren**.

   ![Locatie van de knop om servers te evalueren en migreren](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. In **Azure migrate: detectie en evaluatie** klikt u op **evalueren** en selecteert u **Azure VM**

    ![Locatie van de knop Evalueren](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. In beoordeling van **Server**  >  **evaluatie type**
4. In **Detectiebron**:

    - Als u servers hebt gedetecteerd met behulp van het apparaat, selecteert u servers die zijn **gedetecteerd vanaf Azure migrate apparaat**.
    - Als u servers met een geïmporteerd CSV-bestand hebt gedetecteerd, selecteert u **geïmporteerde servers**. 
    
1. Klik op **bewerken** om de eigenschappen van de evaluatie te bekijken.

    ![Locatie van de knop Alle weergeven om de eigenschappen van de evaluatie te bekijken](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

1. In **Evaluatie-eigenschappen** > **Doeleigenschappen**:
    - Geef onder **Doelregio** de Azure-regio op waarnaar u de migratie wilt uitvoeren.
        - De aanbevelingen voor grootte en kosten zijn gebaseerd op de locatie die u opgeeft. Zodra u de doel locatie van standaard wijzigt, wordt u gevraagd om **gereserveerde instanties** en **VM-serie** op te geven.
        - In Azure Government kunt u evaluaties richten op [deze regio's](migrate-support-matrix.md#supported-geographies-azure-government)
    - In **Opslagtype**,
        - Als u gegevens op basis van prestaties in de evaluatie wilt gebruiken, selecteert u **Automatisch** zodat Azure Migrate een opslagtype aanbeveelt op basis van de IOPS en doorvoer van de schijf.
        - U kunt ook het opslagtype selecteren dat u wilt gebruiken voor de virtuele machine wanneer u deze migreert.
    - Geef in **Gereserveerde instanties** op of u reserveringsinstanties voor de virtuele machine wilt gebruiken wanneer u deze migreert.
        - Als u voor het gebruik van een gereserveerde instantie kiest, kunt u niets opgeven voor **Korting (%)** of **VM-uptime**. 
        - [Meer informatie](https://aka.ms/azurereservedinstances).
 1. In **VM-grootte**:
     - Onder **grootte criterium** selecteert u of u de evaluatie wilt baseren op server configuratie gegevens/meta gegevens of op prestatie gegevens. Als u prestatiegegevens gebruikt:
        - Geef onder **Prestatiegeschiedenis** de gegevensduur aan waarop u de evaluatie wilt baseren
        - Geef onder **Percentielgebruik** de percentielwaarde op die u wilt gebruiken voor de prestatiesteekproef. 
    - Geef onder **VM-reeks** de reeks virtuele Azure-machines op waarop u zich wilt baseren.
        - Als u een evaluatie op basis van prestaties gebruikt, stelt Azure Migrate een waarde voor u voor.
        - Pas de instellingen zo nodig aan. Als u bijvoorbeeld geen productieomgeving hebt die naar de A-serie van virtuele machines in Azure gaat, kunt u de A-serie uitsluiten van de reeks.
    - Geef onder **Comfortfactor** de buffer op die u tijdens de evaluatie wilt gebruiken. Deze houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst. Als u bijvoorbeeld een comfortfactor van twee gebruikt:
    
        **Onderdeel** | **Effectief gebruik** | **Comfortfactor toevoegen (2,0)**
        --- | --- | ---
        Kernen | 2  | 4
        Geheugen | 8 GB | 16 GB
   
1. Onder **Prijzen**:
    - Geef onder **Aanbieding** de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) op als u bent ingeschreven. De evaluatie maakt een schatting van de kosten voor die aanbieding.
    - Selecteer bij **Valuta** de factureringsvaluta voor uw account.
    - Voeg onder **Korting (%)** een abonnementspecifieke korting toe die u bovenop de Azure-aanbieding ontvangt. De standaardinstelling is 0%.
    - Geef onder **Actieve tijdsduur van de VM** het aantal dagen per maand/uren per dag op dat virtuele machines worden uitgevoerd.
        - Dit is handig voor virtuele Azure-machines die niet continu worden uitgevoerd.
        - Kostenramingen zijn gebaseerd op de opgegeven duur.
        - De standaardwaarde is 31 dagen per maand en 24 uur per dag.
    - Geef onder **EA-abonnement** op of u rekening wilt houden met een EA-abonnementskorting (Enterprise Overeenkomst) voor de schatting van de kosten. 
    - Geef onder **Azure Hybrid Benefit** op of u al een Windows Server-licentie hebt. Als u dit wel doet en de actieve softwaregarantie van Windows Server-abonnementen deze dekt, kunt u [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) aanvragen wanneer u licenties naar Azure brengt.

1. Klik op **Opslaan** als u wijzigingen aanbrengt.

    ![Evaluatie-eigenschappen](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. In **servers beoordelen** > klikt u op **volgende**.

1. In **servers selecteren om**  >  de **beoordelings naam** te beoordelen > een naam opgeven voor de evaluatie. 

1. In **selecteren of een groep maken** > selecteert u **nieuwe maken** en geeft u een groeps naam op. 
    
     ![Virtuele machines toevoegen aan een groep](./media/tutorial-assess-vmware-azure-vm/assess-group.png)


1. Selecteer het apparaat en selecteer de virtuele machines die u wilt toevoegen aan de groep. Klik op **Volgende**.


1. Controleer de details van de evaluatie onder **Controleren + evaluatie maken** en klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

1. Nadat de evaluatie is gemaakt, bekijkt u deze in **servers**  >  **Azure migrate: detectie-en evaluatie**-  >  **evaluaties**.

1. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.
    > [!NOTE]
    > Voor evaluaties op basis van prestaties raden we u aan om minstens een dag na het starten van de detectie te wachten voordat u een evaluatie maakt. Dit biedt tijd voor het verzamelen van betrouwbaarder prestatiegegevens. In het ideale geval wacht u na het starten van de detectie op de prestatieduur die u opgeeft (dag/week/maand) voor een classificatie met hoge betrouwbaarheid.

## <a name="review-an-azure-vm-assessment"></a>Een Azure VM-evaluatie controleren

Een Azure VM-evaluatie beschrijft:

- **Azure-gereedheid**: Hiermee wordt aangegeven of servers geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: De geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

### <a name="view-an-azure-vm-assessment"></a>Een Azure VM-evaluatie weer geven

1. Klik in **Windows, Linux en SQL Server**  >  **Azure migrate: detectie en evaluatie** op het nummer naast de **Azure VM-evaluatie**.
2. Selecteer in **Evaluaties** een evaluatie om deze te openen. Ter voorbeeld (schattingen en kosten alleen ter illustratie): 

    ![Evaluatie-overzicht](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid beoordelen

1. Controleer in **Azure Readiness** of servers gereed zijn voor migratie naar Azure.
2. Controleer de server status:
    - **Gereed voor Azure**: Azure Migrate raadt een VM-grootte en schattingen voor de kosten aan voor VM's in de evaluatie.
    - **Gereed met voorwaarden**: Geeft problemen en voorgesteld herstel weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgesteld herstel weer.
    - **Gereedheid onbekend**: Wordt gebruikt wanneer Azure Migrate de gereedheid niet kan evalueren door problemen met de beschikbaarheid van gegevens.

3. Klik op een **Azure-gereedheid**-status. U kunt details van de servergereedheid weergeven en inzoomen op de details van de server, inclusief berekenings-, opslag- en netwerkinstellingen.



### <a name="review-cost-details"></a>Gedetailleerde kosten beoordelen

Deze weergave toont de geschatte berekenings- en opslagkosten om virtuele machines in Azure uit te voeren.

1. Controleer de maandelijkse berekenings- en opslagkosten. De kosten worden geaggregeerd voor alle servers in de geëvalueerde groep.

    - Schattingen van de kosten zijn gebaseerd op de aanbevelingen voor de grootte van een server en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor berekening en opslag worden weergegeven.
    - De schatting van de kosten is voor het uitvoeren van de on-premises servers als IaaS-VM's. De Azure VM-evaluatie houdt geen rekening met de kosten voor PaaS of SaaS.

2. U kunt de schatting van de maandelijkse opslagkosten controleren. In deze weergave worden geaggregeerde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven.
3. U kunt inzoomen om de details voor specifieke servers te bekijken.


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