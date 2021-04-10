---
title: Oproepen ontvangen en erop reageren via HTTPS
description: Binnenkomende HTTPS-aanvragen van externe services verwerken door gebruik te maken van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 83ffccb7bae4fabc10796c36e782e72c661bd346
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063009"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Inkomende HTTPS-aanvragen ontvangen en erop reageren in Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de ingebouwde aanvraag-en reactie actie, kunt u geautomatiseerde taken en werk stromen maken die binnenkomende aanvragen via https kunnen ontvangen. Als u in plaats daarvan uitgaande aanvragen wilt verzenden, gebruikt u de ingebouwde [http-trigger of HTTP-actie](../connectors/connectors-native-http.md).

U kunt bijvoorbeeld uw logische app:

* Ontvangen en reageren op een HTTPS-aanvraag voor gegevens in een on-premises data base.

* Activeer een werk stroom wanneer een externe webhook-gebeurtenis plaatsvindt.

* Ontvangen en reageren op een HTTPS-aanroep vanuit een andere logische app.

In dit artikel wordt beschreven hoe u de actie trigger en reactie aanvragen kunt gebruiken zodat uw logische app inkomende oproepen kan ontvangen en erop kan reageren.

Voor meer informatie over beveiliging, autorisatie en versleuteling voor inkomende oproepen naar uw logische app, zoals [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), voorheen bekend als Secure Sockets Layer (SSL), [Azure Active Directory open verificatie (Azure AD OAuth)](../active-directory/develop/index.yml), uw logische app weer geven met Azure API Management of het beperken van de IP-adressen die afkomstig zijn van binnenkomende oproepen, Zie [beveiligde toegang en gegevens toegang voor inkomende oproepen op op aanvragen gebaseerde triggers](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u geen abonnement hebt, kunt u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md)?

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Aanvraag trigger toevoegen

Deze ingebouwde trigger maakt een hand matig aanroepbaar eind punt dat *alleen* binnenkomende aanvragen via https kan verwerken. Wanneer een beller een aanvraag naar dit eind punt verzendt, wordt de trigger voor de [aanvraag](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) geactiveerd en wordt de logische app uitgevoerd. Zie [werk stromen aanroepen, activeren of nesten met https-eind punten in azure Logic apps](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie over het aanroepen van deze trigger.

Uw logische app houdt een binnenkomende aanvraag alleen gedurende een [beperkte periode](../logic-apps/logic-apps-limits-and-config.md#http-limits)open. Ervan uitgaande dat uw logische app een [reactie actie](#add-response)bevat, als uw logische app na deze tijd niet terugstuurt naar de aanroeper, retourneert uw logische app een `504 GATEWAY TIMEOUT` status naar de aanroeper. Als uw logische app geen reactie actie bevat, retourneert uw logische app onmiddellijk een `202 ACCEPTED` status naar de aanroeper.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Een lege, logische app maken.

1. Wanneer Logic app Designer wordt geopend, voert u in het zoekvak in `http request` als uw filter. Selecteer in de lijst triggers de trigger **Wanneer een HTTP-aanvraag wordt ontvangen** .

   ![Aanvraag trigger selecteren](./media/connectors-native-reqres/select-request-trigger.png)

   Met de aanvraag trigger worden deze eigenschappen weer gegeven:

   ![Trigger voor aanvragen](./media/connectors-native-reqres/request-trigger.png)

   | Naam van eigenschap | JSON-eigenschaps naam | Vereist | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST-URL** | geen | Yes | De eind punt-URL die wordt gegenereerd na het opslaan van de logische app en wordt gebruikt voor het aanroepen van uw logische app |
   | **JSON-schema van aanvraag tekst** | `schema` | No | Het JSON-schema dat de eigenschappen en waarden in de hoofd tekst van de binnenkomende aanvraag beschrijft |
   |||||

1. Voer desgewenst in het vak **JSON-schema van aanvraag tekst** een JSON-schema in dat de hoofd tekst in de binnenkomende aanvraag beschrijft, bijvoorbeeld:

   ![Voor beeld van JSON-schema](./media/connectors-native-reqres/provide-json-schema.png)

   De ontwerp functie gebruikt dit schema om tokens te genereren voor de eigenschappen in de aanvraag. Op die manier kan uw logische app de gegevens van de aanvraag parseren, gebruiken en door geven via de trigger in uw werk stroom.

   Dit is het voorbeeld schema:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Wanneer u een JSON-schema invoert, wordt in de ontwerp functie een herinnering weer gegeven om de koptekst op te `Content-Type` vragen en de waarde voor de header in te stellen op `application/json` . Zie voor meer informatie [inhouds typen verwerken](../logic-apps/logic-apps-content-type.md).

   ![Herinnering voor het toevoegen van de header content-type](./media/connectors-native-reqres/include-content-type.png)

   Deze koptekst ziet er als volgt uit in de JSON-indeling:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Als u een JSON-schema wilt genereren dat is gebaseerd op de verwachte Payload (gegevens), kunt u een hulp programma zoals [JSONSchema.net](https://jsonschema.net)gebruiken, of u kunt de volgende stappen uitvoeren:

   1. Selecteer in de trigger voor de aanvraag een **voor beeld-nettolading gebruiken om een schema te genereren**.

      ![Scherm opname met de selectie voor beeld van nettolading gebruiken om schema te genereren](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Voer de voor beeld-nettolading in en selecteer **gereed**.

      ![Voor beeld-nettolading invoeren om schema te genereren](./media/connectors-native-reqres/enter-payload.png)

      Dit is de nettolading van de steek proef:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Voer de volgende stappen uit om te controleren of de inkomende oproep een aanvraag tekst heeft die overeenkomt met het opgegeven schema:

   1. Selecteer de knop met weglatings tekens (**...**) in de titel balk van de trigger voor de aanvraag.

   1. Schakel in de instellingen van de trigger **schema validatie** in en selecteer **gereed**.

      Als de aanvraag tekst van de inkomende oproep niet overeenkomt met uw schema, retourneert de trigger een `HTTP 400 Bad Request` fout.

1. Als u aanvullende eigenschappen wilt opgeven, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de para meters die u wilt toevoegen.

   | Naam van eigenschap | JSON-eigenschaps naam | Vereist | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **Methode** | `method` | No | De methode die de inkomende aanvraag moet gebruiken om de logische app aan te roepen |
   | **Relatief pad** | `relativePath` | No | Het relatieve pad voor de para meter die door de eind punt-URL van de logische app kan worden geaccepteerd |
   |||||

   In dit voor beeld wordt de eigenschap **Method** toegevoegd:

   ![Methode parameter toevoegen](./media/connectors-native-reqres/add-parameters.png)

   De eigenschap **Method** wordt weer gegeven in de trigger zodat u een methode kunt selecteren in de lijst.

   ![Methode selecteren](./media/connectors-native-reqres/select-method.png)

1. Voeg nu een andere actie toe als de volgende stap in uw werk stroom. Selecteer de **volgende stap** onder de trigger zodat u de actie kunt vinden die u wilt toevoegen.

   U kunt bijvoorbeeld reageren op de aanvraag door [een reactie actie toe te voegen](#add-response), die u kunt gebruiken om een aangepast antwoord te retour neren en dit later in dit onderwerp wordt beschreven.

   Uw logische app houdt de inkomende aanvraag alleen gedurende een [beperkte periode](../logic-apps/logic-apps-limits-and-config.md#http-limits)open. Ervan uitgaande dat uw logische app-werk stroom een reactie actie bevat, als de logische app geen antwoord retourneert nadat deze tijd is verstreken, retourneert uw logische app een `504 GATEWAY TIMEOUT` naar de aanroeper. Als uw logische app geen reactie actie bevat, retourneert uw logische app onmiddellijk een `202 ACCEPTED` antwoord op de aanroeper.

1. Wanneer u klaar bent, slaat u de logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

   Met deze stap wordt de URL gegenereerd die moet worden gebruikt voor het verzenden van de aanvraag die de logische app activeert. Als u deze URL wilt kopiëren, selecteert u het Kopieer pictogram naast de URL.

   ![URL die moet worden gebruikt om de logische app te activeren](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Als u het hash-of hekje-symbool ( **#** ) in de URI wilt opnemen bij het aanroepen van de aanvraag trigger, gebruikt u in plaats daarvan deze gecodeerde versie: `%25%23`

1. Als u uw logische app wilt testen, verzendt u een HTTP-aanvraag naar de gegenereerde URL.

   U kunt bijvoorbeeld een hulp programma zoals [postman](https://www.getpostman.com/) gebruiken om de HTTP-aanvraag te verzenden. Voor meer informatie over de onderliggende JSON-definitie van de trigger en hoe u deze trigger aanroept, raadpleegt u deze onderwerpen, [type aanvraag trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) en [aanroepen, activeren of nesten van werk stromen met http-eind punten in azure Logic apps](../logic-apps/logic-apps-http-endpoint.md).

Voor meer informatie over beveiliging, autorisatie en versleuteling voor inkomende oproepen naar uw logische app, zoals [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), voorheen bekend als Secure Sockets Layer (SSL), [Azure Active Directory open verificatie (Azure AD OAuth)](../active-directory/develop/index.yml), uw logische app weer geven met Azure API Management of het beperken van de IP-adressen die afkomstig zijn van binnenkomende oproepen, Zie [beveiligde toegang en gegevens toegang voor inkomende oproepen op op aanvragen gebaseerde triggers](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="trigger-outputs"></a>Trigger uitvoer

Hier volgt meer informatie over de uitvoer van de aanvraag trigger:

| JSON-eigenschaps naam | Gegevenstype | Beschrijving |
|--------------------|-----------|-------------|
| `headers` | Object | Een JSON-object dat de headers van de aanvraag beschrijft |
| `body` | Object | Een JSON-object waarmee de inhoud van de hoofd tekst van de aanvraag wordt beschreven |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Een reactie actie toevoegen

Wanneer u de aanvraag trigger gebruikt voor het verwerken van inkomende aanvragen, kunt u het antwoord model leren en de lading resultaten terugsturen naar de aanroeper met behulp van de ingebouwde [reactie actie](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action). U kunt de reactie actie *alleen* met de aanvraag trigger gebruiken. Met deze combi natie van de actie trigger en reactie van aanvragen maakt u het [antwoord patroon](https://en.wikipedia.org/wiki/Request%E2%80%93response)voor de aanvraag. Met uitzonde ring van binnen foreach-lussen en tot lussen, en parallelle vertakkingen kunt u de reactie actie overal in uw werk stroom toevoegen.

> [!IMPORTANT]
> Als een antwoord actie deze headers bevat, verwijdert Logic Apps deze headers uit het gegenereerde antwoord bericht zonder dat er een waarschuwing of fout wordt weer gegeven:
>
> * `Allow`
> * `Content-*` kopteksten behalve voor `Content-Disposition` , `Content-Encoding` en `Content-Type` Wanneer u post-en put-bewerkingen gebruikt, maar die niet zijn opgenomen voor Get-bewerkingen
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Hoewel Logic Apps niet stopt met het opslaan van logische apps met een reactie actie met deze headers, worden deze headers door Logic Apps genegeerd.

1. Selecteer in de ontwerp functie voor logische apps onder de stap waarin u een reactie actie wilt toevoegen de optie **nieuwe stap**.

   Bijvoorbeeld met behulp van de trigger voor aanvragen van eerder:

   ![Nieuwe stap toevoegen](./media/connectors-native-reqres/add-response.png)

   Als u een actie tussen de stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen deze stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Onder **Kies een actie**, typt `response` u als filter in het zoekvak en selecteert u de **reactie** actie.

   ![De reactie actie selecteren](./media/connectors-native-reqres/select-response-action.png)

   De trigger voor aanvragen wordt in dit voor beeld samengevouwen voor eenvoud.

1. Voeg waarden toe die vereist zijn voor het antwoord bericht.

   In sommige velden wordt de lijst met dynamische inhoud geopend wanneer u in hun vakken klikt. Vervolgens kunt u tokens selecteren die beschik bare uitvoer van de vorige stappen in de werk stroom vertegenwoordigen. Eigenschappen uit het schema dat in het vorige voor beeld is opgegeven, worden nu weer gegeven in de lijst met dynamische inhoud.

   Bijvoorbeeld, voor het vak **headers** , vermeld `Content-Type` als de sleutel naam en stel de sleutel waarde in op `application/json` zoals eerder in dit onderwerp wordt vermeld. Voor het vak **hoofd tekst** kunt u de uitvoer van de trigger hoofdtekst selecteren in de lijst met dynamische inhoud.

   ![Details van reactie actie](./media/connectors-native-reqres/response-details.png)

   Selecteer **overschakelen naar tekst weergave** om de kopteksten in JSON-indeling weer te geven.

   ![Headers: overschakelen naar tekst weergave](./media/connectors-native-reqres/switch-to-text-view.png)

   Hier vindt u meer informatie over de eigenschappen die u kunt instellen in de reactie actie.

   | Naam van eigenschap | JSON-eigenschaps naam | Vereist | Beschrijving |
   |---------------|--------------------|----------|-------------|
   | **Status code** | `statusCode` | Yes | De status code die in het antwoord moet worden geretourneerd |
   | **Kopteksten** | `headers` | No | Een JSON-object dat een of meer headers beschrijft die in het antwoord moeten worden meegenomen |
   | **Hoofdtekst** | `body` | No | De antwoord tekst |
   |||||

1. Als u aanvullende eigenschappen, zoals een JSON-schema voor de antwoord tekst, wilt opgeven, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de para meters die u wilt toevoegen.

1. Wanneer u klaar bent, slaat u de logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

## <a name="next-steps"></a>Volgende stappen

* [Beveiligde toegang en gegevens toegang voor inkomende oproepen naar activeringen op basis van een aanvraag](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Connectors voor Logic Apps](../connectors/apis-list.md)
