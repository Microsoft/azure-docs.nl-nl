---
title: Ingebouwde triggers en acties voor Azure Logic Apps
description: Gebruik ingebouwde triggers en acties om geautomatiseerde werkstromen te maken die apps, gegevens, services en systemen integreren, werkstromen beheren en gegevens beheren met behulp van Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796729"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Ingebouwde triggers en acties voor Logic Apps


[Ingebouwde triggers](apis-list.md) en acties bieden u manieren om de planning en structuur van uw werkstroom te [beheren,](#control-workflow)uw eigen [code](#run-code-from-workflows)uit te [voeren,](#manage-or-manipulate-data)gegevens te beheren of bewerken en andere taken in uw werkstromen uit te voeren. Afgezien [van beheerde connectors](managed.md)zijn veel ingebouwde bewerkingen niet gekoppeld aan een specifieke service, systeem of protocol. U kunt bijvoorbeeld bijna elke werkstroom volgens een schema starten met behulp van de trigger Terugkeerpatroon. U kunt uw werkstroom ook laten wachten tot deze wordt aangeroepen met behulp van de aanvraagtrigger. Alle ingebouwde bewerkingen worden in de Logic Apps uitgevoerd en voor de meeste hoeft u geen verbinding te maken voordat u deze gebruikt. 

Logic Apps biedt ook ingebouwde bewerkingen voor een kleiner aantal services, systemen en protocollen, zoals Azure Service Bus, Azure Functions, SQL, AS2, en meer. Het aantal en het bereik variëren afhankelijk van of u een logische app met meerdere tenants of een logische app met één tenant maakt. In een paar gevallen zijn zowel een ingebouwde versie als een versie van een beheerde connector beschikbaar. In de meeste gevallen biedt de ingebouwde versie betere prestaties, mogelijkheden, prijzen, en meer. Als u bijvoorbeeld B2B-berichten wilt uitwisselen met behulp van het [AS2-protocol,](../logic-apps/logic-apps-enterprise-integration-as2.md)selecteert u de ingebouwde versie, tenzij u traceringsmogelijkheden nodig hebt, die alleen beschikbaar zijn in de (afgeschafte) beheerde connectorversie.

In de volgende lijst worden slechts enkele taken beschreven die u met [ingebouwde triggers](#understand-triggers-and-actions)en acties kunt uitvoeren:

- Werkstromen uitvoeren met aangepaste en geavanceerde planningen. Voor meer informatie over planning, bekijkt u de sectie [terugkeergedrag in het](apis-list.md#recurrence-behavior)overzicht Logic Apps connector .
- Organiseer en beheer de structuur van uw werkstroom, bijvoorbeeld met behulp van lussen en voorwaarden.
- Werken met variabelen, datums, gegevensbewerkingen, inhoudstransformaties en batchbewerkingen.
- Communiceren met andere eindpunten met behulp van HTTP-triggers en -acties.
- Aanvragen ontvangen en hierop reageren.
- Roep uw eigen functies (Azure Functions), web-apps (Azure-app Services), API's (Azure API Management) aan, andere Logic Apps-werkstromen die aanvragen kunnen ontvangen, en meer.

## <a name="understand-triggers-and-actions"></a>Triggers en acties begrijpen

Logic Apps biedt de volgende ingebouwde triggers en acties:

:::row:::
    :::column:::
        [![Schemapictogram in Logic Apps][schedule-icon]][schedule-doc]
        \
        \
        [**Schema**][schedule-doc]
        \
        \
        [**Terugkeerpatroon:**][schedule-recurrence-doc]Activeer een werkstroom op basis van het opgegeven terugkeerpatroon.
        \
        \
        [**Sliding Window:**][schedule-sliding-window-doc]Activeer een werkstroom die gegevens in doorlopende segmenten moet verwerken.
        \
        \
        [**Vertraging:**][schedule-delay-doc]uw werkstroom onderbreken voor de opgegeven duur.
        \
        \
        [**Vertragen tot:**][schedule-delay-until-doc]uw werkstroom onderbreken tot de opgegeven datum en tijd.
    :::column-end:::
    :::column:::
        [![Batch-pictogram in Logic Apps][batch-icon]][batch-doc]
        \
        \
        [**Batch**][batch-doc]
        \
        \
        [**Batchberichten:**][batch-doc]Activeer een werkstroom die berichten in batches verwerkt.
        \
        \
        [**Berichten verzenden naar batch:**][batch-doc]roep een bestaande werkstroom aan die momenteel begint met een **trigger voor Batch-berichten.**
    :::column-end:::
    :::column:::
        [![HTTP-pictogram in Logic Apps][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Roep een HTTP- of HTTPS-eindpunt aan met behulp van de HTTP-trigger of actie. 
        \
        \
        U kunt ook deze andere ingebouwde HTTP-triggers en -acties gebruiken: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Aanvraagpictogram][http-request-icon]][http-request-doc]
        \
        \
        [**Verzoek**][http-request-doc]
        \
        \
        [**Wanneer een HTTP-aanvraag wordt ontvangen:**][http-request-doc]wacht op een aanvraag van een andere werkstroom, app of service. Deze trigger maakt uw werkstroom aanroepbaar zonder dat deze volgens een schema moet worden gecontroleerd of gepeild.
        \
        \
        [**Antwoord:**][http-request-doc]Reageer op een aanvraag die is ontvangen door de trigger Wanneer **een HTTP-aanvraag wordt** ontvangen in dezelfde werkstroom.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Pictogram API Management Azure-Logic Apps][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        Roep uw eigen triggers en acties aan in API's die u definieert, beheert en publiceert met behulp van [Azure API Management](../api-management/api-management-key-concepts.md). <p><p>**Opmerking:** Niet ondersteund bij gebruik [van de verbruikslaag voor API Management](../api-management/api-management-features.md).
    :::column-end:::
    :::column:::
        [![Azure-app Services-pictogram in Logic Apps][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure-app Services**][azure-app-services-doc]
        \
        \
        Roep apps aan die u maakt en host [op Azure App Service,](../app-service/overview.md)API Apps en Web Apps.
        \
        \
        Wanneer Swagger is opgenomen, worden de triggers en acties die door deze apps zijn gedefinieerd, weergegeven als andere eersteklas triggers en acties in Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Azure Logic Apps pictogram in Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Roep andere werkstromen aan die beginnen met de aanvraagtrigger met de **naam Wanneer een HTTP-aanvraag wordt ontvangen.**
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Code uitvoeren vanuit werkstromen

Logic Apps biedt ingebouwde acties voor het uitvoeren van uw eigen code in uw werkstroom:

:::row:::
    :::column:::
        [![Azure Functions pictogram in Logic Apps][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure-functies**][azure-functions-doc]
        \
        \
        Roep [door Azure gehoste functies aan](../azure-functions/functions-overview.md) om uw eigen *codefragmenten* (C# of Node.js) in uw werkstroom uit te voeren.
    :::column-end:::
    :::column:::
        [![Pictogram inlinecode in Logic Apps][inline-code-icon]][inline-code-doc]
        \
        \
        [**Inlinecode**][inline-code-doc]
        \
        \
        [**JavaScript-code uitvoeren:**][inline-code-doc]voeg uw eigen inline JavaScript-codefragmenten toe en voer *deze uit* in uw werkstroom.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Werkstroom beheren

Logic Apps biedt ingebouwde acties voor het structureren en beheren van de acties in uw werkstroom:

:::row:::
    :::column:::
        [![Actiepictogram Voorwaarde in Logic Apps][condition-icon]][condition-doc]
        \
        \
        [**Voorwaarde**][condition-doc]
        \
        \
        Evalueer een voorwaarde en voer verschillende acties uit op basis van of de voorwaarde waar of onwaar is.
    :::column-end:::
    :::column:::
        [![Voor het pictogram Elke actie in Logic Apps][for-each-icon]][for-each-doc]
        \
        \
        [**Voor elke**][for-each-doc]
        \
        \
        Voer dezelfde acties uit voor elk item in een matrix.
    :::column-end:::
    :::column:::
        [![Pictogram bereikactie in Logic Apps][scope-icon]][scope-doc]
        \
        \
        [**Naam**][scope-doc]
        \
        \
        Groeper acties in *bereiken*, die hun eigen status krijgen nadat de acties in het bereik zijn uitgevoerd.
    :::column-end:::
    :::column:::
        [![Actiepictogram wisselen in Logic Apps][switch-icon]][switch-doc]
        \
        \
        [**Schakelen**][switch-doc]
        \
        \
        Groeper acties *in cases*, waaraan unieke waarden worden toegewezen, met uitzondering van de standaardcase. Voer alleen die case uit waarvan de toegewezen waarde overeenkomt met het resultaat van een expressie, object of token. Als er geen overeenkomsten bestaan, moet u de standaardcase uitvoeren.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Pictogram actie beëindigen in Logic Apps][terminate-icon]][terminate-doc]
        \
        \
        [**Beëindigen**][terminate-doc]
        \
        \
        Stop een actief actieve werkstroom voor logische apps. 
    :::column-end:::
    :::column:::
        [![Actiepictogram Until in Logic Apps][until-icon]][until-doc]
        \
        \
        [**Tot**][until-doc]
        \
        \
        Herhaal acties totdat de opgegeven voorwaarde waar is of een status is gewijzigd.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Gegevens beheren of bewerken

Logic Apps biedt ingebouwde acties voor het werken met gegevensuitvoer en hun indelingen:

:::row:::
    :::column:::
        [![Het actiepictogram Gegevensbewerkingen in Logic Apps][data-operations-icon]][data-operations-doc]
        \
        \
        [**Gegevensbewerkingen**][data-operations-doc]
        \
        \
        Bewerkingen uitvoeren met gegevens. 
        \
        \
        **Opstellen:** maak één uitvoer op basis van meerdere invoertypen met verschillende typen. 
        \
        \
        **CSV-tabel maken:** maak een CSV-tabel (door komma's gescheiden waarden) van een matrix met JSON-objecten. 
        \
        \
        **HTML-tabel maken:** maak een HTML-tabel op basis van een matrix met JSON-objecten. 
        \
        \
        **Matrix filteren:** maak een matrix op basis van items in een andere matrix die voldoen aan uw criteria. 
        \
        \
        **Join:** maak een tekenreeks van alle items in een matrix en scheid deze items van elkaar met het opgegeven scheidingsteken. 
        \
        \
        **JSON parseren:** maak gebruiksvriendelijke tokens op basis van eigenschappen en hun waarden in JSON-inhoud, zodat u deze eigenschappen in uw werkstroom kunt gebruiken. 
        \
        \
        **Selecteer**: Maak een matrix met JSON-objecten door items of waarden in een andere matrix te transformeren en deze items toe tewijsen aan opgegeven eigenschappen.
    :::column-end:::
    :::column:::
        ![Het actiepictogram Datum/tijd in Logic Apps][date-time-icon]
        \
        \
        **Datum/tijd**
        \
        \
        Bewerkingen uitvoeren met tijdstempels.
        \
        \
        **Toevoegen aan tijd:** voeg het opgegeven aantal eenheden toe aan een tijdstempel. 
        \
        \
        **Tijdzone converteren:** converteert een tijdstempel van de brontijdzone naar de doeltijdzone. 
        \
        \
        **Huidige tijd:** retourneerde de huidige tijdstempel als een tekenreeks. 
        \
        \
        **Toekomstige tijd op halen:** retourneert het huidige tijdstempel plus de opgegeven tijdseenheden. 
        \
        \
        **Tijd in het verleden:** retourneert de huidige tijdstempel min de opgegeven tijdseenheden. 
        \
        \
        **Aftrekken van tijd:** een aantal tijdseenheden van een tijdstempel aftrekken.
    :::column-end:::
    :::column:::
        [![Actiepictogram Variabelen in Logic Apps][variables-icon]][variables-doc]
        \
        \
        [**Variabelen**][variables-doc]
        \
        \
        Bewerkingen uitvoeren met variabelen.
        \
        \
        **Toevoegen aan matrixvariabele:** Voeg een waarde in als het laatste item in een matrix die is opgeslagen door een variabele. 
        \
        \
        **Toevoegen aan tekenreeksvariabele**: Voeg een waarde in als het laatste teken in een tekenreeks die is opgeslagen door een variabele.
        \
        \
        **Variabele voor verlagen:** verklein een variabele met een constante waarde.
        \
        \
        **Variabele verhogen:** verhoog een variabele met een constante waarde. 
        \
        \
        **Variabele initialiseren:** maak een variabele en declareer het gegevenstype en de initiële waarde. 
        \
        \
        **Variabele instellen:** wijs een andere waarde toe aan een bestaande variabele. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aangepaste API's maken die u kunt aanroepen vanuit Logic Apps](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Een Azure API Management-service-exemplaar maken voor het beheren en publiceren van uw API's"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Logische apps integreren met App Service API Apps."
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logische apps integreren met Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Berichten in groepen of als batches verwerken"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Een voorwaarde evalueren en verschillende acties uitvoeren op basis van of de voorwaarde waar of onwaar is"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Voer dezelfde acties uit voor elk item in een matrix"
[http-doc]: ./connectors-native-http.md "HTTP- of HTTPS-eindpunten aanroepen vanuit uw logische apps"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-aanvragen ontvangen in uw logische apps"
[http-response-doc]: ./connectors-native-reqres.md "Reageren op HTTP-aanvragen van uw logische apps"
[http-swagger-doc]: ./connectors-native-http-swagger.md "REST-eindpunten aanroepen vanuit uw logische apps"
[http-webhook-doc]: ./connectors-native-webhook.md "Wacht op specifieke gebeurtenissen van HTTP- of HTTPS-eindpunten"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "JavaScript-codefragmenten toevoegen en uitvoeren vanuit uw logische apps"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logische apps integreren met geneste werkstromen"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Matrices selecteren en filteren met behulp van de queryactie."
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Logische apps uitvoeren op basis van een planning"
[schedule-delay-doc]: ./connectors-native-delay.md "De volgende actie uitstellen"
[schedule-delay-until-doc]: ./connectors-native-delay.md "De volgende actie uitstellen"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Logische apps uitvoeren volgens een terugkerend schema"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Logische apps uitvoeren die gegevens in aaneengesloten segmenten moeten verwerken"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Acties organiseren in groepen, die hun eigen status krijgen nadat de acties in de groep zijn uitgevoerd"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organiseer acties in cases waaraan unieke waarden zijn toegewezen. Voer alleen de case uit waarvan de waarde overeenkomt met het resultaat van een expressie, object of token. Als er geen overeenkomsten bestaan, moet u de standaardcase uitvoeren"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Een actief actieve werkstroom voor uw logische app stoppen of annuleren"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Herhaal acties totdat de opgegeven voorwaarde waar is of een status is gewijzigd"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Gegevensbewerkingen uitvoeren, zoals het filteren van matrices of het maken van CSV- en HTML-tabellen"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Bewerkingen uitvoeren met variabelen, zoals initialiseren, instellen, verhogen, verwijderen en aan tekenreeks- of matrixvariabele worden uitgebreid"
