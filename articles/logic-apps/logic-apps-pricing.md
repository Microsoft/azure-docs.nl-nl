---
title: Facturerings model voor prijzen &
description: Overzicht van de werking van de prijzen en het facturerings model voor Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 520b4a0e87f27a90a604947ae0b558066b4ab82f
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937590"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Prijs model voor Azure Logic Apps

[Azure Logic apps](../logic-apps/logic-apps-overview.md) helpt u om geautomatiseerde integratie werk stromen te maken en uit te voeren die in de Cloud kunnen worden geschaald. In dit artikel wordt beschreven hoe facturering en prijzen werken voor Azure Logic Apps. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijs tarieven.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Prijs model verbruik

Voor nieuwe logische apps die worden uitgevoerd in de open bare, ' wereld wijde ', multi tenant Azure Logic Apps service, betaalt u alleen voor wat u gebruikt. Deze Logic apps gebruiken een op verbruik gebaseerd abonnement en prijs model. In uw logische app is elke stap een actie en Azure Logic Apps meter alle acties die worden uitgevoerd in uw logische app.

Acties omvatten bijvoorbeeld:

* [Triggers](#triggers), die speciale acties zijn. Voor alle Logic apps is een trigger als de eerste stap vereist.

* [Ingebouwde of systeem eigen acties](../connectors/apis-list.md#built-in) , zoals http, aanroepen naar Azure Functions en API Management, enzovoort

* Aanroepen naar [beheerde connectors](../connectors/apis-list.md#managed-connectors) , zoals Outlook 365, Dropbox, enzovoort

* [Werk stroom acties beheren](../connectors/apis-list.md#control-workflow) , zoals lussen, voorwaardelijke instructies, enzovoort

[Standaard](../connectors/apis-list.md#managed-connectors) connectors worden in rekening gebracht tegen de [standaard prijs](https://azure.microsoft.com/pricing/details/logic-apps)van de verbinding. Algemeen beschik bare [Enter prise](../connectors/apis-list.md#managed-connectors) -connectors worden in rekening gebracht tegen de [prijs voor Enter prise connector](https://azure.microsoft.com/pricing/details/logic-apps), terwijl open bare preview Enter prise-connectors in rekening worden gebracht tegen de [standaard prijs](https://azure.microsoft.com/pricing/details/logic-apps)voor

Meer informatie over hoe facturering werkt op het niveau van [Triggers](#triggers) en [acties](#actions) . Of Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md)voor meer informatie over limieten.

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Model met vaste prijzen

Een [ *Integration service Environment* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) biedt een geïsoleerde manier om logische apps te maken en uit te voeren die toegang hebben tot bronnen in een virtueel Azure-netwerk. Logic apps die in een ISE worden uitgevoerd, zijn geen kosten voor het bewaren van gegevens. Wanneer u een ISE maakt en alleen tijdens het maken, kunt u een [ISE-niveau of "SKU"](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)kiezen, die verschillende [prijs tarieven](https://azure.microsoft.com/pricing/details/logic-apps)heeft:

* **Premium** ISE: de basis eenheid van deze SKU heeft vaste capaciteit, maar als u meer door voer nodig hebt, kunt u [meer schaal eenheden toevoegen](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) tijdens het maken van ISE of later. Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md#integration-service-environment-ise)voor limieten voor ISE.

* **Ontwikkelaar** ISE: deze SKU heeft geen mogelijkheid om omhoog te schalen, geen Service Level Agreement (SLA) en geen gepubliceerde limieten. Gebruik deze SKU alleen voor experimenteren, ontwikkelen en testen, niet voor productie-of prestatie testen.

Voor Logic apps die u in een ISE maakt en uitvoert, betaalt u voor deze mogelijkheden een [vaste prijs](https://azure.microsoft.com/pricing/details/logic-apps) (versus betalen per gebruik):

* [Ingebouwde](../connectors/apis-list.md#built-in) triggers en acties

  In een ISE worden met ingebouwde triggers en acties het **kern** label weer gegeven en uitgevoerd in dezelfde ISE als uw logische apps.

* [Standard](../connectors/apis-list.md#managed-connectors) -connectors en [Enter prise](../connectors/apis-list.md#enterprise-connectors) -connectors, waarmee u zoveel zakelijke verbindingen kunt maken als u wilt

   Standard-en Enter prise-connectors die het label **ISE** weer geven, worden uitgevoerd in dezelfde ISE als uw logische apps. Connectors die het label ISE niet weer geven, worden uitgevoerd in de open bare, ' wereld wijde ' multi tenant-Logic Apps service. Vaste prijzen gelden ook voor connectors die worden uitgevoerd in de multi tenant-service wanneer u deze gebruikt met Logic apps die worden uitgevoerd in een ISE.

* Gebruik van het [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) zonder extra kosten, op basis van uw [ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **Premium** ISE SKU: één [standaard-laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account

  * **Ontwikkelaar** ISE SKU: Eén integratie account voor de [gratis laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Elk ISE kan een [beperkt aantal integratie accounts](logic-apps-limits-and-config.md#integration-account-limits)hebben, ongeacht de SKU. U kunt deze limiet verhogen voor extra kosten:

  * **Premium** ISE SKU: Maxi maal vier standaard accounts. Geen vrije of basis accounts.

  * **Ontwikkelaar** ISE SKU: Maxi maal vier standaard accounts, of Maxi maal vijf standaard accounts. Geen basis accounts.

  Zie [limieten en configuratie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)voor meer informatie over de limieten van het integratie account. Verderop in dit onderwerp vindt u meer informatie over [de integratie-account lagen en hun prijs model](#integration-accounts) .

<a name="connectors"></a>

## <a name="connectors"></a>Connectors

Azure Logic Apps-connectors helpen uw logische app toegang te bieden tot apps, services en systemen in de Cloud of on-premises met behulp van [Triggers](#triggers), [acties](#actions)of beide. Connectors worden geclassificeerd als Standard of ENTER prise. Zie [connectors for Azure Logic apps](../connectors/apis-list.md)voor een overzicht van deze connectors. Als er geen vooraf gemaakte connectors beschikbaar zijn voor de REST-Api's die u wilt gebruiken in uw Logic apps, kunt u [aangepaste connectors](/connectors/custom-connectors)maken. Dit zijn alleen wrappers rond die rest-api's. Aangepaste connectors worden gefactureerd als standaard connectors. De volgende secties bevatten meer informatie over hoe de facturering voor triggers en acties werkt.

<a name="triggers"></a>

## <a name="triggers"></a>Triggers

Een trigger is altijd de eerste stap in de werk stroom van een logische app. Dit is een speciale actie waarmee een logisch app-exemplaar wordt gemaakt en uitgevoerd wanneer aan specifieke criteria wordt voldaan of een specifieke gebeurtenis plaatsvindt. Triggers handelen op verschillende manieren, wat van invloed is op hoe de logische app wordt gemeten. Hier volgen de verschillende soorten triggers die in Azure Logic Apps bestaan:

* **Trigger voor terugkeer patroon**: u kunt deze algemene trigger gebruiken die niet specifiek is voor een service of systeem, om de werk stroom van de logische app te starten en een logische app-exemplaar te maken dat wordt uitgevoerd op basis van het terugkeer interval dat u in de trigger hebt ingesteld. U kunt bijvoorbeeld een terugkeer patroon trigger instellen die elke drie dagen of op een complexere planning wordt uitgevoerd.

* **Polling trigger**: u kunt deze meer gespecialiseerde terugkeer patroon trigger gebruiken die meestal is gekoppeld aan de beheerde connector voor een specifieke service of systeem, om te controleren of er gebeurtenissen of berichten zijn die voldoen aan de criteria voor het maken en uitvoeren van een logische app-exemplaar op basis van het terugkeer interval dat u in de trigger hebt ingesteld. Zelfs wanneer er geen exemplaar van een logische app wordt gemaakt, bijvoorbeeld wanneer triggers worden overgeslagen, metert de Logic Apps-service elke polling-aanvraag als een uitvoering. Als u het polling-interval wilt opgeven, stelt u de trigger in via de ontwerp functie voor logische apps.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-trigger**: in plaats van een polling trigger te gebruiken, kunt u een webhook-trigger gebruiken om te wachten tot de client een aanvraag verzendt naar de logische app op een specifieke eind punt-URL. Elke aanvraag die naar het eind punt van de webhook wordt verzonden, telt als een actie-uitvoering. De trigger Request en HTTP-webhook zijn bijvoorbeeld zowel generieke webhook-triggers. Sommige connectors voor services of systemen hebben ook webhook-triggers.

<a name="actions"></a>

## <a name="actions"></a>Acties

Azure Logic Apps meters ' ingebouwde ' acties, zoals HTTP, als systeem eigen acties. Ingebouwde acties omvatten bijvoorbeeld HTTP-aanroepen, aanroepen van Azure Functions of API Management, en controle stroom stappen zoals voor waarden, lussen en switch instructies. Elke actie heeft een eigen actie type. Acties die [connectors](/connectors) aanroepen, hebben bijvoorbeeld het type ' ApiConnection '. Deze connectors worden geclassificeerd als Standard-of ENTER prise-connectors, die worden gemeten op basis van hun respectieve [prijzen](https://azure.microsoft.com/pricing/details/logic-apps). Enter prise-connectors in *Preview* worden in rekening gebracht als standaard connectors.

Azure Logic Apps meter alle geslaagde en mislukte acties als uitvoeringen. Logic Apps deze acties echter niet meten:

* Acties die worden overgeslagen vanwege unmet-voor waarden
* Acties die niet worden uitgevoerd omdat de logische app is gestopt voordat deze wordt voltooid

Voor acties die in lussen worden uitgevoerd, telt Azure Logic Apps elke actie voor elke cyclus in de lus. Stel dat u een lus ' voor elke ' hebt waarmee een lijst wordt verwerkt. Logic Apps meter een actie in die lus door het aantal lijst items te vermenigvuldigen met het aantal acties in de lus en de actie toe te voegen waarmee de lus wordt gestart. De berekening voor een lijst met tien items is dus (10 * 1) + 1, wat resulteert in elf actie-uitvoeringen.

## <a name="disabled-logic-apps"></a>Uitgeschakelde Logic apps

Uitgeschakelde Logic apps worden niet in rekening gebracht omdat ze geen nieuwe instanties kunnen maken wanneer ze zijn uitgeschakeld. Nadat u een logische app hebt uitgeschakeld, kan het enige tijd duren voordat de actieve instanties worden gestopt.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integratieaccounts

Een [vast prijs model](https://azure.microsoft.com/pricing/details/logic-apps) is van toepassing op [integratie accounts](logic-apps-enterprise-integration-create-integration-account.md) waar u de [B2B-en EDI](logic-apps-enterprise-integration-b2b.md) [-en XML-verwerkings](logic-apps-enterprise-integration-xml.md) functies in azure Logic apps zonder extra kosten kunt verkennen, ontwikkelen en testen. Elk Azure-abonnement kan Maxi maal een [specifieke limiet van integratie accounts](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)hebben. Elk integratie account kan tot een specifieke [limiet van artefacten](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)worden opgeslagen, waaronder handels partners, overeenkomsten, kaarten, schema's, assembly's, certificaten, batch configuraties, enzovoort.

Azure Logic Apps biedt gratis, Basic-en Standard-integratie accounts. De lagen basis en standaard worden ondersteund door de Logic Apps Service Level Agreement (SLA), terwijl de laag gratis niet wordt ondersteund door een SLA en limieten heeft voor de beschik baarheid, door Voer en het gebruik van de regio. Met uitzonde ring van de integratie accounts voor de gratis laag kunt u in elke Azure-regio meer dan één integratie account hebben. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven.

Als u een [ISE ( *Integration service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)hebt, ongeacht de [SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), kan uw ISE een [beperkt aantal integratie accounts](logic-apps-limits-and-config.md#integration-account-limits)hebben, maar u kunt [deze limiet verhogen voor extra kosten](#fixed-pricing). Zie de sectie vorig [vast prijs model](#fixed-pricing) in dit onderwerp voor meer informatie over hoe het vaste prijs model werkt voor een ISE. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijs tarieven.

Als u wilt kiezen tussen een gratis, basis of standaard integratie account, raadpleegt u deze use-case-beschrijvingen:

* **Gratis**: als u wilt proberen om verkennende scenario's, niet productie scenario's. Deze laag is alleen beschikbaar voor open bare regio's in azure, zoals vs-West of Zuidoost-Azië, maar niet voor [Azure China 21vianet](/azure/china/overview-operations) of [Azure Government](../azure-government/documentation-government-welcome.md).

* **Basis**: voor wanneer u alleen bericht verwerking wilt of als een kleine zakelijke partner die een relatie heeft met een handels partner met een grotere bedrijfs entiteit

* **Standaard**: voor wanneer u COMPLEXere B2B-relaties hebt en een groter aantal entiteiten dat u moet beheren

<a name="data-retention"></a>

## <a name="data-retention"></a>Gegevensretentie

Met uitzonde ring van Logic apps die worden uitgevoerd in een Integration service Environment (ISE), worden alle invoer en uitvoer die zijn opgeslagen in de uitvoerings geschiedenis van de logische app, gefactureerd op basis van de [uitvoerings periode](logic-apps-limits-and-config.md#run-duration-retention-limits)van een logische app. Logic apps die in een ISE worden uitgevoerd, zijn geen kosten voor het bewaren van gegevens. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps)voor prijs tarieven.

Om u te helpen bij het bewaken van het opslag verbruik van uw logische app, kunt u het volgende doen:

* Bekijk het aantal opslag eenheden in GB dat maandelijks door uw logische app wordt gebruikt.

* Bekijk de grootten voor de invoer en uitvoer van een specifieke actie in de uitvoerings geschiedenis van de logische app.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Gebruik van logische app-opslag weer geven

1. Zoek en open uw logische app in de Azure Portal.

1. Selecteer in het menu van de logische app, onder **bewaking**, **metrische gegevens**.

1. Selecteer in het rechterdeel venster onder **grafiek titel** in de lijst **metriek** het **facturerings gebruik voor uitvoeringen van opslag verbruik**.

   Met deze metriek krijgt u het aantal verbruiks eenheden voor opslag in GB per maand dat wordt gefactureerd.

   > [!NOTE]
   > Uitvoeringen die minder dan 500 MB in opslag verbruiken, worden mogelijk niet weer gegeven in de weer gave controle, maar worden nog steeds gefactureerd.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>De invoer-en uitvoer grootte van de actie weer geven

1. Zoek en open uw logische app in de Azure Portal.

1. Selecteer **overzicht** in het menu van de logische app.

1. Selecteer in het rechterdeel venster onder **uitvoerings geschiedenis** de uitvoering met de invoer en uitvoer die u wilt controleren.

1. Kies onder **uitvoering van logische app** de optie **Details uitvoeren**.

1. Selecteer de actie die u wilt weer geven in het detail venster van de **logische app-uitvoering** in de tabel acties, waarin de status en duur van elke actie worden vermeld.

1. Zoek in het actie deel venster van de **logische app** de grootten voor de invoer en uitvoer van die actie. Zoek onder koppeling naar **invoer** en **uitvoer** de koppelingen naar deze invoer en uitvoer.

   > [!NOTE]
   > Voor lussen toont alleen de acties op het hoogste niveau de grootte van de invoer en uitvoer. Voor acties binnen geneste lussen tonen invoer en uitvoer een grootte van nul en geen koppelingen.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure Logic Apps](logic-apps-overview.md)
* [Een logische app maken](quickstart-create-first-logic-app-workflow.md)
