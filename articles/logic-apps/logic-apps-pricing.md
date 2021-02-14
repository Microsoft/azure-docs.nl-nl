---
title: Prijs modellen voor facturerings &
description: Overzicht van de manier waarop prijzen en facturerings modellen werken in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 103855748c4b5d998dfc81eeb4044f5f53dae9e5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372005"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Prijzen en facturerings modellen voor Azure Logic Apps

[Azure Logic apps](../logic-apps/logic-apps-overview.md) helpt u om geautomatiseerde integratie werk stromen te maken en uit te voeren die in de Cloud kunnen worden geschaald. In dit artikel wordt beschreven hoe factuur-en prijs modellen werken voor de Logic Apps-service en gerelateerde resources. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor specifieke prijs tarieven. Zie [kosten plannen en beheren voor Azure Logic apps](plan-manage-costs.md)voor meer informatie over hoe u kosten kunt plannen, beheren en bewaken.

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Prijzen voor meerdere tenants

Een prijs model voor betalen voor gebruik is van toepassing op Logic apps die worden uitgevoerd in de open bare, ' wereld wijde ' multi tenant-Logic Apps service. Alle geslaagde en mislukte uitvoeringen worden gemeten en gefactureerd.

Zo wordt een aanvraag die een polling trigger maakt, nog steeds als een uitvoering gemeten, zelfs als die trigger wordt overgeslagen en er geen werk stroom exemplaar van de logische app wordt gemaakt.

| Items | Description |
|-------|-------------|
| [Ingebouwde](../connectors/apis-list.md#built-in) triggers en acties | Systeem eigen uitvoeren in de Logic Apps-service en worden gemeten met behulp van de prijs van de [ **actie**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Zo zijn de HTTP-trigger en de aanvraag trigger ingebouwde triggers, terwijl de HTTP-actie en de reactie actie ingebouwde acties zijn. Gegevens bewerkingen, batch bewerkingen, variabele bewerkingen en [werk stroom besturings acties](../connectors/apis-list.md#control-workflow), zoals lussen, voor waarden, Switch, parallelle vertakkingen, enzovoort, zijn ook ingebouwde acties. |
| [Standaard-connector](../connectors/apis-list.md#managed-connectors) triggers en-acties <p><p>[Aangepaste connector](../connectors/apis-list.md#custom) triggers en-acties | Gemeten met behulp van de [Standard-connector prijs](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Triggers en acties voor [Enter prise-connector](../connectors/apis-list.md#managed-connectors) | Gemeten met behulp van de prijs van de [Enter prise-connector](https://azure.microsoft.com/pricing/details/logic-apps/). Tijdens de open bare preview worden ondernemings connectors echter met behulp van de [ *Standard* -connector prijs](https://azure.microsoft.com/pricing/details/logic-apps/)gemeten. |
| Acties in [lussen](logic-apps-control-flow-loops.md) | Elke actie die wordt uitgevoerd in een lus, wordt gemeten voor elke herhalings cyclus die wordt uitgevoerd. <p><p>Stel bijvoorbeeld dat u een lus ' voor elke ' hebt die acties bevat die een lijst verwerken. De Logic Apps-service metert elke actie die in die lus wordt uitgevoerd door het aantal lijst items te vermenigvuldigen met het aantal acties in de lus en voegt de actie toe die de lus start. De berekening voor een lijst met tien items is dus (10 * 1) + 1, wat resulteert in elf actie-uitvoeringen. |
| Nieuwe pogingen | Als u de meest eenvoudige uitzonde ringen en fouten wilt verwerken, kunt u een [beleid voor opnieuw proberen](logic-apps-exception-handling.md#retry-policies) instellen voor triggers en acties waarbij wordt ondersteund. Deze nieuwe pogingen samen met de oorspronkelijke aanvraag worden in rekening gebracht tegen tarieven op basis van het feit of de trigger of actie een ingebouwd, standaard of ENTER prise-type heeft. Een actie die wordt uitgevoerd met 2 nieuwe pogingen wordt bijvoorbeeld in rekening gebracht voor drie actie-uitvoeringen. |
| [Gegevens retentie en opslag verbruik](#data-retention) | Gemeten met behulp van de prijs voor het bewaren van gegevens, die u kunt vinden op de [pagina Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/), onder de tabel **prijs informatie** . |
|||

Raadpleeg de volgende artikelen voor meer informatie:

* [Metrische gegevens weer geven voor uitvoeringen en opslag verbruik](plan-manage-costs.md#monitor-billing-metrics)
* [Limieten in Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Geen data limiet

* Triggers die worden overgeslagen vanwege unmet-voor waarden
* Acties die niet zijn uitgevoerd omdat de logische app is gestopt voordat deze werd voltooid
* [Uitgeschakelde Logic apps](#disabled-apps)

### <a name="other-related-resources"></a>Andere verwante bronnen

Logic apps werken met andere gerelateerde resources, zoals integratie accounts, on-premises gegevens gateways en integratie service omgevingen (ISEs). Lees deze secties verderop in dit onderwerp voor meer informatie over de prijzen voor deze resources:

* [On-premises data gateway](#data-gateway) (On-premises gegevensgateway)
* [Prijs model voor integratie account](#integration-accounts)
* [ISE-prijs model](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tips voor het schatten van de verbruiks kosten

Bekijk de volgende tips om u te helpen bij het ramen van nauw keurigere verbruiks kosten:

* Denk na over het mogelijke aantal berichten of gebeurtenissen dat op een wille keurige dag kan arriveren, in plaats van uw berekeningen alleen op het polling-interval te baseren.

* Wanneer een gebeurtenis of bericht voldoet aan de criteria voor triggers, zullen de triggers onmiddellijk een poging doen alle gebeurtenissen die in de wacht staan of berichten die aan de criteria voldoen, te lezen. Dit gedrag houdt in dat ook als u een langere polling-interval selecteert, de trigger wordt geactiveerd op basis van het aantal gebeurtenissen in de wacht of berichten die in aanmerking komen voor het starten van werkstromen. Triggers die dit gedrag volgen, zijn onder andere Azure Service Bus en Azure Event Hub.

  Stel dat u trigger hebt ingesteld waarmee een eind punt elke dag wordt gecontroleerd. Wanneer de trigger het eindpunt controleert en er vijftien gebeurtenissen vindt die aan de criteria voldoen, wordt de trigger geactiveerd en wordt de corresponderende werkstroom vijftien keer uitgevoerd. De Logic Apps-service metert alle acties die door die 15 werk stromen worden uitgevoerd, met inbegrip van de trigger aanvragen.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Prijzen voor ISE

Een vast prijs model is van toepassing op Logic apps die worden uitgevoerd in een [ *Integration service Environment* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Een ISE wordt gefactureerd op basis van de [Integratieserviceomgeving prijs](https://azure.microsoft.com/pricing/details/logic-apps), die afhankelijk is van het [ISE niveau of de *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) die u maakt. Deze prijzen verschillen van de prijzen voor meerdere tenants wanneer u betaalt voor gereserveerde capaciteit en toegewezen resources, ongeacht of u deze gebruikt.

| ISE SKU | Description |
|---------|-------------|
| **Premium** | De basis eenheid heeft [vaste capaciteit](logic-apps-limits-and-config.md#integration-service-environment-ise) en wordt [gefactureerd tegen een uurtarief voor de Premium-SKU](https://azure.microsoft.com/pricing/details/logic-apps). Als u meer door voer wilt doen, kunt u [meer schaal eenheden toevoegen](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) wanneer u uw ISE maakt of daarna. Elke schaal eenheid wordt in rekening gebracht tegen een [uurtarief van ongeveer de basis eenheids](https://azure.microsoft.com/pricing/details/logic-apps)prijs. <p><p>Zie [limieten voor ISE in azure Logic apps](logic-apps-limits-and-config.md#integration-service-environment-ise)voor informatie over capaciteit en limieten. |
| **Developer** | De basis eenheid heeft [vaste capaciteit](logic-apps-limits-and-config.md#integration-service-environment-ise) en wordt [gefactureerd tegen een UURTARIEF voor de SKU van de ontwikkel aars](https://azure.microsoft.com/pricing/details/logic-apps). Deze SKU heeft echter geen SLA (Service Level Agreement), schaal baarheid of redundantie tijdens recycling, wat betekent dat u vertragingen of downtime mogelijk ondervindt. Back-end-updates kunnen de service af en toe onderbreken. <p><p>**Belang rijk**: Zorg ervoor dat u deze SKU alleen gebruikt voor onderzoek, experimenten, ontwikkeling en tests, niet voor productie-of prestatie testen. <p><p>Zie [limieten voor ISE in azure Logic apps](logic-apps-limits-and-config.md#integration-service-environment-ise)voor informatie over capaciteit en limieten. |
|||

### <a name="included-at-no-extra-cost"></a>Zonder extra kosten inbegrepen

| Items | Description |
|-------|-------------|
| [Ingebouwde](../connectors/apis-list.md#built-in) triggers en acties | Geef het **kern** label weer en voer deze uit in dezelfde ISE als uw logische apps. |
| [Standaardconnectoren](../connectors/apis-list.md#managed-connectors) <p><p>[Bedrijfsconnectoren](../connectors/apis-list.md#enterprise-connectors) | -Beheerde connectors die het label **ISE** weer geven, zijn speciaal ontworpen om te werken zonder de on-premises gegevens gateway en worden uitgevoerd in dezelfde ISE als uw Logic apps. ISE prijzen zijn inclusief een groot aantal zakelijke verbindingen. <p><p>-Connectors die het label ISE niet weer geven, worden uitgevoerd in de multi tenant-Logic Apps service. De prijzen voor ISE zijn echter inclusief de uitvoeringen voor Logic apps die worden uitgevoerd in een ISE. |
| Acties in [lussen](logic-apps-control-flow-loops.md) | ISE prijzen omvat elke actie die wordt uitgevoerd in een lus voor elke lussen cyclus. <p><p>Stel bijvoorbeeld dat u een lus ' voor elke ' hebt die acties bevat die een lijst verwerken. Als u het totale aantal uitvoeringen van de actie wilt ophalen, vermenigvuldigt u het aantal lijst items met het aantal acties in de lus en voegt u de actie toe waarmee de lus wordt gestart. De berekening voor een lijst met tien items is dus (10 * 1) + 1, wat resulteert in elf actie-uitvoeringen. |
| Nieuwe pogingen | Als u de meest eenvoudige uitzonde ringen en fouten wilt verwerken, kunt u een [beleid voor opnieuw proberen](logic-apps-exception-handling.md#retry-policies) instellen voor triggers en acties waarbij wordt ondersteund. ISE-prijzen zijn inclusief nieuwe pogingen samen met de oorspronkelijke aanvraag. |
| [Gegevens retentie en opslag verbruik](#data-retention) | Logic apps in een ISE doen geen retentie en opslag kosten in beslag. |
| [Integratieaccounts](#integration-accounts) | Bevat gebruik voor een enkele laag met een integratie account, op basis van ISE SKU, zonder extra kosten. |
|||

Zie [limieten voor ISE in azure Logic apps](logic-apps-limits-and-config.md#integration-service-environment-ise)voor meer informatie over de limiet.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integratieaccounts

Een [integratie account](../logic-apps/logic-apps-pricing.md#integration-accounts) is een afzonderlijke resource die u maakt en koppelt aan Logic apps, zodat u B2B-integratie oplossingen kunt verkennen, bouwen en testen die gebruikmaken van [EDI](logic-apps-enterprise-integration-b2b.md) [-en XML-verwerkings](logic-apps-enterprise-integration-xml.md) mogelijkheden. Azure Logic Apps bieden deze niveaus of lagen voor integratie accounts:

| Laag | Beschrijving |
|------|-------------|
| **Basic** | Voor scenario's waarbij u alleen berichten moet verwerken of als u wilt fungeren als een kleine zakelijke partner die een relatie heeft met een handels partner met een grotere zakelijke entiteit. <p><p>Ondersteund door de Logic Apps SLA. |
| **Standard** | Voor scenario's waarin u complexere B2B-relaties hebt en een groter aantal entiteiten dat u moet beheren. <p><p>Ondersteund door de Logic Apps SLA. |
| **Gratis** | Voor verkennende scenario's, niet voor productie scenario's. Deze laag heeft limieten voor de beschik baarheid, door Voer en het gebruik van de regio. De gratis laag is bijvoorbeeld alleen beschikbaar voor open bare regio's in azure, bijvoorbeeld VS-West of Zuidoost-Azië, maar niet voor [Azure China 21vianet](/azure/china/overview-operations) of [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Opmerking**: wordt niet ondersteund door de Logic apps sla. |
|||

Voor informatie over limieten van het integratie account raadpleegt u [limieten en configuratie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), zoals:

* [Limieten voor integratie accounts per Azure-abonnement](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limieten voor verschillende artefacten per integratie account](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Artefacten zijn onder andere handels partners, overeenkomsten, kaarten, schema's, assembly's, certificaten, batch configuraties, enzovoort.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Integratie accounts voor op verbruik gebaseerde Logic apps

Integratie accounts worden gefactureerd met een vaste [integratie account prijs](https://azure.microsoft.com/pricing/details/logic-apps/) die is gebaseerd op de account tier die u gebruikt.

### <a name="ise-based-logic-apps"></a>Logic apps op basis van ISE

Uw ISE bevat zonder extra kosten één integratie account, op basis van uw ISE-SKU. Voor extra kosten kunt u meer integratie accounts maken voor uw ISE om tot de [totale ISE limiet](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)te gebruiken. Meer informatie over het [ISE-prijs model](#fixed-pricing) vindt u eerder in dit onderwerp.

| ISE SKU | Inbegrepen integratie account | Extra kosten |
|---------|------------------------------|-----------------|
| **Premium** | Eén [standaard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account | Maxi maal 19 meer standaard accounts. Er zijn geen gratis of basis accounts toegestaan. |
| **Developer** | Eén [gratis](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account | Maxi maal 19 extra standaard accounts als u al een gratis account of 20 totaal standaard accounts hebt als u nog geen gratis account hebt. Er zijn geen basis accounts toegestaan. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Gegevens retentie en opslag verbruik

Alle invoer en uitvoer in de uitvoerings geschiedenis van de logische app worden opgeslagen en gemeten op basis van de uitvoerings duur van die app [en de retentie periode](logic-apps-limits-and-config.md#run-duration-retention-limits)voor de geschiedenis.

* Voor logische apps in de multi tenant-Logic Apps-service wordt het opslag verbruik gefactureerd tegen een vaste prijs, die u kunt vinden op de pagina met prijs **informatie** over de [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps).

* Voor Logic apps in ISEs worden geen kosten voor het bewaren van gegevens in rekening gebracht.

Zie [metrische gegevens weer geven voor uitvoeringen en opslag verbruik](plan-manage-costs.md#monitor-billing-metrics)voor het bewaken van het gebruik van opslag verbruik.

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>On-premises gegevensgateway

Een [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) is een afzonderlijke resource die u maakt zodat uw Logic apps toegang hebben tot on-premises gegevens met behulp van specifieke door de gateway ondersteunde connectors. Voor connector bewerkingen die via de gateway worden uitgevoerd, worden kosten in rekening gebracht, maar er worden geen kosten in rekening gebracht voor de gateway zelf.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Uitgeschakelde Logic apps

Uitgeschakelde Logic apps worden niet in rekening gebracht omdat ze geen nieuwe instanties kunnen maken wanneer ze zijn uitgeschakeld. Nadat u een logische app hebt uitgeschakeld, kan het enige tijd duren voordat de actieve instanties worden gestopt.

## <a name="next-steps"></a>Volgende stappen

* [Kosten plannen en beheren voor Azure Logic Apps](plan-manage-costs.md)
