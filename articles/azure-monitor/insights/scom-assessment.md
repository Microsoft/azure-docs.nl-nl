---
title: System Center Operations Manager evalueren met Azure Monitor
description: U kunt de System Center Operations Manager-statuscontrole-oplossing gebruiken om het risico en de status van uw omgevingen volgens een regel matig interval te evalueren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 685bf35bfb6d6e85d70bd1f1b446370d66a320f2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573391"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>De omgeving optimaliseren met behulp van System Center Operations Manager-statuscontrole (preview)

![System Center Operations Manager-statuscontrole-symbool](./media/scom-assessment/scom-assessment-symbol.png)

U kunt de System Center Operations Manager-statuscontrole-oplossing gebruiken om het risico en de status van uw System Center Operations Manager beheer groep volgens een regel matig interval te evalueren. Dit artikel helpt u bij het installeren, configureren en gebruiken van de oplossing, zodat u corrigerende maat regelen kunt nemen voor potentiële problemen.

Deze oplossing biedt een lijst met aanbevelingen die specifiek zijn voor uw geïmplementeerde server infrastructuur. De aanbevelingen worden gecategoriseerd op vier focus gebieden, waarmee u het risico snel kunt begrijpen en corrigerende maat regelen kunt nemen.

De aanbevelingen zijn gebaseerd op de kennis en ervaring die micro soft-technici hebben opgedaan vanuit duizenden klant bezoeken. Elke aanbeveling bevat richt lijnen voor de reden waarom een probleem te voor u is en hoe u de voorgestelde wijzigingen kunt implementeren.

U kunt focus gebieden kiezen die het belangrijkst zijn voor uw organisatie en uw voortgang volgen met betrekking tot het uitvoeren van een risico vrije en goede omgeving.

Nadat u de oplossing hebt toegevoegd en er een evaluatie wordt uitgevoerd, wordt de samenvattings informatie voor de focus gebieden weer gegeven op het **System Center Operations Manager-statuscontrole** dash board voor uw infra structuur. In de volgende secties wordt beschreven hoe u de informatie op het **System Center Operations Manager-statuscontrole** -dash board gebruikt, waar u de aanbevolen acties voor uw Operations Manager omgeving kunt bekijken en vervolgens uitvoeren.

![Tegel System Center Operations Manager oplossing](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager-statuscontrole dash board](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>De oplossing installeren en configureren

De oplossing werkt met micro soft System Center 2012 Operations Manager Service Pack 1, micro soft System Center 2012 R2 Operations Manager, micro soft System Center 2016 Operations Manager, micro soft System Center 2016 Operations Manager en micro soft System Center Operations Manager 1807. Een ondersteunde versie van .NET Framework 4.6.2 moet op elke beheer server worden geïnstalleerd.

Gebruik de volgende informatie om de oplossing te installeren en configureren.

- Voordat u de oplossing status controle in Log Analytics kunt gebruiken, moet u de oplossing hebben geïnstalleerd. Installeer de oplossing vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Nadat de oplossing is toegevoegd aan de werk ruimte, wordt in de tegel **System Center Operations Manager-statuscontrole** op het dash board een extra configuratie bericht weer gegeven. Klik op de tegel en volg de configuratie stappen die worden vermeld op de pagina

  ![Tegel System Center Operations Manager-dash board](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> De configuratie van System Center Operations Manager kan worden uitgevoerd met behulp van een script door de stappen te volgen die worden vermeld op de pagina configuratie van de oplossing in Log Analytics.

 Voer de onderstaande stappen in de volgende volg orde uit om de evaluatie te configureren via Operations Manager Operations-console:
1. [Het run as-account voor System Center Operations Manager-statuscontrole instellen](#operations-manager-run-as-accounts-for-log-analytics)  
2. De System Center Operations Manager-statuscontrole-regel configureren

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Details voor gegevensverzameling van System Center Operations Manager-statuscontrole

De System Center Operations Manager-statuscontrole oplossing verzamelt gegevens uit de volgende bronnen:

* Register
* Windows Management Instrumentation (WMI)
* Gebeurtenislogboek
* Bestandsgegevens
* Rechtstreeks vanuit Operations Manager Power shell-en SQL-query's uit te werken vanaf een beheer server die u hebt opgegeven.  

Gegevens worden verzameld op de beheer server en elke zeven dagen doorgestuurd naar Log Analytics.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Uitvoeren als-accounts van Operations Manager voor Log Analytics

Log Analytics bouwt voort op Management Packs voor werk belastingen om services te kunnen toevoegen. Elke workload vereist systeemspecifieke bevoegdheden om Management Packs uit te voeren in een andere beveiligings context, zoals een domein gebruikers account. Configureer een Operations Manager uitvoeren als-account met bevoegde referenties. Zie [een uitvoeren als-account maken](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) in de Operations Manager documentatie voor meer informatie.

Gebruik de volgende informatie om de Operations Manager uitvoeren als-account voor System Center Operations Manager-statuscontrole in te stellen.

### <a name="set-the-run-as-account"></a>Instellen als Uitvoeren als-account

Het run as-account moet voldoen aan de volgende vereisten voordat u doorgaat:

* Een domein gebruikers account dat lid is van de lokale groep Administrators op alle servers die een Operations Manager Role-Management Server ondersteunen SQL Server hosten van de operationele, Data Warehouse-en ACS-data base, rapportage, webconsole en gateway server.
* De rol van Operations Manager-beheerder voor de beheer groep die wordt beoordeeld
* Als het account geen SQL sysadmin-rechten heeft, voert u het [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) uit om gedetailleerde machtigingen toe te kennen aan het account op elk SQL Server exemplaar dat als host fungeert voor een of alle operations manager-data bases.

1. Selecteer de **beheer** navigatie knop In de Operations Manager-console.
2. Klik onder **Run as-configuratie** op **accounts**.
3. Klik in de wizard **Run as-account maken** op de pagina **Inleiding** op **volgende**.
4. Selecteer op de pagina **algemene eigenschappen** de optie **Windows** in het **Run as-account type:** list.
5. Typ een weergave naam in het tekstvak **weergave naam** en typ eventueel een beschrijving in het vak **Beschrijving** en klik vervolgens op **volgende**.
6. Selecteer op de pagina **distributie beveiliging** de optie **veiliger**.
7. Klik op **Create**.  

Nu het run as-account is gemaakt, moet het worden ingesteld op beheerser vers in de beheer groep en zijn gekoppeld aan een vooraf gedefinieerd run as-profiel zodat werk stromen met de referenties worden uitgevoerd.  

1. Dubbel klik onder **Run as-configuratie**, **accounts** in het resultaten venster op het account dat u eerder hebt gemaakt.
2. Klik op het tabblad **distributie** op **toevoegen** voor het vak **geselecteerde computers** en voeg de beheer server toe om het account naar te distribueren.  Klik twee keer op **OK** om uw wijzigingen op te slaan.
3. Klik onder **Run as-configuratie** op **profielen**.
4. Zoek naar het *SCOM-beoordelings profiel*.
5. De profiel naam moet: het *Run as-Profiel van micro soft System Center Operations Manager-statuscontrole*.
6. Klik met de rechter muisknop en werk de eigenschappen bij en voeg het onlangs gemaakte run as-account toe dat u eerder hebt gemaakt.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-script voor het verlenen van gedetailleerde machtigingen voor het Uitvoeren als-account

Voer het volgende SQL-script uit om de vereiste machtigingen te verlenen voor het uitvoeren als-account op het SQL Server-exemplaar dat wordt gebruikt door Operations Manager die als host fungeert voor de operationele, Data Warehouse-en ACS-data base.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>De statuscontroleregel configureren

De management pack van de System Center Operations Manager-statuscontrole oplossing bevat een regel met de naam *micro soft System Center Operations Manager run Health Check Rule*. Deze regel is verantwoordelijk voor het uitvoeren van de status controle. Als u de regel wilt inschakelen en de frequentie wilt configureren, gebruikt u de onderstaande procedures.

De regel voor het uitvoeren van de status controle van micro soft System Center Operations Manager is standaard uitgeschakeld. Als u de status controle wilt uitvoeren, moet u de regel inschakelen op een beheer server. Voer de volgende stappen uit.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>De regel voor een specifieke beheerserver inschakelen

1. Zoek in de werk ruimte **ontwerpen** van de Operations Manager Operations-console naar de regel *micro soft System Center Operations Manager uitvoerings status controle regel* in het deel venster **regels** .
2. Selecteer in de zoek resultaten de versie die het tekst *type: beheer server* bevat.
3. Klik met de rechter muisknop op de regel en klik vervolgens op **onderdrukkingen**  >  **voor een specifiek object van klasse: beheer server**.
4.  Selecteer in de lijst beschik bare beheerser vers de beheer server waarop de regel moet worden uitgevoerd.  Dit moet dezelfde beheer server zijn die u eerder hebt geconfigureerd om het uitvoeren als-account aan te koppelen.
5.  Zorg ervoor dat u de onderdrukkings waarde wijzigt in **True** voor de **ingeschakelde** parameter waarde.<br><br> ![onderdrukkings parameter](./media/scom-assessment/rule.png)

    In dit venster configureert u de uitvoerings frequentie met behulp van de volgende procedure.

#### <a name="configure-the-run-frequency"></a>De uitvoeringsfrequentie configureren

De evaluatie is zo geconfigureerd dat deze standaard elke 10.080 minuten (of zeven dagen) wordt uitgevoerd. U kunt de waarde vervangen door een minimum waarde van 1440 minuten (of één dag). De waarde vertegenwoordigt de minimale tijds hiaat die vereist is tussen de opeenvolgende evaluatie-uitvoeringen. Volg de onderstaande stappen om het interval te overschrijven.

1. Zoek in de werk ruimte **ontwerpen** van de Operations Manager-console naar de regel *micro soft System Center Operations Manager uitvoerings status controle regel* in de sectie **regels** .
2. Selecteer in de zoek resultaten de versie die het tekst *type: beheer server* bevat.
3. Klik met de rechter muisknop op de regel en klik vervolgens op **onderdrukking van de regel**  >  **voor alle objecten van klasse: beheer server**.
4. Wijzig de waarde van de para meter **interval** in de gewenste interval waarde. In het onderstaande voor beeld is de waarde ingesteld op 1440 minuten (één dag).<br><br> ![interval parameter](./media/scom-assessment/interval.png)<br>  

    Als de waarde is ingesteld op minder dan 1440 minuten, wordt de regel uitgevoerd op een interval van één dag. In dit voor beeld negeert de regel de interval waarde en wordt uitgevoerd met een frequentie van één dag.


## <a name="understanding-how-recommendations-are-prioritized"></a>Inzicht in hoe de prioriteit van aanbevelingen wordt vastgesteld

Aan elke aanbevolen aanbeveling wordt een wegings waarde gegeven die het relatieve belang van de aanbeveling identificeert. Alleen de tien belangrijkste aanbevelingen worden weer gegeven.

### <a name="how-weights-are-calculated"></a>Hoe gewicht wordt berekend

Wegingen zijn aggregatie waarden op basis van drie belang rijke factoren:

- De *kans* dat een probleem is geïdentificeerd, veroorzaakt problemen. Een hogere kans komt overeen met een grotere algemene score voor de aanbeveling.
- De *impact* van het probleem in uw organisatie als er een probleem is. Een hogere impact komt overeen met een grotere algemene score voor de aanbeveling.
- De *inspanningen* die nodig zijn om de aanbeveling te implementeren. Een hogere inspanning is gelijk aan een kleinere algemene score voor de aanbeveling.

De weging voor elke aanbeveling wordt uitgedrukt als een percentage van de totale beschik bare score voor elk focus gebied. Als bijvoorbeeld een aanbeveling in het focus gebied beschik baarheid en bedrijfs continuïteit een Score van 5% heeft, verhoogt de implementatie van die aanbeveling uw totale score voor de beschik baarheid en bedrijfs continuïteit met 5%.

### <a name="focus-areas"></a>Aandachtsgebieden

**Beschik baarheid en bedrijfs continuïteit** : dit focus gebied bevat aanbevelingen voor de beschik baarheid van de service, de tolerantie van uw infra structuur en zakelijke beveiliging.

**Prestaties en schaal baarheid** : dit focus gebied bevat aanbevelingen om de IT-infra structuur van uw organisatie te verg Roten, zodat uw IT-omgeving voldoet aan de huidige prestatie vereisten en kan reageren op veranderende infrastructuur behoeften.

**Upgrade, migratie en implementatie** : dit focus gebied bevat aanbevelingen om u te helpen bij het upgraden, migreren en implementeren van SQL Server naar uw bestaande infra structuur.

**Bewerkingen en controle** : dit focus gebied bevat aanbevelingen om uw IT-activiteiten te stroom lijnen, preventief onderhoud te implementeren en de prestaties te maximaliseren.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Moet u proberen een score van 100% te halen voor elk focusgebied?

Niet noodzakelijkerwijs. De aanbevelingen zijn gebaseerd op de kennis en ervaringen van micro soft-technici over duizenden klant bezoeken. Er zijn echter geen twee server infrastructuren hetzelfde, en specifieke aanbevelingen kunnen meer of minder relevant zijn voor u. Sommige beveiligings aanbevelingen kunnen bijvoorbeeld minder relevant zijn als uw virtuele machines niet worden blootgesteld aan Internet. Enkele Beschik baarheid van aanbevelingen kan minder relevant zijn voor services die ad-hoc gegevens verzameling en-rapportage met lage prioriteit bieden. Problemen die belang rijk zijn voor een volwassen bedrijf zijn mogelijk minder belang rijk voor het opstarten. U kunt ook bepalen welke focus gebieden uw prioriteiten hebben en vervolgens kijken hoe uw scores in de loop van de tijd veranderen.

Elke aanbeveling bevat richt lijnen over waarom het belang rijk is. Gebruik deze richt lijnen om te evalueren of implementatie van de aanbeveling geschikt is voor u, gezien de aard van uw IT-Services en de zakelijke behoeften van uw organisatie.

## <a name="use-health-check-focus-area-recommendations"></a>Aanbevelingen voor het focus gebied status controleren gebruiken

Voordat u een health check-oplossing in Log Analytics kunt gebruiken, moet u de oplossing hebben geïnstalleerd. Zie [een beheer oplossing installeren](./solutions.md)voor meer informatie over het installeren van oplossingen. Nadat deze is geïnstalleerd, kunt u de samen vatting van aanbevelingen bekijken met behulp van de tegel System Center Operations Manager-statuscontrole op de pagina **overzicht** voor uw werk ruimte in de Azure Portal.

Bekijk de samen vatting van de nalevings evaluaties voor uw infra structuur en vervolgens inzoomen op aanbevelingen.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aanbevelingen voor een focus gebied weer geven en corrigerende actie ondernemen
1. Meld u aan bij de Azure-portal op [https://portal.azure.com](https://portal.azure.com).
2. Klik in Azure Portal op **Meer services** in de linkerbenedenhoek. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics**.
3. Selecteer in het deel venster abonnementen Log Analytics een werk ruimte en klik vervolgens op het menu-item **werkruimte samenvatting** .  
4. Klik op de pagina **overzicht** op de tegel **System Center Operations Manager-statuscontrole** .
5. Controleer op de pagina **System Center Operations Manager-statuscontrole** de samenvattings informatie op een van de Blades van het focus gebied en klik vervolgens op een van de aanbevelingen voor de weer gave van het focus gebied.
6. Op een van de focus gebied pagina's kunt u de prioriteiten weer geven die zijn gemaakt voor uw omgeving. Klik op een aanbeveling onder **betrokken objecten** om details weer te geven over waarom de aanbeveling is gedaan.<br><br> ![focus gebied](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. U kunt corrigerende acties uitvoeren die worden voorgesteld in **aanbevolen acties**. Wanneer het item is opgelost, worden in latere evaluaties vastgelegd dat de aanbevolen acties zijn uitgevoerd en wordt de nalevings Score verhoogd. Gecorrigeerde items worden weer gegeven als **door gegeven objecten**.

## <a name="ignore-recommendations"></a>Aanbevelingen negeren

Als u aanbevelingen hebt die u wilt negeren, kunt u een tekst bestand maken dat Log Analytics gebruikt om te voor komen dat aanbevelingen worden weer gegeven in de evaluatie resultaten.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Voor het identificeren van aanbevelingen die u wilt negeren
1. Klik in de Azure Portal op de pagina Log Analytics werk ruimte voor de geselecteerde werk ruimte op de menu opdracht **Zoeken in Logboeken** .
2. Gebruik de volgende query om de aanbevelingen weer te geven die zijn mislukt voor computers in uw omgeving.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Als uw werk ruimte is bijgewerkt naar de [nieuwe log Analytics query taal](../logs/log-query-overview.md), wordt de bovenstaande query gewijzigd in het volgende.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Hier ziet u een scherm opname met de zoek query voor het logboek:<br><br> ![zoeken in logboeken](./media/scom-assessment/scom-log-search.png)<br>

3. Kies de aanbevelingen die u wilt negeren. In de volgende procedure gebruikt u de waarden voor RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Een IgnoreRecommendations.txt tekst bestand maken en gebruiken

1. Maak een bestand met de naam IgnoreRecommendations.txt.
2. Plak of typ elke RecommendationId voor elke aanbeveling die Log Analytics wilt negeren op een afzonderlijke regel en sla het bestand op en sluit het.
3. Plaats het bestand in de volgende map op elke computer waar u wilt dat Log Analytics aanbevelingen negeert.
4. Op de Operations Manager-beheer server- *System Drive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Controleren of de aanbevelingen worden genegeerd

1. Nadat de volgende geplande evaluatie standaard elke zeven dagen wordt uitgevoerd, worden de opgegeven aanbevelingen gemarkeerd als genegeerd en worden ze niet weer gegeven in het dash board status controle.
2. U kunt de volgende zoek query's in het logboek gebruiken om alle genegeerde aanbevelingen weer te geven.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Als uw werk ruimte is bijgewerkt naar de [nieuwe log Analytics query taal](../logs/log-query-overview.md), wordt de bovenstaande query gewijzigd in het volgende.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Als u later besluit dat u genegeerde aanbevelingen wilt zien, verwijdert u eventuele IgnoreRecommendations.txt-bestanden of verwijdert u de RecommendationIDs uit deze.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Veelgestelde vragen over de oplossing van System Center Operations Manager-statuscontrole

*Ik heb de oplossing voor de status controle toegevoegd aan mijn Log Analytics-werk ruimte. Maar de aanbevelingen worden niet weer geven. Waarom niet?* Nadat u de oplossing hebt toegevoegd, gebruikt u de volgende stappen om de aanbevelingen op het Log Analytics dash board weer te geven.  

- [Het run as-account voor System Center Operations Manager-statuscontrole instellen](#operations-manager-run-as-accounts-for-log-analytics)  
- [De System Center Operations Manager-statuscontrole-regel configureren](#configure-the-health-check-rule)


*Is er een manier om te configureren hoe vaak de controle wordt uitgevoerd?* Ja. Zie [de uitvoerings frequentie configureren](#configure-the-run-frequency).

*Als er een andere server wordt gedetecteerd nadat ik de System Center Operations Manager-statuscontrole oplossing heb toegevoegd, wordt deze gecontroleerd?* Ja, nadat de detectie is ingeschakeld, wordt deze standaard elke zeven dagen gecontroleerd.

*Wat is de naam van het proces dat het verzamelen van gegevens doet?* AdvisorAssessment.exe

*Waar wordt het AdvisorAssessment.exe proces uitgevoerd?* AdvisorAssessment.exe wordt uitgevoerd onder het HealthService-proces van de beheer server waarop de status controle regel is ingeschakeld. Met dit proces wordt de detectie van uw hele omgeving gerealiseerd via het verzamelen van externe gegevens.

*Hoe lang duurt het om gegevens te verzamelen?* Het verzamelen van gegevens op de server duurt ongeveer één uur. Dit kan langer duren in omgevingen met veel Operations Manager exemplaren of data bases.

*Wat moet ik doen als ik het interval van de evaluatie ingesteld op minder dan 1440 minuten?* De evaluatie is vooraf geconfigureerd om Maxi maal één keer per dag te worden uitgevoerd. Als u de interval waarde overschrijft naar een waarde die kleiner is dan 1440 minuten, gebruikt de evaluatie 1440 minuten als interval waarde.

*Hoe weet ik of er problemen zijn met de vereisten?* Als de status controle is uitgevoerd en u geen resultaten ziet, is het waarschijnlijk dat sommige van de vereisten voor de controle zijn mislukt. U kunt query's uitvoeren: `Operation Solution=SCOMAssessment` en `SCOMAssessmentRecommendation FocusArea=Prerequisites` in zoeken in Logboeken om de mislukte vereisten weer te geven.

*Er is een `Failed to connect to the SQL Instance (….).` bericht in de vereiste fouten. Wat is het probleem?* AdvisorAssessment.exe, het proces waarmee gegevens worden verzameld, wordt uitgevoerd onder het HealthService-proces op de-beheer server. Als onderdeel van de status controle probeert het proces verbinding te maken met de SQL Server waarop de Operations Manager-Data Base aanwezig is. Deze fout kan optreden wanneer Firewall regels de verbinding met het SQL Server-exemplaar blok keren.

*Welk type gegevens worden verzameld?* De volgende typen gegevens worden verzameld:-WMI gegevens-REGI ster gegevens gegevens Operations Manager gegevens via Windows Power shell, SQL-Query's en File Information Collector.

*Waarom moet ik een uitvoeren als-account configureren?* Met Operations Manager worden verschillende SQL-query's uitgevoerd. Als u wilt dat ze worden uitgevoerd, moet u een uitvoeren als-account gebruiken met de vereiste machtigingen. Daarnaast zijn lokale beheerders referenties vereist voor het opvragen van WMI.

*Waarom worden alleen de tien beste aanbevelingen weer gegeven?* In plaats van een limitatieve lijst met taken te geven, raden we u aan om eerst te focussen op de aanbevelingen met prioriteit. Nadat u ze hebt geadresseerd, worden er extra aanbevelingen beschikbaar. Als u de gedetailleerde lijst wilt zien, kunt u alle aanbevelingen bekijken met zoeken in Logboeken.

*Is er een manier om een aanbeveling te negeren?* Ja, zie de [aanbevelingen negeren](#ignore-recommendations).


## <a name="next-steps"></a>Volgende stappen

- [Zoek logboeken](../logs/log-query-overview.md) voor meer informatie over het analyseren van gedetailleerde System Center Operations Manager-statuscontrole gegevens en aanbevelingen.

