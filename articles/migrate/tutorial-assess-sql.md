---
title: Zelf studie voor het beoordelen van SQL-exemplaren voor migratie naar Azure SQL Managed instance en Azure SQL Database
description: Meer informatie over het maken van een evaluatie voor Azure SQL in Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: d4078d1403df01475c6055dded2bd012e97af98e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557986"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Zelf studie: SQL-exemplaren evalueren voor migratie naar Azure SQL

Als onderdeel van uw migratietraject naar Azure, beoordeelt u uw on-premises werkbelastingen om de gereedheid voor de cloud te meten, risico's in kaart te brengen en de kosten en complexiteit te ramen.
In dit artikel wordt beschreven hoe u gedetecteerde SQL Server instances-data bases in de voor bereiding van de migratie naar Azure SQL kunt beoordelen met behulp van de Azure Migrate: detectie en evaluatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voer een evaluatie uit op basis van de server configuratie en prestatie gegevens.
> * Een Azure SQL-evaluatie bekijken 

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken waar mogelijk de standaardopties. 


## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

- Voordat u deze zelf studie volgt om uw SQL Server-exemplaren te evalueren voor migratie naar Azure SQL, moet u ervoor zorgen dat u de SQL-exemplaren hebt gedetecteerd die u wilt beoordelen met behulp van het Azure Migrate apparaat. [Volg deze zelf studie](tutorial-discover-vmware.md)
- Als u deze functie in een bestaand project wilt uitproberen, moet u ervoor zorgen dat u de [vereisten](how-to-discover-sql-existing-project.md) in dit artikel hebt voltooid.


## <a name="run-an-assessment"></a>Een evaluatie uitvoeren
Voer als volgt een evaluatie uit:
1. Klik op de pagina **overzicht** > **Windows, Linux en SQL Server** op **servers evalueren en migreren**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Overzichts pagina voor Azure Migrate":::
2. Klik op **Azure migrate: detectie en evaluatie** op **beoordeling en** Kies het type evaluatie als **Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Vervolg keuzelijst om het beoordelings type te kiezen als Azure SQL":::
3. In **servers beoordelen** > kunt u het beoordelings type selecteren dat vooraf is geselecteerd als **Azure SQL** en de detectie bron die is standaard ingesteld op servers die zijn **gedetecteerd via Azure migrate apparaat**.

4. Klik op **bewerken** om de eigenschappen van de evaluatie te bekijken.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="De knop bewerken van waaruit de beoordelings eigenschappen kunnen worden aangepast":::
5. In de eigenschappen van de beoordeling > **doel eigenschappen**:
    - Geef onder **Doelregio** de Azure-regio op waarnaar u de migratie wilt uitvoeren. 
        - Azure SQL-configuratie en aanbevelingen voor kosten zijn gebaseerd op de locatie die u opgeeft. 
    - In het **type doel implementatie**,
        - Selecteer **Aanbevolen** als u Azure migrate wilt beoordelen van de gereedheid van uw SQL-instanties voor de migratie naar Azure SQL mi en Azure SQL DB, en u de beste geschikte optie voor de doel implementatie, doellaag, Azure SQL-configuratie en maandelijkse schattingen wilt aanraden. [Meer informatie](concepts-azure-sql-assessment-calculation.md)
        - Selecteer **Azure SQL DB** als u de gereedheid van uw SQL-instanties wilt beoordelen voor de migratie naar Azure SQL-data bases en controleer de doellaag, Azure SQL-configuratie en maandelijkse schattingen.
        - Selecteer **Azure SQL mi** als u de gereedheid van uw SQL-instanties wilt beoordelen voor migratie naar alleen-migreren naar Azure SQL Managed instance en controleer de doellaag, Azure SQL-configuratie en maandelijkse schattingen.
    - Geef bij **gereserveerde capaciteit** op of u gereserveerde capaciteit wilt gebruiken voor de SQL-Server na de migratie.
        - Als u een gereserveerde capaciteits optie selecteert, kunt u geen korting (%) opgeven.

6. In de beoordelings eigenschappen > **beoordelings criteria**:
    - De grootte criteria worden standaard ingesteld op **basis van prestaties** , wat betekent dat met Azure migrate prestatie gegevens worden verzameld die betrekking hebben op SQL-instanties en de data bases die worden beheerd door IT om een optimale Azure SQL database en/of Azure SQL Managed instance-configuratie te aanbevelen. U kunt het volgende opgeven:
        - De **prestatie geschiedenis** om de duur van de gegevens aan te geven waarop u de evaluatie wilt baseren. (De standaard waarde is een dag)
        - **Percentiel gebruik**, om de percentiel waarde aan te geven die u wilt gebruiken voor het voor beeld van de prestaties. (Standaard is het 95e percentiel)
    - Geef onder **Comfortfactor** de buffer op die u tijdens de evaluatie wilt gebruiken. Deze houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst. Als u bijvoorbeeld een comfortfactor van twee gebruikt: 
        
        **Onderdeel** | **Effectief gebruik** | **Comfortfactor toevoegen (2,0)**
        --- | --- | ---
        Kernen | 2  | 4
        Geheugen | 8 GB | 16 GB
   
7. Onder **Prijzen**:
    - Geef in het **programma aanbieding/licentie verlening** de Azure-aanbieding op als u bent Inge schreven. Op dit moment kunt u alleen kiezen uit betalen per gebruik en betalen naar gebruik-dev/test. 
        - U kunt extra korting verkrijgen door gereserveerde capaciteit en Azure Hybrid Benefit boven op de aanbieding voor betalen naar gebruik toe te passen. 
        - U kunt Azure Hybrid Benefit Toep assen op een dev/test met betalen per gebruik. De evaluatie biedt momenteel geen ondersteuning voor het Toep assen van gereserveerde capaciteit boven op de betalen per gebruik-Go/test-aanbieding.
    - Kies **in servicelaag** de meest geschikte optie voor de service tier om uw bedrijfs behoeften te kunnen migreren naar Azure SQL database en/of Azure SQL Managed instance: 
        - Selecteer **Aanbevolen** als u wilt dat Azure migrate de meest geschikte servicelaag aanbeveelt voor uw servers. Dit kan algemeen gebruik of bedrijfs kritiek zijn. Meer informatie
        - Selecteer **Algemeen** als u een Azure SQL-configuratie wilt die is ontworpen voor budget gerichte workloads.
        - Selecteer **bedrijfskritiek** als u een Azure SQL-configuratie wilt die is ontworpen voor workloads met lage latentie en hoge tolerantie voor fouten en snelle failovers.
    - Voeg onder **Korting (%)** een abonnementspecifieke korting toe die u bovenop de Azure-aanbieding ontvangt. De standaardinstelling is 0%.
    - Selecteer bij **Valuta** de factureringsvaluta voor uw account.
    - In **Azure Hybrid Benefit** geeft u op of u al een SQL Server licentie hebt. Als u dit wel doet en de actieve Software Assurance van SQL Server-abonnementen wordt gebruikt, kunt u voor de Azure Hybrid Benefit aanvragen wanneer u licenties naar Azure brengt.
    - Klik op Opslaan als u wijzigingen aanbrengt.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="De knop Opslaan in de eigenschappen van de beoordeling":::
8. In **servers beoordelen** > klikt u op volgende.
9.  In **servers selecteren om**  >  de **beoordelings naam** te beoordelen > een naam opgeven voor de evaluatie.
10. In **selecteren of een groep maken** > selecteert u **nieuwe maken** en geeft u een groeps naam op.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="De locatie van de knop nieuwe groep":::
11. Selecteer het apparaat en selecteer de servers die u aan de groep wilt toevoegen. Klik op Volgende.
12. Bekijk de evaluatie gegevens in beoordeling **+ Create-evaluatie** en klik op beoordeling maken om de groep te maken en de evaluatie uit te voeren.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Locatie van de knop beoordeling bekijken en maken.":::
13. Nadat de evaluatie is gemaakt, gaat u naar **Windows, Linux en SQL Server**  >  **Azure migrate: de tegel detectie en evaluatie** > klikt u op het nummer naast Azure SQL-evaluatie.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Navigatie naar gemaakte evaluatie":::
15. Klik op de naam van de beoordeling die u wilt weer geven.

> [!NOTE]
> Als Azure SQL-evaluaties zijn evaluaties op basis van prestaties, raden we u aan om ten minste een dag na het starten van de detectie te wachten voordat u een evaluatie maakt. Dit biedt tijd voor het verzamelen van betrouwbaarder prestatiegegevens. Als uw detectie nog wordt uitgevoerd, wordt de gereedheid van uw SQL-instanties als **onbekend** gemarkeerd. In het ideale geval moet u na **het starten van de detectie wachten op de prestatie duur die u opgeeft (dag/week/maand)** om de beoordeling voor een classificatie met hoge betrouw baarheid te maken of opnieuw te berekenen. 

## <a name="review-an-assessment"></a>Een evaluatie beoordelen

**Een evaluatie weer geven**:

1. **Windows, Linux en SQL Server**  >  **Azure migrate: detectie en evaluatie** > Klik op het nummer naast Azure SQL-evaluatie.
2. Klik op de naam van de beoordeling die u wilt weer geven. Als voor beeld (alleen voor schattingen en kosten): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="overzicht van SQL-evaluatie":::
3. Controleer de samenvatting van de evaluatie. U kunt ook de evaluatie-eigenschappen bewerken of de evaluatie opnieuw berekenen.

#### <a name="discovered-items"></a>Gedetecteerde items

Dit geeft het aantal SQL-servers, instanties en data bases aan die in deze beoordeling zijn beoordeeld.
    
#### <a name="azure-readiness"></a>Azure-gereedheid

Dit duidt op de distributie van geschatte SQL-exemplaren: 
    
**Type doel implementatie (in eigenschappen van beoordeling)** | **Gereedheid**   
--- | --- |
**Aanbevolen** |  Klaar voor Azure SQL Database, gereed voor Azure SQL Managed instance, gereed voor Azure VM, gereedheids versie onbekend (in het geval de detectie wordt uitgevoerd of er worden een aantal detectie problemen opgelost)
**Azure SQL DB** of **Azure SQL mi** | Gereed voor Azure SQL Database of Azure SQL Managed instance, niet gereed voor Azure SQL Database of Azure SQL Managed instance, gereedheids versie onbekend (in het geval de detectie wordt uitgevoerd of er worden een aantal detectie problemen opgelost)
     
U kunt inzoomen om meer te weten te komen over problemen met de migratie of waarschuwingen die u kunt oplossen voordat u de migratie naar Azure SQL uitvoert. [Meer informatie](concepts-azure-sql-assessment-calculation.md) U kunt ook de aanbevolen Azure SQL-configuraties bekijken voor de migratie naar Azure SQL-data bases en/of beheerde exemplaren.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Kosten Details Azure SQL Database en beheerd exemplaar

De schatting van de maandelijkse kosten omvat reken-en opslag kosten voor Azure SQL-configuraties die overeenkomen met het aanbevolen Azure SQL Database en/of het implementatie type Azure SQL Managed instance. [Meer informatie](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Gereedheid controleren

1. Klik op **Azure SQL-gereedheid**.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Details van Azure SQL-gereedheid":::
1. Bekijk in Azure SQL Readiness de **Azure SQL DB** -gereedheid en **Azure SQL mi-gereedheid** voor de geschatte SQL-exemplaren:
    - **Gereed**: het exemplaar is gereed om te worden gemigreerd naar Azure SQL DB/mi zonder migratie problemen of waarschuwingen. 
        - Gereed (pictogram voor Hyper-en blauwe gegevens): het exemplaar is gereed om te worden gemigreerd naar Azure SQL DB/MI zonder migratie problemen, maar heeft een aantal migratie waarschuwingen die u moet controleren. U kunt klikken op de Hyper link om de migratie waarschuwingen en de aanbevolen herstel richtlijnen te bekijken: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="evaluatie met de status gereed":::       
    - **Niet gereed**: het exemplaar heeft een of meer migratie problemen voor de migratie naar Azure SQL DB/mi. U kunt op de Hyper link klikken en de migratie problemen en de aanbevolen richt lijnen voor herstel bekijken.
    - **Onbekend**: Azure migrate kan de gereedheid niet beoordelen omdat de detectie wordt uitgevoerd of omdat er problemen zijn tijdens de detectie die moeten worden verholpen vanuit de Blade meldingen. Neem contact op met Microsoft Ondersteuning als het probleem zich blijft voordoen. 
1. Bekijk het aanbevolen implementatie type voor het SQL-exemplaar dat is vastgesteld volgens de volgende matrix:

    - **Type doel implementatie** (zoals geselecteerd in de beoordelings eigenschappen): **Aanbevolen**

        **Gereedheid voor Azure SQL DB** | **Azure SQL MI-gereedheid** | **Aanbevolen implementatie type** | **Berekende Azure SQL-configuratie en kosten ramingen?**
         --- | --- | --- | --- |
        Gereed | Gereed | [Meer informatie over](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) Azure SQL DB of Azure SQL mi | Yes
        Gereed | Niet gereed of onbekend | Azure SQL Database | Yes
        Niet gereed of onbekend | Gereed | Azure SQL MI | Yes
        Niet gereed | Niet gereed | Mogelijk klaar voor Azure VM [meer informatie](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | No
        Niet gereed of onbekend | Niet gereed of onbekend | Onbekend | Nee
    
    - **Type doel implementatie** (zoals geselecteerd in de beoordelings eigenschappen): **Azure SQL DB**
    
        **Gereedheid voor Azure SQL DB** | **Berekende Azure SQL-configuratie en kosten ramingen?**
        --- | --- |
        Gereed | Yes
        Niet gereed | Nee
        Onbekend | Nee
    
    - **Type doel implementatie** (zoals geselecteerd in de beoordelings eigenschappen): **Azure SQL mi**
    
        **Azure SQL MI-gereedheid** | **Berekende Azure SQL-configuratie en kosten ramingen?**
         --- | --- |
        Gereed | Yes
        Niet gereed | Nee
        Onbekend | Nee

4. Klik op de naam van het exemplaar inzoomen om het aantal gebruikers databases, exemplaar gegevens, inclusief instantie-eigenschappen, compute (scoped to instance) en opslag Details van de bron database weer te geven.
5. Klik op het aantal gebruikers databases om de lijst met data bases en de bijbehorende gegevens te bekijken. Als voor beeld (alleen voor schattingen en kosten): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="Details van SQL-exemplaar":::
5. Klik op Details weer geven in de kolom migratie problemen om de migratie problemen en waarschuwingen voor een bepaald type doel implementatie te controleren. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Problemen met DB-migratie en waarschuwingen":::

### <a name="review-cost-estimates"></a>Geschatte kosten
De evaluatie samenvatting toont de geschatte maandelijkse reken-en opslag kosten voor Azure SQL-configuraties die overeenkomen met de aanbevolen Azure SQL-data bases en/of het implementatie type beheerde exemplaren.

1. Controleer de maandelijkse totale kosten. De kosten worden geaggregeerd voor alle SQL-exemplaren in de geraamde groep.
    - Schattingen van kosten zijn gebaseerd op de aanbevolen Azure SQL-configuratie voor een exemplaar. 
    - De geschatte maandelijkse kosten voor berekening en opslag worden weergegeven. Als voor beeld (alleen schattingen en kosten bijvoorbeeld):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Kosten Details":::

1. U kunt inzoomen op een instantie niveau om de Azure SQL-configuratie en kosten ramingen op een instantie niveau te bekijken.  
1. U kunt ook inzoomen op de lijst met data bases om de Azure SQL-configuratie en kosten ramingen per Data Base te controleren wanneer een Azure SQL Database configuratie wordt aanbevolen.

### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren
Azure Migrate wijst een betrouwbaarheids classificatie toe aan alle Azure SQL-evaluaties op basis van de beschik baarheid van de prestatie-en gebruiks gegevens punten die nodig zijn om de evaluatie te berekenen voor alle geëvalueerde SQL-instanties en data bases. De classificatie is van één ster (laagste) tot vijf sterren (hoogste).
De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte in de evaluatie. Betrouwbaarheids classificaties zijn als volgt:

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie](concepts-azure-sql-assessment-calculation.md#confidence-ratings) over betrouwbaarheidsclassificaties.


## <a name="next-steps"></a>Volgende stappen

- Meer [informatie](concepts-azure-sql-assessment-calculation.md) over hoe Azure SQL-evaluaties worden berekend.
- Beginnen met het migreren van SQL-exemplaren en-data bases met behulp van [Azure database Migration service](../dms/dms-overview.md).