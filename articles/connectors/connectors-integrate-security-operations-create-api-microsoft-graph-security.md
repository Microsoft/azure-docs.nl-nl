---
title: Beveiligings bewerkingen integreren en beheren & Microsoft Graph beveiliging
description: Verbeter de bedreigings beveiliging, detectie en reactie van uw app met Microsoft Graph Security-& Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: ea861dce23e50bc5fcae50116a3e71793d402f8f
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873315"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Verbeter de beveiliging tegen bedreigingen door beveiligings bewerkingen te integreren met Microsoft Graph Security-& Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [Microsoft Graph Security](/graph/security-concept-overview) connector kunt u verbeteren hoe uw app bedreigingen detecteert, beveiligt en beantwoordt door automatische werk stromen te maken voor de integratie van micro soft-beveiligings producten,-services en-partners. U kunt bijvoorbeeld [Azure Security Center playbooks](../security-center/workflow-automation.md) maken die Microsoft Graph beveiligings entiteiten, zoals waarschuwingen, bewaken en beheren. Hier volgen enkele scenario's die worden ondersteund door de Microsoft Graph Security connector:

* Ontvang waarschuwingen op basis van query's of op waarschuwings-ID. U kunt bijvoorbeeld een lijst ophalen met waarschuwingen met hoge urgentie.

* Waarschuwingen bijwerken. U kunt bijvoorbeeld waarschuwings toewijzingen bijwerken, opmerkingen toevoegen aan waarschuwingen of label waarschuwingen.

* Controleren wanneer waarschuwingen worden gemaakt of gewijzigd door het maken van [waarschuwings abonnementen (webhooks)](/graph/api/resources/webhooks).

* Uw waarschuwings abonnementen beheren. U kunt bijvoorbeeld actieve abonnementen ophalen, de verloop tijd voor een abonnement verlengen of abonnementen verwijderen.

De werk stroom van uw logische app kan acties gebruiken die reacties ontvangen van de Microsoft Graph beveiligings connector en die uitvoer beschikbaar maken voor andere acties in uw werk stroom. U kunt ook andere acties in uw werk stroom gebruiken om de uitvoer van de Microsoft Graph Security connector-acties uit te voeren. Als u bijvoorbeeld waarschuwingen met hoge urgentie wilt ontvangen via de beveiligings connector van Microsoft Graph, kunt u deze waarschuwingen in een e-mail bericht verzenden met behulp van de Outlook-Connector. 

Zie [Microsoft Graph Security API overview](/graph/security-concept-overview)(Engelstalig) voor meer informatie over Microsoft Graph beveiliging. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md). Als u op zoek bent naar energie automatisering of PowerApps, raadpleegt u [Wat is energie automatisering?](https://flow.microsoft.com/) of [Wat is PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Als u de Microsoft Graph Security-connector wilt gebruiken, moet u de Azure Active Directory (AD) *expliciet tenantbeheerderstoestemming hebben gegeven*, als onderdeel van de [Microsoft Graph Security-verificatievereisten](/graph/security-authorization). Deze toestemming vereist de toepassings-ID en naam van de Microsoft Graph Security connector, die u ook in de [Azure Portal](https://portal.azure.com)kunt vinden:

  | Eigenschap | Waarde |
  |----------|-------|
  | **Toepassings naam** | `MicrosoftGraphSecurityConnector` |
  | **Toepassings-id** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Voor het verlenen van toestemming voor de connector kan uw Azure AD-Tenant beheerder de volgende stappen volgen:

  * [Geef toestemming van de tenant-beheerder voor Azure AD-toepassingen](../active-directory/develop/v2-permissions-and-consent.md).

  * Tijdens de eerste uitvoering van uw logische app, kan uw app toestemming vragen aan uw Azure AD-tenantbeheerder via de [toestemmingservaring van de toepassing](../active-directory/develop/application-consent-experience.md).
   
* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Microsoft Graph beveiligings entiteiten, zoals waarschuwingen. Als u een Microsoft Graph beveiligings trigger wilt gebruiken, hebt u een lege logische app nodig. Als u een Microsoft Graph beveiligings actie wilt gebruiken, hebt u een logische app nodig die begint met de juiste trigger voor uw scenario.

## <a name="connect-to-microsoft-graph-security"></a>Verbinding maken met Microsoft Graph beveiliging 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voor lege Logic apps voegt u de trigger en eventuele andere acties toe die u wilt uitvoeren voordat u een Microsoft Graph beveiligings actie toevoegt.

   -of-

   Voor bestaande Logic apps, onder de laatste stap waar u een Microsoft Graph beveiligings actie wilt toevoegen, selecteert u **nieuwe stap**.

   -of-

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken (+) dat wordt weer gegeven en selecteer **een actie toevoegen**.

1. Voer in het zoekvak ' micro soft Graph Security ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

1. Meld u aan met uw Microsoft Graph beveiligings referenties.

1. Geef de benodigde gegevens voor de geselecteerde actie op en blijf door gaan met het bouwen van de werk stroom van uw logische app.

## <a name="add-triggers"></a>Triggers toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app en begint de werk stroom van uw app uit te voeren.

> [!NOTE] 
> Wanneer een trigger wordt geactiveerd, worden alle nieuwe waarschuwingen door de trigger verwerkt. Als er geen waarschuwingen worden ontvangen, wordt de trigger wordt uitgevoerd. De volgende trigger poll wordt uitgevoerd op basis van het terugkeer interval dat u opgeeft in de eigenschappen van de trigger.

In dit voor beeld ziet u hoe u een werk stroom van een logische app kunt starten wanneer er nieuwe waarschuwingen naar uw app worden verzonden.

1.  Maak in de Azure Portal of Visual Studio een lege logische app, waarmee u de ontwerp functie voor logische apps kunt openen. In dit voor beeld wordt de Azure Portal gebruikt.

1.  Voer op de ontwerp functie in het zoekvak ' micro soft Graph Security ' in als uw filter. Selecteer in de lijst triggers deze trigger: **op alle nieuwe waarschuwingen**

1.  Geef in de trigger informatie op over de waarschuwingen die u wilt bewaken. Voor meer eigenschappen opent u de lijst **nieuwe para meter toevoegen** en selecteert u een para meter om die eigenschap toe te voegen aan de trigger.

   | Eigenschap | Eigenschap (JSON) | Vereist | Type | Beschrijving |
   |----------|-----------------|----------|------|-------------|
   | **Interval** | `interval` | Ja | Geheel getal | Een positief geheel getal dat aangeeft hoe vaak de werk stroom wordt uitgevoerd op basis van de frequentie. Dit zijn de minimale en maximale intervallen: <p><p>-Maand: 1-16 maanden <br>-Dag: 1-500 dagen <br>-Uur: 1-12000 uur <br>-Minuut: 1-72000 minuten <br>-Seconde: 1-9999999 seconden <p>Als het interval bijvoorbeeld 6 is en de frequentie ' month ' is, is het terugkeer patroon elke 6 maanden. |
   | **Frequentie** | `frequency` | Ja | Tekenreeks | De tijds eenheid voor het terugkeer patroon: **tweede**, **minuut**, **uur**, **dag**, **week** of **maand** |
   | **Tijdzone** | `timeZone` | Nee | Tekenreeks | Is alleen van toepassing wanneer u een start tijd opgeeft, omdat deze trigger geen [UTC-offset](https://en.wikipedia.org/wiki/UTC_offset)accepteert. Selecteer de tijd zone die u wilt Toep assen. |
   | **Begin tijd** | `startTime` | Nee | Tekenreeks | Geef een begin datum en-tijd op in de volgende indeling: <p><p>JJJJ-MM-DDTuu: mm: SS als u een tijd zone selecteert <p>-of- <p>JJJJ-MM-DDTuu: mm: ssZ als u geen tijd zone selecteert <p>Als u bijvoorbeeld 18 september 2017 om 2:00 uur wilt, geeft u "2017-09-18T14:00:00" op en selecteert u een tijd zone zoals Pacific (standaard tijd). U kunt ook "2017-09-18T14:00:00Z" opgeven zonder tijd zone. <p>**Opmerking:** Deze begin tijd heeft een maximum van 49 jaar in de toekomst en moet voldoen aan de [ISO 8601 date time-specificatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [UTC datum tijd notatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), maar zonder een [UTC-afwijking](https://en.wikipedia.org/wiki/UTC_offset). Als u geen tijd zone selecteert, moet u de letter ' Z ' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de equivalente [zeemijl tijd](https://en.wikipedia.org/wiki/Nautical_time). <p>Voor eenvoudige schema's is de start tijd het eerste voorval, terwijl voor complexe schema's de trigger niet eerder dan de begin tijd wordt geactiveerd. [*Wat zijn de manieren waarop ik de begin datum en-tijd kan gebruiken?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

1.  Ga nu verder met het toevoegen van een of meer acties aan uw logische app voor de taken die u wilt uitvoeren met de trigger resultaten.

## <a name="add-actions"></a>Acties toevoegen

Hier vindt u meer specifieke informatie over het gebruik van de verschillende acties die beschikbaar zijn in de Microsoft Graph Security connector.

### <a name="manage-alerts"></a>Waarschuwingen beheren

Als u de meest recente resultaten wilt filteren, sorteren of ophalen, geeft u *alleen* de [ODATA-query parameters op die door Microsoft Graph worden ondersteund](/graph/query-parameters). *Geef niet* de volledige basis-URL of de http-actie, bijvoorbeeld, `https://graph.microsoft.com/v1.0/security/alerts` of de `GET` of- `PATCH` bewerking op. Hier volgt een specifiek voor beeld waarin de para meters voor een actie **Get Alerts** worden weer gegeven wanneer u een lijst met waarschuwingen met hoge urgentie wilt:

`Filter alerts value as Severity eq 'high'`

Voor meer informatie over de query's die u met deze connector kunt gebruiken, raadpleegt u de [documentatie over Microsoft Graph Security Alerts](/graph/api/alert-list). Meer informatie over de [schema-eigenschappen](/graph/api/resources/alert) die door de connector worden ondersteund om uitgebreide ervaringen met deze connector te maken.

| Bewerking | Beschrijving |
|--------|-------------|
| **Waarschuwingen ophalen** | Ontvang waarschuwingen die worden gefilterd op basis van een of meer [Eigenschappen van waarschuwingen](/graph/api/resources/alert), bijvoorbeeld `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` . | 
| **Waarschuwing op ID ontvangen** | Een specifieke waarschuwing ophalen op basis van de waarschuwings-ID. | 
| **Waarschuwing bijwerken** | Een specifieke waarschuwing bijwerken op basis van de waarschuwings-ID. Om ervoor te zorgen dat u de vereiste en bewerk bare eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [Bewerk bare eigenschappen voor waarschuwingen](/graph/api/alert-update). Als u bijvoorbeeld een waarschuwing wilt toewijzen aan een beveiligings analist zodat deze kan worden onderzocht, kunt u de waarschuwing voor de eigenschap **toegewezen aan** bijwerken. |
|||

### <a name="manage-alert-subscriptions"></a>Waarschuwings abonnementen beheren

Microsoft Graph ondersteunt [*abonnementen*](/graph/api/resources/subscription)of [*webhooks*](/graph/api/resources/webhooks). Als u abonnementen wilt ontvangen, bijwerken of verwijderen, geeft u de Odata-query parameters op die worden [ondersteund door Microsoft Graph](/graph/query-parameters) aan de construct Microsoft Graph entiteit en worden `security/alerts` gevolgd door de ODATA-query. *Neem* de basis-URL niet op, bijvoorbeeld `https://graph.microsoft.com/v1.0` . Gebruik in plaats daarvan de notatie in dit voor beeld:

`security/alerts?$filter=status eq 'NewAlert'`

| Bewerking | Beschrijving |
|--------|-------------|
| **Abonnementen maken** | [Maak een abonnement](/graph/api/subscription-post-subscriptions) dat u op de hoogte brengt van eventuele wijzigingen. U kunt dit abonnement filteren op de specifieke waarschuwings typen die u wilt. U kunt bijvoorbeeld een abonnement maken dat u op de hoogte stelt van waarschuwingen met hoge urgentie. |
| **Actieve abonnementen ophalen** | Niet- [verlopen abonnementen ophalen](/graph/api/subscription-list). | 
| **Abonnement bijwerken** | [Werk een abonnement](/graph/api/subscription-update) bij door de abonnements-id op te geven. Als u uw abonnement wilt uitbreiden, kunt u bijvoorbeeld de eigenschap van het abonnement bijwerken `expirationDateTime` . | 
| **Abonnement verwijderen** | [Een abonnement verwijderen](/graph/api/subscription-delete) door de abonnements-id op te geven. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Threat Intelligence-indica toren beheren

Als u de meest recente resultaten wilt filteren, sorteren of ophalen, geeft u *alleen* de [ODATA-query parameters op die door Microsoft Graph worden ondersteund](/graph/query-parameters). *Geef niet* de volledige basis-URL of de http-actie, bijvoorbeeld, `https://graph.microsoft.com/beta/security/tiIndicators` of de `GET` of- `PATCH` bewerking op. Hier volgt een specifiek voor beeld waarin de para meters voor de actie **Get tiIndicators** worden weer gegeven wanneer u een lijst wilt met het `DDoS` type bedreiging:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Voor meer informatie over de query's die u met deze connector kunt gebruiken, raadpleegt u [' optionele query parameters ' in de referentie documentatie Microsoft Graph Security Threat Intelligence-indicator](/graph/api/tiindicators-list). Als u uitgebreide ervaringen met deze connector wilt maken, leest u meer over de [schema-eigenschappen Threat Intelligence-indicator](/graph/api/resources/tiindicator) die door de connector wordt ondersteund.

| Bewerking | Beschrijving |
|--------|-------------|
| **Threat Intelligence-Indica tors ophalen** | Get tiIndicators gefilterd op basis van een of meer [tiIndicator-eigenschappen](/graph/api/resources/tiindicator), bijvoorbeeld `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Bedreigings informatie-indicator op ID ophalen** | Een specifieke tiIndicator ophalen op basis van de tiIndicator-ID. | 
| **Bedreigings informatie-indicator maken** | Maak een nieuwe tiIndicator door te boeken naar de tiIndicators-verzameling. Om ervoor te zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [vereiste eigenschappen voor het maken van tiIndicator](/graph/api/tiindicators-post). |
| **Meerdere Threat Intelligence-indica toren verzenden** | Maak meerdere nieuwe tiIndicators door een tiIndicators-verzameling te boeken. Om ervoor te zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [vereiste eigenschappen voor het verzenden van meerdere tiIndicators](/graph/api/tiindicator-submittiindicators). |
| **Bedreigings informatie-indicator bijwerken** | Een specifieke tiIndicator bijwerken op basis van de tiIndicator-ID. Zie de [Bewerk bare eigenschappen voor tiIndicator](/graph/api/tiindicator-update)om ervoor te zorgen dat u de vereiste en bewerk bare eigenschappen in uw aanvraag doorgeeft. Als u bijvoorbeeld de actie wilt bijwerken die moet worden toegepast als de indicator wordt vergeleken vanuit het targetProduct-beveiligings hulpprogramma, kunt u de eigenschap **Action** van tiIndicator bijwerken. |
| **Meerdere Threat Intelligence-indica toren bijwerken** | Meerdere tiIndicators bijwerken. Raadpleeg de [vereiste eigenschappen voor het bijwerken van meerdere tiIndicators](/graph/api/tiindicator-updatetiindicators)om ervoor te zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft. |
| **Bedreigings informatie-indicator op ID verwijderen** | Een specifieke tiIndicator verwijderen op basis van de tiIndicator-ID. |
| **Meerdere Threat Intelligence-indica toren per id verwijderen** | Verwijder meerdere tiIndicators met hun Id's. Om ervoor te zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [vereiste eigenschappen voor het verwijderen van meerdere tiIndicators op id's](/graph/api/tiindicator-deletetiindicators). |
| **Meerdere Threat Intelligence-indica toren met externe Id's verwijderen** | Meerdere tiIndicators verwijderen met de externe Id's. Om ervoor te zorgen dat u de vereiste eigenschappen in uw aanvraag doorgeeft, raadpleegt u de [vereiste eigenschappen voor het verwijderen van meerdere tiIndicators door externe id's](/graph/api/tiindicator-deletetiindicatorsbyexternalid). |
|||

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/microsoftgraphsecurity/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
