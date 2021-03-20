---
title: Voor beelden & algemene scenario's
description: Hier vindt u voor beelden, veelvoorkomende scenario's, zelf studies en scenario's voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 5f7afe044ab4f782f2028598c2c56e75edf95860
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92317382"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Veelvoorkomende scenario's, voor beelden, zelf studies en scenario's voor Azure Logic Apps

[Azure Logic apps](../logic-apps/logic-apps-overview.md) helpt u bij het organiseren en integreren van verschillende services door [honderden kant-en-klare connectors](../connectors/apis-list.md)te bieden, variërend van SQL Server of SAP naar Azure Cognitive Services. De Logic Apps-service is ' serverloos ', dus u hoeft zich geen zorgen te maken over schalen of instanties. U hoeft alleen de werk stroom te definiëren met een trigger en de acties die de werk stroom uitvoert. Het onderliggende platform zorgt voor schaal, Beschik baarheid en prestaties. Logic Apps is vooral handig voor het gebruik van cases en scenario's waarin u acties tussen meerdere systemen en services moet coördineren.

In dit artikel worden veelvoorkomende begin punten, voor beelden en scenario's beschreven, zodat u meer informatie kunt over de mogelijkheden en patronen die Azure Logic Apps ondersteunt.

## <a name="common-starting-points-for-logic-app-workflows"></a>Veelvoorkomende begin punten voor logische app-werk stromen

Elke logische app begint met een [*trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts)en slechts één trigger, waarmee de werk stroom van de logische app wordt gestart en de gegevens worden door gegeven als onderdeel van deze trigger. Sommige connectors bieden triggers die in de volgende typen voor komen:

* *Polling triggers*: controleert regel matig een service-eind punt op nieuwe gegevens. Wanneer er nieuwe gegevens bestaan, maakt en voert de trigger een nieuwe werk stroom instantie met de gegevens als invoer.

* *Push triggers*: luistert naar gegevens op een service-eind punt en wacht totdat er een bepaalde gebeurtenis plaatsvindt. Wanneer de gebeurtenis plaatsvindt, wordt de trigger onmiddellijk geactiveerd, wordt er een nieuwe werk stroom instantie gemaakt en uitgevoerd die beschik bare gegevens als invoer gebruikt.

Hier volgen enkele voor beelden van veelgebruikte triggers:

* *Polling* triggers:

  * Met de trigger voor [ **terugkeer patroon**](../connectors/connectors-native-recurrence.md) kunt u de begin datum en-tijd en het terugkeer patroon instellen voor het activeren van uw logische app. U kunt bijvoorbeeld de dagen van de week en tijdstippen van de dag selecteren voor het activeren van uw logische app. Raadpleeg de volgende onderwerpen voor meer informatie:<p>

    * [Terugkerende en geautomatiseerde taken, processen en werkstromen plannen en uitvoeren met Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Zelf studie: automatische, op planning gebaseerde terugkerende werk stromen maken met behulp van Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * De trigger **Wanneer een e-mail wordt ontvangen** , laat uw logische app controleren op nieuwe e-mail van elke e-mail provider die wordt ondersteund door Logic apps, bijvoorbeeld [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/), enzovoort.

    > [!IMPORTANT]
    > Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

    Raadpleeg de volgende onderwerpen voor meer informatie:<p>

    * [Zelfstudie: Geautomatiseerde op goedkeuring gebaseerde workflows maken met behulp van Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Zelfstudie: Automatiseer taken om e-mails te verwerken met behulp van Azure Logic Apps, Azure Functions en Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Met de [ **http-** trigger](../connectors/connectors-native-http.md) kunt u een service-eind punt aanroepen via http of https. Zie voor meer informatie [werk stromen aanroepen, activeren of nesten met behulp van HTTP-eind punten](../logic-apps/logic-apps-http-endpoint.md).

* *Push* triggers:

  * De [trigger voor **aanvragen**](../connectors/connectors-native-reqres.md) kan binnenkomende HTTPS-aanvragen ontvangen.

  * De [ **http-webhook** trigger](../connectors/connectors-native-webhook.md) meldt zich aan bij een service-eind punt door een *call back-URL* bij die service te registreren. Op die manier kan de service alleen een melding sturen wanneer de opgegeven gebeurtenis plaatsvindt, zodat de trigger de service niet hoeft te pollen.

Nadat de opgegeven gebeurtenis plaatsvindt, wordt de trigger geactiveerd, waarmee een nieuw werk stroom exemplaar van de logische app wordt gemaakt en de acties in de werk stroom worden uitgevoerd. U kunt toegang krijgen tot alle gegevens van de trigger in de werk stroom. Met de Twitter **voor een nieuwe Tweet** -trigger wordt de Tweet-inhoud bijvoorbeeld door gegeven aan de uitvoering van de logische app. Voer de volgende Snelstartgids uit om aan de slag te gaan met Azure Logic Apps:

* [Snelstartgids: uw eerste geautomatiseerde werk stroom maken met behulp van Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Snelstartgids: geautomatiseerde taken, processen en werk stromen maken met behulp van Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Snelstartgids: geautomatiseerde werk stromen voor logische apps maken en beheren met Visual Studio code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Mogelijkheden voor controle stroom en fout afhandeling

Logic apps bevatten uitgebreide mogelijkheden voor geavanceerde controle stromen, zoals voor waarden, switches, lussen en bereiken. Voor een robuuste oplossing kunt u ook fout-en uitzonderings afhandeling in uw werk stromen implementeren.

* Verschillende acties uitvoeren op basis van [voorwaardelijke instructies](../logic-apps/logic-apps-control-flow-conditional-statement.md) en [Switch instructies](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Herhaal de stappen of verwerk items in matrices en verzamelingen met lussen](../logic-apps/logic-apps-control-flow-loops.md)
* [Acties groeperen in combi natie met scopes](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Fout-en uitzonderings afhandeling toevoegen aan een werk stroom](../logic-apps/logic-apps-exception-handling.md)
* [Use-case: hoe een gezondheids zorg die gebruikmaakt van de verwerking van logische-app-uitzonde ringen voor HL7 FHIR-werk stromen](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Aangepaste Api's en connectors maken

Voor systemen en services die geen connectors hebben gepubliceerd, kunt u ook logische apps uitbreiden.

* [Aangepaste Api's maken om van Azure Logic Apps aan te roepen](../logic-apps/logic-apps-create-api-app.md)
* [Regel matig controleren op nieuwe gegevens of gebeurtenissen met behulp van het polling trigger-patroon](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Een ogen blik geduld en op nieuwe gegevens of gebeurtenissen Luis teren met het trigger patroon webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Langdurige taken uitvoeren met behulp van het patroon polling-actie](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Langlopende taken uitvoeren met het actie patroon webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Aangepaste connectors in Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Business-to-Business (B2B)-oplossingen bouwen

Voor oplossingen voor bedrijfs integratie en naadloze communicatie tussen organisaties kunt u geautomatiseerde schaal bare werk stromen voor deze scenario's maken door de Enterprise Integration Pack (EIP) met Azure Logic Apps te gebruiken. Hoewel organisaties verschillende protocollen en indelingen gebruiken, kunnen ze elektronisch berichten uitwisselen. De EIP transformeert verschillende indelingen naar een indeling die de systemen van uw organisatie kunnen verwerken en ondersteunt industrie standaard protocollen, waaronder AS2, X12, EDIFACT en RosettaNet. Als u deze oplossingen wilt maken, maakt u een integratie account. Dit is een afzonderlijke Azure-resource die een veilige, schaal bare en beheersbare container biedt voor de artefacten die u definieert en gebruikt met uw logische app-werk stromen. Artefacten zijn bijvoorbeeld handels partners, overeenkomsten, kaarten, schema's, certificaten en batch configuraties.

* [Overzicht: zakelijke oplossingen voor B2B-integratie met Azure Logic Apps en Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Integratieaccounts voor B2B-ondernemingsintegraties maken en beheren met behulp van Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Toegang tot Azure Virtual Network-Resources

Soms hebben uw Logic apps en integratie accounts toegang nodig tot beveiligde bronnen, zoals virtuele machines (Vm's) en andere systemen of services die zich in een virtueel Azure-netwerk bevinden. Als u deze toegang wilt instellen, kunt u een ISE (Integration service Environment) maken waarin u logische apps kunt bouwen en uitvoeren. Een ISE is een privé-en geïsoleerd exemplaar van de Logic Apps service dat gebruikmaakt van speciale resources zoals opslag, en die afzonderlijk van de open bare, ' wereld wijde ' Logic Apps service voor meerdere tenants wordt uitgevoerd. Het scheiden van uw geïsoleerde privé-exemplaar en het open bare Global-exemplaar verminderen ook de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps, ook wel bekend als het effect "ruiserende neighbors".

* [Overzicht: toegang tot resources van het virtuele Azure-netwerk vanaf Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Logic Apps implementeren, beheren en bewaken

U kunt Logic apps volledig ontwikkelen en implementeren met Visual Studio, Azure DevOps of een ander broncode beheer en hulpprogram ma's voor automatisch bouwen. Voor de ondersteuning van de implementatie van werk stromen en afhankelijke verbindingen in een resource sjabloon maakt Logic apps gebruik van Azure resource-implementatie sjablonen. Visual Studio-hulpprogram ma's genereren deze sjablonen automatisch, die u in broncode beheer kunt inchecken voor het maken van versies. Voor meldings-en Diagnostische logboeken voor de uitvoerings status van de werk stroom biedt Azure Logic Apps ook bewaking en waarschuwingen.

### <a name="deploy"></a>Implementeren

* [Snelstartgids: geautomatiseerde taken, processen en werk stromen maken met behulp van Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Overzicht: implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Resource Manager-sjablonen maken voor het automatiseren van de implementatie voor Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Resource Manager-sjablonen inzetten voor Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Voor beeld: verbinding maken met Azure Service Bus wacht rijen van Azure Logic Apps en implementeren met Azure-pijp lijnen in azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voor beeld: verbinding maken met Azure Storage accounts vanuit Azure Logic Apps en implementeren met Azure-pijp lijnen in azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voor beeld: een actie van een functie-app instellen voor Azure Logic Apps en implementeren met Azure-pijp lijnen in azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voor beeld: verbinding maken met een integratie account van Azure Logic Apps en implementeren met Azure-pijp lijnen in azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Voor beeld: Azure-pijp lijnen organiseren met behulp van Azure Logic Apps](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Beheren

* [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Integratie accounts voor B2B-ondernemings integraties maken en beheren](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Integration service Environment (ISE) beheren in Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitor

* [Uitvoeringsstatus bewaken, triggergeschiedenis controleren, en waarschuwingen instellen voor Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Azure Monitor logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Azure Monitor-logboeken instellen en diagnostische gegevens verzamelen voor B2B-berichten in Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Query's weer geven en maken voor het controleren en bijhouden van Azure Monitor logboeken voor Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Inhouds typen, conversies en trans formaties verwerken

U kunt meerdere inhouds typen openen, converteren en transformeren met behulp van de vele functies in de [werk stroom definitie taal](./logic-apps-workflow-definition-language.md)van Azure Logic apps. U kunt bijvoorbeeld converteren tussen een teken reeks, JSON en XML met de- `@json()` en- `@xml()` werk stroom expressies. De Logic Apps-Engine behoudt inhouds typen voor de ondersteuning van inhouds overdracht met een verlies vrije manier tussen services.

* [Inhouds typen verwerken in azure Logic apps](../logic-apps/logic-apps-content-type.md), zoals `application/` , `application/octet-stream` en `multipart/formdata`
* [Naslag Gids voor het gebruik van functies in expressies voor Azure Logic Apps en energie automatisering](../logic-apps/workflow-definition-language-functions-reference.md)
* [Schema voor werk stroom definitie taal voor Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Andere integraties en mogelijkheden

Azure Logic Apps kan worden geïntegreerd met veel services, zoals Azure Functions, Azure API Management, Azure App Service en aangepaste HTTP-eind punten, bijvoorbeeld REST en SOAP.

* [Azure Functions aanroepen vanuit Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Zelf studie: logische apps aanroepen of activeren met behulp van Azure Functions en Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Zelf studie: een streaming Customer Insights-dash board maken met Azure Logic Apps en Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Zelf studie: een functie maken die kan worden geïntegreerd met Azure Logic Apps en Azure Cognitive Services voor het analyseren van Twitter post sentiment](../azure-functions/functions-twitter-email.md)
* [Zelf studie: een sociaal dash board met AI-energie maken met behulp van Power BI en Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Zelfstudie: Wijzigingen in virtuele machines bewaken met Azure Event Grid en Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Zelf studie: externe bewaking van IoT en meldingen met Azure Logic Apps verbinding maken met uw IoT hub en Postvak](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: SOAP-services aanroepen met behulp van Azure Logic Apps](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>End-to-end scenario 's

* [Technisch document: end-to-end case management-integratie met Azure-Services, zoals Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Verhalen van klanten

Lees hoe Azure Logic Apps, samen met andere Azure-Services en micro soft-producten, de flexibiliteit van [deze bedrijven](https://aka.ms/logic-apps-customer-stories) kan verbeteren en zich kan richten op hun kern bedrijven door complexe processen te vereenvoudigen, te organiseren, te automatiseren en te beheren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [connectors voor Logic apps](../connectors/apis-list.md)
* Meer informatie over [B2B Enter prise Integration-scenario's met Azure Logic apps](../logic-apps/logic-apps-enterprise-integration-overview.md)