---
title: Aangepaste analyse regels maken voor het detecteren van bedreigingen met Azure Sentinel | Microsoft Docs
description: Gebruik deze zelf studie voor meer informatie over het maken van aangepaste analyse regels voor het detecteren van beveiligings Risico's met Azure Sentinel. Profiteer van gebeurtenis groepering, waarschuwings groepering en waarschuwings verrijking en meer informatie over automatisch uitgeschakeld.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 70b56e70ec0e6f511142c48cc89720c054807a5c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105042795"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Zelf studie: aangepaste analyse regels maken voor het detecteren van bedreigingen

Nu u [uw gegevens bronnen](quickstart-onboard.md) aan Azure Sentinel hebt gekoppeld, kunt u aangepaste analyse regels maken om u te helpen bij het detecteren van bedreigingen en afwijkend gedrag die aanwezig zijn in uw omgeving. Deze regels zoeken naar specifieke gebeurtenissen of gebeurtenissen sets in uw omgeving, waarschuwt u wanneer bepaalde drempel waarden voor de gebeurtenis of de omstandigheden zijn bereikt, genereert incidenten voor uw SOC to sorteren en onderzoek en reageert op bedreigingen met geautomatiseerde tracking-en herstel processen. 

Deze zelf studie helpt u bij het maken van aangepaste regels voor het detecteren van bedreigingen met Azure Sentinel.

Na het volt ooien van deze zelf studie kunt u het volgende doen:
> [!div class="checklist"]
> * Analytics-regels maken
> * Definiëren hoe gebeurtenissen en waarschuwingen worden verwerkt
> * Definiëren hoe waarschuwingen en incidenten worden gegenereerd
> * Automatische reacties op bedreigingen voor uw regels kiezen

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Een aangepaste analyse regel maken met een geplande query

1. Selecteer in het navigatie menu van de Azure-Sentinel **Analytics**.

1. Selecteer in de actie balk aan de bovenkant de optie **+ maken** en selecteer **geplande query regel**. Hiermee opent u de **wizard Analytics-regel**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Geplande query maken" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Wizard analyse regel-tabblad Algemeen

- Geef een unieke **naam** en **Beschrijving** op. 

- In het veld **tactiek** kunt u kiezen uit verschillende categorieën aanvallen waarmee de regel wordt geclassificeerd. Deze zijn gebaseerd op de tactiek van het [Mitre ATT&VERzonken](https://attack.mitre.org/) Framework.

- Stel de **Ernst** van de waarschuwing in op de juiste manier. 

- Wanneer u de regel maakt, wordt de **status** standaard **ingeschakeld** , wat betekent dat deze onmiddellijk wordt uitgevoerd nadat u deze hebt gemaakt. Als u niet wilt dat deze onmiddellijk wordt uitgevoerd, selecteert u **uitgeschakeld**. de regel wordt toegevoegd aan het tabblad **actieve regels** en u kunt het gebruiken wanneer u het nodig hebt.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Beginnen met het maken van een aangepaste analyse regel":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Definieer de regel query logica en configureer de instellingen

Op het tabblad **regel logica instellen** kunt u een query rechtstreeks in het veld **regel query** schrijven, of de query in log Analytics maken en deze vervolgens kopiëren en plakken.

- Query's worden geschreven in de Kusto-querytaal (KQL). Meer informatie over KQL- [concepten](/azure/data-explorer/kusto/concepts/) en- [query's](/azure/data-explorer/kusto/query/)en over deze handige [naslag handleiding](/azure/data-explorer/kql-quick-reference).

- In het voor beeld in deze scherm afbeelding wordt een query uitgevoerd voor de tabel *SecurityEvent* om een type [mislukte Windows-aanmeldings gebeurtenissen](/windows/security/threat-protection/auditing/event-4625)weer te geven.

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="De logica en instellingen van de query regel configureren" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Hier volgt een andere voorbeeld query: er wordt een waarschuwing weer gegeven wanneer een afwijkend aantal resources wordt gemaakt in [Azure activity](../azure-monitor/essentials/activity-log.md).

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Best practices voor regel query's
    > - De lengte van de query moet tussen 1 en 10.000 tekens lang zijn en mag niet " `search *` " of " `union *` " bevatten.
    >
    > - Het gebruik van ADX-functies voor het maken van Azure Data Explorer query's in het Log Analytics query venster **wordt niet ondersteund**.
    >
    > - Wanneer u de **`bag_unpack`** functie in een query gebruikt, mislukt de query als u de kolommen als velden gebruikt `project field1` en de kolom niet bestaat. Als u dit probleem wilt voor komen, moet u de kolom als volgt projecteren:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Waarschuwings verrijking

> [!IMPORTANT]
> De functies voor de verrijking van waarschuwingen zijn momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.
    
- Gebruik de sectie configuratie van **entiteits toewijzing** om para meters van uw query resultaten toe te wijzen aan door Sentinel herkende entiteiten van Azure. Entiteiten verrijken de uitvoer van de regels (waarschuwingen en incidenten) met essentiële informatie die fungeert als de bouw stenen van eventuele onderzoeken processen en herstel acties die volgen. Ze zijn ook de criteria op basis waarvan u waarschuwingen kunt groeperen in incidenten op het tabblad **incident instellingen** .

    Meer informatie over [entiteiten in azure Sentinel](entities-in-azure-sentinel.md).

    Zie [gegevens velden toewijzen aan entiteiten in azure Sentinel](map-data-fields-to-entities.md) voor volledige instructies voor entiteits toewijzing, samen met belang rijke informatie over [achterwaartse compatibiliteit](map-data-fields-to-entities.md#notes-on-the-new-version).

- Gebruik de sectie **aangepaste Details** configuratie om gebeurtenis gegevens items uit uw query op te halen en deze in te trekken in de waarschuwingen die door deze regel worden geproduceerd, zodat u de zicht baarheid van gebeurtenissen in uw waarschuwingen en incidenten onmiddellijk kunt zien.

    Meer informatie over aangepaste Details van halen in waarschuwingen en de [volledige instructies](surface-custom-details-in-alerts.md).

### <a name="query-scheduling-and-alert-threshold"></a>Query planning en drempel waarde voor waarschuwingen

- Stel in de sectie **query planning** de volgende para meters in:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Query planning en gebeurtenis groepering instellen" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Stel de **query run** in om te bepalen hoe vaak de query wordt uitgevoerd, zo vaak elke vijf minuten of als een regel matig als een keer per 14 dagen.

    - Stel **opzoek gegevens van de laatste** in om de tijds periode te bepalen van de gegevens die worden gedekt door de query. Dit kan bijvoorbeeld de afgelopen 10 minuten aan gegevens of de afgelopen 6 uur aan gegevens opvragen. Het maximum is 14 dagen.

        > [!NOTE]
        > **Query-intervallen en lookback periode**
        >
        >  Deze twee instellingen zijn onafhankelijk van elkaar, tot een bepaald punt. U kunt een query uitvoeren met een kort interval voor een tijds periode die langer is dan het interval (als gevolg van overlappende query's), maar u kunt geen query uitvoeren met een interval dat de dekkings periode overschrijdt, anders worden er hiaten in de algehele query dekking.
        >
        > **Opname vertraging**
        >
        > Voor een **latentie** die kan optreden tussen de generatie van een gebeurtenis bij de bron en de opname in azure Sentinel, en om ervoor te zorgen dat het volledige dekking zonder gegevens duplicatie plaatsvindt, voert Azure Sentinel geplande analyse regels uit op een **vertraging van vijf minuten** van de geplande tijd.
        >
        > Voor een gedetailleerde technische uitleg waarom deze vertraging nood zakelijk is en hoe dit probleem wordt opgelost, raadpleegt u het uitstekende blog bericht van Loek Marsiano voor het onderwerp '[opname vertraging verwerken in azure-waarschuwings regels voor Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)'.

- Gebruik de sectie **waarschuwings drempel** om het gevoeligheids niveau van de regel te definiëren. Stel bijvoorbeeld een **waarschuwing genereren wanneer het aantal query resultaten** **groter is dan** en voer het getal 1000 in als u wilt dat de regel alleen een waarschuwing genereert als de query meer dan 1000 resultaten retourneert telkens wanneer deze wordt uitgevoerd. Dit is een verplicht veld. Als u geen drempel waarde wilt instellen, dus als u wilt dat uw waarschuwing elke gebeurtenis registreert, voert u 0 in het veld getal in.
    
### <a name="results-simulation"></a>Simulatie van resultaten

Selecteer in het gedeelte **simulatie van resultaten** in de rechter kant van de wizard de optie **testen met huidige gegevens** en er wordt een grafiek weer gegeven met de resultaten (logboek gebeurtenissen) die de query zou hebben gegenereerd over de laatste 50 keer dat deze zou zijn uitgevoerd, volgens het momenteel gedefinieerde schema. Als u de query wijzigt, selecteert u opnieuw **testen met huidige gegevens** om de grafiek bij te werken. De grafiek toont het aantal resultaten gedurende de gedefinieerde tijds periode, die wordt bepaald door de instellingen in de sectie **query planning** .
  
Hier ziet u hoe de resultaten simulatie eruit kan zien als de query in de bovenstaande scherm afbeelding. De linkerkant is de standaard weergave en de rechter kant is wat u ziet wanneer u de muis aanwijzer boven een punt in de tijd van de grafiek houdt.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Scherm afbeeldingen met resultaten simulatie":::

Als u ziet dat uw query te veel of te frequente waarschuwingen zou activeren, kunt u experimenteren met de instellingen in de [secties](#query-scheduling-and-alert-threshold) **query planning** en **waarschuwings drempel** en vervolgens **test met huidige gegevens** selecteren.

### <a name="event-grouping-and-rule-suppression"></a>Gebeurtenissen groeperen en regel onderdrukking

> [!IMPORTANT]
> Gebeurtenis groepering is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.
    
- Kies onder **gebeurtenis groepering** een van de twee manieren om de groepering van **gebeurtenissen** in **waarschuwingen** te verwerken: 

    - **Alle gebeurtenissen groeperen in één waarschuwing** (de standaard instelling). De regel genereert één waarschuwing elke keer dat deze wordt uitgevoerd, mits de query meer resultaten oplevert dan de opgegeven **drempel waarde** voor de waarschuwing. De waarschuwing bevat een samen vatting van alle gebeurtenissen die in de resultaten worden geretourneerd. 

    - **Een waarschuwing voor elke gebeurtenis activeren**. De regel genereert een unieke waarschuwing voor elke gebeurtenis die door de query wordt geretourneerd. Dit is handig als u wilt dat gebeurtenissen afzonderlijk worden weer gegeven of dat u ze wilt groeperen op bepaalde para meters, per gebruiker, hostnaam of iets anders. U kunt deze para meters definiëren in de query.
    
        Het aantal waarschuwingen dat momenteel door een regel kan worden gegenereerd, is maximaal 20. Als in een bepaalde regel **gebeurtenis groepering** is ingesteld om **een waarschuwing voor elke gebeurtenis te activeren** en de query van de regel meer dan 20 gebeurtenissen retourneert, genereert elk van de eerste 19 gebeurtenissen een unieke waarschuwing en wordt de twintigste waarschuwing weer gegeven met de volledige set geretourneerde gebeurtenissen. Met andere woorden, de twintigste waarschuwing is wat zou zijn gegenereerd onder de **groep alle gebeurtenissen in één waarschuwings** optie.

    > [!NOTE]
    > Wat is het verschil tussen **gebeurtenissen** en **waarschuwingen**?
    >
    > - Een **gebeurtenis** is een beschrijving van één exemplaar van een actie. Zo kan één vermelding in een logboek bestand tellen als een gebeurtenis. In deze context verwijst een gebeurtenis naar een enkel resultaat dat wordt geretourneerd door een query in een Analytics-regel.
    >
    > - Een **waarschuwing** is een verzameling gebeurtenissen die samen worden genomen en belang rijk zijn in het oogpunt van beveiliging. Een waarschuwing kan één gebeurtenis bevatten als de gebeurtenis aanzienlijke beveiligings implicaties heeft: een administratieve aanmelding vanuit een buitenlands land buiten kantoor uren, bijvoorbeeld.
    >
    > - Op die manier zijn er **incidenten**? De interne logica van Azure Sentinel maakt **incidenten** van **waarschuwingen** of groepen waarschuwingen. De wachtrij incidenten is het brand punt van de sorteren, het onderzoek en het herstel van de sociale analisten.
    > 
    > Azure Sentinel neemt onbewerkte gebeurtenissen van sommige gegevens bronnen en de al verwerkte waarschuwingen van anderen. Het is belang rijk te weten welk abonnement u op elk moment gebruikt.

- In het gedeelte **onderdrukking** kunt u de instelling voor het **uitvoeren van de query stoppen nadat de waarschuwing is gegenereerd** inschakelen **op** als u een waarschuwing hebt ontvangen, wilt u de bewerking van deze regel voor een bepaalde periode voor het overschrijden van het query-interval onderbreken. Als u dit inschakelt, moet u de uitvoering van de **query stoppen** instellen op de tijd die nodig is voor het stoppen van de query, tot 24 uur.

## <a name="configure-the-incident-creation-settings"></a>De instellingen voor het maken van incidenten configureren

Op het tabblad **instellingen voor incidenten** kunt u kiezen of en hoe Azure Sentinel waarschuwingen in incidenten kan omzetten. Als dit tabblad alleen wordt weer gegeven, maakt Azure Sentinel een afzonderlijke, afzonderlijk incident van elke waarschuwing. U kunt ervoor kiezen om geen incidenten te maken of om verschillende waarschuwingen in één incident te groeperen, door de instellingen op dit tabblad te wijzigen.

> [!IMPORTANT]
> Het tabblad incident instellingen is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definieer de instellingen voor het maken van incidenten en het groeperen van waarschuwingen":::

### <a name="incident-settings"></a>Incidentinstellingen

In de sectie **incident instellingen** worden **incidenten gemaakt op basis van waarschuwingen die door deze analyse regel worden geactiveerd** , standaard ingesteld op **ingeschakeld**, wat betekent dat Azure Sentinel één afzonderlijk incident maakt van elke waarschuwing die wordt geactiveerd door de regel.
    
- Als u niet wilt dat deze regel resulteert in het maken van incidenten (als deze regel bijvoorbeeld alleen gegevens voor de volgende analyse verzamelt), stelt u deze in op **uitgeschakeld**.

- Zie de volgende sectie als u een enkel incident wilt maken op basis van een groep waarschuwingen in plaats van één.

### <a name="alert-grouping"></a>Groepering van waarschuwingen

Als u  een enkel incident wilt genereren op basis van een groep van maxi maal 150 vergelijk bare of terugkerende waarschuwingen (zie opmerking), stelt u **gerelateerde waarschuwingen in, die door deze analyse regel worden geactiveerd, naar incidenten** op **ingeschakeld** en stelt u de volgende para meters in.

- **De groep beperken tot waarschuwingen die zijn gemaakt in het geselecteerde tijds bestek**: Bepaal het tijds bestek waarbinnen de vergelijk bare of terugkerende waarschuwingen samen worden gegroepeerd. Alle bijbehorende waarschuwingen binnen deze tijds periode genereren gezamenlijk een incident of een reeks incidenten (afhankelijk van de groeperings instellingen hieronder). Waarschuwingen buiten deze tijds periode genereren een afzonderlijk incident of een reeks incidenten.

- **Groeps waarschuwingen die door deze analyse regel worden geactiveerd in één incident per**: Kies de basis waarop waarschuwingen worden gegroepeerd:

    - **Groepeer waarschuwingen in één incident als alle entiteiten overeenkomen**: waarschuwingen worden gegroepeerd als ze identieke waarden delen voor elk van de toegewezen entiteiten (gedefinieerd op het tabblad set-regel logica hierboven). Dit is de aanbevolen instelling.

    - **Alle waarschuwingen groeperen die door deze regel in één incident worden geactiveerd**: alle waarschuwingen die door deze regel worden gegenereerd, worden gegroepeerd, zelfs als ze geen identieke waarden delen.

    - **Groepeer waarschuwingen in één incident als de geselecteerde entiteiten overeenkomen**: waarschuwingen worden gegroepeerd als ze identieke waarden delen voor een aantal toegewezen entiteiten (die u kunt selecteren in de vervolg keuzelijst). U kunt deze instelling gebruiken als u bijvoorbeeld afzonderlijke incidenten wilt maken op basis van de bron-of doel-IP-adressen.

- **Gesloten afsluitende incidenten opnieuw openen**: als een incident is opgelost en gesloten, en later een andere waarschuwing wordt gegenereerd die tot dat incident moet behoren, stelt u deze instelling in op **ingeschakeld** als u wilt dat het gesloten incident opnieuw wordt geopend en schakelt u deze optie **uit als u** wilt dat de waarschuwing een nieuw incident maakt.
    
    > [!NOTE]
    > **Maxi maal 150 waarschuwingen** kunnen in één incident worden gegroepeerd. Als er meer dan 150 waarschuwingen worden gegenereerd door een regel die deze in één incident groepeert, wordt er een nieuw incident gegenereerd met dezelfde incident Details als het origineel en worden de overmatige waarschuwingen gegroepeerd in het nieuwe incident.

## <a name="set-automated-responses-and-create-the-rule"></a>Automatische antwoorden instellen en de regel maken

1. Op het tabblad **geautomatiseerde antwoorden** selecteert u de playbooks die u automatisch wilt uitvoeren wanneer een waarschuwing wordt gegenereerd door de aangepaste regel. Zie [reageren op bedreigingen](tutorial-respond-threats-playbook.md)voor meer informatie over het maken en automatiseren van playbooks.

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="De instellingen voor automatische antwoorden definiëren":::

1. Selecteer **controleren en maken** om alle instellingen voor de nieuwe waarschuwings regel te bekijken. Wanneer het bericht validatie is voltooid wordt weer gegeven, selecteert u **maken** om uw waarschuwings regel te initialiseren.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Alle instellingen controleren en de regel maken":::

## <a name="view-the-rule-and-its-output"></a>De regel en de uitvoer weer geven
  
- U kunt de zojuist gemaakte aangepaste regel (van het type ' gepland ') vinden in de tabel onder het tabblad **actieve regels** op het hoofd scherm van de **analyse** . In deze lijst kunt u elke regel inschakelen, uitschakelen of verwijderen.

- Als u de resultaten van de waarschuwings regels die u maakt, wilt weer geven, gaat u naar de pagina **incidenten** , waar u de [incidenten](tutorial-investigate-cases.md)kunt sorteren, onderzoeken en de bedreigingen herstelt.

> [!NOTE]
> Waarschuwingen die zijn gegenereerd in azure Sentinel zijn beschikbaar via [Microsoft Graph beveiliging](/graph/security-concept-overview). Zie de [documentatie over Microsoft Graph Security Alerts](/graph/api/resources/security-api-overview)(Engelstalig) voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="issue-no-events-appear-in-query-results"></a>Probleem: er worden geen gebeurtenissen weer gegeven in de query resultaten

Als **gebeurtenis groepering** is ingesteld om **een waarschuwing voor elke gebeurtenis te activeren**, dan in bepaalde scenario's, wanneer de query resultaten op een later tijdstip worden weer gegeven (bijvoorbeeld wanneer u terugdraait op waarschuwingen van een incident), is het mogelijk dat er geen query resultaten worden weer gegeven. Dit komt doordat de verbinding van de gebeurtenis met de waarschuwing wordt bereikt door het hashen van de gegevens van de betreffende gebeurtenis en het opnemen van de hash in de query. Als de query resultaten zijn gewijzigd nadat de waarschuwing is gegenereerd, is de hash niet langer geldig en worden er geen resultaten weer gegeven. 

Als u de gebeurtenissen wilt zien, verwijdert u de regel hand matig met de hash uit de query van de regel en voert u de query uit.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Probleem: een geplande regel kan niet worden uitgevoerd of wordt weer gegeven wanneer automatisch uitgeschakeld is toegevoegd aan de naam

Het is een zeldzame voorval dat een geplande query regel niet kan worden uitgevoerd, maar dit kan gebeuren. Azure Sentinel classificeert fouten vooraf als tijdelijk of permanent, op basis van het specifieke type fout en de omstandigheden die ernaar hebben geleid.

#### <a name="transient-failure"></a>Tijdelijke fout

Een tijdelijke fout treedt op als gevolg van een tijdelijke situatie en gaat binnenkort terug naar normaal, waarbij de uitvoering van de regel slaagt. Enkele voor beelden van fouten die door Azure Sentinel worden geclassificeerd als tijdelijk:

- Het duurt te lang voordat een regel query wordt uitgevoerd en er een time-out optreedt.
- Verbindings problemen tussen gegevens bronnen en Log Analytics, of tussen Log Analytics en Azure Sentinel.
- Andere nieuwe en onbekende fouten worden beschouwd als tijdelijk.

In het geval van een tijdelijke storing wordt de regel door Azure Sentinel continu opnieuw proberen uit te voeren na vooraf vastgestelde en steeds toenemende intervallen, tot een bepaald punt. Daarna wordt de regel opnieuw uitgevoerd op het volgende geplande tijdstip. Een regel wordt nooit automatisch uitgeschakeld vanwege een tijdelijke fout.

#### <a name="permanent-failure---rule-auto-disabled"></a>Permanente fout-automatische uitgeschakelde regel

Een permanente fout treedt op als gevolg van een wijziging in de voor waarden waardoor de regel kan worden uitgevoerd, zonder dat er sprake is van een menselijke interventie. Hier volgen enkele voor beelden van fouten die zijn geclassificeerd als permanent:

- De doel werkruimte (waarop de regel query wordt uitgevoerd) is verwijderd.
- De doel tabel (waarop de regel query wordt uitgevoerd) is verwijderd.
- De Azure-Sentinel is verwijderd uit de doel werkruimte.
- Een functie die door de regel query wordt gebruikt, is niet meer geldig. de service is gewijzigd of verwijderd.
- De machtigingen voor een van de gegevens bronnen van de regel query zijn gewijzigd.
- Een van de gegevens bronnen van de regel query is verwijderd of de verbinding is verbroken.

**In het geval van een vooraf bepaald aantal opeenvolgende permanente fouten, van hetzelfde type en van dezelfde regel,** Azure Sentinel stopt met het uitvoeren van de regel en neemt ook de volgende stappen:

- Hiermee schakelt u de regel uit.
- Voegt de woorden **' automatisch uitgeschakeld '** toe aan het begin van de naam van de regel.
- Hiermee wordt de reden voor de fout (en het uitschakelen) toegevoegd aan de beschrijving van de regel.

U kunt eenvoudig de aanwezigheid van automatische uitgeschakelde regels bepalen door de regel lijst te sorteren op naam. De regels voor automatisch uitschakelen bevinden zich boven aan de lijst.

SOC managers moeten de regel lijst regel matig controleren op de aanwezigheid van automatische uitgeschakelde regels.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u aan de slag gaat met het detecteren van bedreigingen met behulp van Azure Sentinel.

- Meer informatie over het [onderzoeken van incidenten in azure Sentinel](tutorial-investigate-cases.md).
- Meer informatie over [entiteiten in azure Sentinel](entities-in-azure-sentinel.md).
- Meer informatie over het [instellen van automatische bedreigings reacties in azure Sentinel](tutorial-respond-threats-playbook.md).