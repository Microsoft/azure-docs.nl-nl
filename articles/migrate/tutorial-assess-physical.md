---
title: Fysieke servers beoordelen voor migratie naar Azure met Azure Migrate-serverevaluatie
description: 'Beschrijft hoe u met behulp van de service Azure Migrate: Serverevaluatie on-premises fysieke servers kunt detecteren en evalueren voor migratie naar Azure.'
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: b8e59e96d5ecb65933120ecaa4aa43fe4d59d367
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567541"
---
# <a name="tutorial-assess-physical-servers-for-migration-to-azure"></a>Zelfstudie: Fysieke servers beoordelen voor migratie naar Azure

Als onderdeel van uw migratietraject naar Azure, beoordeelt u uw on-premises werkbelastingen om de gereedheid voor de cloud te meten, risico's in kaart te brengen en de kosten en complexiteit te ramen.

In dit artikel leest u hoe u on-premises fysieke servers kunt evalueren voor migratie naar Azure met behulp van het Azure Migrate: Serverevaluatieprogramma.


In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
- Voer een evaluatie uit op basis van metagegevens en configuratie-informatie van de machine.
- Voer een evaluatie uit op basis van prestatiegegevens.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken waar mogelijk de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

- Voordat u deze zelfstudie volgt om uw computers te evalueren voor migratie naar virtuele Azure-machines, moet u controleren of u de machines hebt gedetecteerd die u wilt beoordelen:
    - Volg [deze zelfstudie](tutorial-discover-physical.md) om machines te detecteren met behulp van het Azure Migrate-apparaat. 
    - Volg [deze zelfstudie](tutorial-discover-import.md) om machines te detecteren met behulp van een geïmporteerd CSV-bestand.
- Zorg ervoor dat de fysieke machines die u wilt evalueren geen Windows Server 2003 of SUSE Linux uitvoeren. Evaluatie van deze machines wordt niet ondersteund.


## <a name="decide-which-assessment-to-run"></a>Bepalen welke evaluatie moet worden uitgevoerd


Beslis of u een evaluatie wilt uitvoeren met behulp van de groottecriteria op basis van de configuratiegegevens/metagegevens van de machine die zonder aanpassingen on-premises worden verzameld of aan de hand van dynamische prestatiegegevens.

**Evaluatie** | **Details** | **Aanbeveling**
--- | --- | ---
**As-is on-premises** | Beoordeling op basis van configuratiegegevens/metagegevens van de machine.  | De aanbevolen grootte van virtuele machines is gebaseerd op de grootte van on-premises virtuele machines.<br/><br> Het aanbevolen Azure-schijftype is gebaseerd op de instelling die u selecteert bij het opslagtype voor de evaluatie.
**Op basis van prestaties** | Evaluaties op basis van verzamelde dynamische prestatiegegevens. | De aanbevolen grootte van de virtuele machine is gebaseerd op verbruiksgegevens voor de processor en het geheugen.<br/><br/> Het aanbevolen schijftype is gebaseerd op de IOPS en doorvoer van de on-premises schijven.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een evaluatie uit:

1. Klik op de pagina **Servers** > **Windows- en Linux-servers** op **Servers evalueren en migreren**.

   ![Locatie van de knop om servers te evalueren en migreren](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. In **Azure Migrate: Serverevaluatie** klikt u op **Evalueren**.

    ![Locatie van de knop Evalueren](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. Selecteer onder **Servers evalueren** > **Type evaluatie**, **Virtuele Azure-machine**.
4. In **Detectiebron**:

    - Als u machines hebt gedetecteerd met behulp van het apparaat, selecteert u **Machines die zijn gedetecteerd via een Azure Migrate-apparaat**.
    - Selecteer **Geïmporteerde machines** als u machines hebt gedetecteerd met behulp van een geïmporteerd CSV-bestand. 
    
1. Klik op **bewerken** om de eigenschappen van de evaluatie te bekijken.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Locatie van de knop bewerken om de eigenschappen van de beoordeling te bekijken":::

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
     - Selecteer onder **Criterium voor het aanpassen van de grootte** of u de evaluatie wilt baseren op configuratiegegevens/metagegevens van de machine of op gegevens op basis van de prestaties. Als u prestatiegegevens gebruikt:
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
    - Geef onder **Aanbieding** de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) op als u bent ingeschreven. Serverevaluatie schat de kosten voor die aanbieding.
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

1. In **machines selecteren om**  >  de **beoordelings naam** te beoordelen > een naam opgeven voor de evaluatie. 

1. In **selecteren of een groep maken** > selecteert u **nieuwe maken** en geeft u een groeps naam op. 
    
    :::image type="content" source="./media/tutorial-assess-physical/assess-group.png" alt-text="Virtuele machines toevoegen aan een groep":::


1. Selecteer het apparaat en selecteer de virtuele machines die u wilt toevoegen aan de groep. Klik op **Volgende**.


1. Controleer de details van de evaluatie onder **Controleren + evaluatie maken** en klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

1. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Servers** > **Azure Migrate: Serverevaluatie** > **Evaluaties**.

1. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.
    > [!NOTE]
    > Voor evaluaties op basis van prestaties raden we u aan om minstens een dag na het starten van de detectie te wachten voordat u een evaluatie maakt. Dit biedt tijd voor het verzamelen van betrouwbaarder prestatiegegevens. In het ideale geval wacht u na het starten van de detectie op de prestatieduur die u opgeeft (dag/week/maand) voor een classificatie met hoge betrouwbaarheid.

## <a name="review-an-assessment"></a>Een evaluatie beoordelen

Een evaluatie beschrijft het volgende:

- **Azure-gereedheid**: Hiermee wordt aangegeven of VM's geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: De geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

U geeft een evaluatie als volgt weer:

1. In **Servers** > **Azure Migrate: Serverevaluatie** klikt u op het nummer naast **Evaluaties**.
2. Selecteer in **Evaluaties** een evaluatie om deze te openen. Ter voorbeeld (schattingen en kosten alleen ter illustratie): 

    ![Evaluatie-overzicht](./media/tutorial-assess-physical/assessment-summary.png)

3. Controleer de samenvatting van de evaluatie. U kunt ook de evaluatie-eigenschappen bewerken of de evaluatie opnieuw berekenen.
 
 
### <a name="review-readiness"></a>Gereedheid controleren

1. Klik op **Azure-gereedheid**.
2. Controleer onder **Azure-gereedheid** de status van de virtuele machine:
    - **Gereed voor Azure**: Wordt gebruikt wanneer Azure Migrate een VM-grootte en schattingen voor de kosten aanraadt voor VM's in de evaluatie.
    - **Gereed met voorwaarden**: Geeft problemen en voorgesteld herstel weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgesteld herstel weer.
    - **Gereedheid onbekend**: Wordt gebruikt wanneer Azure Migrate de gereedheid niet kan evalueren vanwege problemen met de beschikbaarheid van gegevens.

3. Selecteer een status voor **Azure-gereedheid**. U kunt details van de VM-gereedheid weergeven. U kunt ook inzoomen op de details van de VM, met inbegrip van reken-, opslag- en netwerkinstellingen.

### <a name="review-cost-estimates"></a>Geschatte kosten

Het evaluatieoverzicht toont de geschatte reken- en opslagkosten om virtuele machines in Azure uit te voeren. 

1. Controleer de maandelijkse totale kosten. De kosten worden geaggregeerd voor alle VM's in de geëvalueerde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte-aanbevelingen voor een machine, de schijven en de eigenschappen ervan.
    - De geschatte maandelijkse kosten voor berekening en opslag worden weergegeven.
    - De schatting van de kosten is voor het uitvoeren van de on-premises virtuele machines op virtuele Azure-machines. De schatting houdt geen rekening met de kosten voor PaaS of SaaS.

2. Controleer de maandelijkse opslagkosten. In deze weergave worden de samengevoegde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven. 
3. U kunt inzoomen om de kostendetails voor specifieke VM's te bekijken.

### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Serverevaluatie wijst een betrouwbaarheidsclassificatie toe aan evaluaties op basis van prestaties. De classificatie is van één ster (laagste) tot vijf sterren (hoogste).

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-physical/confidence-rating.png)

De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte in de evaluatie. De classificatie is op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.

> [!NOTE]
> Betrouwbaarheidsclassificaties worden niet toegewezen als u een evaluatie maakt op basis van een CSV-bestand.

Betrouwbaarheidsclassificaties zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie](concepts-assessment-calculation.md#confidence-ratings-performance-based) over betrouwbaarheidsclassificaties.

## <a name="next-steps"></a>Volgende stappen

- Vind machineafhankelijkheden met behulp van [Toewijzing van afhankelijkheid](concepts-dependency-visualization.md).
- Stel [op agent gebaseerde](how-to-create-group-machine-dependencies.md) afhankelijkheidstoewijzing in.
