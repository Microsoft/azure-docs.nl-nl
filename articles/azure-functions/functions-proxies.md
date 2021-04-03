---
title: Werken met proxy's in Azure Functions
description: Overzicht van het gebruik van Azure Functions-proxy's
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: fb263239f99bcb4ec4c893b700d5c1cce078659f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601370"
---
# <a name="work-with-azure-functions-proxies"></a>Werken met Azure Functions-proxy's

In dit artikel wordt uitgelegd hoe u Azure Functions-proxy's kunt configureren en gebruiken. Met deze functie kunt u eind punten opgeven in de functie-app die door een andere resource worden geïmplementeerd. U kunt deze proxy's gebruiken om een grote API te splitsen in meerdere functie-apps (zoals in een micro service-architectuur), terwijl er nog steeds één API-Opper vlak voor clients wordt gepresenteerd.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Facturering van Standard-functies is van toepassing op proxy-uitvoeringen. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

## <a name="create-a-proxy"></a><a name="create"></a>Een proxy maken

In deze sectie wordt beschreven hoe u een proxy maakt in de functions-Portal.

1. Open de [Azure Portal]en ga vervolgens naar uw functie-app.
2. Selecteer **nieuwe proxy** in het linkerdeel venster.
3. Geef een naam op voor uw proxy.
4. Configureer het eind punt dat wordt weer gegeven in deze functie-app door de **route sjabloon** en **http-methoden** op te geven. Deze para meters gedragen zich op basis van de regels voor [http-triggers].
5. Stel de **back-end-URL** in op een ander eind punt. Dit eind punt kan een functie zijn in een andere functie-app of een andere API. De waarde hoeft niet statisch te zijn en kan verwijzen naar [Toepassings instellingen] en- [para meters van de oorspronkelijke client aanvraag].
6. Klik op **Create**.

Uw proxy bestaat nu als een nieuw eind punt in uw functie-app. Vanuit een client perspectief is het gelijk aan een http trigger in Azure Functions. U kunt de nieuwe proxy proberen door de proxy-URL te kopiëren en deze te testen met uw favoriete HTTP-client.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Aanvragen en antwoorden wijzigen

Met Azure Functions-proxy's kunt u aanvragen wijzigen in en antwoorden van de back-end. Deze trans formaties kunnen variabelen gebruiken zoals gedefinieerd in [variabelen gebruiken].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>De aanvraag voor het maken van een back-end wijzigen

Standaard wordt de back-end-aanvraag geïnitialiseerd als een kopie van de oorspronkelijke aanvraag. Naast het instellen van de URL van de back-end, kunt u wijzigingen aanbrengen aan de HTTP-methode, headers en query teken reeks parameters. De gewijzigde waarden kunnen verwijzen naar [Toepassings instellingen] en [para meters van de oorspronkelijke client aanvraag].

Back-end-aanvragen kunnen worden gewijzigd in de portal door de sectie *aanvraag onderdrukking* uit te vouwen op de pagina met proxy Details. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Het antwoord wijzigen

Standaard wordt het antwoord van de client geïnitialiseerd als een kopie van het back-end-antwoord. U kunt wijzigingen aanbrengen in de status code van het antwoord, de woord groep reden, de koptekst en de hoofd tekst. De gewijzigde waarden kunnen verwijzen naar [Toepassings instellingen], [para meters van de oorspronkelijke client aanvraag]en [para meters uit het back-end-antwoord].

U kunt back-end-aanvragen in de portal wijzigen door de sectie *antwoorden negeren* van de pagina met proxy details uit te vouwen. 

## <a name="use-variables"></a><a name="using-variables"></a>Variabelen gebruiken

De configuratie voor een proxy hoeft niet statisch te zijn. U kunt de voor waarde om variabelen te gebruiken van de oorspronkelijke client aanvraag, het back-end-antwoord of de toepassings instellingen.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Referentie lokale functies
U kunt gebruiken `localhost` om rechtstreeks naar een functie binnen dezelfde functie-app te verwijzen, zonder een retour proxy-aanvraag.

`"backendUri": "https://localhost/api/httptriggerC#1"` verwijst naar een lokale HTTP-functie die op de route wordt geactiveerd `/api/httptriggerC#1`

 
>[!Note]  
>Als uw functie gebruikmaakt van *functie-, beheer-of sys* -autorisatie niveaus, moet u de code en clientId opgeven, conform de oorspronkelijke functie-URL. In dit geval ziet de verwijzing er als volgt uit: `"backendUri": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` we raden u aan deze sleutels op te slaan in [Toepassings instellingen] en ernaar te verwijzen naar die in uw proxy's. Zo voor komt u dat geheimen worden opgeslagen in de bron code. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Referentie aanvraag parameters

U kunt aanvraag parameters gebruiken als invoer voor de URL-eigenschap van de back-end of als onderdeel van het wijzigen van aanvragen en antwoorden. Sommige para meters kunnen worden gekoppeld aan de route sjabloon die is opgegeven in de configuratie van de basis proxy en anderen kunnen afkomstig zijn van de eigenschappen van de inkomende aanvraag.

#### <a name="route-template-parameters"></a>Route sjabloon parameters
De para meters die worden gebruikt in de route sjabloon kunnen worden verwezen met de naam. De parameter namen worden tussen accolades () geplaatst {} .

Als een proxy bijvoorbeeld een route sjabloon heeft, zoals `/pets/{petId}` , kan de back-end-URL de waarde van `{petId}` , zoals in, bevatten `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` . Als de route sjabloon in een Joker teken wordt beëindigd, zoals `/api/{*restOfPath}` , is de waarde `{restOfPath}` een teken reeks representatie van de resterende padsegmenten van de binnenkomende aanvraag.

#### <a name="additional-request-parameters"></a>Aanvullende aanvraag parameters
Naast de para meters van de route sjabloon kunnen de volgende waarden worden gebruikt in configuratie waarden:

* **{Request. Method}**: de HTTP-methode die wordt gebruikt voor de oorspronkelijke aanvraag.
* **{Request. headers. \<HeaderName\> }**: een koptekst die van de oorspronkelijke aanvraag kan worden gelezen. Vervang door *\<HeaderName\>* de naam van de koptekst die u wilt lezen. Als de koptekst niet is opgenomen in de aanvraag, is de waarde de lege teken reeks.
* **{Request. query string. \<ParameterName\> }**: een query reeks parameter die uit de oorspronkelijke aanvraag kan worden gelezen. Vervang door *\<ParameterName\>* de naam van de para meter die u wilt lezen. Als de para meter niet is opgenomen in de aanvraag, is de waarde de lege teken reeks.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Referentie parameters voor back-end

Antwoord parameters kunnen worden gebruikt als onderdeel van het wijzigen van de reactie op de client. De volgende waarden kunnen worden gebruikt in configuratie waarden:

* **{back-end. Response. code status}**: de HTTP-status code die wordt geretourneerd tijdens het back-end-antwoord.
* **{back-end. Response. statusReason}**: de WACHTWOORDZIN voor http die wordt geretourneerd tijdens het back-end-antwoord.
* **{back-end. Response. headers. \<HeaderName\> }**: een header die kan worden gelezen vanuit het back-end-antwoord. Vervang door *\<HeaderName\>* de naam van de koptekst die u wilt lezen. Als de koptekst niet in het antwoord is opgenomen, is de waarde de lege teken reeks.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Referentie toepassings instellingen

U kunt ook verwijzen naar [Toepassings instellingen die zijn gedefinieerd voor de functie-app](./functions-how-to-use-azure-function-app-settings.md) door de naam van de instelling te omgeven door procent tekens (%).

Bijvoorbeeld: een back-end-URL van *https://%ORDER_PROCESSING_HOST%/api/orders* zou% ORDER_PROCESSING_HOST% vervangen door de waarde van de instelling ORDER_PROCESSING_HOST.

> [!TIP] 
> Gebruik toepassings instellingen voor back-endservers wanneer u meerdere implementaties of test omgevingen hebt. Op die manier kunt u ervoor zorgen dat u altijd praat met de juiste back-end voor die omgeving.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Problemen met Proxy's oplossen

Door de markering toe `"debug":true` te voegen aan een proxy in uw `proxies.json` kunt u logboek registratie voor fout opsporing inschakelen. Logboeken worden opgeslagen in `D:\home\LogFiles\Application\Proxies\DetailedTrace` en zijn toegankelijk via de geavanceerde hulp middelen (kudu). HTTP-antwoorden bevatten ook een `Proxy-Trace-Location` header met een URL voor toegang tot het logboek bestand.

U kunt fouten opsporen in een proxy van de client zijde door een header-set toe te voegen `Proxy-Trace-Enabled` aan `true` . Hiermee wordt ook een tracering naar het bestands systeem vastgelegd en wordt de tracerings-URL als een header in het antwoord geretourneerd.

### <a name="block-proxy-traces"></a>Proxy traceringen blok keren

Om veiligheids redenen wilt u mogelijk niet toestaan dat iedereen die uw service aanroept, een tracering kan genereren. Ze hebben geen toegang tot de tracerings inhoud zonder uw aanmeldings gegevens, maar het genereren van de tracering van resources en geeft aan dat u gebruikmaakt van functie-Proxy's.

Schakel traceringen samen uit door toe `"debug":false` te voegen aan een bepaalde proxy in uw `proxies.json` .

## <a name="advanced-configuration"></a>Geavanceerde configuratie

De proxy's die u configureert, worden opgeslagen in een *proxies.js* in het bestand, dat zich in de hoofdmap van een functie-app-map bevindt. U kunt dit bestand hand matig bewerken en implementeren als onderdeel van uw app wanneer u een van de [implementatie methoden](./functions-continuous-deployment.md) gebruikt die door functies worden ondersteund. 

> [!TIP] 
> Als u een van de implementatie methoden niet hebt ingesteld, kunt u ook werken met de *proxies.js* in het bestand in de portal. Ga naar uw functie-app, selecteer **platform functies** en selecteer vervolgens **app service-editor**. Op die manier kunt u de volledige bestands structuur van de functie-app bekijken en vervolgens wijzigingen aanbrengen.

*Proxies.jsop* is gedefinieerd door een proxy object, dat bestaat uit benoemde proxy's en hun definities. Als uw editor dit ondersteunt, kunt u ook verwijzen naar een [JSON-schema](http://json.schemastore.org/proxies) voor het volt ooien van de code. Een voorbeeld bestand kan er als volgt uitzien:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Elke proxy heeft een beschrijvende naam, zoals *Proxy1* in het vorige voor beeld. Het bijbehorende proxy definitie object wordt gedefinieerd door de volgende eigenschappen:

* **matchCondition**: required-een object dat de aanvragen definieert waarmee de uitvoering van deze proxy wordt geactiveerd. Het bevat twee eigenschappen die worden gedeeld met [http-triggers]:
    * _methoden_: een matrix met de HTTP-methoden waarop de proxy reageert. Als deze niet is opgegeven, reageert de proxy op alle HTTP-methoden op de route.
    * _route_: vereist--definieert de route sjabloon en bepaalt welke aanvraag-url's door uw proxy worden beantwoord. In tegens telling tot HTTP-triggers is er geen standaard waarde.
* **backendUri**: de URL van de back-end-bron waarvoor de aanvraag moet worden geproxy. Deze waarde kan verwijzen naar toepassings instellingen en para meters van de oorspronkelijke client aanvraag. Als deze eigenschap niet is opgenomen, reageert Azure Functions met een HTTP-200 OK.
* **requestOverrides**: een object dat trans formaties in de back-end-aanvraag definieert. Zie [een requestOverrides-object definiëren].
* **responseOverrides**: een object dat trans formaties voor het client antwoord definieert. Zie [een responseOverrides-object definiëren].

> [!NOTE] 
> De eigenschap *route* in azure functions-proxy's voldoet niet aan de eigenschap *routePrefix* van de configuratie van de functie-app-host. Als u een voor voegsel wilt opnemen zoals `/api` , moet dit worden opgenomen in de eigenschap *route* .

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> Afzonderlijke proxy's uitschakelen

U kunt afzonderlijke proxy's uitschakelen door toe te voegen `"disabled": true` aan de proxy in het `proxies.json` bestand. Dit zorgt ervoor dat aanvragen die aan de matchCondition voldoen, 404 retour neren.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a> Toepassings instellingen

Het proxy gedrag kan worden beheerd door verschillende app-instellingen. Deze worden beschreven in de [Naslag informatie over de app-instellingen van functions](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> Gereserveerde tekens (teken reeks opmaak)

Proxy's lezen alle teken reeksen uit een JSON-bestand met behulp van \ als een escape-teken. Proxy's interpreteren ook gekrulde accolades. Bekijk een volledige set met voor beelden hieronder.

|Teken|Escape teken|Voorbeeld|
|-|-|-|
|of|{{or}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Een requestOverrides-object definiëren

Het requestOverrides-object definieert wijzigingen die in de aanvraag zijn aangebracht wanneer de back-end-resource wordt aangeroepen. Het object wordt gedefinieerd door de volgende eigenschappen:

* **back-endserver**: de HTTP-methode die wordt gebruikt om de back-end aan te roepen.
* **back-end. Request. \<ParameterName\> query string.**: een query reeks parameter die kan worden ingesteld voor de aanroep van de back-end. Vervang door *\<ParameterName\>* de naam van de para meter die u wilt instellen. Houd er rekening mee dat als er een lege teken reeks wordt opgegeven, de para meter nog steeds wordt opgenomen in de back-end-aanvraag.
* **back-end. Request. \<HeaderName\> headers.**: een header die kan worden ingesteld voor de aanroep van de back-enddatabase. Vervang door *\<HeaderName\>* de naam van de koptekst die u wilt instellen. Houd er rekening mee dat als er een lege teken reeks wordt opgegeven, de para meter nog steeds wordt opgenomen in de back-end-aanvraag.

Waarden kunnen verwijzen naar toepassings instellingen en para meters van de oorspronkelijke client aanvraag.

Een voorbeeld configuratie kan er als volgt uitzien:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Een responseOverrides-object definiëren

Het requestOverrides-object definieert wijzigingen die worden aangebracht in het antwoord dat wordt teruggestuurd naar de client. Het object wordt gedefinieerd door de volgende eigenschappen:

* **Response. code status**: de HTTP-status code die wordt geretourneerd naar de client.
* **Response. statusReason**: de woord groep met de http-reden die moet worden geretourneerd naar de client.
* **Response. Body**: de teken reeks representatie van de hoofd tekst die wordt geretourneerd naar de client.
* **Response. headers. \<HeaderName\>**: een header die kan worden ingesteld voor de reactie op de client. Vervang door *\<HeaderName\>* de naam van de koptekst die u wilt instellen. Als u de lege teken reeks opgeeft, wordt de header niet in het antwoord opgenomen.

Waarden kunnen verwijzen naar toepassings instellingen, para meters van de oorspronkelijke client aanvraag en para meters uit het back-end-antwoord.

Een voorbeeld configuratie kan er als volgt uitzien:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> In dit voor beeld wordt de hoofd tekst van de reactie direct ingesteld, zodat er geen `backendUri` eigenschap nodig is. In het voor beeld ziet u hoe u Azure Functions-proxy's kunt gebruiken om Api's te model leren.

[Azure-portal]: https://portal.azure.com
[HTTP-triggers]: ./functions-bindings-http-webhook.md
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Een requestOverrides-object definiëren]: #requestOverrides
[Een responseOverrides-object definiëren]: #responseOverrides
[Toepassings instellingen]: #use-appsettings
[Variabelen gebruiken]: #using-variables
[para meters van de oorspronkelijke client aanvraag]: #request-parameters
[para meters van het back-end-antwoord]: #response-parameters
