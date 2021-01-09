---
title: Functies toevoegen en aanroepen vanuit Azure Logic Apps
description: Aangepaste code aanroepen en uitvoeren in functies die zijn gemaakt in azure vanuit automatische taken en werk stromen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: fd04e5a7f084de7a95f20b54b99a9e4590f10cd7
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034761"
---
# <a name="call-functions-from-azure-logic-apps"></a>Functies aanroepen vanuit Azure Logic Apps

Als u code wilt uitvoeren waarmee een specifieke taak wordt uitgevoerd in uw Logic apps, kunt u uw eigen functie maken met behulp van [Azure functions](../azure-functions/functions-overview.md). Met deze service kunt u node.js-, C#- en F#-functies maken, zodat u geen volledige app of infrastructuur hoeft te bouwen om code uit te voeren. U kunt ook [Logic apps aanroepen vanuit functies](#call-logic-app). Azure Functions biedt serverloze computers in de Cloud en is handig voor het uitvoeren van taken zoals deze voor beelden:

* Breid het gedrag van uw logische app uit met functies in Node.js of C#.
* Voer berekeningen uit in de werk stroom van uw logische app.
* Geavanceerde opmaak of reken velden Toep assen in uw Logic apps.

Als u code fragmenten wilt uitvoeren zonder Azure Functions te gebruiken, leert u hoe u [inline code kunt toevoegen en uitvoeren](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integratie tussen Logic Apps en Azure Functions werkt momenteel niet met sleuven ingeschakeld.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een functie-app, een container voor een functie die wordt gemaakt in Azure Functions, samen met de functie die u maakt. Als u geen functie-app hebt, moet u [eerst uw functie-app maken](../azure-functions/functions-create-first-azure-function.md). U kunt de functie vervolgens buiten uw logische app maken in de Azure Portal, of [vanuit uw logische](#create-function-designer) app in de ontwerp functie voor logische apps.

* Wanneer u werkt met Logic apps, gelden dezelfde vereisten voor functie-apps en-functies, ongeacht of ze al dan niet bestaande of nieuw zijn:

  * De functie-app en de logische app moeten gebruikmaken van hetzelfde Azure-abonnement.

  * Nieuwe functie-apps moeten .NET of Java script als runtime stack gebruiken. Wanneer u een nieuwe functie toevoegt aan de bestaande functie-apps, kunt u C# of Java script selecteren.

  * De functie maakt gebruik van de **http-trigger** sjabloon.

    De sjabloon voor de HTTP-trigger kan inhoud accepteren die `application/json` van het type is van uw logische app. Wanneer u een functie aan uw logische app toevoegt, worden in de Logic app Designer aangepaste functies weer gegeven die zijn gemaakt op basis van deze sjabloon binnen uw Azure-abonnement.

  * De functie gebruikt geen aangepaste routes tenzij u een OpenAPI- [definitie](../azure-functions/functions-openapi-definition.md) (voorheen een [Swagger-bestand](https://swagger.io/)genoemd) hebt gedefinieerd.

  * Als u een OpenAPI-definitie voor uw functie hebt, biedt de Logic Apps Designer u een rijkere ervaring wanneer u werkt met functie parameters. [Stel uw functie-app in met behulp van de volgende stappen](#function-swagger)voordat u met de logische app functies met OpenAPI-definities kunt vinden en gebruiken.

* De logische app waaraan u de functie wilt toevoegen, inclusief een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) als de eerste stap in uw logische app

  Voordat u acties kunt toevoegen die functies uitvoeren, moet uw logische app beginnen met een trigger. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Functies met OpenAPI-beschrijvingen zoeken

[Genereer](../azure-functions/functions-openapi-definition.md)voor uw functie een OpenAPI-definitie, voorheen bekend als een [Swagger-bestand](https://swagger.io/), voor een uitgebreide ervaring wanneer u werkt met functie parameters in de Logic apps Designer. Ga als volgt te werk om uw functie-app in te stellen zodat uw logische app functies met Swagger-beschrijvingen kan vinden en gebruiken:

1. Zorg ervoor dat de functie-app actief is.

1. Stel in de functie-app [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in zodat alle oorsprongen zijn toegestaan door de volgende stappen uit te voeren:

   1. Selecteer de functie-app in de lijst **functie-apps** . Selecteer **platform functies** CORS in het rechterdeel venster  >  .

      ![Selecteer de functie-app > platform functies > CORS](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Voeg onder **CORS** het **`*`** Joker teken asterisk () toe, maar verwijder alle andere oorsprongen uit de lijst en selecteer **Opslaan**.

      ![' CORS * ' instellen op het Joker teken ' * '](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Eigenschaps waarden in HTTP-aanvragen openen

Webhook-functies kunnen HTTP-aanvragen accepteren als invoer en de aanvragen door geven aan andere functies. Hoewel Logic Apps bijvoorbeeld [functies heeft waarmee datetime-waarden worden geconverteerd](../logic-apps/workflow-definition-language-functions-reference.md), laat deze basis voorbeeld functie Java script zien hoe u toegang hebt tot een eigenschap binnen een aanvraag object dat wordt door gegeven aan de functie en bewerkingen uitvoert op die eigenschaps waarde. In dit voor beeld wordt gebruikgemaakt van de [operator punt (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)om toegang te krijgen tot eigenschappen binnen objecten:

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Wat gebeurt er in deze functie:

1. De functie maakt een `data` variabele en wijst het `body` object in het `request` object toe aan die variabele. De functie maakt gebruik van de operator punt (.) om naar het `body` object in het object te verwijzen `request` :

   ```javascript
   var data = request.body;
   ```

1. De functie heeft nu toegang tot de `date` eigenschap via de `data` variabele en converteert die eigenschaps waarde van datetime-type naar DateString-type door de functie aan te roepen `ToDateString()` . De functie retourneert ook het resultaat via de `body` eigenschap in het antwoord van de functie:

   ```javascript
   body: data.date.ToDateString();
   ```

Nu u uw functie in azure hebt gemaakt, volgt u de stappen om [functies toe te voegen aan Logic apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Functies in Logic apps maken

U kunt functies rechtstreeks vanuit de werk stroom van uw logische app maken met behulp van de ingebouwde Azure Functions actie in de Logic app Designer, maar u kunt deze methode alleen gebruiken voor functies die zijn geschreven in Java script. Voor andere talen kunt u functies maken via de Azure Functions-ervaring in de Azure Portal. Zie [uw eerste functie maken in de Azure Portal](../azure-functions/functions-create-first-azure-function.md)voor meer informatie.

Voordat u de functie in azure kunt maken, moet u echter al een functie-app hebben. Dit is een container voor uw functies. Als u geen functie-app hebt, moet u eerst die functie-app maken. Zie [uw eerste functie maken in de Azure Portal](../azure-functions/functions-create-first-azure-function.md).

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Volg de stap die van toepassing is op uw scenario om uw functie te maken en toe te voegen:

   * Selecteer in de laatste stap van de werk stroom van uw logische app de optie **nieuwe stap**.

   * Beweeg tussen de bestaande stappen in de werk stroom van uw logische app de muis aanwijzer over de pijl, selecteer het plus teken (+) en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' Azure functions ' in als uw filter. Selecteer in de lijst acties de actie **een Azure-functie kiezen** , bijvoorbeeld:

   ![Zoek functies in de Azure Portal.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Selecteer de functie-app in de lijst functie-apps. Nadat de lijst met acties wordt geopend, selecteert u deze actie: **nieuwe functie maken**.

   ![Uw functie-app selecteren](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Definieer uw functie in de editor van de functie definitie:

   1. Geef in het vak **functie naam** een naam op voor de functie.

   1. Voeg in het vak **code** de code toe aan de functie sjabloon, met inbegrip van de reactie en de nettolading die u wilt retour neren naar uw logische app nadat de functie is voltooid. Als u gereed bent, selecteert u **Maken**.

   Bijvoorbeeld:

   ![Uw functie definiëren](./media/logic-apps-azure-functions/add-code-function-definition.png)

   In de code van de sjabloon verwijst het *`context` object* naar het bericht dat door de logische app wordt verzonden via het veld **hoofd tekst** van de aanvraag in een latere stap. Als u toegang wilt krijgen tot de `context` Eigenschappen van het object vanuit uw functie, gebruikt u de volgende syntaxis:

   `context.body.<property-name>`

   Als u bijvoorbeeld naar de `content` eigenschap in het object wilt verwijzen, gebruikt u de volgende `context` syntaxis:

   `context.body.content`

   De sjabloon code bevat ook een `input` variabele, die de waarde van de `data` para meter opslaat, zodat uw functie bewerkingen op die waarde kan uitvoeren. In Java script-functies `data` is de variabele ook een snelkoppeling voor `context.body` .

   > [!NOTE]
   > De `body` eigenschap is hier van toepassing op het `context` object en is niet hetzelfde als het **hoofdtekst** token van de uitvoer van een actie, wat u mogelijk ook doorgeeft aan uw functie.

1. Geef in het vak **hoofd tekst** van de aanvraag de invoer van uw functie op die moet worden opgemaakt als een JavaScript object NOTATION (JSON)-object.

   Deze invoer is het *context object* of het bericht dat uw logische app naar uw functie verzendt. Wanneer u in het veld **hoofd tekst van aanvraag** klikt, wordt de lijst met dynamische inhoud weer gegeven, zodat u tokens kunt selecteren voor uitvoer van de vorige stappen. In dit voor beeld wordt aangegeven dat de context Payload een eigenschap bevat `content` met de naam die de waarde **van het van** -token van de e-mail trigger heeft.

   ![Voor beeld van een hoofd tekst van aanvraag-context object Payload](./media/logic-apps-azure-functions/function-request-body-example.png)

   Hier wordt het context object niet als een teken reeks gecast, zodat de inhoud van het object rechtstreeks aan de JSON-nettolading wordt toegevoegd. Wanneer het context object echter geen JSON-token is dat een teken reeks, een JSON-object of een JSON-matrix doorgeeft, krijgt u een fout melding. Als in dit voor beeld in plaats daarvan het token **ontvangend** wordt gebruikt, kunt u het context object converteren als een teken reeks door dubbele aanhalings tekens toe te voegen.

   ![Object casten als teken reeks](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Als u andere details wilt opgeven, zoals de methode voor gebruik, aanvraag headers of query parameters, of verificatie, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste opties. Voor verificatie zijn uw opties afhankelijk van de geselecteerde functie. Zie [verificatie inschakelen voor functies](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Bestaande functies toevoegen aan Logic apps

Als u bestaande functies wilt aanroepen vanuit uw Logic apps, kunt u functies toevoegen, zoals elke andere actie in de ontwerp functie van de logische app.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **nieuwe stap** onder de stap waar u de functie wilt toevoegen.

1. Voer onder **Kies een actie** in het zoekvak ' Azure functions ' in als uw filter. Selecteer in de lijst acties de actie **een Azure-functie kiezen** .

   ![Zoek een functie in Azure.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Selecteer de functie-app in de lijst functie-apps. Nadat de lijst met functies wordt weer gegeven, selecteert u de functie.

   ![Selecteer de functie-app en de functie](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Voor functies met API-definities (Swagger-beschrijvingen) en die zijn [ingesteld zodat uw logische app deze functies kan vinden en gebruiken](#function-swagger), kunt u **Swagger-acties** selecteren.

   ![Selecteer uw functie-app, "Swagger-acties" en uw functie](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Geef in het vak **hoofd tekst** van de aanvraag de invoer van uw functie op die moet worden opgemaakt als een JavaScript object NOTATION (JSON)-object.

   Deze invoer is het *context object* of het bericht dat uw logische app naar uw functie verzendt. Wanneer u in het veld **hoofd tekst van aanvraag** klikt, wordt de lijst met dynamische inhoud weer gegeven, zodat u tokens kunt selecteren voor uitvoer van de vorige stappen. In dit voor beeld wordt aangegeven dat de context Payload een eigenschap bevat `content` met de naam die de waarde **van het van** -token van de e-mail trigger heeft.

   ![Voor beeld van een hoofd tekst van aanvraag-context object Payload](./media/logic-apps-azure-functions/function-request-body-example.png)

   Hier wordt het context object niet als een teken reeks gecast, zodat de inhoud van het object rechtstreeks aan de JSON-nettolading wordt toegevoegd. Wanneer het context object echter geen JSON-token is dat een teken reeks, een JSON-object of een JSON-matrix doorgeeft, krijgt u een fout melding. Als in dit voor beeld in plaats daarvan het token **ontvangen** is gebruikt, kunt u het context object als een teken reeks casten door dubbele aanhalings tekens toe te voegen:

   ![Object casten als teken reeks](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Als u andere details wilt opgeven, zoals de methode voor het gebruik, aanvraag headers, query parameters of verificatie, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste opties. Voor verificatie zijn uw opties afhankelijk van de geselecteerde functie. Zie [verificatie inschakelen in functies](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Logische apps aanroepen vanuit functions

Wanneer u een logische app vanuit een functie wilt activeren, moet de logische app beginnen met een trigger die een aanroepbaar eind punt biedt. U kunt bijvoorbeeld de logische app starten met de trigger **http**, **aanvragen**, **Azure queues** of **Event grid** . Verzend in uw functie een HTTP POST-aanvraag naar de URL van de trigger en voeg de payload toe die de logische app moet verwerken. Zie [Logic apps aanroepen, activeren of nesten](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie.

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>Verificatie inschakelen voor functies

Voor het eenvoudig verifiëren van toegang tot andere bronnen die worden beveiligd door Azure Active Directory (Azure AD) zonder dat u zich hoeft aan te melden en referenties of geheimen te verstrekken, kan uw logische app gebruikmaken van een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) (voorheen bekend als managed service Identity of MSI). Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te wisselen. Meer informatie over [Azure-Services die ondersteuning bieden voor beheerde identiteiten voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Als u uw logische app instelt voor het gebruik van de door het systeem toegewezen identiteit of een hand matig gemaakte, door de gebruiker toegewezen identiteit, kan de functie in uw logische app ook dezelfde identiteit voor verificatie gebruiken. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over verificatie ondersteuning voor functies in Logic apps.

Voer de volgende stappen uit om de beheerde identiteit in te stellen en te gebruiken met uw functie:

1. Schakel de beheerde identiteit in voor uw logische app en stel de toegang van die identiteit in op de doel bron. Zie [toegang tot Azure-resources verifiëren door beheerde identiteiten te gebruiken in azure Logic apps](../logic-apps/create-managed-service-identity.md).

1. Schakel in uw functie-app verificatie in met de volgende stappen:

   * [Anonieme verificatie instellen in uw functie](#set-authentication-function-app)
   * [Azure AD-verificatie instellen in uw functie-app](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Anonieme verificatie instellen in uw functie

Als u de beheerde identiteit van uw logische app in uw functie wilt gebruiken, moet u het verificatie niveau van uw functie instellen op anoniem. Anders genereert uw logische app de fout ' onjuiste aanvraag '.

1. Zoek in het [Azure Portal](https://portal.azure.com)de functie-app en selecteer deze. Deze stappen gebruiken ' FabrikamFunctionApp ' als de voor beeld-functie-app.

1. Selecteer **platform functies** in het deel venster functie-app. Onder **ontwikkelingsprogram ma's** selecteert u **geavanceerde hulp middelen (kudu)**.

   ![Geavanceerde hulp middelen voor kudu openen](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Selecteer op de titel balk van de kudu-website in het menu **debug console** de optie **cmd**.

   ![Selecteer de optie ' CMD ' in het menu Debug console](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Nadat de volgende pagina wordt weer gegeven, selecteert u in de lijst met mappen **site**  >  **wwwroot**  >  *uw-functie*. Deze stappen gebruiken ' FabrikamAzureFunction ' als voorbeeld functie.

   ![Selecteer ' site ' > ' wwwroot ' > uw functie](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Open het `function.json` bestand om het te bewerken.

   ![Klik op bewerken voor ' function.jsop ' bestand](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. Controleer in het `bindings` object of de `authLevel` eigenschap bestaat. Als de eigenschap bestaat, stelt u de waarde van de eigenschap in op `anonymous` . Als dat niet het geval is, voegt u die eigenschap toe en stelt u de waarde in.

   ![De eigenschap authLevel toevoegen en instellen op anoniem](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Wanneer u klaar bent, slaat u de instellingen op en gaat u verder met de volgende sectie.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Azure AD-verificatie instellen voor uw functie-app

Voordat u met deze taak begint, zoekt en plaatst u deze waarden voor later gebruik:

* De object-ID die wordt gegenereerd voor de door het systeem toegewezen identiteit die uw logische app vertegenwoordigt

  * Als u deze object-ID wilt genereren, moet u de door [het systeem toegewezen identiteit van uw logische app inschakelen](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * Als u deze object-ID niet wilt vinden, opent u de logische app in de ontwerp functie voor logische apps. Selecteer onder **instellingen** in het menu van de logische app de optie **identiteits**  >  **systeem toegewezen**.

* De Directory-ID voor uw Tenant in Azure Active Directory (Azure AD)

  Als u de Directory-ID van uw Tenant wilt ophalen, kunt u de [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) Power shell-opdracht uitvoeren. Of voer de volgende stappen uit in het Azure Portal:

  1. Zoek in het [Azure Portal](https://portal.azure.com)de functie-app en selecteer deze.

  1. Zoek en selecteer uw Azure AD-Tenant. Deze stappen gebruiken ' fabrikam ' als voor beeld-Tenant.

  1. Klik in het menu van de Tenant onder **beheren** op **Eigenschappen**.

  1. Kopieer de Directory-ID van uw Tenant, bijvoorbeeld en sla deze ID op voor later gebruik.

     ![De Directory-ID van de Azure AD-Tenant zoeken en kopiëren](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* De resource-ID voor de doel resource waartoe u toegang wilt krijgen

  * Als u deze resource-Id's wilt vinden, controleert u de [Azure-Services die ondersteuning bieden voor Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Deze resource-ID moet exact overeenkomen met de waarde die door Azure AD wordt verwacht, inclusief alle vereiste afsluitende slashes.

  Deze resource-ID is ook dezelfde waarde die u later in de eigenschap **doel groep** gebruikt bij het [instellen van uw functie actie om de door het systeem toegewezen identiteit te gebruiken](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

U bent nu klaar om Azure AD-verificatie in te stellen voor uw functie-app.

1. Zoek in het [Azure Portal](https://portal.azure.com)de functie-app en selecteer deze.

1. Selecteer **platform functies** in het deel venster functie-app. Onder **netwerken** selecteert u **verificatie/autorisatie**.

   ![Instellingen voor verificatie en autorisatie weer geven](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Wijzig de **app service verificatie** -instelling in **op aan**. Selecteer in de lijst **actie die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** de optie **Aanmelden met Azure Active Directory**. Selecteer onder **Azure Active Directory** de optie **Verificatieproviders**.

   ![Verificatie met Azure AD inschakelen](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. Voer de volgende stappen uit in het deel venster **instellingen van Azure Active Directory** :

   1. Stel de **beheer modus** in op **Geavanceerd**.

   1. Voer in de eigenschap **client-id** de object-id in voor de door het systeem toegewezen identiteit van uw logische app.

   1. Voer in de URL-eigenschap van de **Uitgever** de `https://sts.windows.net/` URL in en voeg de Directory-id van uw Azure AD-Tenant toe.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Voer in de eigenschap **toegestane token doel groepen** de resource-id in voor de doel resource die u wilt openen.

      Deze resource-ID is dezelfde waarde die u later in de eigenschap **doel groep** gebruikt bij het [instellen van uw functie actie om de door het systeem toegewezen identiteit te gebruiken](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   Uw versie ziet er op dit moment ongeveer als volgt uit:

   ![Verificatie-instellingen Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Wanneer u gereed bent, selecteert u **OK**.

1. Ga terug naar de ontwerp functie voor logische apps en volg de [stappen om toegang te verifiëren met de beheerde identiteit](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logic apps connectors](../connectors/apis-list.md)
