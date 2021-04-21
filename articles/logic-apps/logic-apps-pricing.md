---
title: Prijsmodellen & factureringsmodellen
description: Overzicht van de manier waarop prijs- en factureringsmodellen werken in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a3c20dd85c94c359259cf69e25bb9083d56857fc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777145"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Prijs- en factureringsmodellen voor Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) helpt u bij het maken en uitvoeren van geautomatiseerde integratiewerkstromen die kunnen worden geschaald in de cloud. In dit artikel wordt beschreven hoe facturerings- en prijsmodellen werken voor de Logic Apps-service en gerelateerde resources. Zie Prijzen Logic Apps voor [specifieke prijzen.](https://azure.microsoft.com/pricing/details/logic-apps) Zie Kosten plannen en beheren voor Azure Logic Apps voor meer informatie over het plannen, beheren en [bewaken van Azure Logic Apps.](plan-manage-costs.md)

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Prijzen voor meerdere tenants

Een prijsmodel voor betalen voor gebruiksverbruik is van toepassing op logische apps die worden uitgevoerd in de openbare, 'globale' en multi-tenant Logic Apps service. Alle geslaagde en mislukte runs worden gemeten en gefactureerd.

Een aanvraag die een poll-trigger doet, wordt bijvoorbeeld nog steeds gemeten als een uitvoering, zelfs als die trigger wordt overgeslagen en er geen werkstroom-exemplaar van een logische app wordt gemaakt.

| Items | Description |
|-------|-------------|
| [Ingebouwde](../connectors/built-in.md) triggers en acties | Wordt standaard uitgevoerd in de Logic Apps-service en wordt gemeten met behulp van de [ **actieprijs**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>De HTTP-trigger en aanvraagtrigger zijn bijvoorbeeld ingebouwde triggers, terwijl de HTTP-actie en de actie Antwoord ingebouwde acties zijn. Gegevensbewerkingen, batchbewerkingen, variabele bewerkingen en werkstroombeheeracties [,](../connectors/built-in.md)zoals lussen, voorwaarden, switch, parallelle vertakkingen, en meer, zijn ook ingebouwde acties. |
| [Triggers en](../connectors/managed.md) acties voor standaardconnector <p><p>[Triggers en](../connectors/apis-list.md#custom-apis-and-connectors) acties voor aangepaste connectors | Gemeten met behulp van de [prijs van de Standard-connector.](https://azure.microsoft.com/pricing/details/logic-apps/) |
| [Triggers en](../connectors/managed.md) acties voor Bedrijfsconnector | Gemeten met behulp van de [Enterprise-connectorprijs](https://azure.microsoft.com/pricing/details/logic-apps/). Tijdens de openbare preview worden enterprise-connectors echter gemeten met behulp van de [ *prijs van de* Standard-connector.](https://azure.microsoft.com/pricing/details/logic-apps/) |
| Acties binnen [lussen](logic-apps-control-flow-loops.md) | Elke actie die in een lus wordt uitgevoerd, wordt gemeten voor elke luscyclus die wordt uitgevoerd. <p><p>Stel bijvoorbeeld dat u een lus 'voor elke' hebt die acties bevat die een lijst verwerken. De Logic Apps-service metert elke actie die in die lus wordt uitgevoerd door het aantal lijstitems te vermenigvuldigen met het aantal acties in de lus en voegt de actie toe die de lus start. De berekening voor een lijst met 10 items is dus (10 * 1) + 1, wat resulteert in 11 uitvoeringen van acties. |
| Nieuwe pogingen | Als u de meest eenvoudige uitzonderingen en [](logic-apps-exception-handling.md#retry-policies) fouten wilt afhandelen, kunt u een beleid voor opnieuw proberen instellen voor triggers en acties, indien ondersteund. Deze nieuwe aanvragen en de oorspronkelijke aanvraag worden tegen tarieven in rekening gebracht op basis van het ingebouwde, standaard- of enterprise-type voor de trigger of actie. Voor een actie die wordt uitgevoerd met 2 nieuwe acties, worden bijvoorbeeld drie uitvoeringen in rekening gebracht. |
| [Gegevensretentie en opslagverbruik](#data-retention) | Gemeten met behulp van de gegevensretentieprijs, die u kunt vinden op Logic Apps [pagina](https://azure.microsoft.com/pricing/details/logic-apps/)met prijzen, onder de **tabel Prijsdetails.** |
|||

Raadpleeg de volgende artikelen voor meer informatie:

* [Metrische gegevens weergeven voor uitvoeringen en opslagverbruik](plan-manage-costs.md#monitor-billing-metrics)
* [Limieten in Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Geen datameter

* Triggers die worden overgeslagen vanwege niet-voldaan voorwaarden
* Acties die niet zijn uitgevoerd omdat de logische app is gestopt voordat deze is uitgevoerd
* [Uitgeschakelde logische apps](#disabled-apps)

### <a name="other-related-resources"></a>Andere gerelateerde resources

Logische apps werken met andere gerelateerde resources, zoals integratieaccounts, on-premises gegevensgateways en ISE's (Integration Service Environments). Als u meer wilt weten over de prijzen voor deze resources, bekijkt u deze secties verder in dit onderwerp:

* [On-premises data gateway](#data-gateway) (On-premises gegevensgateway)
* [Prijsmodel voor integratieaccounts](#integration-accounts)
* [ISE-prijsmodel](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tips voor het schatten van verbruikskosten

Bekijk deze tips om u te helpen bij het schatten van nauwkeurigere verbruikskosten:

* Houd rekening met het mogelijke aantal berichten of gebeurtenissen dat op een bepaalde dag aankomt, in plaats van uw berekeningen alleen te baseren op het polling-interval.

* Wanneer een gebeurtenis of bericht voldoet aan de criteria voor triggers, zullen de triggers onmiddellijk een poging doen alle gebeurtenissen die in de wacht staan of berichten die aan de criteria voldoen, te lezen. Dit gedrag houdt in dat ook als u een langere polling-interval selecteert, de trigger wordt geactiveerd op basis van het aantal gebeurtenissen in de wacht of berichten die in aanmerking komen voor het starten van werkstromen. Triggers die dit gedrag volgen, zijn onder andere Azure Service Bus en Azure Event Hub.

  Stel bijvoorbeeld dat u een trigger hebt ingesteld die elke dag een eindpunt controleert. Wanneer de trigger het eindpunt controleert en er vijftien gebeurtenissen vindt die aan de criteria voldoen, wordt de trigger geactiveerd en wordt de corresponderende werkstroom vijftien keer uitgevoerd. De Logic Apps service meter alle acties die deze 15 werkstromen uitvoeren, met inbegrip van de triggeraanvragen.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE-prijzen

Een vast prijsmodel is van toepassing op logische apps die worden uitgevoerd in een [ISE *(Integration Service* Environment).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Een ISE wordt gefactureerd met behulp [Integratieserviceomgeving prijs](https://azure.microsoft.com/pricing/details/logic-apps), die afhankelijk is van het [ISE-niveau of de *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) die u maakt. Deze prijzen verschillen van de prijzen voor meerdere tenants omdat u betaalt voor gereserveerde capaciteit en toegewezen resources, ongeacht of u deze gebruikt.

| ISE-SKU | Description |
|---------|-------------|
| **Premium** | De basiseenheid heeft [een vaste capaciteit](logic-apps-limits-and-config.md#integration-service-environment-ise) en wordt gefactureerd tegen een [uurtarief voor de Premium SKU](https://azure.microsoft.com/pricing/details/logic-apps). Als u meer doorvoer nodig hebt, kunt u meer schaaleenheden [toevoegen](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) wanneer u uw ISE of later maakt. Elke schaaleenheid wordt gefactureerd tegen [een uurtarief dat](https://azure.microsoft.com/pricing/details/logic-apps)ongeveer de helft van het basiseenheidtarief is. <p><p>Zie ISE-limieten in Azure Logic Apps voor informatie over capaciteit [Azure Logic Apps.](logic-apps-limits-and-config.md#integration-service-environment-ise) |
| **Developer** | De basiseenheid heeft [een vaste capaciteit](logic-apps-limits-and-config.md#integration-service-environment-ise) en wordt [gefactureerd tegen een uurtarief voor de Developer SKU](https://azure.microsoft.com/pricing/details/logic-apps). Deze SKU heeft echter geen SLA (Service Level Agreement), mogelijkheid voor omhoog schalen of redundantie tijdens het recyclen, wat betekent dat u vertragingen of downtime kunt ervaren. Back-end-updates kunnen de service af en toe onderbreken. <p><p>**Belangrijk:** zorg ervoor dat u deze SKU alleen gebruikt voor verkenning, experimenten, ontwikkeling en testen, niet voor productie- of prestatietests. <p><p>Zie ISE-limieten in Azure Logic Apps voor informatie [over capaciteits- Azure Logic Apps.](logic-apps-limits-and-config.md#integration-service-environment-ise) |
|||

### <a name="included-at-no-extra-cost"></a>Inbegrepen zonder extra kosten

| Items | Description |
|-------|-------------|
| [Ingebouwde](../connectors/built-in.md) triggers en acties | Geef het **Core-label** weer en voer uit in dezelfde ISE als uw logische apps. |
| [Standaardconnectoren](../connectors/managed.md) <p><p>[Bedrijfsconnectoren](../connectors/managed.md#enterprise-connectors) | - Beheerde connectors die het **ISE-label** weergeven, zijn speciaal ontworpen om te werken zonder de on-premises gegevensgateway en worden uitgevoerd in dezelfde ISE als uw logische apps. De ISE-prijzen omvatten zo veel Enterprise-verbindingen als u wilt. <p><p>- Connectors die het ISE-label niet weergeven, worden uitgevoerd in de multiten tenant Logic Apps service. IsE-prijzen omvatten deze uitvoeringen echter voor logische apps die worden uitgevoerd in een ISE. |
| Acties binnen [lussen](logic-apps-control-flow-loops.md) | ISE-prijzen omvatten elke actie die wordt uitgevoerd in een lus voor elke luscyclus die wordt uitgevoerd. <p><p>Stel bijvoorbeeld dat u een lus 'voor elke' hebt die acties bevat die een lijst verwerken. Om het totale aantal uitvoeringen van acties op te halen, vermenigvuldigt u het aantal lijstitems met het aantal acties in de lus en voegt u de actie toe die de lus start. De berekening voor een lijst met 10 items is dus (10 * 1) + 1, wat resulteert in 11 uitvoeringen van acties. |
| Nieuwe pogingen | Als u de meest eenvoudige uitzonderingen en [](logic-apps-exception-handling.md#retry-policies) fouten wilt afhandelen, kunt u een beleid voor opnieuw proberen instellen voor triggers en acties, indien ondersteund. De ISE-prijzen omvatten nieuwe aanvragen, samen met de oorspronkelijke aanvraag. |
| [Gegevensretentie en opslagverbruik](#data-retention) | Voor logische apps in een ISE worden geen retentie- en opslagkosten in rekening brengen. |
| [Integratieaccounts](#integration-accounts) | Bevat zonder extra kosten het gebruik voor één integratieaccountlaag, op basis van de ISE-SKU. |
|||

Zie [ISE-limieten in](logic-apps-limits-and-config.md#integration-service-environment-ise)Azure Logic Apps.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integratieaccounts

Een [integratieaccount](../logic-apps/logic-apps-pricing.md#integration-accounts) is een afzonderlijke resource die u maakt en koppelt aan logische apps, zodat u B2B-integratieoplossingen kunt verkennen, bouwen en testen die gebruikmaken van [EDI-](logic-apps-enterprise-integration-b2b.md) en XML-verwerkingsmogelijkheden. [](logic-apps-enterprise-integration-xml.md)

Azure Logic Apps biedt deze integratieaccountniveaus of -lagen die variëren [in](https://azure.microsoft.com/pricing/details/logic-apps/) prijs- en [factureringsmodel,](logic-apps-pricing.md#integration-accounts)afhankelijk van of uw logische apps zijn gebaseerd op verbruik of is gebaseerd op ISE:

| Laag | Beschrijving |
|------|-------------|
| **Basic** | Voor scenario's waarin u alleen de verwerking van berichten wilt of wilt fungeren als een kleine zakelijke partner die een handelspartnerrelatie heeft met een grotere bedrijfsentiteit. <p><p>Ondersteund door de Logic Apps SLA. |
| **Standard** | Voor scenario's waarin u complexere B2B-relaties hebt en een groter aantal entiteiten dat u moet beheren. <p><p>Ondersteund door de Logic Apps SLA. |
| **Gratis** | Voor experimentele scenario's, niet voor productiescenario's. Deze laag heeft limieten voor de beschikbaarheid, doorvoer en het gebruik van regio's. De gratis laag is bijvoorbeeld alleen beschikbaar voor openbare regio's in Azure, bijvoorbeeld VS - west of Azië - zuidoost, maar niet [voor](/azure/china/overview-operations) Azure China 21Vianet of [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Opmerking:** niet ondersteund door de Logic Apps SLA. |
|||

Zie Limieten en configuratie [](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)voor Azure Logic Apps voor meer informatie over limieten voor integratieaccounts, zoals:

* [Limieten voor integratieaccounts per Azure-abonnement](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limieten voor verschillende artefacten per integratieaccount.](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) Artefacten omvatten handelspartners, overeenkomsten, kaarten, schema's, assemblies, certificaten, batchconfiguraties, en meer.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Integratieaccounts voor logische apps op basis van verbruik

Integratieaccounts worden gefactureerd met een vaste [integratieaccountprijs](https://azure.microsoft.com/pricing/details/logic-apps/) die is gebaseerd op de accountlaag die u gebruikt.

### <a name="ise-based-logic-apps"></a>Logische apps op basis van ISE

Zonder extra kosten bevat uw ISE één integratieaccount op basis van uw ISE-SKU. Tegen extra kosten kunt u meer integratieaccounts maken voor uw ISE om maximaal de totale [ISE-limiet te gebruiken.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Meer informatie over het [ISE-prijsmodel](#fixed-pricing) eerder in dit onderwerp.

| ISE-SKU | Opgenomen integratieaccount | Toeslag |
|---------|------------------------------|-----------------|
| **Premium** | Eén [Standard-integratieaccount](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Nog maximaal 19 Standard-accounts. Gratis of Basic-accounts zijn niet toegestaan. |
| **Developer** | Eén gratis [integratieaccount](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Nog maximaal 19 Standard-accounts als u al een gratis account hebt, of 20 totaal standard-accounts als u geen gratis account hebt. Er zijn geen Basic-accounts toegestaan. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Gegevensretentie en opslagverbruik

Alle invoer en uitvoer in de uitvoergeschiedenis van uw logische app worden opgeslagen en gemeten op basis van de duur van de app-run en de retentieperiode voor [de geschiedenis.](logic-apps-limits-and-config.md#run-duration-retention-limits)

* Voor logische apps in de Logic Apps-service voor meerdere tenants wordt het opslagverbruik gefactureerd tegen een vaste  prijs. Deze vindt u op de [pagina Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps)onder de tabel Prijsgegevens.

* Voor logische apps in ISE's worden voor opslagverbruik geen kosten voor gegevensretentie in rekening brengen.

Zie View [metrics for executions and storage consumption (Metrische gegevens weergeven voor uitvoeringen en opslagverbruik)](plan-manage-costs.md#monitor-billing-metrics)om het gebruik van opslag te bewaken.

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>On-premises gegevensgateway

Een [on-premises gegevensgateway](../logic-apps/logic-apps-gateway-install.md) is een afzonderlijke resource die u maakt, zodat uw logische apps toegang hebben tot on-premises gegevens met behulp van specifieke gateway-ondersteunde connectors. Voor connectorbewerkingen die via de gateway worden uitgevoerd, worden kosten in rekening gebracht, maar voor de gateway zelf worden geen kosten in rekening gebracht.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Uitgeschakelde logische apps

Er worden geen kosten in rekening gebracht voor uitgeschakelde logische apps omdat ze geen nieuwe exemplaren kunnen maken terwijl ze zijn uitgeschakeld. Nadat u een logische app hebt uitgeschakeld, kan het enige tijd duren voordat alle exemplaren die momenteel worden uitgevoerd, volledig worden gestopt.

## <a name="next-steps"></a>Volgende stappen

* [Kosten voor Azure Logic Apps](plan-manage-costs.md)
