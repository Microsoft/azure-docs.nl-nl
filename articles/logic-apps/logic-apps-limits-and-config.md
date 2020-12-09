---
title: Limieten en configuratie
description: Service limieten, zoals de duur, de door Voer en de capaciteit, plus configuratie waarden, zoals IP-adressen die kunnen worden toegestaan, voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 12/07/2020
ms.openlocfilehash: 154be7e4340c798ba1d014b210361f666864797e
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921515"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informatie over limieten en configuratie voor Azure Logic Apps

In dit artikel worden de limieten en configuratie gegevens voor het maken en uitvoeren van geautomatiseerde werk stromen met Azure Logic Apps beschreven. Zie [limieten en configuratie in energie automatisering](/flow/limits-and-config)voor energie automatisering.

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Definitie limieten van logische apps

Dit zijn de limieten voor een definitie van een enkele logische app:

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Acties per werkstroom | 500 | Als u deze limiet wilt uitbreiden, kunt u indien nodig geneste werk stromen toevoegen. |
| Toegestane nest diepte voor acties | 8 | Als u deze limiet wilt uitbreiden, kunt u indien nodig geneste werk stromen toevoegen. |
| Werk stromen per regio per abonnement | 1000 | |
| Triggers per werk stroom | 10 | Wanneer u werkt in de code weergave, niet de ontwerper |
| Limiet voor switch Scope cases | 25 | |
| Variabelen per werk stroom | 250 | |
| Naam voor `action` of `trigger` | 80 tekens | |
| Tekens per expressie | 8\.192 | |
| Lengte van `description` | 256 tekens | |
| Maximum aantal `parameters` | 50 | |
| Maximum aantal `outputs` | 10 | |
| Maximale grootte voor `trackedProperties` | 16.000 tekens |
| Inline code actie-maximum aantal code tekens | 1.024 tekens | Als u deze limiet wilt uitbreiden tot 100.000 tekens, maakt u uw logische apps met het resource type **Logic app (preview)** , hetzij met [behulp van de Azure Portal](create-stateful-stateless-workflows-azure-portal.md) , hetzij met behulp van [Visual Studio code en de uitbrei ding **Azure Logic apps (preview)**](create-stateful-stateless-workflows-visual-studio-code.md). |
| Inline code actie-maximale duur voor het uitvoeren van code | 5 seconden | Als u deze limiet wilt uitbreiden naar een 15 seconden, maakt u uw Logic apps met het resource type **Logic app (preview)** , [door gebruik te maken van de Azure Portal](create-stateful-stateless-workflows-azure-portal.md) of [door middel van Visual Studio code en de uitbrei ding **Azure Logic apps (preview)**](create-stateful-stateless-workflows-visual-studio-code.md). |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Limieten voor uitvoerings duur en bewaar periode

Dit zijn de limieten voor het uitvoeren van een enkele logische app:

| Name | Limiet voor meerdere tenants | Limiet voor de integratie service omgeving | Notities |
|------|--------------------|---------------------------------------|-------|
| Uitvoeringsduur | 90 dagen | 366 dagen | De uitvoerings duur wordt berekend met behulp van de begin tijd van de uitvoering en de limiet die is opgegeven in de werk stroom instelling, [**uitvoerings geschiedenis in dagen**](#change-duration) op die begin tijd. <p><p>Als u de standaard limiet wilt wijzigen, raadpleegt u de [uitvoerings duur en de retentie van de geschiedenis in de opslag ruimte wijzigen](#change-duration). |
| Bewaar periode voor geschiedenis in opslag | 90 dagen | 366 dagen | Als de duur van een uitvoering de huidige Bewaar limiet voor de uitvoerings geschiedenis overschrijdt, wordt de uitvoering verwijderd uit de uitvoerings geschiedenis in de opslag. Of de uitvoering is voltooid of er een time-out optreedt, de retentie van de uitvoerings geschiedenis wordt altijd berekend met behulp van de begin tijd van de uitvoering en de huidige limiet die is opgegeven in de werk stroom instelling, de [**Bewaar periode voor de geschiedenis in dagen**](#change-retention). Ongeacht de vorige limiet wordt de huidige limiet altijd gebruikt voor het berekenen van de Bewaar periode. <p><p>Als u de standaard limiet wilt wijzigen en meer informatie wilt, raadpleegt u de [duur en de retentie van de uitvoerings geschiedenis in de opslag ruimte wijzigen](#change-retention). Als u de maximum limiet wilt verhogen, [neemt u contact op met het Logic apps team](mailto://logicappsemail@microsoft.com) voor hulp bij uw vereisten. |
| Mini maal terugkeer patroon | 1 seconde | 1 seconde ||
| Maximum interval van terugkeer patroon | 500 dagen | 500 dagen ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>De uitvoerings duur en de retentie van de geschiedenis in de opslag wijzigen

Met dezelfde instelling bepaalt u het maximum aantal dagen dat een werk stroom kan worden uitgevoerd en voor het behouden van de uitvoerings geschiedenis in de opslag. Volg deze stappen om de standaard-of huidige limiet voor deze eigenschappen te wijzigen.

* Voor logische apps in azure met meerdere tenants is de standaard limiet van 90 dagen gelijk aan de maximum limiet. U kunt deze waarde alleen verlagen.

* Voor Logic apps in een integratie service omgeving kunt u de standaard limiet van 90 dagen verlagen of verhogen.

Stel dat u de limiet voor het bewaren van 90 dagen tot 30 dagen hebt beperkt. Een 60-daagse-oude uitvoering wordt verwijderd uit de uitvoerings geschiedenis. Als u de retentie periode van 30 dagen tot 60 dagen verhoogt, blijft de uitvoering van 20 dagen oud in de geschiedenis van de uitvoeringen voor een andere 40-dag.

> [!IMPORTANT]
> Als de duur van een uitvoering de huidige Bewaar limiet voor de uitvoerings geschiedenis overschrijdt, wordt de uitvoering verwijderd uit de uitvoerings geschiedenis in de opslag. Om te voor komen dat de uitvoerings geschiedenis verloren gaat, moet u ervoor zorgen dat de Bewaar limiet *altijd* hoger is dan de langst mogelijke duur van de uitvoering.

1. Zoek en selecteer **Logic apps** in het zoekvak [Azure Portal](https://portal.azure.com) .

1. Zoek en selecteer uw logische app. Open uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **werk stroom instellingen** in het menu van de logische app.

1. Selecteer onder **runtime-opties** in de lijst **uitvoerings geschiedenis uitvoeren in dagen** de optie **aangepast**.

1. Sleep de schuif regelaar om het gewenste aantal dagen te wijzigen.

1. Wanneer u klaar bent, selecteert u op de werk balk **werk stroom instellingen** de optie **Opslaan**.

Als u een Azure Resource Manager sjabloon voor uw logische app genereert, wordt deze instelling weer gegeven als een eigenschap in de resource definitie van de werk stroom, die wordt beschreven in de [verwijzing naar de sjabloon micro soft. Logic-werk stromen](/azure/templates/microsoft.logic/workflows):

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

## <a name="concurrency-looping-and-debatching-limits"></a>Gelijktijdigheid, herhalingen en limieten voor het debatchren

Dit zijn de limieten voor het uitvoeren van een enkele logische app:

### <a name="loops"></a>Lussen

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Elementen van foreach-matrix | 100.000 | Deze limiet beschrijft het hoogste aantal matrix items dat voor elke lus kan worden verwerkt. <p><p>Als u grotere matrices wilt filteren, kunt u de [query actie](logic-apps-perform-data-operations.md#filter-array-action)gebruiken. |
| Gelijktijdigheid van foreach | Met gelijktijdigheid: 20 <p><p>Met gelijktijdigheid op: <p><p>-Standaard: 20 <br>-Min: 1 <br>-Maximum: 50 | Deze limiet is het hoogste aantal ' for each '-herhalingen die tegelijkertijd kunnen worden uitgevoerd, of parallel. <p><p>Als u deze limiet wilt wijzigen, raadpleegt u ["voor elke" gelijktijdige overschrijding](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) "of [" voor elk "elke" herhalen](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Until-iteraties | -Standaard: 60 <br>-Min: 1 <br>-Maximum: 5.000 | Het hoogste aantal cycli dat een ' until '-lus kan hebben tijdens het uitvoeren van een logische app. <p><p>Als u deze limiet wilt wijzigen, selecteert u in de lus until de optie **limieten wijzigen** en geeft u de waarde voor de eigenschap **aantal** op. |
| Until-time-out | -Standaard: PT1H (1 uur) | De maximale hoeveelheid tijd dat de ' until '-lus kan worden uitgevoerd voordat deze wordt afgesloten en is opgegeven in [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). De time-outwaarde wordt geëvalueerd voor elke lus-cyclus. Als een actie in de lus langer duurt dan de time-outlimiet, wordt de huidige cyclus niet gestopt. De volgende cyclus wordt echter niet gestart omdat niet wordt voldaan aan de limiet voorwaarde. <p><p>Als u deze limiet wilt wijzigen, selecteert u in de lus until de optie **limieten wijzigen** en geeft u de waarde op voor de eigenschap **timeout** . |
||||

### <a name="concurrency-and-debatching"></a>Gelijktijdigheid en batch verwerking

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Gelijktijdigheid van triggers | Met gelijktijdigheid: onbeperkt <p><p>Met gelijktijdigheid op, wat u niet ongedaan kunt maken na het inschakelen van: <p><p>-Standaard: 25 <br>-Min: 1 <br>-Maximum: 50 | Deze limiet is het hoogste aantal logische app-exemplaren dat tegelijkertijd of parallel kan worden uitgevoerd. <p><p>**Opmerking**: wanneer gelijktijdigheid is ingeschakeld, is de limiet voor SplitOn beperkt tot 100 items voor het [debatchiseren van matrices](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Zie voor het wijzigen van deze limiet de [gelijktijdige overschrijding](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) van de trigger of [trigger instanties sequentieel](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximum aantal wachtende uitvoeringen | Met gelijktijdigheid: <p><p>-Min: 1 <br>-Maximum: 50 <p><p>Met gelijktijdigheid op: <p><p>-Min: 10 plus het aantal gelijktijdige uitvoeringen (activerings gelijktijdigheid) <br>-Maximum: 100 | Deze limiet is het hoogste aantal logische app-exemplaren dat kan worden uitgevoerd als het maximum aantal gelijktijdige exemplaren van de logische app al wordt uitgevoerd. <p><p>Zie de limiet voor het uitvoeren van een [wacht tijd wijzigen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)om deze limiet te wijzigen. |
| SplitOn-items | Met gelijktijdigheid: 100.000 <p><p>Met gelijktijdigheid op: 100 | Voor triggers die een matrix retour neren, kunt u een expressie opgeven die gebruikmaakt van een eigenschap SplitOn die de [matrix items in meerdere workflowexemplaren voor verwerking splitst of opsplitst](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) , in plaats van een foreach-lus te gebruiken. Deze expressie verwijst naar de matrix die moet worden gebruikt voor het maken en uitvoeren van een workflowexemplaar voor elk matrix item. <p><p>**Opmerking**: wanneer gelijktijdigheid is ingeschakeld, is de limiet van SplitOn beperkt tot 100 items. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Doorvoerlimieten

Dit zijn de limieten voor een definitie van een enkele logische app:

### <a name="multi-tenant-logic-apps-service"></a>Multi tenant-Logic Apps service

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Actie: uitvoeringen per 5 minuten | 100.000 is de standaard limiet, maar 300.000 is de maximum limiet. | Als u de standaard limiet voor de logische app wilt verhogen, raadpleegt u [uitvoeren in de modus voor hoge door](#run-high-throughput-mode)Voer, in de preview-versie. Of u kunt [de werk belasting indien nodig verdelen over meerdere logische apps](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) . |
| Actie: gelijktijdige uitgaande oproepen | ~2500 | U kunt het aantal gelijktijdige aanvragen verminderen of de duur beperken als dat nodig is. |
| Runtime-eind punt: gelijktijdige inkomende aanroepen | ~ 1.000 | U kunt het aantal gelijktijdige aanvragen verminderen of de duur beperken als dat nodig is. |
| Runtime-eind punt: Lees oproepen per vijf minuten  | 60.000 | Deze limiet is van toepassing op aanroepen die de onbewerkte invoer en uitvoer ophalen uit de uitvoerings geschiedenis van een logische app. U kunt de werk belasting naar meerdere apps distribueren als dat nodig is. |
| Runtime-eind punt: aanroepen aanroepen per 5 minuten | 45.000 | U kunt de werk belasting naar meerdere apps distribueren als dat nodig is. |
| Inhouds doorvoer per 5 minuten | 600 MB | U kunt de werk belasting naar meerdere apps distribueren als dat nodig is. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Uitvoeren in de modus voor hoge door Voer

Voor een definitie van een enkele Logic-app is het aantal acties dat elke 5 minuten wordt uitgevoerd een [standaard limiet](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Als u de standaard limiet wilt verhogen naar het maximum voor uw logische app, kunt u de modus voor hoge door Voer inschakelen, in de preview-versie. Of u kunt [de werk belasting indien nodig verdelen over meerdere logische apps](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) .

1. Selecteer in het Azure Portal, in het menu van de logische app, onder **instellingen** de **werk stroom instellingen**.

1. Wijzig de instelling onder **runtime opties**  >  **hoge door Voer** in **op aan**.

   ![Scherm opname van het menu van de logische app in Azure Portal met werk stroom instellingen en hoge door Voer is ingesteld op aan.](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Als u deze instelling wilt inschakelen in een ARM-sjabloon voor het implementeren van uw logische app, voegt u in het `properties` object voor de resource definitie van uw logische app het `runtimeConfiguration` object toe waarvoor de `operationOptions` eigenschap is ingesteld op `OptimizedForHighThroughput` :

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

Zie [overzicht: de implementatie voor Azure Logic apps automatiseren met behulp van Azure Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)voor meer informatie over de resource definitie van de logische app.

### <a name="integration-service-environment-ise"></a>Integration service Environment (ISE)

Dit zijn de doorvoer limieten voor de [Premium ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

| Name | Limiet | Notities |
|------|-------|-------|
| Uitvoerings limiet basis eenheid | Systeem-beperkt wanneer de capaciteit van de infra structuur 80% bereikt | Biedt ~ 4.000 actie-uitvoeringen per minuut, wat ~ 160.000.000 actie uitvoeringen per maand is | |
| Limiet voor het uitvoeren van schaal eenheden | Systeem-beperkt wanneer de capaciteit van de infra structuur 80% bereikt | Elke schaal eenheid kan ~ 2.000 extra actie-uitvoeringen per minuut bieden, wat ~ 80.000.000 meer actie-uitvoeringen per maand | |
| Maximale schaal eenheden die u kunt toevoegen | 10 | |
||||

[Neem contact op met het Logic apps team](mailto://logicappsemail@microsoft.com) om aan uw vereisten te voldoen, om aan de slag te gaan met de normale verwerking, of om belasting tests uit te voeren.

> [!NOTE]
> De [ISE-SKU voor ontwikkel aars](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) heeft geen gepubliceerde limieten, geen mogelijkheden voor het schalen van omhoog en geen Service Level Agreement (Sla). Gebruik deze SKU alleen voor experimenteren, ontwikkelen en testen, niet voor productie-of prestatie testen.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Gatewaylimieten

Azure Logic Apps ondersteunt schrijf bewerkingen, met inbegrip van invoeg acties en updates via de gateway. Deze bewerkingen hebben echter [limieten voor de grootte van de nettolading](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="http-limits"></a>

## <a name="http-limits"></a>HTTP-limieten

Dit zijn de limieten voor één binnenkomende of uitgaande oproep:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Time-outperiode

Sommige connector bewerkingen maken asynchrone aanroepen of Luis teren naar webhook-aanvragen, zodat de time-out voor deze bewerkingen mogelijk langer is dan deze limieten. Zie de technische Details voor de specifieke connector en ook [werk stroom triggers en acties](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)voor meer informatie.

| Name | Logic Apps (multi tenant) | Logic Apps (preview-versie) | Integratie service omgeving | Notities |
|------|---------------------------|----------------------|---------------------------------|-------|
| Uitgaande aanvraag | 120 seconden <br>(2 minuten) | 230 seconden <br>(3,9 minuten) | 240 seconden <br>(4 minuten) | Voor beelden van uitgaande aanvragen zijn aanroepen van de HTTP-trigger of actie. Zie [Azure Logic Apps Preview](logic-apps-overview-preview.md)voor meer informatie over de preview-versie. <p><p>**Tip**: gebruik een [asynchroon polling-patroon](../logic-apps/logic-apps-create-api-app.md#async-pattern) of een [until-lus](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)voor het uitvoeren van bewerkingen die langer worden uitgevoerd. Als u de time-outlimieten wilt omzeilen wanneer u een andere logische app aanroept die een [aanroepbaar eind punt](logic-apps-http-endpoint.md)heeft, kunt u in plaats daarvan de ingebouwde Azure Logic apps actie gebruiken, die u kunt vinden in de connector kiezer onder **ingebouwde**. |
| Inkomende aanvraag | 120 seconden <br>(2 minuten) | 230 seconden <br>(3,9 minuten) | 240 seconden <br>(4 minuten) | Voor beelden van binnenkomende aanvragen zijn onder andere aanroepen die zijn ontvangen door de aanvraag trigger, de HTTP-webhook-trigger en de HTTP-webhook-actie. Zie [Azure Logic Apps Preview](logic-apps-overview-preview.md)voor meer informatie over de preview-versie. <p><p>**Opmerking**: voor de oorspronkelijke beller om het antwoord te krijgen, moeten alle stappen in het antwoord binnen de limiet worden voltooid, tenzij u een andere logische app als geneste werk stroom aanroept. Zie [Logic apps aanroepen, activeren of nesten](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie. |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Berichtgrootte

| Name | Limiet voor meerdere tenants | Limiet voor de integratie service omgeving | Notities |
|------|--------------------|---------------------------------------|-------|
| Berichtgrootte | 100 MB | 200 MB | Zie [grote berichten verwerken met Chunking](../logic-apps/logic-apps-handle-large-messages.md)om deze limiet te omzeilen. Sommige connectors en Api's ondersteunen echter mogelijk geen Chunking of zelfs de standaard limiet. <p><p>-Connectors zoals AS2, X12 en EDIFACT hebben hun eigen [B2B-bericht limieten](#b2b-protocol-limits). <br>-ISE-connectors maken gebruik van de ISE-limiet, niet de limieten voor niet-ISE-connectors. |
| Bericht grootte met Chunking | 1 GB | 5 GB | Deze limiet geldt voor acties die systeem eigen ondersteuning bieden voor Chunking of waarmee u Chunking in de runtime configuratie kunt inschakelen. <p><p>Als u een ISE gebruikt, wordt deze limiet door de Logic Apps-Engine ondersteund, maar de connectors hebben hun eigen segment beperking tot de limiet van de engine. Zie de [API-naslag informatie voor de Azure Blob Storage-connector](/connectors/azureblob/). Zie [grote berichten afhandelen met Chunking](../logic-apps/logic-apps-handle-large-messages.md)voor meer informatie over segmenteren. |
|||||

#### <a name="character-limits"></a>Teken limieten

| Name | Notities |
|------|-------|
| Limiet voor evaluatie van expressie | 131.072 tekens | De `@concat()` , `@base64()` , `@string()` expressies mogen niet langer zijn dan deze limiet. |
| Maximum aantal tekens van aanvraag-URL | 16.384 tekens |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Beleid voor opnieuw proberen

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Nieuwe pogingen | 90 | De standaard is 4. Als u de standaard waarde wilt wijzigen, gebruikt u de [para meter beleid opnieuw proberen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximale vertraging nieuwe poging | 1 dag | Als u de standaard waarde wilt wijzigen, gebruikt u de [para meter beleid opnieuw proberen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimale vertraging nieuwe poging | 5 seconden | Als u de standaard waarde wilt wijzigen, gebruikt u de [para meter beleid opnieuw proberen](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Verificatie limieten

Dit zijn de limieten voor een logische app die begint met een trigger voor aanvragen en waarmee [Azure Active Directory open verificatie](../active-directory/develop/index.yml) (Azure AD OAuth) voor het autoriseren van binnenkomende oproepen naar de aanvraag trigger wordt ingeschakeld:

| Name | Limiet | Notities |
| ---- | ----- | ----- |
| Autorisatie beleid voor Azure AD | 5 | |
| Claims per autorisatie beleid | 10 | |
| Claim waarde-maximum aantal tekens | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limieten voor aangepaste connectors

Dit zijn de limieten voor aangepaste connectors die u kunt maken op basis van web-Api's.

| Name | Limiet voor meerdere tenants | Limiet voor de integratie service omgeving | Notities |
|------|--------------------|---------------------------------------|-------|
| Aantal aangepaste connectors | 1000 per Azure-abonnement | 1000 per Azure-abonnement ||
| Aantal aanvragen per minuut voor een aangepaste connector | 500 aanvragen per minuut per verbinding | 2.000 aanvragen per minuut per *aangepaste connector* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Beheerde identiteiten

| Name | Limiet |
|------|-------|
| Beheerde identiteiten per logische app | De door het systeem toegewezen identiteit of 1 aan de gebruiker toegewezen identiteit |
| Aantal Logic apps die een beheerde identiteit hebben in een Azure-abonnement per regio | 1000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limieten voor integratieaccounts

Elk Azure-abonnement heeft deze limieten voor het integratie account:

* Eén integratie account voor de [gratis laag](../logic-apps/logic-apps-pricing.md#integration-accounts) per Azure-regio. Deze laag is alleen beschikbaar voor open bare regio's in azure, zoals vs-West of Zuidoost-Azië, maar niet voor [Azure China 21vianet](/azure/china/overview-operations) of [Azure Government](../azure-government/documentation-government-welcome.md).

* 1.000 totaal aantal integratie accounts, met inbegrip van integratie accounts in een [ISE (Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) voor zowel [ontwikkel aars als Premium-sku's](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Elke ISE, of [Developer of Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), is beperkt tot 20 totaal aantal integratie accounts:

  | ISE SKU | Limieten voor integratieaccounts |
  |---------|----------------------------|
  | **Premium** | 20 totaal- [standaard](../logic-apps/logic-apps-pricing.md#integration-accounts) accounts, met inbegrip van één standaard account gratis. Er zijn geen gratis of basis accounts toegestaan. |
  | **Developer** | 20 totaal- [gratis](../logic-apps/logic-apps-pricing.md#integration-accounts) (beperkt tot 1 account) en [standaard](../logic-apps/logic-apps-pricing.md#integration-accounts) gecombineerd, of alle standaard accounts. Er zijn geen basis accounts toegestaan. Gebruik de [Developer-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) voor experimenteren, ontwikkelen en testen, maar niet voor productie-of prestatie testen. |
  |||

Aanvullende kosten zijn van toepassing op integratie accounts die u toevoegt buiten de integratie accounts die zijn opgenomen in een ISE. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturerings werkzaamheden voor ISEs. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven.

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limieten voor artefacten per integratie account

Dit zijn de limieten voor het aantal artefacten voor elke laag van de integratie-account.
Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#integration-accounts)voor meer informatie over de prijzen en facturering voor integratie accounts.

> [!NOTE]
> Gebruik de laag gratis alleen voor experimentele scenario's, niet voor productie scenario's. Deze laag beperkt de door Voer en het gebruik en heeft geen SLA (Service Level Agreement).

| Artefact | Gratis | Basic | Standard |
|----------|------|-------|----------|
| EDI-handels overeenkomsten | 10 | 1 | 1000 |
| EDI-handels partners | 25 | 2 | 1000 |
| Maps | 25 | 500 | 1000 |
| Schema 's | 25 | 500 | 1000 |
| Assembly's | 10 | 25 | 1000 |
| Certificaten | 25 | 2 | 1000 |
| Batch configuraties | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Capaciteits limieten artefacten

| Artefact | Limiet | Notities |
| -------- | ----- | ----- |
| Assembly | 8 MB | Als u bestanden wilt uploaden die groter zijn dan 2 MB, gebruikt u een [Azure-opslag account en een BLOB-container](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Kaart (XSLT-bestand) | 8 MB | Als u bestanden wilt uploaden die groter zijn dan 2 MB, gebruikt u de [Azure Logic apps-rest API-kaarten](/rest/api/logic/maps/createorupdate). <p><p>**Opmerking**: de hoeveelheid gegevens of records die een kaart kan verwerken is gebaseerd op de grootte van het bericht en de time-outlimieten van de actie in azure Logic apps. Als u bijvoorbeeld een HTTP-actie gebruikt, op basis van de grootte van het [http-bericht en time-outlimieten](#http-limits), kan een toewijzing gegevens verwerken tot de maximale grootte van de HTTP-berichten als de bewerking is voltooid binnen de time-outlimiet van http. |
| Schema | 8 MB | Als u bestanden wilt uploaden die groter zijn dan 2 MB, gebruikt u een [Azure-opslag account en een BLOB-container](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Doorvoerlimieten

| Runtime-eind punt | Gratis | Basic | Standard | Notities |
|------------------|------|-------|----------|-------|
| Lees aanroepen per 5 minuten | 3000 | 30.000 | 60.000 | Deze limiet is van toepassing op aanroepen die de onbewerkte invoer en uitvoer ophalen uit de uitvoerings geschiedenis van een logische app. U kunt de werk belasting naar meerdere accounts distribueren als dat nodig is. |
| Aanroepen starten per 5 minuten | 3000 | 30.000 | 45.000 | U kunt de werk belasting naar meerdere accounts distribueren als dat nodig is. |
| Tracerings aanroepen per 5 minuten | 3000 | 30.000 | 45.000 | U kunt de werk belasting naar meerdere accounts distribueren als dat nodig is. |
| Gelijktijdige aanroepen blok keren | ~ 1.000 | ~ 1.000 | ~ 1.000 | Hetzelfde voor alle Sku's. U kunt het aantal gelijktijdige aanvragen verminderen of de duur beperken als dat nodig is. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B-Protocol (AS2, X12, EDIFACT) bericht grootte

Dit zijn de limieten voor de bericht grootte die van toepassing zijn op B2B-protocollen:

| Name | Limiet voor meerdere tenants | Limiet voor de integratie service omgeving | Notities |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2-100 MB<br>v1-25 MB | v2-200 MB <br>v1-25 MB | Van toepassing op decoderen en coderen |
| X12 | 50 MB | 50 MB | Van toepassing op decoderen en coderen |
| EDIFACT | 50 MB | 50 MB | Van toepassing op decoderen en coderen |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Logic apps uitschakelen of verwijderen

Wanneer u een logische app uitschakelt, worden er geen nieuwe uitvoeringen geïnstantieerd. Alle lopende uitvoeringen en in behandeling zijnde uitvoeringen gaan door totdat ze zijn voltooid. Dit kan enige tijd in beslag nemen.

Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Firewall configuratie: IP-adressen en service Tags

De IP-adressen die Azure Logic Apps gebruikt voor binnenkomende en uitgaande aanroepen, zijn afhankelijk van de regio waar de logische app zich bevindt. *Alle* Logic apps in dezelfde regio gebruiken dezelfde IP-adresbereiken. Sommige [Energiebeheer](/power-automate/getting-started) aanroepen, zoals **http-** en **http + OpenAPI** -aanvragen, gaan rechtstreeks via de Azure Logic apps-service en komen van de IP-adressen die hier worden vermeld. Zie [limieten en configuratie in energie automatisering](/flow/limits-and-config#ip-address-configuration)voor meer informatie over IP-adressen die worden gebruikt door automatische energie automatisering.

> [!TIP]
> Om de complexiteit te verminderen bij het maken van beveiligings regels, kunt u optioneel [service Tags](../virtual-network/service-tags-overview.md)gebruiken, in plaats van de Logic apps IP-adressen voor elke regio op te geven, verderop in deze sectie.
> Deze tags werken in de regio's waar de Logic Apps-service beschikbaar is:
>
> * **LogicAppsManagement**: vertegenwoordigt de inkomende IP-adres voorvoegsels voor de Logic apps-service.
> * **LogicApps**: geeft de uitgaande IP-adres voorvoegsels voor de Logic apps-service.

* Voor [Azure China 21vianet](/azure/china/)zijn vaste of gereserveerde IP-adressen niet beschikbaar voor [aangepaste connectors](../logic-apps/custom-connector-overview.md) en [beheerde connectors](../connectors/apis-list.md#managed-api-connectors), bijvoorbeeld Azure Storage, SQL Server, Office 365 Outlook, enzovoort.

* Ter ondersteuning van de aanroepen die uw Logic apps rechtstreeks aanbrengt met [http](../connectors/connectors-native-http.md), [http + Swagger](../connectors/connectors-native-http-swagger.md)en andere HTTP-aanvragen, stelt u uw firewall in met alle [inkomende](#inbound) *en* [uitgaande](#outbound) IP-adressen die worden gebruikt door de Logic apps-service, op basis van de regio's waar uw Logic apps bestaan. Deze adressen worden weer gegeven onder de kopteksten **binnenkomend** en **uitgaand** in deze sectie en worden per regio gesorteerd.

* Ter ondersteuning van de aanroepen die [beheerde connectors](../connectors/apis-list.md#managed-api-connectors) maken, stelt u uw firewall in met *alle* [uitgaande](#outbound) IP-adressen die door deze connectors worden gebruikt, op basis van de regio's waar uw Logic apps bestaan. Deze adressen worden weer gegeven onder de kop **uitgaand** in deze sectie en worden per regio gesorteerd.

* Als u communicatie wilt inschakelen voor logische apps die worden uitgevoerd in een Integration service Environment (ISE), moet u [deze poorten openen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Als uw Logic apps problemen hebben met het openen van Azure-opslag accounts die [firewall-en firewall regels](../storage/common/storage-network-security.md)gebruiken, hebt u [verschillende opties om toegang in te scha kelen](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Logic apps hebben bijvoorbeeld geen directe toegang tot opslag accounts die gebruikmaken van firewall regels en die zich in dezelfde regio bevinden. Als u echter de [uitgaande IP-adressen voor beheerde connectors in uw regio](../logic-apps/logic-apps-limits-and-config.md#outbound)toestaat, hebben uw Logic apps toegang tot opslag accounts die zich in een andere regio bevinden, behalve wanneer u de Azure Table Storage-of Azure Queue Storage-connectors gebruikt. Voor toegang tot uw Table Storage of Queue Storage kunt u in plaats daarvan de HTTP-trigger en acties gebruiken. Zie voor andere opties [toegang tot opslag accounts achter firewalls](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Inkomende IP-adressen

In deze sectie worden alleen de inkomende IP-adressen voor de Azure Logic Apps-service weer gegeven. Als u Azure Government hebt, raadpleegt u [Azure Government-inkomend IP-adressen](#azure-government-inbound).

> [!TIP]
> Als [u de complexiteit](../virtual-network/service-tags-overview.md)wilt beperken wanneer u beveiligings regels maakt, kunt u eventueel de servicetag **LogicAppsManagement** gebruiken in plaats van inkomende Logic apps IP-adres voorvoegsels op te geven voor elke regio.
> Deze tag werkt in de regio's waar de Logic Apps-service beschikbaar is.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Multi tenant Azure-inkomende IP-adressen

| Multi tenant-regio | IP |
|---------------------|----|
| Australië - oost | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australia Southeast | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazil South | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
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
| Japan East | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japan - west | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Korea - centraal | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea - zuid | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| VS - noord-centraal | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa - noord | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Zuid-Afrika - noord | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Zuid-Afrika - west | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| South Central US | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| India - zuid | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Azië - zuidoost | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Zwitserland - noord | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| UAE - centraal | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Verenigd Koninkrijk Zuid | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Verenigd Koninkrijk West | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| VS - west-centraal | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa -west | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| India - west | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| VS - west | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| West US 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government-inkomende IP-adressen

| Azure Government regio | IP |
|-------------------------|----|
| VS (overheid) - Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| VS (overheid) - Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| VS (overheid) - Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD Central | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Uitgaande IP-adressen

In deze sectie vindt u de uitgaande IP-adressen voor de Azure Logic Apps-service en beheerde connectors. Als u Azure Government hebt, raadpleegt u [Azure Government-uitgaande IP-adressen](#azure-government-outbound).

> [!TIP]
> Als [u de complexiteit](../virtual-network/service-tags-overview.md)wilt beperken wanneer u beveiligings regels maakt, kunt u eventueel de servicetag **LogicApps** gebruiken in plaats van uitgaande Logic apps IP-adres voorvoegsels opgeven voor elke regio.
> Voor beheerde connectors kunt u optioneel het **AzureConnectors** -service label gebruiken in plaats van de uitgaande IP-adres voorvoegsels voor beheerde connectors op te geven voor elke regio. Deze tags werken in de regio's waar de Logic Apps-service beschikbaar is. 

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Multi tenant-Azure-uitgaande IP-adressen

| Multi tenant-regio | Logic Apps IP-adres | IP van beheerde connectors |
|---------------------|---------------|-----------------------|
| Australië - oost | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Australia Southeast | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Brazil South | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
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
| Japan East | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Japan - west | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Korea - centraal | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Korea - zuid | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| VS - noord-centraal | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Europa - noord | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Zuid-Afrika - noord | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Zuid-Afrika - west | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| South Central US | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| India - zuid | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Azië - zuidoost | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Zwitserland - noord | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| UAE - centraal | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Verenigd Koninkrijk Zuid | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Verenigd Koninkrijk West | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| VS - west-centraal | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| Europa -west | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| India - west | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| VS - west | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| West US 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government-uitgaande IP-adressen

| Regio | Logic Apps IP-adres | IP van beheerde connectors |
|--------|---------------|-----------------------|
| US DoD Central | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| VS (overheid) - Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| VS (overheid) - Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| VS (overheid) - Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken van uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Meer informatie over [algemene voor beelden en scenario's](../logic-apps/logic-apps-examples-and-scenarios.md)
