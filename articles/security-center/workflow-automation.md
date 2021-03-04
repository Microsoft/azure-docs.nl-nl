---
title: Werk stroom automatisering in Azure Security Center | Microsoft Docs
description: Meer informatie over het maken en automatiseren van werk stromen in Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6268ff6cfb3d3e856edcd8f84af930d52f4cf9d3
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096170"
---
# <a name="automate-responses-to-security-center-triggers"></a>Reacties op Security Center triggers automatiseren

Elk beveiligings programma bevat meerdere werk stromen voor reactie op incidenten. Deze processen kunnen het melden van relevante belanghebbenden, het starten van een wijzigings beheer proces en het Toep assen van specifieke herbemiddelings stappen zijn. Beveiligings experts raden u aan zo veel mogelijk stappen van deze procedures te automatiseren. Automation vermindert de overhead. Het kan ook de beveiliging verbeteren door ervoor te zorgen dat de proces stappen snel, consistent en volgens uw vooraf gedefinieerde vereisten worden uitgevoerd.

In dit artikel wordt de functie werk stroom automatisering van Azure Security Center beschreven. Deze functie kan Logic Apps activeren over beveiligings waarschuwingen, aanbevelingen en wijzigingen in de naleving van de regelgeving. U kunt bijvoorbeeld Security Center een e-mail bericht verzenden wanneer er een waarschuwing wordt weer gegeven. U leert ook hoe u Logic Apps maakt met behulp van [Azure Logic apps](../logic-apps/logic-apps-overview.md).


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|Gratis|
|Vereiste rollen en machtigingen:|De rol of **eigenaar** van de **beveiligings beheerder** voor de resource groep<br>Moet ook schrijf machtigingen hebben voor de doel resource<br><br>Als u met Azure Logic Apps werk stromen wilt werken, moet u ook over de volgende Logic Apps-rollen/-machtigingen beschikken:<br> - De machtigingen voor de [logische app-operator](../role-based-access-control/built-in-roles.md#logic-app-operator) zijn vereist of de toegang tot de logische app wordt gelezen/geactiveerd (deze rol kan geen Logic apps maken of bewerken; alleen bestaande *uitvoeren* )<br> - De Inzender machtigingen van de [logische app](../role-based-access-control/built-in-roles.md#logic-app-contributor) zijn vereist voor het maken en wijzigen van logische apps<br>Als u logische app-connectors wilt gebruiken, hebt u mogelijk aanvullende referenties nodig om u aan te melden bij hun respectieve services (bijvoorbeeld uw exemplaren van Outlook/teams/toegestane vertraging)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Een logische app maken en definiëren wanneer deze automatisch moet worden uitgevoerd 

1. Selecteer op de zijbalk van Security Center **werk stroom automatisering**.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Lijst met werk stroom Automatiseringen":::

    Op deze pagina kunt u nieuwe Automation-regels maken, maar ook bestaande voor het inschakelen, uitschakelen of verwijderen.

1. Als u een nieuwe werk stroom wilt definiëren, klikt u op **werk stroom automatisering toevoegen**. 

    Er wordt een deel venster met de opties voor uw nieuwe automatisering weer gegeven. Hier kunt u het volgende invoeren:
    1. Een naam en beschrijving voor de automatisering.
    1. De triggers waarmee deze automatische werk stroom wordt gestart. U wilt bijvoorbeeld dat uw logische app wordt uitgevoerd wanneer er een beveiligings waarschuwing wordt gegenereerd die ' SQL ' bevat.

        > [!NOTE]
        > Als uw trigger is een aanbeveling met ' subaanbevelingen ', bijvoorbeeld **evaluaties van beveiligings problemen voor uw SQL-data bases**, wordt de logische app niet geactiveerd voor elke nieuwe beveiligings zoek actie. alleen wanneer de status van de aanbeveling van het bovenliggende wordt gewijzigd.

    1. De logische app die wordt uitgevoerd wanneer aan de voor waarden van de trigger wordt voldaan. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Deel venster werk stroom automatisering toevoegen":::

1. Klik in de sectie acties op **een nieuw item maken** om het proces voor het maken van de logische app te starten.

    U wordt geAzure Logic Apps.

    [![Een nieuwe logische app maken](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Voer een naam, resource groep en locatie in en klik op **maken**.

1. In de nieuwe logische app kunt u kiezen uit ingebouwde, vooraf gedefinieerde sjablonen uit de beveiligings categorie. U kunt ook een aangepaste stroom van gebeurtenissen definiëren die moeten worden uitgevoerd wanneer dit proces wordt geactiveerd.

    > [!TIP]
    > Soms worden para meters in een logische app in de connector opgenomen als onderdeel van een teken reeks en niet in hun eigen veld. Zie stap #14 van het [werken met para meters voor logische apps tijdens het bouwen van Azure Security Center werk stroom automatisering](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121)voor een voor beeld van het extra heren van para meters.

    De ontwerp functie voor logische Apps ondersteunt deze Security Center-triggers:

    - **Wanneer een Azure Security Center aanbeveling wordt gemaakt of geactiveerd** : als uw logische app gebruikmaakt van een aanbeveling die wordt afgeschaft of vervangen, werkt de automatisering niet meer en moet u de trigger bijwerken. Zie [Azure Security Center opmerkingen](release-notes.md)bij de release als u wijzigingen in aanbevelingen wilt bijhouden.

    - **Wanneer een Azure Security Center waarschuwing wordt gemaakt of geactiveerd** , kunt u de trigger aanpassen zodat deze alleen betrekking heeft op waarschuwingen met de ernst niveaus die u interesseren.
    
    - **Wanneer een Security Center reglementaire nalevings beoordeling wordt gemaakt of geactiveerd** , worden Automation-activeringen uitgevoerd op basis van updates voor nalevings evaluaties van de regelgeving.

    > [!NOTE]
    > Als u de verouderde trigger gebruikt wanneer een reactie op een Azure Security Center waarschuwing wordt geactiveerd, worden uw logische apps niet gestart door de functie werk stroom automatisering. Gebruik in plaats daarvan een van de hierboven genoemde triggers. 

    [![Voor beeld van logische app](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Nadat u uw logische app hebt gedefinieerd, keert u terug naar het deel venster definitie van werk stroom automatisering ("werk stroom automatisering toevoegen"). Klik op **vernieuwen** om ervoor te zorgen dat de nieuwe logische app beschikbaar is voor selectie.

    ![Vernieuwen](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selecteer uw logische app en sla de automatisering op. Houd er rekening mee dat in de vervolg keuzelijst voor logische apps alleen Logic Apps worden weer gegeven met ondersteunende Security Center Connect oren die hierboven worden genoemd.


## <a name="manually-trigger-a-logic-app"></a>Een logische app hand matig activeren

U kunt Logic Apps ook hand matig uitvoeren wanneer u een beveiligings waarschuwing of aanbeveling bekijkt.

Als u een logische app hand matig wilt uitvoeren, opent u een waarschuwing of een aanbeveling en klikt u op **logische app activeren**:

[![Een logische app hand matig activeren](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Werk stroom automatisering op schaal configureren met de geleverde beleids regels

Het automatiseren van de processen voor bewaking en reageren op incidenten van uw organisatie kan de tijd die nodig is om beveiligingsincidenten te onderzoeken en te verhelpen aanzienlijk verbeteren.

Als u uw automatiserings configuraties in uw organisatie wilt implementeren, gebruikt u het opgegeven Azure Policy beleid ' DeployIfNotExist ' dat hieronder wordt beschreven om werk stroom automatiserings procedures te maken en te configureren.

Aan de slag met [sjablonen voor werkstroomautomatisering](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Deze beleids regels implementeren:

1. Selecteer in de onderstaande tabel het beleid dat u wilt Toep assen:

    |Doel  |Beleid  |Beleids-id  |
    |---------|---------|---------|
    |Werkstroomautomatisering voor beveiligingswaarschuwingen|[Werkstroomautomatisering implementeren voor Azure Security Center-waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Werkstroomautomatisering voor beveiligingsaanbevelingen|[Werkstroomautomatisering implementeren voor Azure Security Center-aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |Werk stroom automatisering voor wijzigingen in de naleving van de regelgeving|[Werk stroom automatisering implementeren voor Azure Security Center naleving van regelgeving](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > U kunt deze ook vinden door te zoeken naar Azure Policy:
    > 1. Open Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Toegang tot Azure Policy":::
    > 2. Selecteer in het menu Azure Policy **definities** en zoek ze op naam. 

1. Selecteer op de pagina relevante Azure Policy **toewijzen**.
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="De Azure Policy toewijzen":::

1. Open elk tabblad en stel de para meters naar wens in:
    1. Stel op het tabblad **basis beginselen** het bereik voor het beleid in. Als u gecentraliseerd beheer wilt gebruiken, wijst u het beleid toe aan de beheer groep met de abonnementen die de automatiserings configuratie voor werk stromen gaan gebruiken. 
    1. Stel op het tabblad **para meters** de gegevens van de resource groep en het gegevens type in. 
        > [!TIP]
        > Elke para meter heeft een knop info met uitleg over de beschik bare opties.
        >
        > Het tabblad para meters van Azure Policy (1) biedt toegang tot vergelijk bare configuratie opties als de werk stroom automatiserings pagina van Security Center (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="De para meters in werk stroom automatisering vergelijken met Azure Policy" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Als u deze toewijzing wilt Toep assen op bestaande abonnementen, opent u het tabblad **herstel** en selecteert u de optie voor het maken van een herstel taak.

1. Controleer de pagina samen vatting en selecteer **maken**.


## <a name="data-types-schemas"></a>Gegevens type schema's

Als u de onbewerkte gebeurtenis schema's wilt bekijken van de beveiligings waarschuwingen of aanbevelingen die zijn door gegeven aan de logische app-instantie, gaat u naar de [schema gegevens typen werk stroom automatisering](https://aka.ms/ASCAutomationSchemas). Dit kan handig zijn in gevallen waarin u geen gebruik maakt van Security Center ingebouwde logische app-connectors die hierboven worden genoemd. in plaats daarvan kunt u het JSON-schema van de logische app gebruiken om dit hand matig te parseren.


## <a name="faq-for-workflow-automation"></a>Veelgestelde vragen voor werk stroom automatisering

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Ondersteunt werk stroom automatisering alle scenario's voor bedrijfs continuïteit of herstel na nood gevallen (BCDR)?

Wanneer u uw omgeving voorbereidt voor BCDR-scenario's, waarbij de doel resource een storing of een andere nood situatie ondervindt, is het de verantwoordelijkheid van de organisatie om gegevens verlies te voor komen door back-ups te maken op basis van de richt lijnen van Azure Event Hubs, Log Analytics-werk ruimte en logische app.

Voor elke actieve automatisering wordt u aangeraden een identieke (uitgeschakelde) automatisering te maken en deze op een andere locatie op te slaan. Wanneer er een storing optreedt, kunt u deze back-upautomatiseringen inschakelen en normale bewerkingen onderhouden.

Meer informatie over [bedrijfs continuïteit en herstel na nood gevallen voor Azure Logic apps](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Logic Apps maakt, hoe u de uitvoering ervan automatiseert in Security Center en ze hand matig uitvoert.

Zie voor gerelateerd materiaal: 

- [De module Microsoft Learn voor het automatiseren van een beveiligings antwoord met werk stroom automatisering](/learn/modules/resolve-threats-with-azure-security-center/)
- [Aanbevelingen voor beveiliging in Azure Security Center](security-center-recommendations.md)
- [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
- [Over Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Connectors voor Azure Logic Apps](../connectors/apis-list.md)
- [Schema's van gegevenstypen van werkstroomautomatisering](https://aka.ms/ASCAutomationSchemas)