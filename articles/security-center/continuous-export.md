---
title: Continue export kan waarschuwingen en aanbevelingen van Azure Security Center verzenden naar Log Analytics werk ruimten of Azure Event Hubs
description: Meer informatie over het configureren van continue export van beveiligings waarschuwingen en aanbevelingen voor Log Analytics werk ruimten of Azure Event Hubs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/08/2020
ms.author: memildin
ms.openlocfilehash: bdca5a753a49c26587db27892b54c2cb88910c83
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862459"
---
# <a name="continuously-export-security-center-data"></a>Security Center-gegevens continu exporteren

Azure Security Center genereert gedetailleerde beveiligings waarschuwingen en aanbevelingen. U kunt deze weer geven in de portal of via programmatische hulpprogram ma's. Mogelijk moet u ook enkele of al deze gegevens exporteren voor bijhouden met andere controle hulpprogramma's in uw omgeving. 

Met **doorlopend exporteren** kunt u volledig aanpassen *wat* wordt geëxporteerd en *waar* het gaat. U kunt deze bijvoorbeeld zodanig configureren dat:

- Alle waarschuwingen met hoge urgentie worden verzonden naar een Azure Event hub
- Alle problemen met de ernst van gemiddeld of op het hoogste niveau van de scan van beveiligings lekken van uw SQL-servers worden verzonden naar een specifieke Log Analytics-werk ruimte
- Specifieke aanbevelingen worden naar een event hub of Log Analytics werk ruimte bezorgd wanneer ze worden gegenereerd 

In dit artikel wordt beschreven hoe u doorlopend exporteren naar Log Analytics werk ruimten of Azure Event Hubs kunt configureren.

> [!NOTE]
> Als u Security Center wilt integreren met een SIEM, raadpleegt u [streaming-waarschuwingen naar een Siem-, via-of IT-Service beheer oplossing](export-to-siem.md).

> [!TIP]
> Security Center biedt ook de mogelijkheid om een eenmalig hand matige export naar CSV uit te voeren. Meer informatie vindt u in [hand matig eenmalige export van waarschuwingen en aanbevelingen](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemeen verkrijgbaar (GA)|
|Prijzen:|Gratis|
|Vereiste rollen en machtigingen:|<ul><li>**Beveiligings beheerder** of- **eigenaar** voor de resource groep</li><li>Schrijf machtigingen voor de doel resource</li><li>Als u het Azure Policy beleid ' DeployIfNotExist ' gebruikt dat hieronder wordt beschreven, hebt u ook machtigingen nodig voor het toewijzen van beleid</li></ul>|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov, andere gov<br>![Yes](./media/icons/yes-icon.png) Gov China (naar Event hub)|
|||





## <a name="set-up-a-continuous-export"></a>Een continue export instellen 

U kunt continue export configureren vanaf de Security Center pagina's in Azure Portal, via het Security Center REST API of op schaal met behulp van de meegeleverde Azure Policy sjablonen. Selecteer het juiste tabblad hieronder voor meer informatie.

### <a name="use-the-azure-portal"></a>[**Azure Portal gebruiken**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>Continue export configureren vanaf de Security Center pagina's in Azure Portal

De onderstaande stappen zijn nodig om een doorlopende export naar Log Analytics werk ruimte of Azure Event Hubs in te stellen.

1. Selecteer op de zijbalk van Security Center de **& instellingen voor prijzen**.
1. Selecteer het specifieke abonnement waarvoor u de gegevens export wilt configureren.
1. Selecteer in de zijbalk van de pagina instellingen voor dat abonnement **doorlopend exporteren**.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Opties voor exporteren in Azure Security Center":::

    Hier ziet u de export opties. Er is een tabblad voor elk beschik bare export doel. 

1. Selecteer het gegevens type dat u wilt exporteren en kies uit de filters voor elk type (bijvoorbeeld alleen waarschuwingen met hoge Ernst exporteren).
1. Als uw selectie een van deze vier aanbevelingen bevat, kunt u desgewenst de evaluaties van beveiligings problemen samen voegen:
    - De conclusies van de evaluatie van beveiligings problemen voor uw SQL-data bases moeten worden hersteld
    - De resultaten van evaluatie van beveiligings problemen op uw SQL-servers op computers moeten worden hersteld (preview-versie)
    - Beveiligingsproblemen met installatiekopieën in Azure Container Registry moeten worden hersteld (mogelijk gemaakt door Qualys)
    - Beveiligingsproblemen op uw virtuele machines moeten worden hersteld
    - Er moeten systeemupdates op uw computers worden geïnstalleerd

    Schakel de optie **beveiligings resultaten bevatten** in om de resultaten op te vragen met deze aanbevelingen.

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="De schakeloptie Beveiligingsresultaten insluiten in de configuratie voor continue export" :::

1. Kies in het gebied export doel de locatie waar u de gegevens wilt opslaan. Gegevens kunnen worden opgeslagen in een doel voor een ander abonnement (bijvoorbeeld op een centraal Event hub-exemplaar of een centrale Log Analytics-werk ruimte).
1. Selecteer **Opslaan**.

### <a name="use-the-rest-api"></a>[**REST-API gebruiken**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Continue export configureren met behulp van de REST API

Continue export kan worden geconfigureerd en beheerd via de API voor Azure Security Center [Automation](/rest/api/securitycenter/automations). Gebruik deze API om regels te maken of bij te werken voor het exporteren naar een van de volgende mogelijke bestemmingen:

- Azure Event Hub
- Log Analytics-werkruimte
- Azure Logic Apps 

De API biedt extra functionaliteit die niet beschikbaar is via de Azure Portal, bijvoorbeeld:

* **Groter volume** : met de API kunt u meerdere export configuraties maken voor één abonnement. De pagina **doorlopend exporteren** in de portal-gebruikers interface van Security Center ondersteunt slechts één export configuratie per abonnement.

* **Aanvullende functies** : de API biedt aanvullende para meters die niet worden weer gegeven in de gebruikers interface. U kunt bijvoorbeeld labels toevoegen aan uw Automation-resource en uw export definiëren op basis van een breder aantal waarschuwings-en aanbevelings eigenschappen dan die worden weer geven op de pagina **doorlopend exporteren** in de gebruikers interface van de portal van Security Center.

* **Meer gericht bereik** : de API biedt een gedetailleerder niveau voor het bereik van uw export configuraties. Wanneer u een export met de API definieert, kunt u dit doen op het niveau van de resource groep. Als u de pagina **doorlopend exporteren** gebruikt in de gebruikers interface van de portal van Security Center, moet u deze definiëren op abonnements niveau.

    > [!TIP]
    > Als u meerdere export configuraties hebt ingesteld met behulp van de API of als u alleen API-only-para meters hebt gebruikt, worden deze extra functies niet weer gegeven in de gebruikers interface van Security Center. In plaats daarvan wordt er een banner met de melding dat er andere configuraties bestaan.

Meer informatie over de automatiserings-API vindt u in de [documentatie van rest API](/rest/api/securitycenter/automations).





### <a name="deploy-at-scale-with-azure-policy"></a>[**Implementeren op schaal met Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Continue export op schaal configureren met de geleverde beleids regels

Het automatiseren van de processen voor bewaking en reageren op incidenten van uw organisatie kan de tijd die nodig is om beveiligingsincidenten te onderzoeken en te verhelpen aanzienlijk verbeteren.

Als u uw continue export configuraties in uw organisatie wilt implementeren, gebruikt u het opgegeven Azure Policy beleid ' DeployIfNotExist ' dat hieronder wordt beschreven om doorlopende export procedures te maken en te configureren.

**Deze beleids regels implementeren**

1. Selecteer in de onderstaande tabel het beleid dat u wilt Toep assen:

    |Doel  |Beleid  |Beleids-id  |
    |---------|---------|---------|
    |Continue export naar Event Hub|[Export implementeren in Event Hub voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Continue export naar Log Analytics-werkruimte|[Export implementeren in Log Analytics-werkruimte voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > U kunt deze ook vinden door te zoeken naar Azure Policy:
    > 1. Open Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Toegang tot Azure Policy":::
    > 2. Selecteer in het menu Azure Policy **definities** en zoek ze op naam. 

1. Selecteer op de pagina relevante Azure Policy **toewijzen**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="De Azure Policy toewijzen":::

1. Open elk tabblad en stel de para meters naar wens in:
    1. Stel op het tabblad **basis beginselen** het bereik voor het beleid in. Als u gecentraliseerd beheer wilt gebruiken, wijst u het beleid toe aan de beheer groep met de abonnementen die continue export configuratie zullen gebruiken. 
    1. Stel op het tabblad **para meters** de gegevens van de resource groep en het gegevens type in. 
        > [!TIP]
        > Elke para meter heeft een knop info met uitleg over de beschik bare opties.
        >
        > Het tabblad para meters van Azure Policy (1) biedt toegang tot vergelijk bare configuratie opties als de pagina doorlopend exporteren van Security Center (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="De para meters vergelijken in doorlopend exporteren met Azure Policy" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Als u deze toewijzing wilt Toep assen op bestaande abonnementen, opent u het tabblad **herstel** en selecteert u de optie voor het maken van een herstel taak.
1. Controleer de pagina samen vatting en selecteer **maken**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Informatie over het exporteren naar een Log Analytics-werk ruimte

Als u Azure Security Center gegevens in een Log Analytics werk ruimte wilt analyseren of als u Azure-waarschuwingen wilt gebruiken in combi natie met Security Center waarschuwingen, stelt u doorlopend exporteren naar uw Log Analytics-werk ruimte in.

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabellen en schema's

Beveiligings waarschuwingen en aanbevelingen worden respectievelijk opgeslagen in de *SecurityAlert* -en *SecurityRecommendations* -tabellen. 

De naam van de Log Analytics oplossing die deze tabellen bevat, is afhankelijk van of u Azure Defender hebt ingeschakeld: Security (Beveiliging en audit) of SecurityCenterFree. 

> [!TIP]
> Als u de gegevens in de doel werkruimte wilt zien, moet u een van deze oplossingen inschakelen **beveiliging en audit** of **SecurityCenterFree**.

![De * SecurityAlert *-tabel in Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Als u de gebeurtenis schema's van de geëxporteerde gegevens typen wilt weer geven, gaat u naar de [log Analytics-tabel schema's](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Geëxporteerde waarschuwingen en aanbevelingen in Azure Monitor weer geven

U kunt er ook voor kiezen om geëxporteerde beveiligings waarschuwingen en/of aanbevelingen in [Azure monitor](../azure-monitor/platform/alerts-overview.md)weer te geven. 

Azure Monitor biedt een uniforme waarschuwings ervaring voor verschillende Azure-waarschuwingen, waaronder diagnostisch logboeken, metrische waarschuwingen en aangepaste waarschuwingen op basis van Log Analytics werkruimte query's.

Als u waarschuwingen en aanbevelingen van Security Center in Azure Monitor wilt weer geven, configureert u een waarschuwings regel op basis van Log Analytics query's (logboek waarschuwing):

1. Selecteer op de pagina **waarschuwingen** van Azure monitor **nieuwe waarschuwings regel**.

    ![Pagina waarschuwingen van Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Configureer op de pagina regel maken uw nieuwe regel (op dezelfde manier als u een [waarschuwings regel voor het logboek configureert in azure monitor](../azure-monitor/platform/alerts-unified-log.md)):

    * Selecteer voor **resource** de log Analytics werk ruimte waarnaar u beveiligings waarschuwingen en aanbevelingen hebt geëxporteerd.

    * Selecteer **Condition** voor voor waarde **aangepaste logboek zoekopdracht**. Configureer op de pagina die wordt weer gegeven, de query, de lookback periode en de frequentie periode. In de zoek query kunt u *SecurityAlert* of *SecurityRecommendation* typen om een query uit te voeren op de gegevens typen die Security Center doorlopend exporteren naar wanneer u de functie continue export naar log Analytics inschakelt. 
    
    * U kunt desgewenst de [actie groep](../azure-monitor/platform/action-groups.md) configureren die u wilt activeren. Actie groepen kunnen e-mail verzenden, ITSM tickets, webhooks en meer activeren.
    ![Waarschuwings regel Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

U ziet nu nieuwe Azure Security Center waarschuwingen of aanbevelingen (afhankelijk van de geconfigureerde regels voor continue export en de voor waarde die u in uw Azure Monitor waarschuwings regel hebt opgegeven) in Azure Monitor waarschuwingen, waarbij automatisch een actie groep wordt geactiveerd (indien opgegeven).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Hand matige eenmalige export van waarschuwingen en aanbevelingen

Als u een CSV-rapport voor waarschuwingen of aanbevelingen wilt downloaden, opent u de pagina **beveiligings waarschuwingen** of **aanbevelingen** en selecteert u de knop **CSV-rapport downloaden** .

[![Waarschuwings gegevens downloaden als een CSV-bestand](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Deze rapporten bevatten waarschuwingen en aanbevelingen voor resources van de momenteel geselecteerde abonnementen.


## <a name="faq---continuous-export"></a>Veelgestelde vragen-continue export

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Wat zijn de kosten voor het exporteren van gegevens?

Er zijn geen kosten verbonden aan het inschakelen van continue export. Er kunnen kosten in rekening worden gebracht voor opname en retentie van gegevens in uw Log Analytics-werk ruimte, afhankelijk van uw configuratie. 

Meer informatie over de [prijzen van log Analytics werk ruimte](https://azure.microsoft.com/pricing/details/monitor/).

Meer informatie over de [prijzen van Azure Event hub](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>Bevat de export gegevens over de huidige status van alle resources?

Nee. Continue export is gebouwd voor het streamen van **gebeurtenissen**:

- **Waarschuwingen** die zijn ontvangen voordat de export werd ingeschakeld, worden niet geëxporteerd.
- **Aanbevelingen** worden verzonden wanneer de nalevings status van een resource wordt gewijzigd. Bijvoorbeeld wanneer een resource in orde verandert in een slechte status. Daarom worden, net als bij waarschuwingen, aanbevelingen voor bronnen waarvan de status niet is gewijzigd sinds u de export hebt ingeschakeld, niet geëxporteerd.


### <a name="why-are-recommendations-sent-at-different-intervals"></a>Waarom worden aanbevelingen met verschillende intervallen verzonden?

Verschillende aanbevelingen hebben verschillende intervallen voor nalevings evaluatie, wat kan variëren van een paar minuten tot elke paar dagen. Daarom verschillen de aanbevelingen in de tijd die ze nodig hebben om te worden weer gegeven in uw export.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Ondersteunt continue export alle scenario's voor bedrijfs continuïteit of herstel na nood gevallen (BCDR)?

Wanneer u uw omgeving voorbereidt voor BCDR-scenario's, waarbij de doel resource een storing of een andere nood situatie ondervindt, is het de verantwoordelijkheid van de organisatie om gegevens verlies te voor komen door back-ups te maken op basis van de richt lijnen van Azure Event Hubs, Log Analytics-werk ruimte en logische app.

Meer informatie vindt u in [Azure Event hubs-geo-nood herstel](../event-hubs/event-hubs-geo-dr.md).


### <a name="is-continuous-export-available-with-azure-security-center-free"></a>Is continue export beschikbaar met Azure Security Center gratis?

Ja. Houd er rekening mee dat veel Security Center waarschuwingen alleen worden gegeven wanneer u Azure Defender hebt ingeschakeld. Een goede manier om een voor beeld te bekijken van de waarschuwingen die u in uw geëxporteerde gegevens krijgt, is om de waarschuwingen te zien die worden weer gegeven op de pagina's van Security Center in de Azure Portal.



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u continue export van uw aanbevelingen en waarschuwingen kunt configureren. U hebt ook geleerd hoe u uw waarschuwings gegevens kunt downloaden als een CSV-bestand. 

Raadpleeg de volgende documentatie voor gerelateerde materialen: 

- Meer informatie over [werk stroom automatiserings sjablonen](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Documentatie over Azure Event Hubs](../event-hubs/index.yml)
- [Documentatie voor Azure Sentinel](../sentinel/index.yml)
- [Azure Monitor-documentatie](../azure-monitor/index.yml)
- [Schema's voor het exporteren van gegevens typen](https://aka.ms/ASCAutomationSchemas)