---
title: Connectors voor Azure Logic Apps
description: Overzicht van connectors voor het bouwen van geautomatiseerde werkstromen met Azure Logic Apps. Meer informatie over hoe verschillende typen connectors, triggers en acties werken.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771349"
---
# <a name="connectors-for-azure-logic-apps"></a>Connectors voor Azure Logic Apps

In Azure Logic Apps *helpen connectors* u snel toegang te krijgen tot gebeurtenissen, gegevens en acties van andere apps, services, systemen, protocollen en platforms. U kunt deze taken vaak uitvoeren zonder aanvullende code. U kunt ook connectors gebruiken om Logic Apps werkstromen te bouwen die gebruikmaken van deze informatie in cloudomgevingen, on-premises of hybride omgevingen.

Er zijn honderden connectors beschikbaar voor Logic Apps. Als gevolg hiervan richt deze documentatie zich op enkele van de populairste en meest gebruikte connectors voor Logic Apps. Zie Documentatie over connectors voor volledige informatie over [connectors](/connectors)in Logic Apps, Microsoft Power Automate en Microsoft Power Apps. 

Zie prijsmodel voor Logic Apps [informatie](../logic-apps/logic-apps-pricing.md)over prijzen en Logic Apps [prijsinformatie.](https://azure.microsoft.com/pricing/details/logic-apps/)

Als u uw logische app wilt integreren met een service of API die geen connector heeft, kunt u de service aanroepen via een protocol, zoals HTTP, of een aangepaste [connector maken.](#custom-apis-and-connectors)

## <a name="what-are-connectors"></a>Wat zijn connectors?

In Azure Logic Apps bieden connectors *triggers* en acties die u gebruikt om taken uit te voeren in de werkstroom van uw logische app.  Elke trigger en actie heeft eigenschappen die u kunt configureren. Voor sommige triggers en acties moet u [verbindingen](#connection-configuration) maken en configureren, zodat uw werkstroom toegang heeft tot een specifieke service of een specifiek systeem.

### <a name="triggers"></a>Triggers

Een *trigger* is altijd de eerste stap in een werkstroom, waarmee de gebeurtenis wordt opgegeven waarmee de werkstroom wordt gestart. Er zijn meerdere soorten triggers:

- *Poll-triggers* controleren regelmatig een specifieke service of een specifiek systeem volgens een opgegeven planning om te controleren op nieuwe gegevens of een specifieke gebeurtenis. Als er nieuwe gegevens beschikbaar zijn of als de specifieke gebeurtenis zich voordeed, maken en voeren deze triggers een nieuw exemplaar van uw werkstroom uit. Deze nieuwe instantie kan vervolgens de gegevens gebruiken die als invoer worden doorgegeven.
- *Push-triggers* luisteren naar nieuwe gegevens of naar een gebeurtenis, zonder polling. Wanneer er nieuwe gegevens beschikbaar zijn of wanneer de gebeurtenis zich voordeed, maken en voeren deze triggers een nieuw exemplaar van uw logische app uit. Dit nieuwe exemplaar kan vervolgens de gegevens gebruiken die als invoer worden doorgegeven.

U kunt bijvoorbeeld een werkstroom maken die iets doet wanneer een bestand wordt geüpload naar uw FTP-server. U kunt de FTP-connectortrigger met de **naam Wanneer een** bestand wordt toegevoegd of gewijzigd, toevoegen als eerste stap in uw werkstroom. Vervolgens kunt u een planning opgeven om regelmatig te controleren op uploadgebeurtenissen.

Een trigger geeft ook alle invoer en andere vereiste gegevens door aan uw werkstroom, waar latere acties naar die gegevens in de werkstroom kunnen verwijzen en deze kunnen gebruiken. U kunt bijvoorbeeld de Office 365 Outlook-trigger gebruiken met de naam **Wanneer er een nieuwe e-mail binnenkomt.** U kunt deze trigger configureren om de inhoud van elk nieuw e-mailbericht door te geven, zoals de afzender, de onderwerpregel, de body, bijlagen, en meer. Vervolgens kunt u die informatie in uw logische app verwerken met behulp van acties.

### <a name="actions"></a>Acties

Een *actie* is een bewerking die de trigger volgt en een soort taak in uw werkstroom uitvoert. U kunt meerdere acties gebruiken in uw logische app. U kunt bijvoorbeeld een SQL-trigger hebben die nieuwe klantgegevens in een SQL database. Uw werkstroom kan een eerste SQL-actie bevatten die de klantgegevens opneemt, gevolgd door een andere actie die niet noodzakelijkerwijs een SQL-actie is, die de gegevens verwerkt.

## <a name="connector-categories"></a>Connectorcategorieën

In Logic Apps zijn er meestal ingebouwde of beheerde connectorversies van triggers en acties. In beide versies zijn een klein aantal triggers en acties beschikbaar. De specifieke versies zijn afhankelijk van het maken van een logische app met meerdere tenants of van een logische app met één tenant, die momenteel alleen beschikbaar is in [Logic Apps Preview.](../logic-apps/logic-apps-overview-preview.md)

[Ingebouwde triggers en](built-in.md) acties worden standaard uitgevoerd op de Logic Apps-runtime, hoeven geen verbindingen te maken en voeren dit soort taken uit:

- [Voer code uit in uw werkstromen.](built-in.md#run-code-from-workflows)
- [Organiseer en beheer uw gegevens.](built-in.md#control-workflow)
- [Gegevens beheren of bewerken.](built-in.md#manage-or-manipulate-data)

[Beheerde connectors](managed.md) worden geïmplementeerd, gehost en beheerd door Microsoft. Deze connectors bieden triggers en acties voor cloudservices, on-premises systemen of beide. Deze omvatten:

- [On-premises connectors die](managed.md#on-premises-connectors) u helpen toegang te krijgen tot gegevens en resources in on-premises systemen.
- [Enterprise-connectors,](managed.md#enterprise-connectors)versies van sommige Logic Apps connectors die toegang bieden tot bedrijfssystemen.
- [Integratieaccountconnectoren die](managed.md#integration-account-connectors)ondersteuning bieden voor B2B-communicatiescenario's (Business-to-Business).
- [Integratieserviceomgeving (ISE)-connectors](managed.md#ise-connectors) die een kleine groep beheerde connectors zijn die alleen [beschikbaar zijn voor ISE's.](#ise-and-connectors)

## <a name="connection-configuration"></a>Verbindingsconfiguratie

Voor de meeste connectors  moet u eerst een verbinding maken met de doelservice of het doelsysteem voordat u de triggers of acties in uw logische app kunt gebruiken. Als u een verbinding wilt maken, moet u uw identiteit verifiëren met accountreferenties en soms andere verbindingsgegevens. Voordat uw werkstroom bijvoorbeeld toegang kan krijgen tot uw e-mailaccount van Office 365 Outlook in een logische app, moet u een verbinding met dat account autoreren.

Voor connectors die gebruikmaken van Azure Active Directory (Azure AD) OAuth, zoals Office 365, Salesforce of GitHub, moet u zich aanmelden bij de service waar uw toegangstoken [is](../security/fundamentals/encryption-overview.md) versleuteld en veilig is opgeslagen in een Azure-geheim. Andere connectors, zoals FTP en SQL, vereisen een verbinding met configuratiegegevens, zoals het serveradres, de gebruikersnaam en het wachtwoord. Deze verbindingsconfiguratiegegevens worden [ook versleuteld en veilig opgeslagen in Azure](../security/fundamentals/encryption-overview.md).

Tot stand gebrachte verbindingen hebben toegang tot de doelservice of het doelsysteem zolang dat is mogelijk met die service of het systeem. Voor services die gebruikmaken van Azure AD OAuth-verbindingen, zoals Office 365 en Dynamics, Logic Apps toegangstokens voor onbepaalde tijd vernieuwd. Andere services hebben mogelijk limieten voor hoe lang de Logic Apps-service een token kan gebruiken zonder te vernieuwen. Over het algemeen maken sommige acties, zoals het wijzigen van uw wachtwoord, alle toegangstokens ongeldig.

Hoewel u verbindingen maakt vanuit een werkstroom, zijn verbindingen afzonderlijke Azure-resources met hun eigen resourcedefinities. Als u deze verbindingsresourcedefinities wilt bekijken, [downloadt](../logic-apps/manage-logic-apps-with-visual-studio.md)u uw logische app vanuit Azure naar Visual Studio . Deze methode is de eenvoudigste manier om een geldige, geparameteriseerde sjabloon voor logische apps te maken die voornamelijk gereed is voor implementatie.

> [!TIP]
> Als uw organisatie u geen toegang geeft tot specifieke resources via [](../logic-apps/block-connections-connectors.md) Logic Apps-connectors, kunt u de mogelijkheid blokkeren om dergelijke verbindingen te maken met behulp [van Azure Policy](../governance/policy/overview.md).

## <a name="recurrence-behavior"></a>Terugkeerpatroon

Terugkerende ingebouwde triggers, zoals de trigger [Terugkeerpatroon,](../connectors/connectors-native-recurrence.md)worden native uitgevoerd in Azure Logic Apps en verschillen van terugkerende triggers op basis van verbindingen, zoals de trigger voor de Office 365 Outlook-connector, waar u eerst een verbinding moet maken.

Als een terugkeerpatroon voor beide soorten triggers geen specifieke begindatum en -tijd opgeeft, wordt het eerste terugkeerpatroon onmiddellijk uitgevoerd wanneer u de logische app opgeeft of implementeert, ondanks de instelling van het terugkeerpatroon van de trigger. Om dit gedrag te voorkomen, geeft u een begindatum en -tijd op voor wanneer u het eerste terugkeerpatroon wilt uitvoeren.

### <a name="recurrence-for-built-in-triggers"></a>Terugkeerpatroon voor ingebouwde triggers

Terugkerende ingebouwde triggers volgen de planning die u hebt ingesteld, met inbegrip van een opgegeven tijdzone. Als een terugkeerpatroon echter geen andere geavanceerde planningsopties opgeeft, zoals specifieke tijden voor het uitvoeren van toekomstige terugkeerpatroon, zijn deze terugkeerpatroon gebaseerd op de laatste uitvoering van de trigger. Als gevolg hiervan kunnen de begintijden voor deze terugkeerpatroon worden beïnvloed door factoren zoals latentie tijdens opslagoproepen. Zie de sectie terugkeerproblemen voor hulp [bij het oplossen van](#recurrence-issues) problemen.

### <a name="recurrence-for-connection-based-triggers"></a>Terugkeerpatroon voor triggers op basis van een verbinding

In terugkerende triggers op basis van verbindingen, zoals Office 365 Outlook, is de planning niet het enige stuurprogramma dat de uitvoering bepaalt. De tijdzone bepaalt alleen de initiële begintijd. Volgende uitvoeringen zijn afhankelijk van het terugkeerschema, de laatste uitvoering van de trigger en andere factoren die ertoe kunnen leiden dat uitvoeringstijden veranderen of onverwacht gedrag produceren. Deze omvatten:

- Of de trigger toegang heeft tot een server met meer gegevens, die de trigger onmiddellijk probeert op te halen.
- Fouten of nieuwe pogingen die de trigger veroorzaakt.
- Latentie tijdens opslagoproepen.
- Het opgegeven schema wordt niet onderhouden wanneer zomer- en wintertijd (DST) wordt gestart en beëindigd.
- Andere factoren die van invloed kunnen zijn op de volgende run time.

Zie de sectie Terugkeerpatroonproblemen voor hulp [bij het oplossen](#recurrence-issues) van problemen. 

### <a name="recurrence-issues"></a>Problemen met terugkeerpatroon

Probeer de volgende oplossingen om ervoor te zorgen dat uw werkstroom wordt uitgevoerd op de opgegeven begintijd en geen terugkeerpatroon mist, met name wanneer de frequentie in dagen of langer is.

Wanneer DST van kracht wordt, past u het terugkeerpatroon handmatig aan zodat uw werkstroom op het verwachte tijdstip blijft worden uitgevoerd. Anders verschuift de begintijd één uur vooruit wanneer DST wordt gestart en één uur achterwaarts wanneer DST eindigt. Zie Terugkeerpatroon voor zomer- en standaardtijd voor meer informatie [en voorbeelden.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)

Als u een trigger **Terugkeerpatroon** gebruikt, geeft u een tijdzone, een begindatum en -tijd op. Daarnaast configureert u specifieke tijden voor het  uitvoeren van volgende terugkeerpatroon in de  eigenschappen Deze uren en **Op** deze minuten, die alleen beschikbaar zijn voor de frequenties Dag **en** Week. Enige tijdvensters kunnen echter nog steeds problemen veroorzaken wanneer de tijd verschuift. 

Overweeg het gebruik van [ **een sliding window-trigger**](../connectors/connectors-native-sliding-window.md) in **plaats van een terugkeertrigger** om gemiste terugkeerpatroon te voorkomen.

## <a name="custom-apis-and-connectors"></a>Aangepaste API's en connectors

Als u API's wilt aanroepen die aangepaste code uitvoeren of die niet beschikbaar zijn als connectors, kunt u het Logic Apps-platform uitbreiden door aangepaste code API Apps [.](../logic-apps/logic-apps-create-api-app.md) 

U kunt ook [aangepaste connectors maken](../logic-apps/custom-connector-overview.md) voor alle REST- of SOAP-API's, waardoor deze API's beschikbaar zijn voor elke logische app in uw Azure-abonnement. 

Als u aangepaste API Apps connectors openbaar wilt maken voor iedereen die u in Azure kunt gebruiken, kunt u [connectors indienen voor Microsoft-certificering.](/connectors/custom-connectors/submit-certification)

## <a name="ise-and-connectors"></a>ISE en connectors

Voor werkstromen die directe toegang tot resources in een virtueel Azure-netwerk nodig hebben, kunt u een [speciale ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken waar u uw werkstromen op toegewezen resources kunt bouwen, implementeren en uitvoeren. Zie Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps voor meer informatie over het maken [van ISE'Azure Logic Apps.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

Aangepaste connectors die in een ISE zijn gemaakt, werken niet met de on-premises gegevensgateway. Deze connectors hebben echter rechtstreeks toegang tot on-premises gegevensbronnen die zijn verbonden met een virtueel Azure-netwerk dat als host voor de ISE wordt gebruikt. Logische apps in een ISE hebben de gegevensgateway dus waarschijnlijk niet nodig tijdens de communicatie met deze resources. Als u aangepaste connectors hebt die u buiten een ISE hebt gemaakt waarvoor de on-premises gegevensgateway is vereist, kunnen logische apps in een ISE deze connectors gebruiken.

Wanneer u in de Logic Apps Designer door de connectors bladert die u wilt gebruiken voor logische apps in een ISE, wordt er een **CORE-label** weergegeven op ingebouwde triggers en acties, terwijl het **ISE-label** op sommige connectors wordt weergegeven.

:::row:::
    :::column:::
        ![Voorbeeld van CORE-connector](./media/apis-list/example-core-connector.png)
        \
        \
        **Core**
        \
        \
        Ingebouwde triggers en acties met dit label worden uitgevoerd in dezelfde ISE als uw logische apps.
    :::column-end:::
    :::column:::
        ![Voorbeeld van ISE-connector](./media/apis-list/example-ise-connector.png)
        \
        \
        **Ise**
        \
        \
        Beheerde connectors met dit label worden uitgevoerd in dezelfde ISE als uw logische apps. Als u een on-premises systeem hebt dat is verbonden met een virtueel Azure-netwerk, geeft een ISE uw logische apps rechtstreeks toegang tot dat systeem zonder de [on-premises gegevensgateway](../logic-apps/logic-apps-gateway-connection.md). In plaats daarvan kunt u de **ISE-connector** van dat systeem gebruiken, indien beschikbaar, een HTTP-actie of een [aangepaste connector.](connectors-overview.md#custom-apis-and-connectors) Gebruik on-premises gegevensgateway voor on-premises systemen die geen **ISE-connectors** hebben. Zie ISE-connectors om de beschikbare [ISE-connectors te bekijken.](#ise-and-connectors)
    :::column-end:::
    :::column:::
        ![Voorbeeld van een multi-tenant-connector](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Geen label     \
        \
        Alle andere connectors zonder het **core-** of **ISE-label,** die u kunt blijven gebruiken, worden uitgevoerd in de globale, multiten tenant Logic Apps service.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Bekende problemen

Hier volgen bekende problemen voor Logic Apps connectors.

#### <a name="error-badgateway-client-request-id-guid"></a>Fout: BadGateway. Clientaanvraag-id: {GUID}

Deze fout wordt veroorzaakt door het bijwerken van de tags in een logische app waarbij een of meer verbindingen geen Azure Active Directory (Azure AD) OAuth-verificatie ondersteunen, zoals SFTP ad SQL, die verbindingen verbreken. Om dit gedrag te voorkomen, moet u deze tags niet bijwerken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aangepaste API's maken die u kunt aanroepen vanuit Logic Apps](/logic-apps/logic-apps-create-api-app)
