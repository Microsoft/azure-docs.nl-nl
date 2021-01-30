---
title: Planning voor het beheren van de kosten voor Azure Logic Apps
description: Meer informatie over het plannen en beheren van de kosten voor Azure Logic Apps met behulp van kosten analyse in de Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 58e12862cf00b500bced105d67fede8599c2a257
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99180452"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Kosten plannen en beheren voor Azure Logic Apps

Dit artikel helpt u bij het plannen en beheren van de kosten voor Azure Logic Apps. Voordat u resources maakt of toevoegt met behulp van deze service, kunt u een schatting maken van uw kosten met behulp van de Azure-prijs calculator. Nadat u Logic Apps-resources hebt gebruikt, kunt u budgetten instellen en kosten bewaken met behulp van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Als u gebieden wilt identificeren waar u mogelijk wilt handelen, kunt u ook prognose kosten bekijken en uitgaven trends bewaken.

Houd er rekening mee dat de kosten voor Logic Apps slechts een deel uitmaken van de maandelijkse kosten in uw Azure-factuur. Hoewel in dit artikel wordt uitgelegd hoe u de kosten voor Logic Apps kunt schatten en beheren, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief alle services van derden. Nadat u vertrouwd bent met het beheren van de kosten voor Logic Apps, kunt u soort gelijke methoden Toep assen om de kosten te beheren voor alle Azure-Services die in uw abonnement worden gebruikt.

## <a name="prerequisites"></a>Vereisten

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ondersteunt de meeste typen Azure-accounts. Zie [inzicht in cost management gegevens](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)om alle ondersteunde account typen weer te geven. Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig.

Zie [Toegang tot gegevens toewijzen](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Inzicht in het facturerings model

Azure Logic Apps wordt uitgevoerd op een Azure-infra structuur die de [kosten](https://azure.microsoft.com/pricing/details/logic-apps/) toeneemt wanneer u nieuwe resources implementeert. Zorg ervoor dat u het [facturerings model voor de Logic apps-service in combi natie met gerelateerde Azure-resources](logic-apps-pricing.md)begrijpt en de kosten beheert als gevolg van deze afhankelijkheden wanneer u wijzigingen aanbrengt in geïmplementeerde resources.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Kosten die normaal gesp roken toenemen met Azure Logic Apps

De Logic Apps-service is van toepassing op verschillende prijs modellen, op basis van de resources die u maakt en gebruikt:

* Logische app-resources die u maakt en uitvoert in de multi tenant-Logic Apps service, maken gebruik van een [prijs model voor verbruik](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Logische app-bronnen die u maakt en uitvoert in een [Integration service Environment (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken gebruik van een [vast prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Hier zijn andere bronnen die kosten in rekening brengen wanneer u deze maakt voor gebruik met Logic apps:

* Een [integratie account](../logic-apps/logic-apps-pricing.md#integration-accounts) is een afzonderlijke resource die u maakt en koppelt aan Logic apps voor het bouwen van B2B-integraties. Integratie accounts gebruiken een [vast prijs model](../logic-apps/logic-apps-pricing.md#integration-accounts) waarbij het tarief is gebaseerd op het type of de *laag* van het integratie account dat u gebruikt.

* Een [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) is een afzonderlijke resource die u maakt als een implementatie locatie voor logische apps die directe toegang nodig hebben tot bronnen in een virtueel netwerk. ISEs gebruik een [vast prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing) waarbij het tarief is gebaseerd op de ISE-SKU die u maakt en andere instellingen.

* Een [aangepaste connector](../logic-apps/logic-apps-pricing.md#consumption-pricing) is een afzonderlijke resource die u maakt voor een rest API die geen vooraf ontwikkelde connector heeft die u in uw Logic apps kunt gebruiken. Voor het uitvoeren van aangepaste connectors wordt een [verbruiks prijs model](../logic-apps/logic-apps-pricing.md#consumption-pricing) gebruikt, behalve wanneer u deze gebruikt in een ISE.

* In de multi tenant-Logic Apps service worden de kosten voor het [bewaren van gegevens en het opslag verbruik](../logic-apps/logic-apps-pricing.md#data-retention) samengevoegd met een [vast prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing). Invoer en uitvoer van de uitvoerings geschiedenis worden bijvoorbeeld opgeslagen in een achterliggende opslag, die verschilt van opslag resources die u onafhankelijk van uw logische app maakt, beheert en er toegang toe hebt.

  In een ISE worden er geen kosten in rekening gebracht voor het bewaren van gegevens en het opslag verbruik.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Kosten kunnen toenemen na het verwijderen van resources

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Nadat u een logische app hebt verwijderd, worden er door de Logic Apps-service geen nieuwe werk stroom exemplaren gemaakt of uitgevoerd. Alle lopende uitvoeringen en in behandeling zijnde uitvoeringen worden echter voortgezet totdat ze zijn voltooid. Afhankelijk van het aantal uitvoeringen kan dit proces enige tijd in beslag nemen. Zie [Logic apps beheren](manage-logic-apps-with-azure-portal.md#delete-logic-apps)voor meer informatie.

Als u deze resources hebt nadat u een logische app hebt verwijderd, blijven deze resources bestaan en worden de kosten samengevoegd totdat u ze verwijdert:

* Azure-resources die u maakt en beheert onafhankelijk van de logische app die verbinding maakt met deze resources, bijvoorbeeld Azure function-apps, Event hubs, gebeurtenis rasters enzovoort

* Integratieaccounts

* Integratie service omgevingen (ISEs)

  Als u [een ISE verwijdert](ise-manage-integration-service-environment.md#delete-ise), blijven het gekoppelde virtuele netwerk van Azure, subnetten en andere gerelateerde resources bestaan. Nadat u de ISE hebt verwijderd, moet u mogelijk wachten tot een bepaald aantal uur voordat u het virtuele netwerk of de subnetten kunt verwijderen.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Monetair tegoed gebruiken met Azure Logic Apps

U kunt betalen voor Azure Logic Apps kosten met uw EA monetaire toezeg ging-tegoed. U kunt het tegoed van EA monetaire toezeg ging echter niet gebruiken om te betalen voor kosten voor producten en services van derden, waaronder die van de Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Kosten schatten

Voordat u resources maakt met Azure Logic Apps, kunt u de kosten schatten met behulp van de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/). Bekijk het [prijs model voor Azure Logic apps](../logic-apps/logic-apps-pricing.md)voor meer informatie.

1. Selecteer op de [pagina Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/)in het menu links de optie **integratie**  >  **Azure Logic apps**.

   ![Scherm opname van de Azure-prijs calculator waarvoor ' Azure Logic Apps ' is geselecteerd.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Schuif naar beneden op de pagina totdat u de Azure Logic Apps prijs calculator kunt bekijken. In de verschillende secties voor Azure-resources die rechtstreeks verband houden met Azure Logic Apps, voert u de aantallen resources in die u wilt gebruiken en het aantal intervallen waarover u deze resources zou kunnen gebruiken.

   Deze scherm afbeelding toont een voor beeld van een kosten raming door gebruik te maken van de Calculator:

   ![Voor beeld van de geschatte kosten in de Azure-prijs calculator](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Als u uw kosten ramingen wilt bijwerken terwijl u nieuwe gerelateerde resources maakt en gebruikt, keert u terug naar deze reken machine en werkt u deze resources hier bij.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Maak budgetten en waarschuwingen

Om u te helpen de kosten voor uw Azure-account of-abonnement proactief te beheren, kunt u [budgetten](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) en [waarschuwingen](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken met behulp van de [Azure Cost Management-en facturerings](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) service en-mogelijkheden.  Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking.

Waarschuwingen die zijn gebaseerd op uitgaven vergeleken met budget-en kosten drempels, melden automatisch belanghebbenden over het uitgeven van afwijkingen en het overeden van Risico's. Als u meer granulatie wilt gebruiken in uw bewaking, kunt u ook budgetten maken die gebruikmaken van filters voor specifieke resources of services in Azure. Filters zorgen ervoor dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie [groeps-en filter opties](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie over de filter opties.

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Kosten bewaken

De kosten voor de resource gebruiks eenheid zijn afhankelijk van tijds intervallen, zoals seconden, minuten, uren en dagen, of voor het gebruik van eenheden, zoals bytes, MB, enzovoort. Enkele voor beelden zijn de dag, de huidige en de vorige maand en het jaar. U kunt in de loop van de tijd meer weer gaven gebruiken om de uitgaven trends te identificeren. Wanneer u de functies voor kosten analyse gebruikt, kunt u de kosten weer geven als grafieken en tabellen over verschillende tijds intervallen. Als u budgetten en kosten prognoses hebt gemaakt, kunt u ook gemakkelijk vinden waar budgetten worden overschreden en er mogelijk overbestedingen zijn opgetreden.

Nadat u de kosten hebt bespaard voor resources die in Azure worden gemaakt of gebruikt, kunt u deze kosten op de volgende manieren controleren en controleren:

* [Logische app-uitvoeringen en opslag verbruik bewaken](#monitor-billing-metrics) met behulp van Azure monitor

* [Kosten analyse](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) uitvoeren met behulp van [Azure Cost Management en facturering](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Uitvoeringen van logische apps en opslag verbruik bewaken

Met Azure Monitor kunt u deze metrische gegevens weer geven voor een specifieke logische app:

* Factureer bare actie-uitvoeringen
* Factureer bare trigger uitvoeringen
* Facturerings gebruik voor uitvoering van systeem eigen bewerkingen
* Facturerings gebruik voor het uitvoeren van standaard-connectors
* Facturerings gebruik voor opslag verbruik
* Totaal aantal factureer bare uitvoeringen

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Metrische gegevens over de uitvoering en het opslag gebruik weer geven

1. Zoek en open uw logische app in de Azure Portal. Selecteer **metrische gegevens** onder **bewaking** in het menu van de logische app.

1. Open in het deel venster aan de rechter kant onder **grafiek titel** in de metrische balk de lijst **metrische gegevens** en selecteer de gewenste metrische gegevens.

   > [!NOTE]
   > Het opslag verbruik wordt gemeten als het aantal opslag eenheden (GB) dat door uw logische app wordt gebruikt en wordt gefactureerd. Uitvoeringen die kleiner zijn dan 500 MB in opslag, worden mogelijk niet weer gegeven in de weer gave controle, maar ze worden nog steeds gefactureerd.

   ![Scherm opname van het deel venster metrische gegevens met de geopende lijst "metrische gegevens".](./media/logic-apps-pricing/select-metric.png)

1. Selecteer de gewenste tijds periode in de rechter bovenhoek van het venster.

1. [Ga als volgt te werk](#view-input-output-sizes)om andere gegevens over de opslag te bekijken, met name de invoer-en uitvoer grootte van de logische app in de uitvoerings geschiedenis.

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>De invoer-en uitvoer grootte van de actie weer geven in de uitvoerings geschiedenis

1. Zoek en open uw logische app in de Azure Portal.

1. Selecteer **overzicht** in het menu van de logische app.

1. Selecteer in het deel venster aan de rechter kant onder **uitvoerings geschiedenis** de uitvoering met de invoer en uitvoer die u wilt weer geven.

1. Selecteer onder **Logic app run** de optie **Details uitvoeren**.

1. Selecteer de actie die u wilt weer geven in de tabel acties van het detail venster voor het uitvoeren van de **logische app** , waarin de status en duur van elke actie worden vermeld.

1. Zoek in het actie deel venster van de **logische app** de grootten voor de invoer en uitvoer van die actie. Zoek onder koppeling naar **invoer** en **uitvoer** de koppelingen naar deze invoer en uitvoer.

   > [!NOTE]
   > Voor lussen toont alleen de acties op het hoogste niveau de grootte van de invoer en uitvoer. Voor acties binnen geneste lussen tonen invoer en uitvoer een grootte van nul en geen koppelingen.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Kosten analyse uitvoeren met behulp van Azure Cost Management en facturering

Als u de kosten voor de Logic Apps-service wilt bekijken op basis van een bepaald bereik, bijvoorbeeld een Azure-abonnement, kunt u de [kosten analyse](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) mogelijkheden in [Azure Cost Management en facturering](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)gebruiken.

1. Open in het Azure Portal het bereik dat u wilt, zoals uw Azure-abonnement. Selecteer in het linkermenu onder **Cost Management** **kosten analyse**.

   Wanneer u het deel venster kosten analyse voor het eerst opent, worden in de bovenste grafiek de werkelijke en verwachte gebruiks kosten weer gegeven voor alle services in het abonnement voor de huidige maand.

   ![Scherm opname van het deel venster Azure Portal en kosten analyse met voor beeld voor de werkelijke en geraamde kosten in een abonnement.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Als u de scopes wilt wijzigen, selecteert u in het deel venster **kosten analyse** op de **werk** balk filters het bereik filter. In het deel venster **bereik selecteren** gaat u naar het gewenste bereik.

   De ring diagrammen tonen de huidige kosten per Azure-service, per Azure-regio (locatie) en per resource groep.

   ![Scherm opname van het deel venster Azure Portal en kosten analyse met een voor beeld van een ring diagram voor services, regio's en resource groepen.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Als u de grafiek wilt filteren op een bepaald gebied, zoals een service of resource, selecteert u in de filter balk **filter toevoegen**.

1. Selecteer in de lijst aan de linkerkant het filter type, bijvoorbeeld **service naam**. Selecteer in de lijst aan de rechter kant het filter, bijvoorbeeld **Logic apps**. Wanneer u klaar bent, selecteert u het groene vinkje.

   ![Scherm opname van het deel venster Azure Portal en kosten analyse met filters electies.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Dit is bijvoorbeeld het resultaat voor de Logic Apps-service:

   ![Scherm opname van het deel venster Azure Portal en kosten analyse met de resultaten die worden gefilterd op ' Logic apps '.](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Kostengegevens exporteren

Als u meer gegevens analyse op kosten wilt uitvoeren, kunt u [kosten gegevens exporteren](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Een financieel team kan deze gegevens bijvoorbeeld analyseren met Excel of Power BI. U kunt uw kosten op een dagelijks, wekelijks of maandelijks schema exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Andere manieren om kosten te beheren en te verlagen

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Probeer de volgende opties om u te helpen bij het verminderen van de kosten voor uw logische APS en gerelateerde resources:

* Gebruik, indien mogelijk, [ingebouwde triggers en acties](../connectors/apis-list.md#built-in), die minder kosten per uitvoering dan [beheerde connector triggers en-acties](../connectors/apis-list.md#managed-connectors).

  Zo kunt u kosten besparen bij het openen van andere resources door gebruik te maken van de [http-actie](../connectors/connectors-native-http.md) of door het aanroepen van een functie die u hebt gemaakt met behulp van de [Azure functions-service](../azure-functions/functions-overview.md) en de [ingebouwde Azure functions actie](../logic-apps/logic-apps-azure-functions.md)te gebruiken. Het gebruik van Azure Functions daarentegen kost echter ook kosten. Zorg er dus voor dat u de opties vergelijkt.

* [Geef nauw keurige trigger voorwaarden](logic-apps-workflow-actions-triggers.md#trigger-conditions) op voor het uitvoeren van een werk stroom.

  U kunt bijvoorbeeld opgeven dat een trigger alleen wordt geactiveerd wanneer de doel website een interne server fout retourneert. In de JSON-definitie van de trigger gebruikt `conditions` u de eigenschap om een voor waarde op te geven die verwijst naar de status code van de trigger.

* Als een trigger een polling-versie en een webhook-versie heeft, probeert u de webhook-versie, waarmee wordt gewacht tot de opgegeven gebeurtenis plaatsvindt voordat deze wordt gestart, in plaats van regel matig te controleren op de gebeurtenis.

* Roep uw logische app aan via een andere service zodat de trigger alleen wordt geactiveerd wanneer de werk stroom moet worden uitgevoerd.

  U kunt bijvoorbeeld uw logische app aanroepen vanuit een functie die u maakt en uitvoert met behulp van de Azure Functions-service. Zie bijvoorbeeld [Logic apps aanroepen of activeren met behulp van Azure functions en Azure service bus](logic-apps-scenario-function-sb-trigger.md).

* [Schakel Logic apps uit](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) die niet voortdurend hoeven te worden uitgevoerd, of [Verwijder Logic apps](manage-logic-apps-with-azure-portal.md#delete-logic-apps) die u niet meer nodig hebt. Schakel, indien mogelijk, alle andere resources uit die u niet voortdurend actief hoeft te hebben.

## <a name="next-steps"></a>Volgende stappen

* [Uw investeringen in de cloud optimaliseren met Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Kosten beheren met kosten analyse](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Onverwachte kosten voorkomen](https://docs.microsoft.com/azure/cost-management-billing/understand/analyze-unexpected-charges?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Neem de [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door


