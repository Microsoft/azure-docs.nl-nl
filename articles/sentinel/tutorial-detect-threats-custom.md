---
title: Aangepaste analyseregels maken om bedreigingen te detecteren met Azure Sentinel| Microsoft Docs
description: Gebruik deze zelfstudie om te leren hoe u aangepaste analyseregels maakt om beveiligingsrisico's te detecteren met Azure Sentinel. Profiteer van gebeurtenisgroepering, waarschuwingsgroepering en waarschuwingsverrijking en begrijp AUTOMATISCH UITGESCHAKELD.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 180a5edd00b6085ffd91568471ca763f5e4e9711
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814852"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Zelfstudie: Aangepaste analyseregels maken om bedreigingen te detecteren

Nu u uw [](quickstart-onboard.md) gegevensbronnen hebt verbonden met Azure Sentinel, kunt u aangepaste analyseregels maken om bedreigingen en afwijkende gedragingen in uw omgeving te detecteren. Deze regels zoeken naar specifieke gebeurtenissen of reeksen gebeurtenissen in uw omgeving, waarschuwen u wanneer bepaalde drempelwaarden of voorwaarden voor gebeurtenissen zijn bereikt, genereren incidenten voor uw SOC om bedreigingen te onderzoeken en te onderzoeken met automatische tracerings- en herstelprocessen. 

Deze zelfstudie helpt u bij het maken van aangepaste regels voor het detecteren van bedreigingen met Azure Sentinel.

Na het voltooien van deze zelfstudie kunt u het volgende doen:
> [!div class="checklist"]
> * Analyseregels maken
> * Definiëren hoe gebeurtenissen en waarschuwingen worden verwerkt
> * Definiëren hoe waarschuwingen en incidenten worden gegenereerd
> * Automatische bedreigingsreacties kiezen voor uw regels

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Een aangepaste analyseregel maken met een geplande query

1. Selecteer in Azure Sentinel navigatiemenu **Analytics.**

1. Selecteer in de actiebalk bovenaan **+ Maken en** selecteer Geplande **queryregel.** Hiermee opent u de **wizard Analyseregel.**

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Geplande query maken" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Wizard Analyseregel - tabblad Algemeen

- Geef een unieke **naam en** een **Beschrijving op.** 

- In het **veld Tactieken** kunt u kiezen uit de categorieën van aanvallen waarmee u de regel classificeert. Deze zijn gebaseerd op de tactieken van het [MITRE ATT-&CK-framework.](https://attack.mitre.org/)

- Stel waar nodig **de ernst van** de waarschuwing in. 

- Wanneer u de regel maakt, **is** **de status** standaard Ingeschakeld, wat betekent dat deze onmiddellijk wordt uitgevoerd nadat u klaar bent met het maken ervan. Als u niet wilt dat de regel onmiddellijk wordt uitgevoerd, selecteert u Uitgeschakeld. De regel wordt toegevoegd aan het tabblad Actieve regels en u kunt de regel daar inschakelen wanneer u deze nodig hebt. 

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Een aangepaste analyseregel maken":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>De logica van de regelquery definiëren en instellingen configureren

Op het **tabblad Regellogica** instellen kunt u  een query rechtstreeks in het veld Regelquery schrijven of de query maken in Log Analytics en deze hier kopiëren en plakken.

- Query's worden geschreven in de Kusto-querytaal (KQL). Meer informatie over [](/azure/data-explorer/kusto/concepts/) KQL-concepten en [-query's](/azure/data-explorer/kusto/query/)en deze handige [snelzoekgids.](/azure/data-explorer/kql-quick-reference)

- In het voorbeeld in deze schermopname wordt een query op de *tabel SecurityEvent* weergegeven om een type mislukte [Windows-aanmeldingsgebeurtenissen weer te geven.](/windows/security/threat-protection/auditing/event-4625)

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Logica en instellingen voor queryregels configureren" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Hier is nog een voorbeeldquery, een query die u waarschuwt wanneer er een afwijkende hoeveelheid resources wordt gemaakt in [Azure Activity](../azure-monitor/essentials/activity-log.md).

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Best practices voor regelquery's
    > - De querylengte moet tussen de 1 en 10.000 tekens lang zijn en mag niet `search *` " " of " " `union *` bevatten. U kunt door de [gebruiker gedefinieerde functies gebruiken om](/azure/data-explorer/kusto/query/functions/user-defined-functions) de beperking van de querylengte op te lossen.
    >
    > - Het gebruik van ADX-functies Azure Data Explorer query's in het Log Analytics-queryvenster **wordt niet ondersteund.**
    >
    > - Wanneer u de functie in een query gebruikt en u de kolommen projectt als velden met behulp van ' en de kolom niet bestaat, mislukt **`bag_unpack`** `project field1` de query. Als bescherming tegen dit gebeurt, moet u de kolom als volgt projecteren:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Verrijking van waarschuwingen

> [!IMPORTANT]
> De functies voor waarschuwingsverrijking zijn momenteel beschikbaar als **PREVIEW.** Zie de Aanvullende gebruiksvoorwaarden voor [Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die bètaversies, preview-functies of anderszins nog niet algemeen beschikbaar zijn.
    
- Gebruik de **sectie Entiteitstoewijzingsconfiguratie** om parameters uit de queryresultaten toe te Azure Sentinel herkende entiteiten. Entiteiten verrijken de uitvoer van de regels (waarschuwingen en incidenten) met essentiële informatie die fungeert als de bouwstenen van onderzoekprocessen en herstelacties die volgen. Dit zijn ook de criteria waarmee u waarschuwingen kunt groeperen in incidenten op het **tabblad Incidentinstellingen.**

    Meer informatie over [entiteiten in Azure Sentinel](entities-in-azure-sentinel.md).

    Zie [Gegevensvelden toewijzen aan entiteiten in](map-data-fields-to-entities.md) Azure Sentinel voor volledige instructies voor entiteitstoewijzing, samen met belangrijke informatie over [achterwaartse compatibiliteit.](map-data-fields-to-entities.md#notes-on-the-new-version)

- Gebruik de **configuratiesectie** Aangepaste details om gebeurtenisgegevensitems uit uw query te extraheren en deze weer te geven in de waarschuwingen die door deze regel worden geproduceerd, zodat u direct inzicht krijgt in de gebeurtenisinhoud in uw waarschuwingen en incidenten.

    Meer informatie over het weergeven van aangepaste details in waarschuwingen en de [volledige instructies.](surface-custom-details-in-alerts.md)

### <a name="query-scheduling-and-alert-threshold"></a>Queryplanning en waarschuwingsdrempelwaarde

- Stel in **de sectie Queryplanning** de volgende parameters in:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Queryplanning en gebeurtenisgroepering instellen" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Stel **Elke query uitvoeren** in om te bepalen hoe vaak de query wordt uitgevoerd, zo vaak als elke 5 minuten of zo vaak als elke 14 dagen.

    - Stel **Opzoekgegevens** van de laatste in om de periode te bepalen van de gegevens die door de query worden gedekt. Er kunnen bijvoorbeeld query's worden uitgevoerd op de afgelopen 10 minuten aan gegevens of de afgelopen 6 uur aan gegevens. Het maximum is 14 dagen.

        > [!NOTE]
        > **Queryintervallen en lookback-periode**
        >
        >  Deze twee instellingen zijn onafhankelijk van elkaar, tot een bepaald punt. U kunt een query uitvoeren met een kort interval voor een periode die langer is dan het interval (in feite overlappende query's), maar u kunt een query niet uitvoeren met een interval dat de dekkingsperiode overschrijdt, anders hebt u hiaten in de totale querydekking.
        >
        > **Opnamevertraging**
        >
        > Om rekening te houden met **latentie** die kan optreden tussen het genereren van een gebeurtenis bij de bron en de opname ervan in Azure Sentinel,  en om een volledige dekking zonder gegevensduplicatie te garanderen, voert Azure Sentinel geplande analyseregels uit op een vertraging van vijf minuten vanaf de geplande tijd.
        >
        > Voor een gedetailleerde technische uitleg over waarom deze vertraging nodig is en hoe dit probleem wordt opgelost, zie de uitstekende blogpost van Ron Mars technical over het onderwerp "[Handling ingestion delay in Azure Sentinel scheduled alert rules](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)" (Opnamevertraging verwerken in Azure Sentinel geplande waarschuwingsregels).

- Gebruik de **sectie Waarschuwingsdrempel** om het gevoeligheidsniveau van de regel te definiëren. Stel bijvoorbeeld Waarschuwing genereren wanneer het aantal **queryresultaten** is groter **dan** in en voer het getal 1000 in als u wilt dat de regel alleen een waarschuwing genereert als de query telkens wanneer deze wordt uitgevoerd meer dan 1000 resultaten retourneert. Dit is een vereist veld, dus als u geen drempelwaarde wilt instellen, dat wil zeggen, als u wilt dat uw waarschuwing elke gebeurtenis registreert, voert u 0 in het getalveld in.
    
### <a name="results-simulation"></a>Simulatie van resultaten

Selecteer  in het gebied Resultatensimulatie aan de  rechterkant van de wizard de optie Testen met huidige gegevens. Azure Sentinel geeft een grafiek weer van de resultaten (logboekgebeurtenissen) die de query in de afgelopen 50 keer zou hebben gegenereerd, volgens de momenteel gedefinieerde planning. Als u de query wijzigt, **selecteert u opnieuw Testen met huidige gegevens om** de grafiek bij te werken. In de grafiek ziet u het aantal resultaten gedurende de gedefinieerde periode, dat wordt bepaald door de instellingen in de sectie **Queryplanning.**
  
Hier ziet u hoe de resultatensimulatie eruit kan zien voor de query in de bovenstaande schermopname. De linkerkant is de standaardweergave en de rechterkant is wat u ziet wanneer u de muisaanwijzer over een bepaald tijdstip in de grafiek beweegt.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Schermopnamen van resultatensimulatie":::

Als u ziet dat uw query te veel of te vaak waarschuwingen activeert,  kunt u experimenteren met de instellingen in de secties **Queryplanning** en [Waarschuwingsdrempelwaarde](#query-scheduling-and-alert-threshold) en selecteert u opnieuw **Testen** met huidige gegevens.

### <a name="event-grouping-and-rule-suppression"></a>Gebeurtenisgroepering en regelonderdrukking

> [!IMPORTANT]
> Gebeurtenisgroepering is momenteel beschikbaar in **preview.** Zie de aanvullende gebruiksvoorwaarden voor [Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die bètaversies of preview-functies hebben of die nog niet algemeen beschikbaar zijn.
    
- Kies **onder Gebeurtenisgroepering** een van de twee manieren om het groeperen van gebeurtenissen in **waarschuwingen** te **verwerken:** 

    - **Groeperen van alle gebeurtenissen in één waarschuwing** (de standaardinstelling). De regel genereert telkens wanneer deze wordt uitgevoerd één waarschuwing, zolang de query meer resultaten retourneert dan de opgegeven **waarschuwingsdrempelwaarde hierboven.** De waarschuwing bevat een samenvatting van alle gebeurtenissen die in de resultaten worden geretourneerd. 

    - **Een waarschuwing voor elke gebeurtenis activeren**. De regel genereert een unieke waarschuwing voor elke gebeurtenis die door de query wordt geretourneerd. Dit is handig als u wilt dat gebeurtenissen afzonderlijk worden weergegeven of als u ze wilt groeperen op bepaalde parameters, op gebruiker, hostnaam of iets anders. U kunt deze parameters definiëren in de query.
    
        Het aantal waarschuwingen dat momenteel door een regel kan worden gegenereerd, is maximaal 20. Als in een bepaalde regel Gebeurtenisgroepering **is** ingesteld op Een waarschuwing activeren voor elke gebeurtenis en de query van de regel meer dan 20 gebeurtenissen retourneert, genereert elk van de eerste 19 gebeurtenissen een unieke waarschuwing en wordt met de 20e waarschuwing de volledige set geretourneerde gebeurtenissen samengevat.  Met andere woorden, de 20e waarschuwing is wat er zou zijn gegenereerd onder de optie Alle gebeurtenissen **groeperen in één waarschuwingsoptie.**

    > [!NOTE]
    > Wat is het verschil tussen **gebeurtenissen en** **waarschuwingen?**
    >
    > - Een **gebeurtenis** is een beschrijving van één exemplaar van een actie. Eén vermelding in een logboekbestand kan bijvoorbeeld als een gebeurtenis worden geteld. In deze context verwijst een gebeurtenis naar één resultaat dat wordt geretourneerd door een query in een analyseregel.
    >
    > - Een **waarschuwing** is een verzameling gebeurtenissen die samen belangrijk zijn vanuit het oogpunt van beveiliging. Een waarschuwing kan één gebeurtenis bevatten als de gebeurtenis aanzienlijke gevolgen heeft voor de beveiliging, bijvoorbeeld een administratieve aanmelding vanuit een ander land buiten kantooruren.
    >
    > - Wat zijn **incidenten?** Azure Sentinel interne logica maakt **incidenten** op uit **waarschuwingen** of groepen waarschuwingen. De incidentenwachtrij is het centrale punt van het werk van SOC-analisten: triage, onderzoek en herstel.
    > 
    > Azure Sentinel worden onbewerkte gebeurtenissen uit sommige gegevensbronnen opgenomen en worden waarschuwingen van andere bronnen al verwerkt. Het is belangrijk om te weten met welke u te maken hebt.

- In  de sectie Onderdrukking kunt u de instelling Stop  **running query after alert is generated** (Stoppen met uitvoeren van query nadat de waarschuwing is gegenereerd) aan zetten als u, zodra u een waarschuwing krijgt, de bewerking van deze regel wilt opschorten voor een periode van tijd die het queryinterval overschrijdt. Als u deze in kunt stellen, moet u Stoppen met uitvoeren **van query** voor instellen op de hoeveelheid tijd dat de query niet meer mag worden uitgevoerd, maximaal 24 uur.

## <a name="configure-the-incident-creation-settings"></a>De instellingen voor het maken van incidenten configureren

Op het **tabblad Incidentinstellingen** kunt u kiezen of en hoe Azure Sentinel waarschuwingen om te zetten in incidenten waarop actie kan worden ondernomen. Als dit tabblad met rust wordt gelaten, Azure Sentinel één incident maken, gescheiden van elke waarschuwing. U kunt ervoor kiezen om geen incidenten te maken of verschillende waarschuwingen te groeperen in één incident door de instellingen op dit tabblad te wijzigen.

> [!IMPORTANT]
> Het tabblad incidentinstellingen is momenteel beschikbaar als **PREVIEW.** Zie de Aanvullende gebruiksvoorwaarden voor [Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die bètaversies, preview-functies of anderszins nog niet algemeen beschikbaar zijn.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definieer de instellingen voor het maken van incidenten en het groeperen van waarschuwingen":::

### <a name="incident-settings"></a>Incidentinstellingen

In de sectie  **Incidentinstellingen** wordt Incidenten maken op basis van waarschuwingen die worden geactiveerd door deze analyseregel standaard ingesteld op **Ingeschakeld.** Dit betekent dat Azure Sentinel één afzonderlijk incident maakt van elke waarschuwing die door de regel wordt geactiveerd.
    
- Als u niet wilt dat deze regel leidt tot het maken van incidenten (bijvoorbeeld als deze regel alleen is om informatie te verzamelen voor volgende analyse), stelt u deze in op **Uitgeschakeld.**

- Als u wilt dat er één incident wordt gemaakt van een groep waarschuwingen, in plaats van één voor elke waarschuwing, bekijkt u de volgende sectie.

### <a name="alert-grouping"></a>Waarschuwingsgroepering

Als  u in de sectie Waarschuwingsgroepering wilt dat één incident wordt gegenereerd op basis van een groep van maximaal 150 vergelijkbare of terugkerende waarschuwingen (zie opmerking), stelt u Groepsgerelateerde waarschuwingen, geactiveerd door deze **analyseregel,** in op Ingeschakeld voor incidenten en stelt u de volgende parameters in.

- **Beperk de groep tot waarschuwingen die binnen** het geselecteerde tijdsbestek zijn gemaakt: bepaal het tijdsbestek waarin de vergelijkbare of terugkerende waarschuwingen worden gegroepeerd. Alle bijbehorende waarschuwingen binnen dit tijdsbestek genereren gezamenlijk een incident of een reeks incidenten (afhankelijk van de onderstaande groeperingsinstellingen). Waarschuwingen buiten dit tijdsbestek genereren een afzonderlijk incident of een reeks incidenten.

- **Groepswaarschuwingen die door deze analyseregel worden** geactiveerd in één incident door : Kies de basis waarop waarschuwingen worden gegroepeerd:

    - **Waarschuwingen groeperen** in één incident als alle entiteiten overeenkomen: waarschuwingen worden gegroepeerd als ze identieke waarden delen voor elk van de entiteiten die zijn gedefinieerd (gedefinieerd op het tabblad Regellogica instellen hierboven). Dit is de aanbevolen instelling.

    - **Groepeert alle waarschuwingen die door** deze regel worden geactiveerd in één incident: alle waarschuwingen die door deze regel worden gegenereerd, worden gegroepeerd, zelfs als ze geen identieke waarden delen.

    - **Groepswaarschuwingen** in één incident als de geselecteerde entiteiten overeenkomen: waarschuwingen worden gegroepeerd als ze identieke waarden delen voor sommige van de entiteiten die zijn toegevoegd (die u kunt selecteren in de vervolgkeuzelijst). U kunt deze instelling gebruiken als u bijvoorbeeld afzonderlijke incidenten wilt maken op basis van de bron- of doel-IP-adressen.

- Gesloten overeenkomende incidenten opnieuw **openen:** als een incident is opgelost en gesloten en later een andere waarschuwing  wordt gegenereerd die bij dat incident hoort,  stelt u deze instelling in op Ingeschakeld als u wilt dat het gesloten incident opnieuw wordt geopend en laat u Uitgeschakeld staan als u wilt dat de waarschuwing een nieuw incident maakt.
    
    > [!NOTE]
    > **Er kunnen maximaal 150 waarschuwingen** worden gegroepeerd in één incident. Als er meer dan 150 waarschuwingen worden gegenereerd door een regel die ze in één incident groepeert, wordt er een nieuw incident gegenereerd met dezelfde incidentdetails als het oorspronkelijke incident en worden de overtollige waarschuwingen gegroepeerd in het nieuwe incident.

## <a name="set-automated-responses-and-create-the-rule"></a>Automatische antwoorden instellen en de regel maken

1. Selecteer op **het tabblad Geautomatiseerde** antwoorden de playbooks die u automatisch wilt uitvoeren wanneer een waarschuwing wordt gegenereerd door de aangepaste regel. Zie Respond to threats (Reageren op bedreigingen) voor meer informatie over het maken en [automatiseren van playbooks.](tutorial-respond-threats-playbook.md)

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="De instellingen voor geautomatiseerde antwoorden definiëren":::

1. Selecteer **Controleren en maken om** alle instellingen voor de nieuwe waarschuwingsregel te controleren. Wanneer het bericht Validatie is geslaagd wordt weergegeven, selecteert u **Maken om** de waarschuwingsregel te initialiseren.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Alle instellingen controleren en de regel maken":::

## <a name="view-the-rule-and-its-output"></a>De regel en de uitvoer ervan weergeven
  
- U vindt de zojuist gemaakte aangepaste regel (van het type Gepland) in de tabel onder het **tabblad Actieve** regels op het hoofdscherm **Analyse.** In deze lijst kunt u elke regel inschakelen, uitschakelen of verwijderen.

- Als u de resultaten wilt bekijken van  de waarschuwingsregels die u maakt, gaat u naar de pagina Incidenten, waar u incidenten kunt [zoeken,](tutorial-investigate-cases.md)onderzoeken en de bedreigingen kunt herstellen.

> [!NOTE]
> Waarschuwingen die worden gegenereerd in Azure Sentinel zijn beschikbaar [via Microsoft Graph Security](/graph/security-concept-overview). Zie de documentatie Microsoft Graph [Beveiligingswaarschuwingen voor meer informatie.](/graph/api/resources/security-api-overview)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="issue-no-events-appear-in-query-results"></a>Probleem: Er worden geen gebeurtenissen weergegeven in queryresultaten

Als gebeurtenisgroepering **is** ingesteld om een waarschuwing voor elke gebeurtenis te **activeren,** is het mogelijk dat er in bepaalde scenario's, wanneer de queryresultaten op een later tijdstip worden weergegeven (bijvoorbeeld wanneer u terugvoeert naar waarschuwingen van een incident), er geen queryresultaten worden weergegeven. Dit komt doordat de verbinding van de gebeurtenis met de waarschuwing wordt bereikt door het hashen van de informatie van de specifieke gebeurtenis en het opnemen van de hash in de query. Als de queryresultaten zijn gewijzigd sinds de waarschuwing is gegenereerd, is de hash niet langer geldig en worden er geen resultaten weergegeven. 

Als u de gebeurtenissen wilt zien, verwijdert u handmatig de regel met de hash uit de query van de regel en voer u de query uit.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Probleem: Een geplande regel kan niet worden uitgevoerd of wordt weergegeven met AUTOMATISCH UITGESCHAKELD toegevoegd aan de naam

Het komt zelden voor dat een geplande queryregel niet kan worden uitgevoerd, maar dit kan wel gebeuren. Azure Sentinel classificeert fouten van te voren als tijdelijk of permanent, op basis van het specifieke type fout en de omstandigheden die ertoe hebben geleid.

#### <a name="transient-failure"></a>Tijdelijke fout

Een tijdelijke fout treedt op als gevolg van een tijdelijke situatie die binnenkort weer normaal wordt, waarna de uitvoering van de regel slaagt. Enkele voorbeelden van fouten die Azure Sentinel als tijdelijk worden classificeert:

- Het duurt te lang om een regelquery uit te voeren en er t/m een times-out uit te voeren.
- Connectiviteitsproblemen tussen gegevensbronnen en Log Analytics, of tussen Log Analytics en Azure Sentinel.
- Andere nieuwe en onbekende fouten worden beschouwd als tijdelijk.

In het geval van een tijdelijke fout blijft Azure Sentinel de regel opnieuw uitvoeren na vooraf bepaalde en steeds toenemende intervallen, tot een bepaald punt. Daarna wordt de regel pas opnieuw uitgevoerd op het volgende geplande tijdstip. Een regel wordt nooit automatisch uitgeschakeld vanwege een tijdelijke fout.

#### <a name="permanent-failure---rule-auto-disabled"></a>Permanente fout - regel automatisch uitgeschakeld

Een permanente fout treedt op als gevolg van een wijziging in de voorwaarden waardoor de regel kan worden uitgevoerd, die zonder menselijke tussenkomst niet terugkeert naar hun vorige status. Hier volgen enkele voorbeelden van fouten die zijn geclassificeerd als permanent:

- De doelwerkruimte (waarop de regelquery is uitgevoerd) is verwijderd.
- De doeltabel (waarop de regelquery is uitgevoerd) is verwijderd.
- Azure Sentinel is verwijderd uit de doelwerkruimte.
- Een functie die wordt gebruikt door de regelquery is niet langer geldig; deze is gewijzigd of verwijderd.
- Machtigingen voor een van de gegevensbronnen van de regelquery zijn gewijzigd.
- Een van de gegevensbronnen van de regelquery is verwijderd of de verbinding is verbroken.

**In het geval van een vooraf bepaald aantal opeenvolgende permanente fouten, van** hetzelfde type en volgens dezelfde regel, Azure Sentinel de regel niet meer probeert uit te voeren en voert ook de volgende stappen uit:

- Hiermee schakelt u de regel.
- Voegt de woorden **'AUTO DISABLED' toe** aan het begin van de naam van de regel.
- Hiermee wordt de reden voor de fout (en het uitschakelen) toegevoegd aan de beschrijving van de regel.

U kunt eenvoudig de aanwezigheid van automatisch uitgeschakelde regels bepalen door de lijst met regels op naam te sorteren. De automatisch uitgeschakelde regels staan bovenaan of bovenaan de lijst.

SOC-managers moeten de regellijst regelmatig controleren op de aanwezigheid van regels voor automatisch uitgeschakelde regels.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u bedreigingen kunt detecteren met behulp van Azure Sentinel.

- Meer informatie over [het onderzoeken van incidenten in Azure Sentinel](tutorial-investigate-cases.md).
- Meer informatie [over entiteiten in Azure Sentinel](entities-in-azure-sentinel.md).
- Meer informatie over het [instellen van geautomatiseerde bedreigingsreacties in Azure Sentinel](tutorial-respond-threats-playbook.md).
