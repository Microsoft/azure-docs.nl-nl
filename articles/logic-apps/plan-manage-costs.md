---
title: Plannen voor het beheren van kosten voor Azure Logic Apps
description: Meer informatie over het plannen en beheren van kosten voor Azure Logic Apps met behulp van kostenanalyse in de Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 03/24/2021
ms.openlocfilehash: ec2e1098df4c21704ee7c17852b893630cd3fd27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761813"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Kosten voor Azure Logic Apps

Dit artikel helpt u bij het plannen en beheren van kosten voor Azure Logic Apps. Voordat u resources maakt of toevoegt met behulp van deze service, moet u een schatting maken van uw kosten met behulp van de Azure-prijscalculator. Nadat u uw resources Logic Apps, kunt u budgetten instellen en kosten bewaken met behulp van [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Als u gebieden wilt identificeren waarop u mogelijk actie wilt ondernemen, kunt u ook geraamde kosten bekijken en uitgaventrends bewaken.

Houd er rekening mee dat de kosten voor Logic Apps slechts deel uitmaken van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Logic Apps kunt schatten en beheren, wordt u gefactureerd voor alle Azure-services en -resources die worden gebruikt in uw Azure-abonnement, met inbegrip van services van derden. Nadat u bekend bent met het beheren van kosten voor Logic Apps, kunt u vergelijkbare methoden toepassen om de kosten te beheren voor alle Azure-services die in uw abonnement worden gebruikt.

## <a name="prerequisites"></a>Vereisten

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ondersteunt de meeste Typen Azure-account. Als u alle ondersteunde accounttypen wilt weergeven, zie [Informatie over Cost Management gegevens.](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig.

Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Inzicht in het factureringsmodel

Azure Logic Apps wordt uitgevoerd op een Azure-infrastructuur die [kosten optelt](https://azure.microsoft.com/pricing/details/logic-apps/) bij het implementeren van nieuwe resources. Zorg ervoor dat u inzicht hebt in het factureringsmodel voor de Logic Apps-service, samen met gerelateerde [Azure-resources,](logic-apps-pricing.md)en beheer de kosten als gevolg van deze afhankelijkheden wanneer u wijzigingen aan de geïmplementeerde resources aan brengen.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Kosten die doorgaans toenemen met Azure Logic Apps

De Logic Apps-service past verschillende prijsmodellen toe, op basis van de resources die u maakt en gebruikt:

* Resources van logische apps die u maakt en in de multi-tenant Logic Apps-service, maken gebruik van [een prijsmodel voor verbruik.](../logic-apps/logic-apps-pricing.md#consumption-pricing)

* Voor resources van logische apps die u maakt en in een [ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maakt, wordt een [vast prijsmodel gebruikt.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

Hier volgen andere resources die kosten met zich mee brengen wanneer u ze maakt voor gebruik met logische apps:

* Een [integratieaccount](../logic-apps/logic-apps-pricing.md#integration-accounts) is een afzonderlijke resource die u maakt en koppelt aan logische apps voor het bouwen van B2B-integraties. Integratieaccounts gebruiken een [vast prijsmodel](../logic-apps/logic-apps-pricing.md#integration-accounts) waarbij het tarief is gebaseerd op het type integratieaccount *of* de laag die u gebruikt.

* Een [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) is een afzonderlijke resource die u maakt als implementatielocatie voor logische apps die directe toegang tot resources in een virtueel netwerk nodig hebben. ISE's gebruiken [een vast prijsmodel](../logic-apps/logic-apps-pricing.md#fixed-pricing) waarbij het tarief is gebaseerd op de ISE-SKU die u maakt en andere instellingen.

* Een [aangepaste connector](../logic-apps/logic-apps-pricing.md#consumption-pricing) is een afzonderlijke resource die u maakt voor een REST API die geen vooraf gebouwde connector heeft die u in uw logische apps kunt gebruiken. Uitvoeringen van aangepaste connectors maken gebruik [van een prijsmodel voor verbruik,](../logic-apps/logic-apps-pricing.md#consumption-pricing) behalve wanneer u ze in een ISE gebruikt.

* In de multi-tenant Logic Apps-service [worden](../logic-apps/logic-apps-pricing.md#data-retention) kosten voor gegevensretentie en opslagverbruik gemaakt met behulp van een [vast prijsmodel.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Invoer en uitvoer van de uitvoergeschiedenis worden bijvoorbeeld achter de schermen opgeslagen. Dit verschilt van opslagbronnen die u onafhankelijk van uw logische app maakt, beheert en gebruikt.

  In een ISE worden er geen kosten in rekening brengen voor gegevensretentie en opslagverbruik.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>De kosten kunnen toenemen na verwijdering van de resource

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Nadat u een logische app hebt verwijderd, Logic Apps de service geen nieuwe werkstroom-exemplaren meer maken of uitvoeren. Alle uitvoeringen die worden uitgevoerd en in behandeling zijn, worden echter voortgezet totdat ze zijn afgerond. Afhankelijk van het aantal van deze runs kan dit proces enige tijd duren. Zie Logische apps beheren [voor meer informatie.](manage-logic-apps-with-azure-portal.md#delete-logic-apps)

Als u deze resources hebt nadat u een logische app hebt verwijderd, blijven deze resources bestaan en worden er kosten gemaakt totdat u ze verwijdert:

* Azure-resources die u onafhankelijk van de logische app maakt en beheert die verbinding maakt met deze resources, bijvoorbeeld Azure-functie-apps, Event Hubs, Gebeurtenisrasters, en meer

* Integratieaccounts

* Integratieserviceomgevingen (ISE's)

  Als u [een ISE verwijdert,](ise-manage-integration-service-environment.md#delete-ise)blijven het gekoppelde virtuele Azure-netwerk, de subnetten en andere gerelateerde resources bestaan. Nadat u de ISE hebt verwijderd, moet u mogelijk een bepaald aantal uren wachten voordat u het virtuele netwerk of de subnetten kunt verwijderen.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Financieel tegoed gebruiken met Azure Logic Apps

U kunt betalen voor Azure Logic Apps met uw financiële toezeggingstegoed van EA. U kunt echter geen tegoed van de financiële toezegging van EA gebruiken om te betalen voor kosten voor producten en services van derden, inclusief die van de Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Kosten schatten

Voordat u resources maakt met Azure Logic Apps, moet u een schatting maken van uw kosten met behulp van de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/). Voor meer informatie bekijkt [u Prijsmodel voor Azure Logic Apps.](../logic-apps/logic-apps-pricing.md)

1. Selecteer op [de pagina Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/)in het linkermenu **de** optie  >  **Integratie Azure Logic Apps**.

   ![Schermopname van de Azure-prijscalculator met 'Azure Logic Apps' geselecteerd.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Schuif omlaag op de pagina totdat u de prijscalculator Azure Logic Apps bekijken. Voer in de verschillende secties voor Azure-resources die rechtstreeks zijn gerelateerd aan Azure Logic Apps het aantal resources in dat u wilt gebruiken en het aantal intervallen waarover u deze resources kunt gebruiken.

   In deze schermopname ziet u een voorbeeld van een kostenraming met behulp van de rekenmachine:

   ![Voorbeeld van geschatte kosten in de Azure-prijscalculator](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Als u uw kostenramingen wilt bijwerken terwijl u nieuwe gerelateerde resources maakt en gebruikt, gaat u terug naar deze calculator en werk u deze resources hier bij.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Maak budgetten en waarschuwingen

Als u de kosten voor uw Azure-account of [](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) -abonnement proactief wilt beheren, kunt u budgetten en waarschuwingen maken met behulp van [Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) service en mogelijkheden. [](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)  Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en -resourcegroepen, zodat ze nuttig zijn als onderdeel van een algehele strategie voor kostenbewaking.

Op basis van uitgaven in vergelijking met budget- en kostendrempels stellen waarschuwingen belanghebbenden automatisch op de hoogte van afwijkende uitgaven en te hoge risico's. Als u meer granulariteit in uw bewaking wilt, kunt u ook budgetten maken die gebruikmaken van filters voor specifieke resources of services in Azure. Filters zorgen ervoor dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie Groeps- en filteropties voor meer informatie over [de filteropties.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Kosten bewaken

Kosten voor resourcegebruikseenheden variëren per tijdsinterval, zoals seconden, minuten, uren en dagen, of per eenheidsgebruik, zoals bytes, megabytes, en meer. Enkele voorbeelden zijn per dag, huidige en vorige maand en jaar. Als u overschakelt naar langere weergaven gedurende een periode, kunt u bestedingstrends identificeren. Wanneer u de functies voor kostenanalyse gebruikt, kunt u kosten weergeven als grafieken en tabellen in verschillende tijdsintervallen. Als u budgetten en kostenprognoses hebt gemaakt, kunt u ook eenvoudig zien waar budgetten worden overschreden en er te veel is uitgevallen.

Nadat u kosten begint te maken voor resources die in Azure worden gemaakt of gebruikt, kunt u deze kosten op de volgende manieren controleren en controleren:

* [Uitvoeringen van logische apps en opslagverbruik bewaken met](#monitor-billing-metrics) behulp van Azure Monitor

* Kostenanalyse [uitvoeren](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) met behulp van [Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Uitvoeringen van logische apps en opslagverbruik bewaken

Met Azure Monitor kunt u deze metrische gegevens voor een specifieke logische app weergeven:

* Factureerbare uitvoeringen van acties
* Factureerbare triggeruitvoeringen
* Factureringsgebruik voor uitvoeringen van native bewerking
* Factureringsgebruik voor standaarduitvoeringen van connectors
* Factureringsgebruik voor opslagverbruik
* Totaal aantal factureerbare uitvoeringen

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Metrische gegevens over uitvoering en opslagverbruik weergeven

1. Zoek en open Azure Portal logische app in de Azure Portal. Selecteer in het menu van uw logische app onder **Bewaking** de optie **Metrische gegevens.**

1. Open in het rechterdeelvenster onder **Grafiektitel** in de  metrische balk de lijst Metrische gegevens en selecteer de metrische gegevens die u wilt.

   > [!NOTE]
   > Het opslagverbruik wordt gemeten als het aantal opslageenheden (GB) dat uw logische app gebruikt en wordt gefactureerd. Runs die minder dan 500 MB in opslag gebruiken, worden mogelijk niet weergegeven in de bewakingsweergave, maar worden wel gefactureerd.

   ![Schermopname van het deelvenster Metrische gegevens met de geopende lijst met metrische gegevens.](./media/logic-apps-pricing/select-metric.png)

1. Selecteer in de rechterbovenhoek van het deelvenster de periode die u wilt.

1. Volg deze stappen om andere verbruiksgegevens voor opslag weer te geven, met name de invoer- en uitvoergrootten van acties in de uitvoergeschiedenis [van uw](#view-input-output-sizes)logische app.

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Invoer- en uitvoergrootten van acties weergeven in de uitvoergeschiedenis

1. Zoek en open Azure Portal logische app in de Azure Portal.

1. Selecteer Overzicht in het menu van **uw** logische app.

1. Selecteer in het rechterdeelvenster onder **Geschiedenis** van runs de run met de in- en uitvoer die u wilt weergeven.

1. Selecteer **onder Uitvoeren van logische app** de optie Details **uitvoeren.**

1. Selecteer in **het deelvenster Details** uitvoeren van logische app in de tabel Acties, waarin de status en duur van elke actie worden vermeld, de actie die u wilt weergeven.

1. Zoek in **het actiedeelvenster** Logische app de grootten voor de in- en uitvoer van die actie. Zoek **onder de koppeling Invoer** en de koppeling **Uitvoer** de koppelingen naar die invoer en uitvoer.

   > [!NOTE]
   > Voor lussen tonen alleen de acties op het hoogste niveau grootten voor hun in- en uitvoer. Voor acties binnen geneste lussen hebben invoer en uitvoer geen grootte en geen koppelingen.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Kostenanalyse uitvoeren met behulp van Azure Cost Management and Billing

Als u de kosten voor de Logic Apps-service wilt controleren op basis van een [](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) specifiek bereik, bijvoorbeeld een Azure-abonnement, kunt u de mogelijkheden voor kostenanalyse gebruiken in [Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. Open in Azure Portal bereik dat u wilt, zoals uw Azure-abonnement. Selecteer in het linkermenu **onder Cost Management** de optie **Kostenanalyse.**

   Wanneer u het deelvenster kostenanalyse voor het eerst opent, worden in de bovenste grafiek de werkelijke en geraamde gebruikskosten weergegeven voor alle services in het abonnement voor de huidige maand.

   ![Schermopname van Azure Portal en het deelvenster Kostenanalyse met een voorbeeld van de werkelijke en geraamde kosten in een abonnement.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Als u de scopes wilt wijzigen, selecteert u in **het deelvenster Kostenanalyse** op de filtersbalk het filter **Bereik.** Schakel in **het deelvenster** Bereik selecteren over naar het bereik dat u wilt.

   Onder de cirkeldiagrammen worden de huidige kosten per Azure-services, per Azure-regio (locatie) en per resourcegroep.

   ![Schermopname van Azure Portal en het deelvenster Kostenanalyse met voorbeeldgrafieken voor services, regio's en resourcegroepen.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Als u de grafiek wilt filteren op een specifiek gebied, zoals een service of resource, selecteert u filter toevoegen op de **filtersbalk.**

1. Selecteer in de lijst aan de linkerkant het filtertype, bijvoorbeeld **Servicenaam.** Selecteer in de lijst aan de rechterkant het filter, bijvoorbeeld logische **apps.** Wanneer u klaar bent, selecteert u het groene vinkje.

   ![Schermopname van Azure Portal en het deelvenster Kostenanalyse met filterselecties.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Dit is bijvoorbeeld het resultaat voor de Logic Apps service:

   ![Schermopname van Azure Portal en het deelvenster Kostenanalyse met resultaten gefilterd op 'logische apps'.](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Kostengegevens exporteren

Wanneer u meer gegevensanalyses op kosten wilt uitvoeren, kunt u [kostengegevens exporteren](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslagaccount. Een financieel team kan deze gegevens bijvoorbeeld analyseren met Excel of Power BI. U kunt uw kosten exporteren volgens een dagelijks, wekelijks of maandelijks schema en een aangepast datumbereik instellen. Het exporteren van kostengegevens is de aanbevolen manier om kostengegevenssets op te halen.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Andere manieren om kosten te beheren en te verlagen

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Probeer de volgende opties om de kosten voor uw logische aps en gerelateerde resources te verlagen:

* Gebruik indien mogelijk [ingebouwde triggers](../connectors/built-in.md)en acties , die minder kosten om per uitvoering uit te voeren dan triggers en acties van [beheerde connectors.](../connectors/managed.md)

  U kunt bijvoorbeeld kosten verlagen bij het openen van andere resources met behulp van de [HTTP-actie](../connectors/connectors-native-http.md) of door een functie aan te roepen die u hebt gemaakt met behulp van de [Azure Functions-service](../azure-functions/functions-overview.md) en de [ingebouwde Azure Functions-actie](../logic-apps/logic-apps-azure-functions.md). Als u echter Azure Functions, worden er ook kosten in rekening brengen. Zorg er dus voor dat u uw opties vergelijkt.

* [Geef precieze triggervoorwaarden op voor](logic-apps-workflow-actions-triggers.md#trigger-conditions) het uitvoeren van een werkstroom.

  U kunt bijvoorbeeld opgeven dat een trigger alleen wordt uitgevoerd wanneer de doelwebsite een interne serverfout retourneert. Gebruik in de JSON-definitie van de trigger de eigenschap om een voorwaarde op te geven die verwijst naar de `conditions` statuscode van de trigger.

* Als een trigger een polling-versie en een webhook-versie heeft, probeert u de webhookversie, die wacht tot de opgegeven gebeurtenis zich voor het activeren voordeed, in plaats van regelmatig te controleren op de gebeurtenis.

* Roep uw logische app aan via een andere service, zodat de trigger alleen wordt uitgevoerd wanneer de werkstroom moet worden uitgevoerd.

  U kunt bijvoorbeeld uw logische app aanroepen vanuit een functie die u maakt en uit te voeren met behulp van de Azure Functions service. Zie bijvoorbeeld Logische [apps aanroepen of activeren met behulp van Azure Functions en Azure Service Bus.](logic-apps-scenario-function-sb-trigger.md)

* [Schakel logische apps](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) uit die niet voortdurend hoeven te worden uitgevoerd of verwijder logische [apps](manage-logic-apps-with-azure-portal.md#delete-logic-apps) die u helemaal niet meer nodig hebt. Schakel indien mogelijk alle andere resources uit die u niet voortdurend actief hoeft te hebben.

## <a name="next-steps"></a>Volgende stappen

* [Uw investeringen in de cloud optimaliseren met Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Kosten beheren met kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Onverwachte kosten voorkomen](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* De [begeleide Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) volgen