---
title: VMware-servers beoordelen voor migratie naar Azure VMware-oplossing (AVS) met Azure Migrate
description: Meer informatie over het beoordelen van servers in VMware-omgevingen voor migratie naar AVS met Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 31bf3909012231996bd340cfa4d388f0fe20a4f5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782121"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Zelf studie: VMware-servers evalueren voor migratie naar AVS

Als onderdeel van uw migratietraject naar Azure, beoordeelt u uw on-premises werkbelastingen om de gereedheid voor de cloud te meten, risico's in kaart te brengen en de kosten en complexiteit te ramen.

Dit artikel laat u zien hoe u gedetecteerde virtuele VMware-machines/-servers kunt beoordelen voor migratie naar Azure VMware-oplossing (AVS) met behulp van de Azure Migrate. AVS is een beheerde service waarmee u het VMware-platform in Azure kunt uitvoeren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
- Voer een evaluatie uit op basis van meta gegevens van de server en configuratie-informatie.
- Voer een evaluatie uit op basis van prestatiegegevens.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken waar mogelijk de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.



## <a name="prerequisites"></a>Vereisten

Voordat u deze zelf studie volgt om uw servers te evalueren voor migratie naar AVS, moet u ervoor zorgen dat u de servers hebt gedetecteerd die u wilt beoordelen:

- [Volg deze zelf studie](tutorial-discover-vmware.md)om servers te detecteren met behulp van het Azure migrate apparaat. 
- [Volg deze zelf studie](tutorial-discover-import.md)om servers te detecteren met een geïmporteerd CSV-bestand.


## <a name="decide-which-assessment-to-run"></a>Bepalen welke evaluatie moet worden uitgevoerd


Beslis of u een evaluatie wilt uitvoeren met behulp van formaat criteria op basis van de server configuratie gegevens/meta gegevens die worden verzameld als-is on-premises of op dynamische prestatie gegevens.

**Evaluatie** | **Details** | **Aanbeveling**
--- | --- | ---
**As-is on-premises** | Beoordeling op basis van server configuratie gegevens/meta gegevens.  | De aanbevolen knooppunt grootte in AVS is gebaseerd op de on-premises VM/server grootte, samen met de instellingen die u opgeeft in de evaluatie van het knooppunt type, het opslag type en de instelling voor fout-op-afstand.
**Op basis van prestaties** | Evaluaties op basis van verzamelde dynamische prestatiegegevens. | De aanbevolen knooppuntgrootte in AVS is gebaseerd op de gegevens over het CPU- en geheugengebruik, samen met de instellingen die u opgeeft in de evaluatie van het knooppunttype, het opslagtype en de instelling voor fouttolerantie.

> [!NOTE]
> De evaluatie van de Azure VMware-oplossing (AVS) kan alleen worden gemaakt voor virtuele VMware-machines/-servers.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een evaluatie uit:

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

## <a name="review-an-assessment"></a>Een evaluatie beoordelen

Een AVS-evaluatie beschrijft het volgende:

- AVS-gereedheid: of de on-premises servers geschikt zijn voor migratie naar de Azure VMware-oplossing (AVS).
- Aantal AVS-knoop punten: het geschatte aantal AVS-knoop punten dat is vereist voor het uitvoeren van de-servers.
- Gebruik over AVS-knooppunten: Geprojecteerde CPU-, geheugen- en opslaggebruik op alle knooppunten.
    - Gebruik is een vooraf prefactorie in de volgende overhead van Cluster beheer, zoals de vCenter Server, NSX Manager (groot), NSX Edge, als HCX is geïmplementeerd, ook de HCX Manager en IX toestellen die ~ 44vCPU (11 CPU), 75GB aan RAM en 722GB aan opslag gebruiken vóór compressie en ontdubbeling. 
- Schatting van maandelijkse kosten: de geschatte maandelijkse kosten voor alle knoop punten van de Azure VMware-oplossing (AVS) die de on-premises servers uitvoeren.

## <a name="view-an-assessment"></a>Een evaluatie weergeven

U geeft een evaluatie als volgt weer:

1. In **Windows, Linux en SQL Server**  >  **Azure migrate: detectie en evaluatie** klikt u op het nummer naast * * Azure VMware-oplossing * *.

1. Selecteer in **Evaluaties** een evaluatie om deze te openen. Ter voorbeeld (schattingen en kosten alleen ter illustratie): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Overzicht van AVS-evaluatie":::

1. Controleer de samenvatting van de evaluatie. U kunt ook de evaluatie-eigenschappen bewerken of de evaluatie opnieuw berekenen.
 

### <a name="review-readiness"></a>Gereedheid controleren

1. Klik op **Azure-gereedheid**.
2. Controleer in **Azure Readiness** de gereedheids status.

    - **Gereed voor AVS**: de server kan zonder wijzigingen worden gemigreerd naar Azure AVS. De server wordt gestart in AVS, met volledige AVS-ondersteuning.
    - **Klaar met voor waarden**: de server heeft mogelijk compatibiliteits problemen met de huidige vSphere-versie. Mogelijk moeten VMware-hulpprogramma's worden geïnstalleerd of andere instellingen worden geconfigureerd voordat er sprake is van volledige functionaliteit in AVS.
    - **Niet gereed voor AVS**: De virtuele machine wordt niet gestart in AVS. Als er bijvoorbeeld een extern apparaat is gekoppeld aan een on-premises VMware-Server (zoals een CD-ROM) en u VMware VMotion gebruikt, mislukt de VMotion-bewerking.
 - **Gereedheid onbekend**: Azure migrate kan de server voorbereiding niet bepalen vanwege onvoldoende meta gegevens die zijn verzameld uit de on-premises omgeving.

3. Bekijk het voorgestelde hulpprogramma.

    - VMware HCX of ENTER prise: voor VMware-servers is de VMware Hybrid Cloud extension (HCX)-oplossing het aanbevolen migratie programma voor het migreren van uw on-premises werk belasting naar uw Azure VMware-oplossing (AVS) Private Cloud. Meer informatie
    - Onbekend: voor servers die worden geïmporteerd via een CSV-bestand, is het standaard hulp programma voor migratie onbekend. Voor VMware-servers wordt u aangeraden de VMware Hybrid Cloud extension (HCX)-oplossing te gebruiken.
4. Klik op een Azure-gereedheidsstatus. U kunt details van de servergereedheid weergeven en inzoomen op de details van de server, inclusief berekenings-, opslag- en netwerkinstellingen.

### <a name="review-cost-estimates"></a>Geschatte kosten

In de evaluatie samenvatting worden de geschatte reken-en opslag kosten van het uitvoeren van servers in azure weer gegeven. 

1. Controleer de maandelijkse totale kosten. De kosten worden geaggregeerd voor alle servers in de geëvalueerde groep.

    - Kosten ramingen zijn gebaseerd op het aantal AVS-knoop punten dat is vereist voor het overwegen van de resource vereisten van alle servers in totaal.
    - Omdat de prijzen voor AVS per knooppunt gelden, zijn de totale kosten zonder verdeling van de rekenkosten en opslagkosten.
    - De schatting van de kosten is voor het uitvoeren van de on-premises servers in AVS. AVS-evaluatie houdt geen rekening met PaaS of SaaS-kosten.

2. Controleer de geschatte de maandelijkse opslag. In deze weergave worden de samengevoegde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven. 
3. U kunt inzoomen om de details van de kosten voor specifieke servers te bekijken.

### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Serverevaluatie wijst een betrouwbaarheidsclassificatie toe aan evaluaties op basis van prestaties. De classificatie is van één ster (laagste) tot vijf sterren (hoogste).

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

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

- Server afhankelijkheden zoeken met [afhankelijkheids toewijzing](concepts-dependency-visualization.md).
- Stel [agentloze](how-to-create-group-machine-dependencies-agentless.md) of [op agent gebaseerde](how-to-create-group-machine-dependencies.md) afhankelijkheidstoewijzing in.
