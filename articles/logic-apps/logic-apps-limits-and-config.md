---
title: Limieten en configuratie
description: Servicelimieten, zoals duur, doorvoer en capaciteit, plus configuratiewaarden, zoals IP-adressen die moeten worden toegestaan, voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/16/2021
ms.openlocfilehash: 286da1412e8a74ffbf34e4abb493241914d4f925
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764869"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informatie over limieten en configuratie voor Azure Logic Apps

In dit artikel worden de limieten en configuratiedetails beschreven voor het maken en uitvoeren van geautomatiseerde werkstromen met Azure Logic Apps. Zie Power Automate en configuratie [in](/flow/limits-and-config)Power Automate.

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Limieten voor definities van logische apps

Dit zijn de limieten voor de definitie van één logische app:

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Acties per werkstroom | 500 | Als u deze limiet wilt uitbreiden, kunt u waar nodig geneste werkstromen toevoegen. |
| Toegestane nestdiepte voor acties | 8 | Als u deze limiet wilt uitbreiden, kunt u waar nodig geneste werkstromen toevoegen. |
| Werkstromen per regio per abonnement | 1000 | |
| Triggers per werkstroom | 10 | Wanneer u in de codeweergave werkt, niet in de ontwerpfunctie |
| Limiet voor bereikgevallen wisselen | 25 | |
| Variabelen per werkstroom | 250 | |
| Naam voor `action` of `trigger` | 80 tekens | |
| Tekens per expressie | 8\.192 | |
| Lengte van `description` | 256 tekens | |
| Maximumaantal `parameters` | 50 | |
| Maximumaantal `outputs` | 10 | |
| Maximale grootte voor `trackedProperties` | 16.000 tekens |
| Inline-codeactie: maximum aantal codetekens | 1024 tekens | Als u deze limiet wilt uitbreiden naar 100.000 tekens, maakt u uw logische apps met het resourcetype Logische app **(preview)** met behulp van de [Azure Portal of](create-stateful-stateless-workflows-azure-portal.md) met behulp van Visual Studio Code en de extensie [Azure Logic Apps **(preview).**](create-stateful-stateless-workflows-visual-studio-code.md) |
| Inline-codeactie: maximale duur voor het uitvoeren van code | 5 seconden | Als u deze limiet wilt uitbreiden naar 15 seconden, maakt u uw logische apps met het resourcetype Logische app **(preview)** met behulp van [de Azure Portal of](create-stateful-stateless-workflows-azure-portal.md) met behulp van Visual Studio Code en de extensie Azure Logic Apps [ **(preview).**](create-stateful-stateless-workflows-visual-studio-code.md) |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Limieten voor de duur en bewaargeschiedenis van de run

Dit zijn de limieten voor het uitvoeren van één logische app:

| Name | Limiet voor meerdere tenants | Omgevingslimiet integratieservice | Notities |
|------|--------------------|---------------------------------------|-------|
| Uitvoeringsduur | 90 dagen | 366 dagen | De duur van de run wordt berekend met behulp van de begintijd van een run en de limiet die is opgegeven in de werkstroominstelling Bewaarperiode van de rungeschiedenis [**in**](#change-duration) dagen op die begintijd. <p><p>Zie Duur en retentie van de geschiedenis in opslag wijzigen om de standaardlimiet [te wijzigen.](#change-duration) |
| Bewaarperiode van de rungeschiedenis in opslag | 90 dagen | 366 dagen | Als de duur van een run de retentielimiet voor de huidige rungeschiedenis overschrijdt, wordt de run verwijderd uit de geschiedenis van de run in de opslag. Ongeacht of de run is voltooid of een time-out heeft, wordt de bewaarperiode voor de rungeschiedenis altijd berekend met behulp van de begintijd van de run en de huidige limiet die is opgegeven in de werkstroominstelling, Bewaarperiode van de rungeschiedenis [**in dagen**](#change-retention). Ongeacht de vorige limiet wordt de huidige limiet altijd gebruikt voor het berekenen van de retentie. <p><p>Zie Duur wijzigen en bewaarperiode van de rungeschiedenis in opslag voor meer [informatie.](#change-retention) Als u de maximumlimiet wilt verhogen, [neem dan contact op Logic Apps team voor](mailto://logicappspm@microsoft.com) hulp bij uw vereisten. |
| Minimaal terugkeerpatroon | 1 seconde | 1 seconde ||
| Maximaal herhalingsinterval | 500 dagen | 500 dagen ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Duur en retentie van geschiedenis in opslag wijzigen

Met dezelfde instelling bepaalt u het maximum aantal dagen dat een werkstroom kan worden uitgevoerd en voor het bewaren van de rungeschiedenis in de opslag. Volg deze stappen om de standaard- of huidige limiet voor deze eigenschappen te wijzigen.

* Voor logische apps in Azure met meerdere tenants is de standaardlimiet van 90 dagen gelijk aan de maximumlimiet. U kunt deze waarde alleen verlagen.

* Voor logische apps in een integratieserviceomgeving kunt u de standaardlimiet van 90 dagen verlagen of verhogen.

Stel bijvoorbeeld dat u de retentielimiet verlaagt van 90 dagen naar 30 dagen. Een 60-daagse run wordt verwijderd uit de geschiedenis van de runs. Als u de retentieperiode verhoogt van 30 dagen naar 60 dagen, blijft een 20-daagse run nog 40 dagen in de geschiedenis van de runs.

> [!IMPORTANT]
> Als de duur van een run de retentielimiet voor de huidige run history overschrijdt, wordt de run verwijderd uit de geschiedenis van de run in de opslag. Om te voorkomen dat de rungeschiedenis verloren gaat, moet u ervoor zorgen dat de bewaarlimiet altijd *hoger* is dan de langst mogelijke duur van de run.

1. Zoek en [Azure Portal](https://portal.azure.com) logische apps in het **zoekvak.**

1. Zoek en selecteer uw logische app. Open uw logische app in de ontwerper van logische apps.

1. Selecteer werkstroominstellingen in het menu van **de** logische app.

1. Selecteer **onder Runtimeopties** in de lijst **Runtime-geschiedenisretentie in** dagen de optie **Aangepast.**

1. Sleep de schuifregelaar om het aantal dagen te wijzigen dat u wilt.

1. Wanneer u klaar bent, selecteert u Opslaan op **de werkbalk** **Werkstroominstellingen.**

Als u een Azure Resource Manager voor uw logische app genereert, wordt deze instelling weergegeven als een eigenschap in de resourcedefinitie van uw werkstroom, die wordt beschreven in de [microsoft.Logic workflows-sjabloonverwijzing:](/azure/templates/microsoft.logic/workflows)

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Gelijktijdigheid, lussen en limieten voor samenloop

Dit zijn de limieten voor het uitvoeren van één logische app:

### <a name="loops"></a>Lussen

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Foreach-matrixitems | 100.000 | Deze limiet beschrijft het maximum aantal matrixitems dat een 'voor elke'-lus kan verwerken. <p><p>Als u grotere matrices wilt filteren, kunt u de [queryactie gebruiken.](logic-apps-perform-data-operations.md#filter-array-action) |
| Gelijktijdigheid van foreach | Met gelijktijdigheid uitgeschakeld: 20 <p><p>Met gelijktijdigheid op: <p><p>- Standaardinstelling: 20 <br>- Minimum: 1 <br>- Maximaal: 50 | Deze limiet is het maximum aantal herhalingen van de lus 'voor elke' die tegelijkertijd of parallel kunnen worden uitgevoerd. <p><p>Zie Change ['for each' concurrency limit (Limiet](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) voor elke gelijktijdigheid wijzigen) of [Run 'for each'-lussen opeenvolgend](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)om deze limiet te wijzigen. |
| Until-iteraties | - Standaardinstelling: 60 <br>- Minimum: 1 <br>- Maximaal: 5000 | Het maximum aantal cycli dat een 'Until'-lus kan hebben tijdens het uitvoeren van een logische app. <p><p>Als u deze limiet wilt wijzigen, selecteert u in de lusvorm 'Until' de optie **Limieten** wijzigen en geeft u de waarde op voor de **eigenschap Count.** |
| Tot time-out | - Standaardinstelling: PT1H (1 uur) | De meeste tijd dat de lus 'Until' kan worden uitgevoerd voordat deze wordt afgesloten en is opgegeven in [ISO 8601-indeling.](https://en.wikipedia.org/wiki/ISO_8601) De time-outwaarde wordt geëvalueerd voor elke luscyclus. Als een actie in de lus langer duurt dan de time-outlimiet, wordt de huidige cyclus niet gestopt. De volgende cyclus start echter niet omdat niet aan de limietvoorwaarde wordt voldaan. <p><p>Als u deze limiet wilt wijzigen, selecteert u in de lusvorm 'Until' de optie **Limieten** wijzigen en geeft u de waarde op voor de **eigenschap Timeout.** |
||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>Gelijktijdigheid en afhandeling

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Gelijktijdigheid van triggers | Met gelijktijdigheid uitgeschakeld: Onbeperkt <p><p>Met gelijktijdigheid aan, wat u niet ongedaan kunt maken na het inschakelen: <p><p>- Standaardinstelling: 25 <br>- Minimum: 1 <br>- Maximaal: 100 | Deze limiet is het maximum aantal exemplaren van logische apps dat tegelijkertijd of parallel kan worden uitgevoerd. <p><p>**Opmerking:** wanneer gelijktijdigheid is ingeschakeld, wordt de SplitOn-limiet gereduceerd tot 100 items voor het [keuren van matrices.](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) <p><p>Zie Gelijktijdigheidslimiet voor [triggers](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) wijzigen of Exemplaren opeenvolgend activeren om deze limiet [te wijzigen.](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger) |
| Maximum aantal wachtende runs | Met gelijktijdigheid uitgeschakeld: <p><p>- Minimum: 1 <br>- Maximaal: 50 <p><p>Met gelijktijdigheid op: <p><p>- Min: 10 plus het aantal gelijktijdige runs (gelijktijdigheid van triggers) <br>- Maximaal: 100 | Deze limiet is het maximum aantal exemplaren van logische apps dat kan wachten om te worden uitgevoerd wanneer in uw logische app al het maximum aantal gelijktijdige exemplaren wordt uitgevoerd. <p><p>Zie Limiet voor wachtende runs [wijzigen om deze limiet te wijzigen.](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs) |
| SplitOn-items | Met gelijktijdigheid uitgeschakeld: 100.000 <p><p>Met gelijktijdigheid op: 100 | Voor triggers die een matrix retourneren, kunt u een expressie opgeven die gebruikmaakt van een 'SplitOn'-eigenschap die matrixitems [splitst](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) of splitst in meerdere werkstroomprocessen voor verwerking, in plaats van een Foreach-lus te gebruiken. Deze expressie verwijst naar de matrix die moet worden gebruikt voor het maken en uitvoeren van een werkstroom-exemplaar voor elk matrixitem. <p><p>**Opmerking:** wanneer gelijktijdigheid is ingeschakeld, wordt de splitslimiet beperkt tot 100 items. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Doorvoerlimieten

Dit zijn de limieten voor de definitie van één logische app:

### <a name="multi-tenant-logic-apps-service"></a>Multi-tenant Logic Apps service

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Actie: Uitvoeringen per rolling interval van 5 minuten | - 100.000 uitvoeringen (standaard) <p><p>- 300.000 uitvoeringen (maximaal in de modus voor hoge doorvoer)  | Als u de standaardlimiet wilt verhogen tot de maximumlimiet voor uw logische app, gaat u naar [Uitvoeren in](#run-high-throughput-mode)de modus voor hoge doorvoer. Deze is in preview. U kunt de workload indien nodig ook distribueren over meer dan één [logische app.](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) |
| Actie: Gelijktijdige uitgaande aanroepen | ~2500 | U kunt het aantal gelijktijdige aanvragen verminderen of de duur zo nodig verminderen. |
| Runtime-eindpunt: Gelijktijdige binnenkomende aanroepen | ~1000 | U kunt het aantal gelijktijdige aanvragen verminderen of de duur zo nodig verminderen. |
| Runtime-eindpunt: aanroepen per 5 minuten lezen  | 60.000 | Deze limiet geldt voor aanroepen die de onbewerkte invoer en uitvoer van de uitvoergeschiedenis van een logische app krijgen. U kunt de werkbelasting naar meer dan één app distribueren, indien nodig. |
| Runtime-eindpunt: aanroepen per 5 minuten aanroepen | 45.000 | U kunt werkbelasting naar meer dan één app distribueren, indien nodig. |
| Doorvoer van inhoud per 5 minuten | 600 MB | U kunt werkbelasting naar meer dan één app distribueren, indien nodig. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Uitvoeren in de modus voor hoge doorvoer

Voor één definitie van een logische app geldt een standaardlimiet voor het aantal acties dat elke 5 minuten [wordt uitgevoerd.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Als u de standaardlimiet wilt verhogen naar de [maximumlimiet](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) voor uw logische app, wat drie keer de standaardlimiet is, kunt u de modus voor hoge doorvoer inschakelen, die in preview is. U kunt de [workload indien nodig ook distribueren over](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) meer dan één logische app.

1. Selecteer in Azure Portal menu van uw logische app onder **Instellingen** de optie **Werkstroominstellingen.**

1. Wijzig **onder Runtime-opties**  >  **Hoge doorvoer** de instelling in **Aan.**

   ![Schermopname van het menu van de logische app in Azure Portal met 'Werkstroominstellingen' en 'Hoge doorvoer' ingesteld op 'Aan'.](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Als u deze instelling wilt inschakelen in een ARM-sjabloon voor het implementeren van uw logische app, voegt u in het object voor de resourcedefinitie van uw logische app het object toe met de eigenschap `properties` `runtimeConfiguration` ingesteld op `operationOptions` `OptimizedForHighThroughput` :

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Zie [Overview: Automate deployment for Azure Logic Apps by using Azure Resource Manager templates](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)voor meer informatie over de resourcedefinitie van uw logische app.

### <a name="integration-service-environment-ise"></a>Integratieserviceomgeving (ISE)

* [ISE-SKU voor ontwikkelaars:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)biedt maximaal 500 uitvoeringen per minuut, maar houd rekening met de volgende overwegingen:

  * Zorg ervoor dat u deze SKU alleen gebruikt voor verkenning, experimenten, ontwikkeling of testen, niet voor productie- of prestatietests. Deze SKU heeft geen SLA (Service Level Agreement), mogelijkheid voor omhoog schalen of redundantie tijdens het recyclen, wat betekent dat er vertragingen of downtime kunnen ontstaan.

  * Back-end-updates kunnen de service af en toe onderbreken.

* [Premium ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)in de volgende tabel worden de doorvoerlimieten van deze SKU beschreven, maar als u deze limieten bij normale verwerking wilt overschrijden of belastingstests wilt uitvoeren die deze limieten overschrijden, neem dan contact op met het [Logic Apps-team](mailto://logicappsemail@microsoft.com) voor hulp bij uw vereisten.

  | Name | Limiet | Notities |
  |------|-------|-------|
  | Uitvoeringslimiet voor basiseenheid | Systeem beperkt wanneer de infrastructuurcapaciteit 80% bereikt | Biedt ~4000 uitvoeringen van acties per minuut, wat ongeveer 160 miljoen uitvoeringen per maand is |
  | Uitvoeringslimiet voor schaaleenheid | Systeem beperkt wanneer de infrastructuurcapaciteit 80% bereikt | Elke schaaleenheid kan ~2000 extra uitvoeringen van acties per minuut bieden. Dit zijn ongeveer 80 miljoen extra uitvoeringen van acties per maand |
  | Maximum aantal schaaleenheden dat u kunt toevoegen | 10 | |
  ||||

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Gatewaylimieten

Azure Logic Apps ondersteunt schrijfbewerkingen, inclusief invoegingen en updates, via de gateway. Deze bewerkingen hebben echter [limieten voor de grootte van de nettolading.](/data-integration/gateway/service-gateway-onprem#considerations)

<a name="http-limits"></a>

## <a name="http-limits"></a>HTTP-limieten

Dit zijn de limieten voor één binnenkomende of uitgaande aanroep:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Time-outduur

Sommige connectorbewerkingen voeren asynchrone aanroepen uit of luisteren naar webhookaanvragen, zodat de time-out voor deze bewerkingen langer kan zijn dan deze limieten. Zie de technische details voor de specifieke connector en ook Werkstroomtriggers [en -acties voor meer informatie.](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

| Name | Logic Apps (multi-tenant) | Logic Apps (preview) | Integratieserviceomgeving | Notities |
|------|---------------------------|----------------------|---------------------------------|-------|
| Uitgaande aanvraag | 120 seconden <br>(2 minuten) | 230 seconden <br>(3,9 minuten) | 240 seconden <br>(4 minuten) | Voorbeelden van uitgaande aanvragen zijn aanroepen die worden gedaan door de HTTP-trigger of -actie. Zie Preview voor meer informatie over [de preview Azure Logic Apps versie.](logic-apps-overview-preview.md) <p><p>**Tip:** Voor langere bewerkingen gebruikt u een [asynchrone polling-patroon](../logic-apps/logic-apps-create-api-app.md#async-pattern) of een [until-lus.](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action) Als u time-outlimieten wilt ronddraaien wanneer u een andere logische app aanroept die een aanroepbaar eindpunt [heeft,](logic-apps-http-endpoint.md)kunt u in plaats daarvan de ingebouwde Azure Logic Apps-actie gebruiken, die u kunt vinden in de connector- picker onder **Ingebouwd.** |
| Inkomende aanvraag | 120 seconden <br>(2 minuten) | 230 seconden <br>(3,9 minuten) | 240 seconden <br>(4 minuten) | Voorbeelden van binnenkomende aanvragen zijn aanroepen die worden ontvangen door de aanvraagtrigger, http-webhooktrigger en http-webhookactie. Zie Preview voor meer informatie over [de preview Azure Logic Apps versie.](logic-apps-overview-preview.md) <p><p>**Opmerking:** voor de oorspronkelijke aanroeper om het antwoord op te halen, moeten alle stappen in het antwoord binnen de limiet zijn, tenzij u een andere logische app aanroept als een geneste werkstroom. Zie Logische apps aanroepen, [activeren of nesten voor meer informatie.](../logic-apps/logic-apps-http-endpoint.md) |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Berichtgrootte

| Name | Limiet voor meerdere tenants | Omgevingslimiet integratieservice | Notities |
|------|--------------------|---------------------------------------|-------|
| Berichtgrootte | 100 MB | 200 MB | Zie Grote berichten verwerken met segmentering om deze limiet [te beperken.](../logic-apps/logic-apps-handle-large-messages.md) Sommige connectors en API's ondersteunen echter mogelijk geen segmentering of zelfs de standaardlimiet. <p><p>- Connectors zoals AS2, X12 en EDIFACT hebben hun eigen [B2B-berichtlimieten.](#b2b-protocol-limits) <br>- ISE-connectors gebruiken de ISE-limiet, niet hun limieten voor niet-ISE-connectors. |
| Berichtgrootte met segmentering | 1 GB | 5 GB | Deze limiet geldt voor acties die systeemeigen ondersteuning bieden voor segmentering of waarmee u segmentering in de runtimeconfiguratie kunt inschakelen. <p><p>Als u een ISE gebruikt, ondersteunt de Logic Apps-engine deze limiet, maar connectors hebben hun eigen segmenteringslimieten tot de enginelimiet. Zie bijvoorbeeld de API-verwijzing van [de Azure Blob Storage-connector.](/connectors/azureblob/) Zie Grote berichten verwerken met segmentering voor meer informatie [over segmentering.](../logic-apps/logic-apps-handle-large-messages.md) |
|||||

#### <a name="character-limits"></a>Tekenlimieten

| Name | Limiet | Notities |
|------|-------|-------|
| Limiet voor evaluatie van expressie | 131.072 tekens | De `@concat()` `@base64()` expressies , `@string()` en mogen niet langer zijn dan deze limiet. |
| Limiet voor AANVRAAG-URL-tekens | 16.384 tekens | |
||||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Beleid voor opnieuw proberen

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Nieuwe pogingen | 90 | De standaard is 4. Als u de standaardwaarde wilt wijzigen, gebruikt u de [beleidsparameter voor opnieuw proberen.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
| Maximale vertraging nieuwe poging | 1 dag | Als u de standaardwaarde wilt wijzigen, gebruikt u de [beleidsparameter voor opnieuw proberen.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
| Minimale vertraging nieuwe poging | 5 seconden | Als u de standaardwaarde wilt wijzigen, gebruikt u de [beleidsparameter voor opnieuw proberen.](../logic-apps/logic-apps-workflow-actions-triggers.md) |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Verificatielimieten

Dit zijn de limieten voor een logische app die begint met een aanvraagtrigger en [die Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) in staat stelt om binnenkomende aanroepen naar de aanvraagtrigger toe te staan:

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Azure AD-autorisatiebeleid | 5 | |
| Claims per autorisatiebeleid | 10 | |
| Claimwaarde: maximum aantal tekens | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limieten voor aangepaste connectors

Dit zijn de limieten voor aangepaste connectors die u kunt maken met web-API's.

| Name | Limiet voor meerdere tenants | Omgevingslimiet integratieservice | Notities |
|------|--------------------|---------------------------------------|-------|
| Aantal aangepaste connectors | 1000 per Azure-abonnement | 1000 per Azure-abonnement ||
| Aantal aanvragen per minuut voor een aangepaste connector | 500 aanvragen per minuut per verbinding | 2000 aanvragen per minuut per aangepaste *connector* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Beheerde identiteiten

| Name | Limiet |
|------|-------|
| Beheerde identiteiten per logische app | De door het systeem toegewezen identiteit of 1 door de gebruiker toegewezen identiteit |
| Aantal logische apps met een beheerde identiteit in een Azure-abonnement per regio | 1000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limieten voor integratieaccounts

Elk Azure-abonnement heeft de volgende integratieaccountlimieten:

* Eén [integratieaccount voor](../logic-apps/logic-apps-pricing.md#integration-accounts) de gratis laag per Azure-regio. Deze laag is alleen beschikbaar voor openbare regio's in Azure, bijvoorbeeld VS - west of Azië - zuidoost, maar niet [voor](/azure/china/overview-operations) Azure China 21Vianet of [Azure Government](../azure-government/documentation-government-welcome.md).

* 1000 integratieaccounts, inclusief integratieaccounts in [alle ISE-omgevingen (Integration Service Environments)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) voor zowel [ontwikkelaars- als Premium-SKU's.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Elke ISE, of dit nu Developer of [Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)is, kan zonder extra kosten gebruikmaken van één integratieaccount, hoewel het inbegrepen accounttype per ISE-SKU varieert. U kunt meer integratieaccounts voor uw ISE maken tot de totale limiet voor extra [kosten:](logic-apps-pricing.md#fixed-pricing)

  | ISE-SKU | Limieten voor integratieaccounts |
  |---------|----------------------------|
  | **Premium** | 20 accounts, inclusief één Standard-account zonder extra kosten. Met deze SKU kunt u alleen [Standard-accounts](../logic-apps/logic-apps-pricing.md#integration-accounts) hebben. Gratis of Basic-accounts zijn niet toegestaan. |
  | **Developer** | In totaal 20 accounts, waaronder één [gratis](../logic-apps/logic-apps-pricing.md#integration-accounts) account (beperkt tot 1). Met deze SKU kunt u een van de volgende combinaties hebben: <p>- Een gratis account en maximaal 19 [Standard-accounts.](../logic-apps/logic-apps-pricing.md#integration-accounts) <br>- Geen gratis account en maximaal 20 Standard-accounts. <p>Er zijn geen Basic- of extra gratis accounts toegestaan. <p><p>**Belangrijk:** gebruik de [developer-SKU voor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) experimenteren, ontwikkeling en testen, maar niet voor productie- of prestatietests. |
  |||

Zie het Logic Apps prijsmodel voor meer informatie over [de manier waarop prijzen en facturering werken Logic Apps ISE's.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Zie prijzen voor Logic Apps [prijzen.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefactlimieten per integratieaccount

Hier zijn de limieten voor het aantal artefacten voor elke integratieaccountlaag. Zie prijzen voor Logic Apps [prijzen.](https://azure.microsoft.com/pricing/details/logic-apps/) Zie het Logic Apps prijsmodel voor meer informatie over hoe prijzen en facturering [werken Logic Apps integratieaccounts.](../logic-apps/logic-apps-pricing.md#integration-accounts)

> [!NOTE]
> Gebruik de gratis laag alleen voor experimentele scenario's, niet voor productiescenario's. Deze laag beperkt de doorvoer en het gebruik en heeft geen Service Level Agreement (SLA).

| Artefact | Gratis | Basic | Standard |
|----------|------|-------|----------|
| EDI-handelsovereenkomsten | 10 | 1 | 1000 |
| EDI-handelspartners | 25 | 2 | 1000 |
| Maps | 25 | 500 | 1000 |
| Schema 's | 25 | 500 | 1000 |
| Assembly's | 10 | 25 | 1000 |
| Certificaten | 25 | 2 | 1000 |
| Batchconfiguraties | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Capaciteitslimieten voor artefacten

| Artefact | Limiet | Notities |
| -------- | ----- | ----- |
| Assembly | 8 MB | Als u bestanden wilt uploaden die groter zijn dan 2 MB, gebruikt u een [Azure-opslagaccount en blobcontainer](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Kaart (XSLT-bestand) | 8 MB | Als u bestanden wilt uploaden die groter zijn dan 2 MB, gebruikt [u de Azure Logic Apps REST API - Maps](/rest/api/logic/maps/createorupdate). <p><p>**Opmerking:** de hoeveelheid gegevens of records die een kaart kan verwerken, is gebaseerd op de berichtgrootte en time-outlimieten voor acties in Azure Logic Apps. Als u bijvoorbeeld een HTTP-actie gebruikt, op basis van de [grootte](#http-limits)en time-outlimieten van HTTP-berichten, kan een kaart gegevens verwerken tot de limiet voor HTTP-berichten als de bewerking binnen de http-time-outlimiet is voltooid. |
| Schema | 8 MB | Als u bestanden wilt uploaden die groter zijn dan 2 MB, gebruikt u een [Azure-opslagaccount en blobcontainer](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Doorvoerlimieten

| Runtime-eindpunt | Gratis | Basic | Standard | Notities |
|------------------|------|-------|----------|-------|
| Aanroepen lezen per 5 minuten | 3000 | 30,000 | 60.000 | Deze limiet geldt voor aanroepen die de onbewerkte invoer en uitvoer van de uitvoergeschiedenis van een logische app krijgen. U kunt de workload zo nodig verdelen over meer dan één account. |
| Aanroepen per 5 minuten aanroepen | 3000 | 30,000 | 45.000 | U kunt de workload zo nodig verdelen over meer dan één account. |
| Aanroepen per 5 minuten bijhouden | 3000 | 30,000 | 45.000 | U kunt de workload zo nodig verdelen over meer dan één account. |
| Gelijktijdige aanroepen blokkeren | ~1000 | ~1000 | ~1000 | Hetzelfde voor alle SKU's. U kunt het aantal gelijktijdige aanvragen verminderen of de duur zo nodig verminderen. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Berichtgrootte B2B-protocol (AS2, X12, EDIFACT)

Dit zijn de berichtgroottelimieten die van toepassing zijn op B2B-protocollen:

| Name | Limiet voor meerdere tenants | Omgevingslimiet integratieservice | Notities |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 25 MB | v2 - 200 MB <br>v1 - 25 MB | Is van toepassing op decoderen en coderen |
| X12 | 50 MB | 50 MB | Is van toepassing op decoderen en coderen |
| EDIFACT | 50 MB | 50 MB | Is van toepassing op decoderen en coderen |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Logische apps uitschakelen of verwijderen

Wanneer u een logische app uit schakelt, worden er geen nieuwe runs gemaakt. Alle uitvoeringen die worden uitgevoerd en in behandeling zijn, worden voortgezet totdat ze zijn voltooid. Dit kan enige tijd in beslag nemen.

Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Firewallconfiguratie: IP-adressen en servicetags

Wanneer uw logische app moet communiceren via een firewall die verkeer beperkt tot  specifieke IP-adressen, moet die firewall toegang toestaan voor zowel de [binnenkomende](#inbound) als uitgaande [IP-adressen](#outbound) die worden gebruikt door de Logic Apps-service of runtime in de Azure-regio waar uw logische app zich bevindt. *Alle* logische apps in dezelfde regio gebruiken dezelfde IP-adresbereiken.

Als u bijvoorbeeld aanroepen wilt ondersteunen die logische apps in de regio VS - west verzenden of ontvangen via ingebouwde triggers en acties, zoals  de [HTTP-trigger](../connectors/connectors-native-http.md)of -actie, moet uw firewall toegang toestaan voor alle binnenkomende IP-adressen van de Logic Apps-service en uitgaande IP-adressen die aanwezig zijn in de regio VS - west. 

Als uw logische app ook beheerde [connectors](../connectors/managed.md)gebruikt, zoals de Office 365 Outlook-connector of SQL-connector, of aangepaste [connectors](/connectors/custom-connectors/)gebruikt, moet de firewall ook toegang toestaan voor alle uitgaande  [IP-adressen](#outbound) van de beheerde connector in de Azure-regio van uw logische app. Als u bovendien aangepaste connectors gebruikt die toegang hebben tot on-premises resources via de on-premises gegevensgatewayresource in Azure, moet u de installatie van de gateway instellen om toegang toe te staan voor de bijbehorende uitgaande [IP-adressen](logic-apps-gateway-connection.md)van beheerde *[connectors.](#outbound)*

Zie de volgende onderwerpen voor meer informatie over het instellen van communicatie-instellingen op de gateway:

* [Communicatie-instellingen voor de on-premises gegevensgateway aanpassen](/data-integration/gateway/service-gateway-communication)
* [Proxyinstellingen configureren voor de on-premises gegevensgateway](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Overwegingen voor IP-configuratie van firewall

Voordat u uw firewall met IP-adressen in stelt, moet u de volgende overwegingen bekijken:

* Als u Power Automate [gebruikt,](/power-automate/getting-started)gaan sommige acties, zoals **HTTP** en HTTP **+ OpenAPI,** rechtstreeks via de Azure Logic Apps-service en zijn ze afkomstig van de IP-adressen die hier worden vermeld. Zie Limieten en configuratie voor Power Automate voor meer informatie over de IP-adressen die door Power Automate worden [Power Automate.](/flow/limits-and-config#ip-address-configuration)

* Voor [Azure China 21Vianet](/azure/china/)zijn vaste of gereserveerde IP-adressen niet beschikbaar voor aangepaste [connectors](../logic-apps/custom-connector-overview.md) en voor beheerde [connectors,](../connectors/managed.md)zoals Azure Storage, SQL Server, Office 365 Outlook, en meer.

* Als uw logische apps worden uitgevoerd in een [ISE (Integration Service Environment),](connect-virtual-network-vnet-isolated-environment-overview.md)moet u ervoor zorgen dat u [deze poorten ook opent.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)

* Om u te helpen de beveiligingsregels die u wilt maken te vereenvoudigen, kunt u in plaats daarvan [servicetags](../virtual-network/service-tags-overview.md) gebruiken in plaats van IP-adres voorvoegsels voor elke regio op te geven. Deze tags werken in de regio's waar Logic Apps service beschikbaar is:

  * **LogicAppsManagement: vertegenwoordigt** de inkomende IP-adres voorvoegsels voor de Logic Apps service.

  * **LogicApps:** vertegenwoordigt de uitgaande IP-adres voorvoegsels voor de Logic Apps service.

  * **AzureConnectors:** vertegenwoordigt de IP-adres voorvoegsels voor beheerde connectors die inkomende webhook-callbacks maken naar de Logic Apps-service en uitgaande aanroepen naar hun respectieve services, zoals Azure Storage of Azure Event Hubs.

* Als uw logische apps problemen hebben met het openen van Azure-opslagaccounts die gebruikmaken van [firewalls](../storage/common/storage-network-security.md)en firewallregels, hebt u verschillende andere opties [om toegang in te stellen.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

  Logische apps hebben bijvoorbeeld niet rechtstreeks toegang tot opslagaccounts die gebruikmaken van firewallregels en zich in dezelfde regio bevinden. Als u echter de uitgaande IP-adressen voor beheerde [connectors in](../logic-apps/logic-apps-limits-and-config.md#outbound)uw regio toe staan, hebben uw logische apps toegang tot opslagaccounts in een andere regio, behalve wanneer u de Azure Table Storage- of Azure Queue Storage-connectors gebruikt. Als u toegang wilt Table Storage of Queue Storage, kunt u in plaats daarvan de HTTP-trigger en -acties gebruiken. Zie Toegang tot opslagaccounts [achter firewalls voor andere opties.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Binnenkomende IP-adressen

In deze sectie worden alleen de binnenkomende IP-adressen voor de Azure Logic Apps service vermeld. Als u een Azure Government hebt, zie [Azure Government - Inkomende IP-adressen.](#azure-government-inbound)

> [!TIP]
> Om de complexiteit bij het maken van beveiligingsregels te verminderen, kunt u desgewenst de [servicetag](../virtual-network/service-tags-overview.md), **LogicAppsManagement** gebruiken in plaats van voorvoegsels voor binnenkomende Logic Apps IP-adressen op te geven voor elke regio. U kunt eventueel ook de servicetag **AzureConnectors** gebruiken voor beheerde connectors die inkomende webhook-callbacks maken naar de Logic Apps-service, in plaats van ip-adres voor binnenkomende beheerde connectors voor elke regio op te geven. Deze tags werken in de regio's waar Logic Apps service beschikbaar is.
>
> De volgende connectors maken inkomende webhook-callbacks naar de Logic Apps service:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign Demo, Adobe Sign Preview, Adobe Sign Stage, Azure Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign Demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, Shifts for Microsoft Teams, Teamwork Projects, Typeform
>
> \***SAP:** de retouroproeper is afhankelijk van of de implementatieomgeving Azure of ISE met meerdere tenants is. In de omgeving met meerdere tenants maakt de on-premises gegevensgateway de aanroep terug naar Logic Apps service. In een ISE maakt de SAP-connector de aanroep terug naar de Logic Apps service.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Azure met meerdere tenants : binnenkomende IP-adressen

| Regio met meerdere tenants | IP |
|---------------------|----|
| Australië - oost | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australië - zuidoost | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazilië - zuid | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Brazilië - zuidoost | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Canada - midden | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Canada - oost | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| India - centraal | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Central US | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Azië - oost | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| VS - oost | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| VS - oost 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Frankrijk - centraal | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Frankrijk - zuid | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Duitsland - noord | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Duitsland - west-centraal | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Japan - oost | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japan - west | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Korea - centraal | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea - zuid | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| VS - noord-centraal | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa - noord | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Noorwegen - oost | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Zuid-Afrika - noord | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Zuid-Afrika - west | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| VS - zuid-centraal | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| India - zuid | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Azië - zuidoost | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Zwitserland - noord | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Zwitserland - west | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| UAE - centraal | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| VAE - noord | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Verenigd Koninkrijk Zuid | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Verenigd Koninkrijk West | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| VS - west-centraal | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa -west | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| India - west | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| VS - west | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| VS - west 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government - Binnenkomende IP-adressen

| Azure Government regio | IP |
|-------------------------|----|
| VS (overheid) - Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| VS (overheid) - Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| VS (overheid) - Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD Central | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Uitgaande IP-adressen

In deze sectie vindt u de uitgaande IP-adressen voor de Azure Logic Apps-service en beheerde connectors. Als u een Azure Government hebt, zie [Azure Government - Uitgaande IP-adressen.](#azure-government-outbound)

> [!TIP]
> Als u de complexiteit wilt verminderen bij het maken van beveiligingsregels, kunt u eventueel de [servicetag](../virtual-network/service-tags-overview.md), **LogicApps,** gebruiken in plaats van uitgaande Logic Apps IP-adres voor elke regio op te geven. U kunt eventueel ook de servicetag **AzureConnectors** gebruiken voor beheerde connectors die uitgaande aanroepen naar hun respectieve services uitvoeren, zoals Azure Storage of Azure Event Hubs, in plaats van ip-adres voor uitgaande beheerde connectors voor elke regio op te geven. Deze tags werken in de regio's waar Logic Apps service beschikbaar is.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Azure met meerdere tenants : uitgaande IP-adressen

| Regio met meerdere tenants | Logic Apps IP-adres | IP van beheerde connectors |
|---------------------|---------------|-----------------------|
| Australië - oost | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Australië - zuidoost | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Brazilië - zuid | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Brazilië - zuidoost | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 | 23.97.120.109, 23.97.121.26 |
| Canada - midden | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Canada - oost | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| India - centraal | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| Central US | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Azië - oost | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| VS - oost | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| VS - oost 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| Frankrijk - centraal | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Frankrijk - zuid | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Duitsland - noord | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Duitsland - west-centraal | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Japan - oost | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Japan - west | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Korea - centraal | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Korea - zuid | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| VS - noord-centraal | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Europa - noord | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Noorwegen - oost | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Zuid-Afrika - noord | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Zuid-Afrika - west | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| VS - zuid-centraal | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| India - zuid | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Azië - zuidoost | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Zwitserland - noord | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| Zwitserland - west | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| UAE - centraal | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| VAE - noord | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Verenigd Koninkrijk Zuid | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Verenigd Koninkrijk West | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| VS - west-centraal | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| Europa -west | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| India - west | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| VS - west | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| VS - west 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government - Uitgaande IP-adressen

| Region | Logic Apps IP-adres | IP van beheerde connectors |
|--------|---------------|-----------------------|
| US DoD Central | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| VS (overheid) - Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| VS (overheid) - Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| VS (overheid) - Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Meer informatie over [algemene voorbeelden en scenario's](../logic-apps/logic-apps-examples-and-scenarios.md)
