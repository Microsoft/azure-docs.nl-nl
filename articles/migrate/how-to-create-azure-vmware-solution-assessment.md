---
title: Een AVS-evaluatie maken met Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u een AVS-evaluatie met Azure Migrate maakt
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 72372e6365a2535e449681549a515c3f8594f2f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786597"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Een evaluatie van een Azure VMware-oplossing (AVS) maken

In dit artikel wordt beschreven hoe u een evaluatie van een Azure VMware-oplossing (AVS) maakt voor on-premises servers in VMware-omgeving met Azure Migrate: detectie en evaluatie.

[Azure migrate](migrate-services-overview.md) helpt u bij het migreren naar Azure. Azure Migrate biedt een gecentraliseerde hub voor het bijhouden van detectie, beoordeling en migratie van on-premises infra structuur, toepassingen en gegevens naar Azure. De hub biedt Azure-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV-aanbiedingen (Independent Software Vendor) van derden.

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](./create-manage-projects.md) .
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het hulp programma Azure Migrate: detectie en evaluatie hebt [toegevoegd](how-to-assess.md) .
- Als u een evaluatie wilt maken, moet u een Azure Migrate apparaat voor [VMware](how-to-set-up-appliance-vmware.md)instellen, dat de on-premises servers detecteert en de meta gegevens en prestaties naar Azure migrate verzendt: detectie en evaluatie. [Meer informatie](migrate-appliance.md).
- U kunt [de meta gegevens van de server ook importeren](./tutorial-discover-import.md) in CSV-indeling (door komma's gescheiden waarden).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Overzicht van evaluatie van Azure VMware-oplossing (AVS)

Er zijn drie soorten evaluaties die u kunt maken met behulp van Azure Migrate: detectie en evaluatie.

***Beoordelings type** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. U kunt uw on-premises servers in [VMware](how-to-set-up-appliance-vmware.md) en [Hyper-V-](how-to-set-up-appliance-hyper-v.md) omgeving, en [fysieke servers](how-to-set-up-appliance-physical.md) voor migratie naar virtuele Azure-machines evalueren met dit beoordelings type.
**Azure SQL** | Beoordelingen voor het migreren van uw on-premises SQL-servers vanuit uw VMware-omgeving naar Azure SQL Database of Azure SQL Managed instance.
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). U kunt uw on-premises servers in de [VMware-omgeving](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware-oplossing (AVS) met dit beoordelings type. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> De evaluatie van de Azure VMware-oplossing (AVS) kan alleen worden gemaakt voor servers in de VMware-omgeving.


Er zijn twee soorten grootte criteria die u kunt gebruiken om de evaluaties van Azure VMware-oplossingen (AVS) te maken:

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens van on-premises servers. | **Aanbevolen knooppunt grootte**: op basis van het CPU-en geheugen gebruik gegevens, samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen knooppunt grootte**: op basis van de on-premises server grootte samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Een evaluatie van de Azure VMware-oplossing (AVS) uitvoeren

1.  Klik op de pagina **overzicht** > **Windows, Linux en SQL Server** op **servers evalueren en migreren**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Overzichts pagina voor Azure Migrate":::

1. Klik in **Azure migrate: detectie en evaluatie** op **evalueren**.

   ![De positie van de knop bepalen](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. Selecteer in het  >  **beoordelings type** servers beoordelen de **Azure VMware-oplossing (AVS)**.

1. In **Detectiebron**:

    - Als u servers hebt gedetecteerd met behulp van het apparaat, selecteert u servers die zijn **gedetecteerd vanaf Azure migrate apparaat**.
    - Als u servers met een geïmporteerd CSV-bestand hebt gedetecteerd, selecteert u **geïmporteerde servers**. 
    
1. Klik op **bewerken** om de eigenschappen van de evaluatie te bekijken.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Pagina voor het selecteren van de evaluatie-instellingen":::
 

1. In **Evaluatie-eigenschappen** > **Doeleigenschappen**:

    - Geef onder **Doelregio** de Azure-regio op waarnaar u de migratie wilt uitvoeren.
       - De aanbevelingen voor grootte en kosten zijn gebaseerd op de locatie die u opgeeft.
   - Het **opslag type** wordt standaard ingesteld op **vSAN**. Dit is het standaard opslagtype voor een privécloud.
   - **Gereserveerde instanties** worden momenteel niet ondersteund voor AVS-knooppunten.
1. In **VM-grootte**:
    - Het **knooppunt type** wordt standaard ingesteld op **AV36**. Azure Migrate adviseert het knoop punt van knoop punten die nodig zijn om de servers te migreren naar AVS.
    - In de **instelling FTT, RAID-niveau**, selecteert u de combi natie van niet-toegestaan en RAID.  De geselecteerde FTT-optie, gecombineerd met de on-premises server vereiste schijf, bepaalt de totale vSAN-opslag die is vereist in AVS.
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
    - In de **aanbieding** wordt de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) die u hebt Inge schreven, weer gegeven. De evaluatie maakt een schatting van de kosten voor die aanbieding.
    - Selecteer bij **Valuta** de factureringsvaluta voor uw account.
    - Voeg onder **Korting (%)** een abonnementspecifieke korting toe die u bovenop de Azure-aanbieding ontvangt. De standaardinstelling is 0%.

1. Klik op **Opslaan** als u wijzigingen aanbrengt.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Evaluatie-eigenschappen":::

1. Klik onder **Servers evalueren** op **Volgende**.

1. In **servers selecteren om**  >  de **beoordelings naam** te beoordelen > een naam opgeven voor de evaluatie. 
 
1. In **selecteren of een groep maken** > selecteert u **nieuwe maken** en geeft u een groeps naam op. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Servers toevoegen aan een groep":::
 
1. Selecteer het apparaat en selecteer de servers die u aan de groep wilt toevoegen. Klik op **Volgende**.

1. Controleer de details van de evaluatie onder **Controleren + evaluatie maken** en klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    > [!NOTE]
    > Voor evaluaties op basis van prestaties raden we u aan om minstens een dag na het starten van de detectie te wachten voordat u een evaluatie maakt. Dit biedt tijd voor het verzamelen van betrouwbaarder prestatiegegevens. In het ideale geval wacht u na het starten van de detectie op de prestatieduur die u opgeeft (dag/week/maand) voor een classificatie met hoge betrouwbaarheid.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Een evaluatie van een Azure VMware-oplossing (AVS) controleren

Een evaluatie van de Azure VMware-oplossing (AVS) beschrijft:

- **Gereedheid voor Azure VMware-oplossing (AVS)**: of de on-premises servers geschikt zijn voor migratie naar de Azure VMware-oplossing (AVS).
- **Aantal AVS-knoop punten**: het geschatte aantal AVS-knoop punten dat is vereist voor het uitvoeren van de-servers.
- **Gebruik over AVS-knoop punten**: geprojecteerde CPU, geheugen en opslag gebruik op alle knoop punten.
    - Gebruik is inclusief de voor-factorisatie in de volgende overhead van Cluster beheer, zoals de vCenter Server, NSX Manager (groot), NSX Edge, als HCX is geïmplementeerd, ook de HCX Manager en IX toestellen die gebruikmaken van ~ 44vCPU (11 CPU), 75GB RAM en 722GB van opslag vóór compressie en ontdubbeling.
- **Schatting van maandelijkse kosten**: de geschatte maandelijkse kosten voor alle knoop punten van de Azure VMware-oplossing (AVS) die de on-premises vm's uitvoeren.


### <a name="view-an-assessment"></a>Een evaluatie weergeven

1. In **Windows, Linux en SQL Server**  >  **Azure migrate: detectie en evaluatie** klikt u op het nummer naast * * Azure VMware-oplossing * *.

1. Selecteer in **Evaluaties** een evaluatie om deze te openen. Ter voorbeeld (schattingen en kosten alleen ter illustratie): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Overzicht van AVS-evaluatie":::

1. Controleer de samenvatting van de evaluatie. U kunt ook de evaluatie-eigenschappen bewerken of de evaluatie opnieuw berekenen.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Gereedheid voor de Azure VMware-oplossing (AVS) controleren

1. Controleer in **Azure Readiness** of de servers gereed zijn voor migratie naar AVS.

2. Controleer de server status:
    - **Gereed voor AVS**: de server kan zonder wijzigingen worden gemigreerd naar Azure (AVS). Deze wordt gestart in AVS met volledige AVS-ondersteuning.
    - **Klaar met voor waarden**: de server heeft mogelijk compatibiliteits problemen met de huidige vSphere-versie en vereist mogelijk VMware-hulpprogram ma's en of andere instellingen voordat de volledige functionaliteit van de server in AVS kan worden bereikt.
    - **Niet gereed voor AVS**: de server kan niet worden gestart in AVS. Als de on-premises server bijvoorbeeld een extern apparaat heeft dat is aangesloten, zoals een cd-rom, mislukt de bewerking van VMotion (als VMware VMotion wordt gebruikt).
    - **Gereedheid onbekend**: Azure migrate kan de gereedheid van de server niet bepalen vanwege onvoldoende meta gegevens die zijn verzameld uit de on-premises omgeving.

3. Bekijk het voorgestelde hulp programma:
    - **VMware HCX of ENTER prise**: voor VMware-servers is de VMware Hybrid Cloud extension (HCX)-oplossing het aanbevolen migratie programma voor het migreren van uw on-premises werk belasting naar uw Azure VMware-oplossing (AVS) Private Cloud. [Meer informatie](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Onbekend**: voor servers die worden geïmporteerd via een CSV-bestand, is het standaard hulp programma voor migratie onbekend. Voor VMware-servers wordt u aangeraden de VMware Hybrid Cloud extension (HCX)-oplossing te gebruiken. 

4. Klik op een **AVS-gereedheids** status. U kunt details van de VM-gereedheid weergeven en inzoomen op de details van de VM, inclusief berekenings-, opslag- en netwerkinstellingen.

### <a name="review-cost-details"></a>Gedetailleerde kosten beoordelen

In deze weer gave worden de geschatte kosten van het uitvoeren van servers in de Azure VMware-oplossing (AVS) weer gegeven.

1. Controleer de maandelijkse totale kosten. De kosten worden geaggregeerd voor alle servers in de geëvalueerde groep. 

    - Kosten ramingen zijn gebaseerd op het aantal AVS-knoop punten dat is vereist voor het overwegen van de resource vereisten van alle servers in totaal.
    - Naarmate de prijzen voor de Azure VMware-oplossing (AVS) per knoop punt zijn, heeft de totale kosten geen reken kosten en de distributie van de opslag kosten.
    - De schatting van de kosten is voor het uitvoeren van de on-premises servers in AVS. AVS-evaluatie houdt geen rekening met PaaS of SaaS-kosten.
    
2. U kunt de schatting van de maandelijkse opslagkosten controleren. In deze weergave worden geaggregeerde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven.

3. U kunt inzoomen om de details voor specifieke servers te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u evaluaties op basis van prestaties uitvoert, wordt er een betrouwbaarheidsclassificatie aan de evaluatie toegewezen.

![Betrouwbaarheidsclassificatie](./media/how-to-create-assessment/confidence-rating.png)

- Er wordt een classificatie van 1 ster (laagst) tot 5 sterren (hoogst) toegekend.
- De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die de evaluatie geeft.
- De betrouwbaarheidsclassificatie is op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.
- Voor de grootte van op basis van prestaties hebben AVS-evaluaties de gebruiks gegevens voor CPU-en server geheugen nodig. De volgende prestatie gegevens worden verzameld, maar niet gebruikt in aanbevelingen voor het aanpassen van de AVS-evaluaties:
  - De schijf-IOPS en doorvoer gegevens voor elke schijf die aan de server is gekoppeld.
  - De netwerk-I/O voor het afhandelen van de grootte van de prestaties voor elke netwerk adapter die is gekoppeld aan een server.

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