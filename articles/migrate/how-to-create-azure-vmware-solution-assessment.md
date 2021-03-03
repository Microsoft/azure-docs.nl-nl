---
title: Een AVS-evaluatie met Azure Migrate server evaluatie maken | Microsoft Docs
description: Hierin wordt beschreven hoe u een AVS-evaluatie maakt met het hulp programma voor het evalueren van Azure Migrate-servers
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e386db1ee2042d75a31d4a9de2a5174e904c6b5c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732969"
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
> De evaluatie van de Azure VMware-oplossing (AVS) kan alleen worden gemaakt voor virtuele VMware-machines.


Er zijn twee soorten grootte criteria die u kunt gebruiken om de evaluaties van Azure VMware-oplossingen (AVS) te maken:

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens van on-premises Vm's. | **Aanbevolen knooppunt grootte**: op basis van het CPU-en geheugen gebruik gegevens, samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen knooppunt grootte**: op basis van de on-PREMISes VM-grootte samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Een evaluatie van de Azure VMware-oplossing (AVS) uitvoeren

1. Klik op de pagina **Servers** > **Windows- en Linux-servers** op **Servers evalueren en migreren**.

   ![Locatie van de knop om servers te evalueren en migreren](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. In **Azure Migrate: Serverevaluatie** klikt u op **Evalueren**.

1. Selecteer in het  >  **beoordelings type** servers beoordelen de **Azure VMware-oplossing (AVS)**.

1. In **Detectiebron**:

    - Als u machines hebt gedetecteerd met behulp van het apparaat, selecteert u **Machines die zijn gedetecteerd via een Azure Migrate-apparaat**.
    - Selecteer **Geïmporteerde machines** als u machines hebt gedetecteerd met behulp van een geïmporteerd CSV-bestand. 
    
1. Klik op **bewerken** om de eigenschappen van de evaluatie te bekijken.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Pagina voor het selecteren van de evaluatie-instellingen":::
 

1. In **Evaluatie-eigenschappen** > **Doeleigenschappen**:

    - Geef onder **Doelregio** de Azure-regio op waarnaar u de migratie wilt uitvoeren.
       - De aanbevelingen voor grootte en kosten zijn gebaseerd op de locatie die u opgeeft.
   - Het **opslag type** wordt standaard ingesteld op **vSAN**. Dit is het standaard opslagtype voor een privécloud.
   - **Gereserveerde instanties** worden momenteel niet ondersteund voor AVS-knooppunten.
1. In **VM-grootte**:
    - Het **knooppunt type** wordt standaard ingesteld op **AV36**. Azure Migrate adviseert het knooppunt van knooppunten die nodig zijn om de virtuele machines naar AVS te migreren.
    - In de **instelling FTT, RAID-niveau**, selecteert u de combi natie van niet-toegestaan en RAID.  De geselecteerde FTT-optie bepaalt samen met de schijfvereiste voor de on-premises virtuele machine de totale vSAN-opslag die is vereist in AVS.
    - Geef bij **CPU-overbezetting** de verhouding op van virtuele kerngeheugens die zijn gekoppeld aan één fysiek kerngeheugen in het AVS-knooppunt. Een overschrijding van meer dan 4:1 kan leiden tot verminderde prestaties, maar kan worden gebruikt voor werkbelastingen van het type webserver.
    - Geef in **geheugen overdoorvoer factor** de verhouding van geheugen op dat op het cluster moet worden doorgevoerd. Een waarde van 1 betekent 100% geheugen gebruik, 0,5 bijvoorbeeld 50%, en 2 maakt gebruik van 200% van het beschik bare geheugen. U kunt alleen waarden van 0,5 tot en met 10 tot één decimaal positie toevoegen.
    - Geef bij ontdubbeling **en compressie factor** de verwachte ontdubbeling en compressie factor op voor uw workloads. De werkelijke waarde kan worden verkregen via on-premises vSAN of opslag configuratie. Dit kan per werk belasting verschillen. Een waarde van 3 zou betekenen dat er voor 300 GB schijf ruimte alleen 100 GB mag worden gebruikt. Een waarde van 1 betekent dat u geen duplicaten of compressie ontdubbelt. U kunt alleen waarden van 1 tot en met 10 tot één decimaal positie toevoegen.
1. In **Knooppuntgrootte**: 
    - Selecteer onder **Criterium voor het aanpassen van de grootte** of u de evaluatie wilt baseren op statische metagegevens of op gegevens op basis van de prestaties. Als u prestatiegegevens gebruikt:
        - Geef onder **Prestatiegeschiedenis** de gegevensduur aan waarop u de evaluatie wilt baseren
        - Geef onder **Percentielgebruik** de percentielwaarde op die u wilt gebruiken voor de prestatiesteekproef. 
    - Geef onder **Comfortfactor** de buffer op die u tijdens de evaluatie wilt gebruiken. Deze houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst. Als u bijvoorbeeld een comfortfactor van twee gebruikt:
    
        **Onderdeel** | **Effectief gebruik** | **Comfortfactor toevoegen (2,0)**
        --- | --- | ---
        Kernen | 2  | 4
        Geheugen | 8 GB | 16 GB  

1. Onder **Prijzen**:
    - In **Aanbieding** wordt de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarvoor u bent ingeschreven weergegeven; serverevaluatie schat de kosten voor die aanbieding.
    - Selecteer bij **Valuta** de factureringsvaluta voor uw account.
    - Voeg onder **Korting (%)** een abonnementspecifieke korting toe die u bovenop de Azure-aanbieding ontvangt. De standaardinstelling is 0%.

1. Klik op **Opslaan** als u wijzigingen aanbrengt.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Evaluatie-eigenschappen":::

1. Klik onder **Servers evalueren** op **Volgende**.

1. In **machines selecteren om**  >  de **beoordelings naam** te beoordelen > een naam opgeven voor de evaluatie. 
 
1. In **selecteren of een groep maken** > selecteert u **nieuwe maken** en geeft u een groeps naam op. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Virtuele machines toevoegen aan een groep":::
 
1. Selecteer het apparaat en selecteer de virtuele machines die u wilt toevoegen aan de groep. Klik op **Volgende**.

1. Controleer de details van de evaluatie onder **Controleren + evaluatie maken** en klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    > [!NOTE]
    > Voor evaluaties op basis van prestaties raden we u aan om minstens een dag na het starten van de detectie te wachten voordat u een evaluatie maakt. Dit biedt tijd voor het verzamelen van betrouwbaarder prestatiegegevens. In het ideale geval wacht u na het starten van de detectie op de prestatieduur die u opgeeft (dag/week/maand) voor een classificatie met hoge betrouwbaarheid.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Een evaluatie van een Azure VMware-oplossing (AVS) controleren

Een evaluatie van de Azure VMware-oplossing (AVS) beschrijft:

- **Gereedheid voor Azure VMware-oplossing (AVS)**: of de on-premises vm's geschikt zijn voor migratie naar de Azure VMware-oplossing (AVS).
- **Aantal AVS-knoop punten**: het geschatte aantal AVS-knoop punten dat is vereist voor het uitvoeren van de vm's.
- **Gebruik over AVS-knoop punten**: geprojecteerde CPU, geheugen en opslag gebruik op alle knoop punten.
    - Gebruik is inclusief de voor-factorisatie in de volgende overhead van Cluster beheer, zoals de vCenter Server, NSX Manager (groot), NSX Edge, als HCX is geïmplementeerd, ook de HCX Manager en IX toestellen die gebruikmaken van ~ 44vCPU (11 CPU), 75GB RAM en 722GB van opslag vóór compressie en ontdubbeling.
- **Schatting van maandelijkse kosten**: de geschatte maandelijkse kosten voor alle knoop punten van de Azure VMware-oplossing (AVS) die de on-premises vm's uitvoeren.


### <a name="view-an-assessment"></a>Een evaluatie weergeven

1. Klik in **Migratiedoelen** >  **Servers**, op **Evaluaties** in **Azure Migrate: Serverevaluatie**.

2. Klik in **Evaluaties** op een evaluatie om deze te openen.

    :::image type="content" source="./media/how-to-create-avs-assessment/avs-assessment-summary.png" alt-text="Overzicht van AVS-evaluatie":::

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
    - De schatting van de kosten is voor het uitvoeren van de on-premises virtuele machines in AVS. AVS-evaluatie houdt geen rekening met PaaS of SaaS-kosten.
    
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