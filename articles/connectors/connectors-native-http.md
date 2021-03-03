---
title: Service-eind punten aanroepen met behulp van HTTP of HTTPS
description: Uitgaande HTTP-of HTTPS-aanvragen verzenden naar service-eind punten van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 02/18/2021
tags: connectors
ms.openlocfilehash: dab5b755347e46d8d509e8014bba8f496ca9c900
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719437"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Service-eindpunten aanroepen via HTTP of HTTPS vanuit Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de ingebouwde http-trigger of-actie kunt u geautomatiseerde taken en werk stromen maken die uitgaande aanvragen kunnen verzenden naar eind punten op andere services en systemen via http of https. Als u in plaats daarvan binnenkomende HTTPS-aanroepen wilt ontvangen en beantwoorden, gebruikt u de ingebouwde [aanvraag-en reactie actie](../connectors/connectors-native-reqres.md).

U kunt bijvoorbeeld een service-eind punt voor uw website bewaken door dat eind punt op een specifiek schema te controleren. Wanneer de opgegeven gebeurtenis plaatsvindt op dat eind punt, zoals uw website, activeert de gebeurtenis de werk stroom van uw logische app en worden de acties uitgevoerd in die werk stroom.

* Als u een eind punt in een terugkerend schema wilt controleren of *pollen* , [voegt u de http-trigger](#http-trigger) als eerste stap in de werk stroom toe. Telkens wanneer de trigger het eind punt controleert, wordt de trigger aangeroepen of een *aanvraag* naar het eind punt verzonden. Het antwoord van het eind punt bepaalt of de werk stroom van uw logische app wordt uitgevoerd. De trigger geeft inhoud van het antwoord van het eind punt door aan de acties in uw logische app.

* Als u een eind punt van ergens anders in uw werk stroom wilt aanroepen, [voegt u de HTTP-actie toe](#http-action). Het antwoord van het eind punt bepaalt hoe de resterende acties van uw werk stroom worden uitgevoerd.

In dit artikel wordt beschreven hoe u de HTTP-trigger en de HTTP-actie gebruikt, zodat uw logische app uitgaande oproepen naar andere services en systemen kan verzenden.

Voor informatie over versleuteling, beveiliging en autorisatie voor uitgaande oproepen vanuit uw logische app, zoals [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), voorheen bekend als Secure Sockets Layer (SSL), zelfondertekende certificaten of [Azure Active Directory open verificatie (Azure AD OAuth)](../active-directory/develop/index.yml), raadpleegt [u beveiligde toegang en gegevens toegang voor uitgaande oproepen naar andere services en systemen](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor het doel eindpunt dat u wilt aanroepen

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md)?

* De logische app waarvan u het doel eindpunt wilt aanroepen. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met de http-trigger. Als u de HTTP-actie wilt gebruiken, start u de logische app met een wille keurige trigger. In dit voor beeld wordt de HTTP-trigger als eerste stap gebruikt.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Een HTTP-trigger toevoegen

Deze ingebouwde trigger maakt een HTTP-aanroep van de opgegeven URL voor een eind punt en retourneert een antwoord.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw lege logische app in de ontwerp functie voor logische apps.

1. Selecteer in het zoekvak van de ontwerp functie **ingebouwd**. Voer in het zoekvak `http` als uw filter in. Selecteer in de lijst **Triggers** de **http-** trigger.

   ![HTTP-trigger selecteren](./media/connectors-native-http/select-http-trigger.png)

   In dit voor beeld wordt de naam van de trigger gewijzigd in ' HTTP trigger ', zodat de stap een meer beschrijvende naam heeft. Daarnaast voegt het voor beeld een HTTP-actie toe en beide namen moeten uniek zijn.

1. Geef de waarden voor de [http-trigger parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) op die u wilt opnemen in de aanroep van het doel eindpunt. Stel het terugkeer patroon in om te bepalen hoe vaak het doel eindpunt moet worden gecontroleerd met de trigger.

   ![HTTP-trigger parameters invoeren](./media/connectors-native-http/http-trigger-parameters.png)

   Als u een ander verificatie type dan **geen** selecteert, verschillen de verificatie-instellingen op basis van uw selectie. Zie de volgende onderwerpen voor meer informatie over de beschik bare verificatie typen voor HTTP:

   * [Verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Toegang tot resources met beheerde identiteiten verifiëren](../logic-apps/create-managed-service-identity.md)

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

1. Ga door met het bouwen van de werk stroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Als u klaar bent, kunt u uw logische app niet opslaan. Selecteer **Opslaan** op de werkbalk van de ontwerper.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Een HTTP-actie toevoegen

Met deze ingebouwde actie wordt een HTTP-aanroep naar de opgegeven URL voor een eind punt gemaakt en wordt een antwoord geretourneerd.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt de HTTP-trigger als eerste stap gebruikt.

1. Selecteer **nieuwe stap** onder de stap waar u de http-actie wilt toevoegen.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Selecteer onder **Kies een actie** de optie **Ingebouwd**. Voer in het zoekvak `http` als uw filter in. Selecteer in de lijst **acties** de **http-** actie.

   ![HTTP-actie selecteren](./media/connectors-native-http/select-http-action.png)

   In dit voor beeld wordt de naam van de actie gewijzigd in ' HTTP Action ' zodat de stap een meer beschrijvende naam heeft.

1. Geef de waarden op voor de [http-actie parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) die u wilt opnemen in de aanroep van het doel eindpunt.

   ![HTTP-actie parameters invoeren](./media/connectors-native-http/http-action-parameters.png)

   Als u een ander verificatie type dan **geen** selecteert, verschillen de verificatie-instellingen op basis van uw selectie. Zie de volgende onderwerpen voor meer informatie over de beschik bare verificatie typen voor HTTP:

   * [Verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Toegang tot resources met beheerde identiteiten verifiëren](../logic-apps/create-managed-service-identity.md)

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

1. Als u klaar bent, kunt u uw logische app niet opslaan. Selecteer **Opslaan** op de werkbalk van de ontwerper.

## <a name="trigger-and-action-outputs"></a>Trigger-en actie-uitvoer

Hier vindt u meer informatie over de uitvoer van een HTTP-trigger of actie, waarmee deze gegevens worden geretourneerd:

| Eigenschap | Type | Beschrijving |
|----------|------|-------------|
| `headers` | JSON-object | De headers van de aanvraag |
| `body` | JSON-object | Het object met de inhoud van de hoofd tekst van de aanvraag |
| `status code` | Geheel getal | De status code van de aanvraag |
|||

| Statuscode | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Geaccepteerd |
| 400 | Ongeldige aanvraag |
| 401 | Niet geautoriseerd |
| 403 | Verboden |
| 404 | Niet gevonden |
| 500 | Interne serverfout. Er is een onbekende fout opgetreden. |
|||

## <a name="content-with-multipartform-data-type"></a>Inhoud met meerdelige/formulier-gegevens type

Als u inhoud wilt afhandelen die `multipart/form-data` van het type HTTP-aanvragen heeft, kunt u een JSON-object met de `$content-type` kenmerken en toevoegen `$multipart` aan de hoofd tekst van de HTTP-aanvraag met deze indeling.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Stel dat u een logische app hebt waarmee een HTTP POST-aanvraag voor een Excel-bestand naar een website wordt verzonden met behulp van de API van die site, die het `multipart/form-data` type ondersteunt. Deze actie kan er als volgt uitzien:

![Meerdelige formulier gegevens](./media/connectors-native-http/http-action-multipart.png)

Hier volgt hetzelfde voor beeld waarin de JSON-definitie van de HTTP-actie in de onderliggende werk stroom definitie wordt weer gegeven:

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>Inhoud met Application/x-www-form-urlencoded type

Om formulier-urlencoded gegevens in de hoofd tekst van een HTTP-aanvraag te bieden, moet u opgeven dat de gegevens het `application/x-www-form-urlencoded` inhouds type hebben. Voeg de header toe aan de HTTP-trigger of actie `content-type` . Stel de waarde voor de header in op `application/x-www-form-urlencoded` .

Stel bijvoorbeeld dat u een logische app hebt die een HTTP POST-aanvraag verzendt naar een website die het type ondersteunt `application/x-www-form-urlencoded` . Deze actie kan er als volgt uitzien:

![Scherm afbeelding met een HTTP-aanvraag waarbij de header content-type is ingesteld op Application/x-www-form-urlencoded](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Asynchroon gedrag van aanvraag-antwoord

Standaard volgen alle op HTTP gebaseerde acties in Azure Logic Apps het standaard [asynchrone bewerkings patroon](/azure/architecture/patterns/async-request-reply). Met dit patroon wordt aangegeven dat nadat een HTTP-actie een aanvraag aanroept of naar een eind punt, service, systeem of API stuurt, de ontvanger onmiddellijk een antwoord ["202 accepted"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) retourneert. Met deze code wordt bevestigd dat de ontvanger de aanvraag heeft geaccepteerd, maar nog niet is verwerkt. Het antwoord kan een `location` kop bevatten die de URL en een vernieuwings-id specificeert die de oproepende functie kan gebruiken om de status van de asynchrone aanvraag te controleren of te bekijken totdat de ontvanger de verwerking stopt en een ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) -antwoord of ander niet-202-antwoord retourneert. De aanroeper hoeft echter niet te wachten tot de verwerking is voltooid en kan de volgende actie blijven uitvoeren. Zie asynchrone micro service- [integratie dwingt micro service autonom](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)af voor meer informatie.

* In de ontwerp functie van de logische app is de HTTP-actie, maar niet geactiveerd, een **asynchrone patroon** instelling, die standaard is ingeschakeld. Met deze instelling geeft u op dat de aanroeper niet wacht totdat de verwerking is voltooid en kan worden verplaatst naar de volgende actie, maar blijft de status controleren totdat de verwerking stopt. Als deze instelling is uitgeschakeld, wordt hiermee aangegeven dat de aanroeper wacht totdat de verwerking is voltooid voordat u verdergaat met de volgende actie.

  Voer de volgende stappen uit om deze instelling te vinden:

  1. Selecteer op de titel balk van de HTTP-actie de knop met weglatings tekens (**...**) om de actie-instellingen te openen.

  1. Zoek de instelling voor **asynchroon patroon** .

     ![Instelling voor asynchroon patroon](./media/connectors-native-http/asynchronous-pattern-setting.png)

* De onderliggende JavaScript Object Notation-definitie (JSON) van de HTTP-actie volgt impliciet het patroon van de asynchrone bewerking.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Asynchrone bewerkingen uitschakelen

Soms wilt u het asynchrone gedrag van de HTTP-actie in specifieke scenario's overwegen, bijvoorbeeld wanneer u het volgende wilt doen:

* [HTTP-time-outs voor langlopende taken voor komen](#avoid-http-timeouts)
* [Controleren van locatie headers uitschakelen](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>Instelling voor **asynchroon patroon** uitschakelen

1. Selecteer in de ontwerp functie voor logische apps op de titel balk van de HTTP-actie de knop met weglatings tekens (**...**). Hiermee worden de instellingen van de actie geopend.

1. Zoek de instelling voor **asynchroon patroon** , schakel de instelling in op **uit** als deze optie is ingeschakeld en selecteer **gereed**.

   ![De instelling asynchroon patroon uitschakelen](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Asynchroon patroon in de JSON-definitie van de actie uitschakelen

Voeg in de onderliggende JSON-definitie van de HTTP-actie [de `"DisableAsyncPattern"` bewerkings optie](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) toe aan de definitie van de actie zodat de actie in plaats daarvan het synchrone bewerkings patroon volgt. Zie ook [acties uitvoeren in een synchrone bewerkings patroon](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern)voor meer informatie.

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>HTTP-time-outs voor langlopende taken voor komen

HTTP-aanvragen hebben een [time-outlimiet](../logic-apps/logic-apps-limits-and-config.md#http-limits). Als u een langlopende HTTP-actie hebt die een time-out heeft wegens deze limiet, hebt u de volgende opties:

* [Schakel het asynchrone bewerkings patroon van de HTTP-actie uit](#disable-asynchronous-operations) zodat de actie niet voortdurend wordt gecontroleerd of Controleer de status van de aanvraag. In plaats daarvan wordt gewacht tot de ontvanger reageert met de status en resultaten nadat de aanvraag is verwerkt.

* Vervang de HTTP-actie door de [http-webhook-actie](../connectors/connectors-native-webhook.md), die wacht totdat de ontvanger reageert met de status en resultaten nadat de aanvraag is verwerkt.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Controleren van locatie headers uitschakelen

Sommige eind punten, services, systemen of Api's retour neren een ' 202 geaccepteerd ' antwoord dat geen header heeft `location` . U kunt de volgende opties hebben om te voor komen dat een HTTP-actie voortdurend de status van de aanvraag controleert wanneer de `location` header niet bestaat:

* [Schakel het asynchrone bewerkings patroon van de HTTP-actie uit](#disable-asynchronous-operations) zodat de actie niet voortdurend wordt gecontroleerd of Controleer de status van de aanvraag. In plaats daarvan wordt gewacht tot de ontvanger reageert met de status en resultaten nadat de aanvraag is verwerkt.

* Vervang de HTTP-actie door de [http-webhook-actie](../connectors/connectors-native-webhook.md), die wacht totdat de ontvanger reageert met de status en resultaten nadat de aanvraag is verwerkt.

## <a name="known-issues"></a>Bekende problemen

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Wegge laten HTTP-headers

Als een HTTP-trigger of actie deze headers bevat, verwijdert Logic Apps deze headers uit het gegenereerde aanvraag bericht zonder dat er een waarschuwing of fout wordt weer gegeven:

* `Accept-*` headers, behalve voor `Accept-version`
* `Allow`
* `Content-*` headers, met uitzonde ring van `Content-Disposition` , `Content-Encoding` en `Content-Type` , die worden geaccepteerd wanneer u de post-en put-bewerkingen gebruikt. Als u de GET-bewerking gebruikt, worden deze headers echter door Logic Apps neergezet.
* `Cookie` header, maar Logic Apps voldoet aan elke waarde die u opgeeft met behulp van de eigenschap **cookie** .
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Hoewel Logic Apps niet stopt met het opslaan van logische apps die gebruikmaken van een HTTP-trigger of actie met deze headers, Logic Apps deze headers negeren.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de volgende secties voor meer informatie over trigger-en actie parameters:

* [HTTP-trigger parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP-actie parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>Volgende stappen

* [Beveiligde toegang en gegevens toegang voor uitgaande oproepen naar andere services en systemen](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [Connectors voor Logic Apps](../connectors/apis-list.md)
