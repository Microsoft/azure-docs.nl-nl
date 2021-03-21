---
title: Optimaliseer uw Active Directory-omgeving met Azure Monitor | Microsoft Docs
description: U kunt de Active Directory Health Check-oplossing gebruiken om het risico en de status van uw omgevingen volgens een regel matig interval te evalueren.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: bff61ec9dfcb985ea0111ca58bfd58273e1fe432
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723245"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>De Active Directory-omgeving optimaliseren met behulp van Active Directory-statuscontrole in Azure Monitor

![AD Health Check-symbool](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

U kunt de Active Directory Health Check-oplossing gebruiken om het risico en de status van uw server omgevingen een regel matig interval te evalueren. Dit artikel helpt u bij het installeren en gebruiken van de oplossing, zodat u corrigerende maat regelen kunt nemen voor potentiële problemen.

Deze oplossing biedt een lijst met aanbevelingen die specifiek zijn voor uw geïmplementeerde server infrastructuur. De aanbevelingen worden gecategoriseerd op vier focus gebieden, zodat u het risico snel kunt begrijpen en actie kunt ondernemen.

De aanbevelingen zijn gebaseerd op de kennis en ervaring die micro soft-technici hebben opgedaan vanuit duizenden klant bezoeken. Elke aanbeveling bevat richt lijnen voor de reden waarom een probleem te voor u is en hoe u de voorgestelde wijzigingen kunt implementeren.

U kunt focus gebieden kiezen die het belangrijkst zijn voor uw organisatie en uw voortgang volgen met betrekking tot het uitvoeren van een risico vrije en goede omgeving.

Nadat u de oplossing hebt toegevoegd en een controle is voltooid, wordt de samenvattings informatie voor de focus gebieden weer gegeven op het **ad Health Check** -dash board voor de infra structuur in uw omgeving. In de volgende secties wordt beschreven hoe u de informatie op het **ad Health Check** -dash board gebruikt, waar u de aanbevolen acties voor uw Active Directory server infrastructuur kunt bekijken en vervolgens uitvoeren.  

![afbeelding van de tegel AD Health check](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![afbeelding van het dash board AD Health check](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Vereisten

* De Active Directory Health Check-oplossing vereist een ondersteunde versie van .NET Framework 4.6.2 of hoger die is geïnstalleerd op elke computer met de Log Analytics-agent voor Windows (ook wel micro soft Monitoring Agent (MMA) genoemd).  De agent wordt gebruikt door System Center 2016-Operations Manager, Operations Manager 2012 R2 en Azure Monitor.
* De oplossing biedt ondersteuning voor domein controllers met Windows Server 2008 en 2008 R2, Windows Server 2012 en 2012 R2, Windows Server 2016 en Windows Server 2019.
* Een Log Analytics-werk ruimte om de Active Directory Health Check-oplossing toe te voegen vanuit de Azure Marketplace in de Azure Portal. Er is geen aanvullende configuratie vereist.

  > [!NOTE]
  > Nadat u de oplossing hebt toegevoegd, wordt het AdvisorAssessment.exe-bestand toegevoegd aan servers met agents. Configuratie gegevens worden gelezen en vervolgens naar Azure Monitor in de Cloud verzonden voor verwerking. Logica wordt toegepast op de ontvangen gegevens en de gegevens worden vastgelegd door de cloudservice.
  >
  >

Voor het uitvoeren van de status controle op uw domein controllers die lid zijn van het domein dat moet worden geëvalueerd, is voor elke domein controller in dat domein een agent en connectiviteit met Azure Monitor een van de volgende ondersteunde methoden vereist:

1. Installeer de [log Analytics-agent voor Windows](../agents/agent-windows.md) als de domein controller niet al wordt bewaakt door System Center 2016-Operations Manager of Operations Manager 2012 R2.
2. Als het wordt bewaakt met System Center 2016-Operations Manager of Operations Manager 2012 R2 en de beheer groep is niet geïntegreerd met Azure Monitor, kan de domein controller meerdere locaties hebben met Azure Monitor om gegevens te verzamelen en door te sturen naar de service en nog steeds door Operations Manager worden bewaakt.  
3. Als uw Operations Manager-beheer groep is geïntegreerd met de service, moet u de domein controllers voor het verzamelen van gegevens door de service toevoegen volgens de stappen onder door [agents beheerde computers toevoegen](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) nadat u de oplossing in uw werk ruimte hebt ingeschakeld.  

De agent op uw domein controller die aan een Operations Manager-beheer groep rapporteert, verzamelt gegevens, stuurt deze door naar de toegewezen beheer server en wordt vervolgens rechtstreeks van een beheer server naar Azure Monitor verzonden.  De gegevens worden niet naar de Operations Manager-data bases geschreven.  

## <a name="active-directory-health-check-data-collection-details"></a>Details voor gegevensverzameling van Active Directory-statuscontrole

Active Directory status controle verzamelt gegevens uit de volgende bronnen met behulp van de agent die u hebt ingeschakeld:

- Register
- LDAP
- .NET Framework
- Gebeurtenislogboek
- Active Directory Service interfaces (ADSI)
- Windows PowerShell
- Bestandsgegevens
- Windows Management Instrumentation (WMI)
- De API voor het hulp programma DCDIAG
- API voor de File Replication-service (NTFRS)
- Aangepaste C#-code

Gegevens worden verzameld op de domein controller en worden elke zeven dagen doorgestuurd naar Azure Monitor.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Inzicht in hoe de prioriteit van aanbevelingen wordt vastgesteld

Aan elke aanbevolen aanbeveling wordt een wegings waarde gegeven die het relatieve belang van de aanbeveling identificeert. Alleen de tien belangrijkste aanbevelingen worden weer gegeven.

### <a name="how-weights-are-calculated"></a>Hoe gewicht wordt berekend

Wegingen zijn aggregatie waarden op basis van drie belang rijke factoren:

* De *kans* dat een probleem is geïdentificeerd, veroorzaakt problemen. Een hogere kans komt overeen met een grotere algemene score voor de aanbeveling.
* De *impact* van het probleem in uw organisatie als er een probleem is. Een hogere impact komt overeen met een grotere algemene score voor de aanbeveling.
* De *inspanningen* die nodig zijn om de aanbeveling te implementeren. Een hogere inspanning is gelijk aan een kleinere algemene score voor de aanbeveling.

De weging voor elke aanbeveling wordt uitgedrukt als een percentage van de totale beschik bare score voor elk focus gebied. Als een aanbeveling in het gebied beveiliging en nalevings focus bijvoorbeeld een Score van 5% heeft, verhoogt deze aanbeveling de algehele score voor beveiliging en naleving met 5%.

### <a name="focus-areas"></a>Aandachtsgebieden

**Beveiliging en naleving** : dit focus gebied bevat aanbevelingen voor mogelijke beveiligings dreigingen en inbreuken, bedrijfs beleid en technische, juridische en wettelijke nalevings vereisten.

**Beschik baarheid en bedrijfs continuïteit** : dit focus gebied bevat aanbevelingen voor de beschik baarheid van de service, de tolerantie van uw infra structuur en zakelijke beveiliging.

**Prestaties en schaal baarheid** : dit focus gebied bevat aanbevelingen om de IT-infra structuur van uw organisatie te verg Roten, zodat uw IT-omgeving voldoet aan de huidige prestatie vereisten en kan reageren op veranderende infrastructuur behoeften.

**Upgrade, migratie en implementatie** : dit focus gebied bevat aanbevelingen om u te helpen bij het upgraden, migreren en implementeren van Active Directory naar uw bestaande infra structuur.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Moet u proberen een score van 100% te halen voor elk focusgebied?

Niet noodzakelijkerwijs. De aanbevelingen zijn gebaseerd op de kennis en ervaringen van micro soft-technici over duizenden klant bezoeken. Er zijn echter geen twee server infrastructuren hetzelfde, en specifieke aanbevelingen kunnen meer of minder relevant zijn voor u. Sommige beveiligings aanbevelingen kunnen bijvoorbeeld minder relevant zijn als uw virtuele machines niet worden blootgesteld aan Internet. Enkele Beschik baarheid van aanbevelingen kan minder relevant zijn voor services die ad-hoc gegevens verzameling en-rapportage met lage prioriteit bieden. Problemen die belang rijk zijn voor een volwassen bedrijf zijn mogelijk minder belang rijk voor het opstarten. U kunt ook bepalen welke focus gebieden uw prioriteiten hebben en vervolgens kijken hoe uw scores in de loop van de tijd veranderen.

Elke aanbeveling bevat richt lijnen over waarom het belang rijk is. Gebruik deze richt lijnen om te evalueren of implementatie van de aanbeveling geschikt is voor u, gezien de aard van uw IT-Services en de zakelijke behoeften van uw organisatie.

## <a name="use-health-check-focus-area-recommendations"></a>Aanbevelingen voor het focus gebied status controleren gebruiken

Nadat deze is geïnstalleerd, kunt u de samen vatting van de aanbevelingen bekijken met behulp van de tegel status controle op de pagina oplossing in het Azure Portal.

Bekijk de samen vatting van de nalevings evaluaties voor uw infra structuur en vervolgens inzoomen op aanbevelingen.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aanbevelingen voor een focus gebied weer geven en corrigerende actie ondernemen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Klik op de pagina **overzicht** op de tegel **Active Directory status controle** .

2. Controleer op de pagina **status controle** de samenvattings informatie op een van de Blades van het focus gebied en klik vervolgens op een van de aanbevelingen voor het focus gebied weer te geven.

3. Op een van de focus gebied pagina's kunt u de prioriteiten weer geven die zijn gemaakt voor uw omgeving. Klik op een aanbeveling onder **betrokken objecten** om details weer te geven over waarom de aanbeveling is gedaan.

    ![afbeelding van aanbevelingen voor de status controle](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. U kunt corrigerende acties uitvoeren die worden voorgesteld in **aanbevolen acties**. Wanneer het item is geadresseerd, worden in latere evaluatie records vastgelegd dat de aanbevolen acties zijn uitgevoerd en wordt de nalevings Score verhoogd. Gecorrigeerde items worden weer gegeven als **door gegeven objecten**.

## <a name="ignore-recommendations"></a>Aanbevelingen negeren

Als u aanbevelingen hebt die u wilt negeren, kunt u een tekst bestand maken dat Azure Monitor gebruikt om te voor komen dat aanbevelingen worden weer gegeven in de evaluatie resultaten.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Om te bepalen welke aanbevelingen u wilt negeren

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Gebruik de volgende query om de aanbevelingen weer te geven die zijn mislukt voor computers in uw omgeving.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Hier volgt een scherm opname van de logboek query: <

![mislukte aanbevelingen](media/ad-assessment/ad-failed-recommendations.png)

Kies de aanbevelingen die u wilt negeren. In de volgende procedure gebruikt u de waarden voor RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Een IgnoreRecommendations.txt tekst bestand maken en gebruiken

1. Maak een bestand met de naam IgnoreRecommendations.txt.

2. Plak of typ elke RecommendationId voor elke aanbeveling die Azure Monitor wilt negeren op een afzonderlijke regel en sla het bestand op en sluit het.

3. Plaats het bestand in de volgende map op elke computer waar u wilt dat Azure Monitor aanbevelingen negeert.

   * Op computers met micro soft Monitoring Agent (rechtstreeks verbonden of via Operations Manager)- *Systeem station*: \Program Files\Microsoft monitoring Agent\Agent
   * Op de Operations Manager 2012 R2-beheer server- *Systeem station*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Op de Operations Manager 2016-beheer server- *System Drive*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Controleren of de aanbevelingen worden genegeerd

Nadat de volgende geplande status controle standaard elke zeven dagen wordt uitgevoerd, worden de opgegeven aanbevelingen gemarkeerd als *genegeerd* en worden ze niet weer gegeven op het dash board.

1. U kunt de volgende logboek query's gebruiken om alle genegeerde aanbevelingen weer te geven.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Als u later besluit dat u genegeerde aanbevelingen wilt zien, verwijdert u eventuele IgnoreRecommendations.txt-bestanden of verwijdert u de RecommendationIDs uit deze.

## <a name="ad-health-check-solutions-faq"></a>Veelgestelde vragen over oplossingen van AD-statuscontrole

*Welke controles worden uitgevoerd door de AD-evaluatie-oplossing?*

* Met de volgende query wordt een beschrijving weer gegeven van alle controles die op dit moment worden uitgevoerd:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
De resultaten kunnen vervolgens naar Excel worden geëxporteerd voor verdere controle.

*Hoe vaak wordt een status controle uitgevoerd?*

* De controle wordt elke zeven dagen uitgevoerd.

*Is er een manier om te configureren hoe vaak de status controle wordt uitgevoerd?*

* Momenteel niet.

*Als er een andere server voor wordt gedetecteerd nadat ik een oplossing voor de status controle heb toegevoegd, wordt deze gecontroleerd*

* Ja, zodra deze is gedetecteerd, wordt dit na elke zeven dagen gecontroleerd.

*Als een server buiten gebruik wordt gesteld, wordt deze verwijderd uit de status controle?*

* Als een server drie weken geen gegevens verzendt, wordt deze verwijderd.

*Wat is de naam van het proces dat het verzamelen van gegevens doet?*

* AdvisorAssessment.exe

*Hoe lang duurt het voordat gegevens worden verzameld?*

* De werkelijke gegevens verzameling op de server duurt ongeveer 1 uur. Het kan langer duren op servers met een groot aantal Active Directory servers.

*Is er een manier om te configureren wanneer gegevens worden verzameld?*

* Momenteel niet.

*Waarom worden alleen de tien beste aanbevelingen weer gegeven?*

* In plaats van een uitgebreide lijst met taken te geven, raden we u aan om eerst te focussen op de aanbevelingen met prioriteit. Nadat u ze hebt geadresseerd, worden er extra aanbevelingen beschikbaar. Als u de gedetailleerde lijst wilt zien, kunt u alle aanbevelingen weer geven met behulp van een logboek query.

*Is er een manier om een aanbeveling te negeren?*

* Ja, zie de sectie [aanbevelingen negeren](#ignore-recommendations) hierboven.

## <a name="next-steps"></a>Volgende stappen

Gebruik [Azure monitor-logboek query's](../logs/log-query-overview.md) om te leren hoe u gedetailleerde gegevens over AD health checks en aanbevelingen kunt analyseren.

