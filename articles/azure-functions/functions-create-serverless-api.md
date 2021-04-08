---
title: Een HTTP-eind punt in Azure Functions aanpassen
description: Meer informatie over het aanpassen van een HTTP trigger-eind punt in Azure Functions
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7375a46245fbe523ddf0512bb5a55371adff64e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683739"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Een HTTP-eind punt in Azure Functions aanpassen

In dit artikel leert u hoe u met Azure Functions uiterst schaal bare Api's kunt bouwen. Azure Functions wordt geleverd met een verzameling ingebouwde HTTP-triggers en-bindingen, waarmee u eenvoudig een eind punt kunt ontwerpen in verschillende talen, waaronder Node.js, C# en meer. In dit artikel gaat u een HTTP-trigger aanpassen voor het verwerken van specifieke acties in het API-ontwerp. U bereidt zich ook voor op het uitbreiden van uw API door deze te integreren met Azure Functions-proxy's en model-Api's in te stellen. Deze taken worden uitgevoerd boven op de serverloze Compute-omgeving, zodat u zich geen zorgen hoeft te maken over het schalen van resources. u kunt zich gewoon richten op uw API-logica.

## <a name="prerequisites"></a>Vereisten 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

De resulterende functie wordt gebruikt voor de rest van dit artikel.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="customize-your-http-function"></a>Uw HTTP-functie aanpassen

De functie HTTP-activering is standaard zo geconfigureerd dat elke HTTP-methode wordt geaccepteerd. U kunt ook de standaard-URL gebruiken `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . In deze sectie wijzigt u de functie zodat deze alleen reageert op aanvragen ophalen met `/api/hello` . 

1. Navigeer in de Azure-portal naar uw functie. Selecteer **integratie** in het linkermenu en selecteer vervolgens **http (vereist)** onder **trigger**.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="Een HTTP-functie aanpassen":::

1. Gebruik de HTTP-trigger instellingen zoals opgegeven in de volgende tabel.

    | Veld | Voorbeeldwaarde | Beschrijving |
    |---|---|---|
    | Routesjabloon | /hello | Hiermee wordt bepaald welke route wordt gebruikt om deze functie aan te roepen |
    | Autorisatieniveau | Anoniem | Optioneel: dit maakt uw functie toegankelijk zonder een API-sleutel |
    | Geselecteerde HTTP-methoden | GET | Hiermee is het alleen geselecteerde HTTP-methoden toegestaan om deze functie aan te roepen |

    U hebt het `/api` voor voegsel van het basis traject niet in de route sjabloon ingevoegd, omdat het wordt verwerkt door een globale instelling.

1. Selecteer **Opslaan**.

Zie [Azure functions HTTP-bindingen](./functions-bindings-http-webhook.md)voor meer informatie over het aanpassen van http-functies.

### <a name="test-your-api"></a>Uw API testen

Test vervolgens uw functie om te zien hoe deze werkt met het nieuwe API-Opper vlak:
1. Selecteer op de pagina functie de optie **code + test** in het menu links.

1. Selecteer **functie-URL ophalen** in het bovenste menu en kopieer de URL. Controleer of het pad nu wordt gebruikt `/api/hello` .
 
1. Kopieer de URL naar een nieuw browsertabblad of de REST-client van uw voorkeur. 

   Browsers gebruiken standaard ophalen.
 
1. Voeg para meters toe aan de query reeks in uw URL. 

   Bijvoorbeeld `/api/hello/?name=John`.
 
1. Druk op ENTER om te controleren of het werkt. U ziet het antwoord '*Hallo Piet*'.

1. U kunt ook proberen het eind punt aan te roepen met een andere HTTP-methode om te bevestigen dat de functie niet is uitgevoerd. Gebruik hiervoor een REST-client, zoals krul, Postman of Fiddler.

## <a name="proxies-overview"></a>Overzicht van Azure Functions-proxy's

In de volgende sectie maakt u uw API via een proxy. Met Azure Functions-proxy's kunt u aanvragen doorsturen naar andere resources. U definieert een HTTP-eind punt net als met de HTTP-trigger. In plaats van code te schrijven om uit te voeren wanneer dat eind punt wordt aangeroepen, geeft u een URL naar een externe implementatie. Als u dit doet, kunt u meerdere API-bronnen samen stellen in één API-Opper vlak, waarmee clients eenvoudig kunnen worden verbruikt. Dit is handig als u uw API als micro Services wilt maken.

Een proxy kan naar elke HTTP-bron verwijzen, zoals:
- Azure Functions 
- API Apps in [Azure App Service](../app-service/overview.md)
- Docker-containers in [App Service in Linux](../app-service/overview.md#app-service-on-linux)
- Elke andere gehoste API

Zie [Werken met Azure Functions-proxy's] voor meer informatie over proxy's.

## <a name="create-your-first-proxy"></a>Uw eerste proxy maken

In deze sectie maakt u een nieuwe proxy die fungeert als een frontend voor uw algemene API. 

### <a name="setting-up-the-frontend-environment"></a>De front-endomgeving instellen

Herhaal de stappen uit [Een functie-app maken](./functions-get-started.md) om een nieuwe functie-app te maken waarin u uw proxy maakt. De URL van deze nieuwe app fungeert als de front-end voor de API en de functie-app die u eerder hebt bewerkt, fungeert als back-end.

1. Navigeer naar uw nieuwe front-end functie-app in de portal.
1. Selecteer **Platformfuncties** en kies **Toepassingsinstellingen**.
1. Schuif omlaag naar **Toepassings instellingen**, waarbij sleutel-waardeparen worden opgeslagen en maak een nieuwe instelling met de sleutel `HELLO_HOST` . Stel de waarde ervan in op de host van uw back-end functie-app, zoals `<YourBackendApp>.azurewebsites.net`. Deze waarde maakt deel uit van de URL die u eerder hebt gekopieerd bij het testen van uw HTTP-functie. Later in de configuratie verwijst u naar deze instelling.

    > [!NOTE] 
    > App-instellingen worden aanbevolen voor de configuratie van de host om een in code vastgelegde omgevingsafhankelijkheid voor de proxy te voorkomen. Het gebruik van app-instellingen betekent dat u de proxyconfiguratie tussen omgevingen kunt verplaatsen en de omgevingsspecifieke app-instellingen worden toegepast.

1. Selecteer **Opslaan**.

### <a name="creating-a-proxy-on-the-frontend"></a>Een proxy maken op de front-end

1. Ga terug naar de front-end-functie-app in de portal.

1. Selecteer **proxy's** in het menu aan de linkerkant en selecteer vervolgens **toevoegen**. 

1. Gebruik op de pagina **nieuwe proxy** de instellingen in de volgende tabel en selecteer vervolgens **maken**.

    | Veld | Voorbeeldwaarde | Beschrijving |
    |---|---|---|
    | Name | HelloProxy | Een beschrijvende naam die alleen wordt gebruikt voor beheer |
    | Routesjabloon | /api/remotehello | Hiermee wordt bepaald welke route wordt gebruikt om deze proxy aan te roepen |
    | URL van back-end | https://%HELLO_HOST%/api/hello | Geeft het eindpunt aan waarnaar de aanvraag moet worden geproxied |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Een proxy maken":::

    Azure Functions-proxy's levert niet het `/api` basispad voor het basis traject dat moet worden opgenomen in de route sjabloon. De `%HELLO_HOST%` syntaxis verwijst naar de app-instelling die u eerder hebt gemaakt. De omgezette URL verwijst naar uw oorspronkelijke functie.

1. Probeer uw nieuwe proxy uit door de URL van de proxy te kopiëren en deze te testen in de browser of met uw favoriete HTTP-client:
    - Gebruik voor een anonieme functie:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - Voor een functie met autorisatie gebruik:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>Een mock-API maken

Vervolgens gebruikt u een proxy om een model-API voor uw oplossing te maken. Met deze proxy kan client ontwikkeling worden uitgevoerd, zonder dat de back-end volledig is geïmplementeerd. Later in de ontwikkeling kunt u een nieuwe functie-app maken die deze logica ondersteunt en uw proxy ernaar omleiden.

Als u deze model-API wilt maken, maakt u een nieuwe proxy met de [app service-editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Navigeer eerst naar uw nieuwe functie-app in de portal. Selecteer **platform functies** en onder **ontwikkelingsprogram ma's** vindt u **app service-editor**. De App Service-editor wordt geopend op een nieuw tabblad.

Selecteer `proxies.json` in de linkernavigatiebalk. In dit bestand wordt de configuratie voor al uw proxy's opgeslagen. Als u een van de [functies voor implementatie methoden](./functions-continuous-deployment.md)gebruikt, onderhoudt u dit bestand in broncode beheer. Zie [Geavanceerde configuratie van proxy's](./functions-proxies.md#advanced-configuration) voor meer informatie over dit bestand.

Als u tot nu toe hebt gevolgd, moet uw proxies.jser als volgt uitzien:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Vervolgens voegt u de model-API toe. Vervang uw proxies.jsdoor een bestand met de volgende code:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Met deze code wordt een nieuwe proxy toegevoegd, `GetUserByName` zonder de `backendUri` eigenschap. In plaats van een andere resource aan te roepen, wordt de standaardreactie van Azure Functions-proxy's gewijzigd via het negeren van een antwoord. Het negeren van aanvragen en antwoorden kan ook worden gebruikt in combinatie met een back-end-URL. Deze techniek is vooral nuttig wanneer u een verouderd systeem gebruikt, waarbij u mogelijk kopteksten, query parameters, enzovoort moet wijzigen. Zie [Aanvragen en antwoorden negeren in proxy's](./functions-proxies.md) voor meer informatie over het negeren van aanvragen en antwoorden.

Test uw mock-API door het `<YourProxyApp>.azurewebsites.net/api/users/{username}`-eindpunt aan te roepen met behulp van een browser of de REST-client van uw voorkeur. Vervang _{username}_ door een tekenreekswaarde die een gebruikersnaam vertegenwoordigt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een API kunt bouwen en aanpassen op Azure Functions. U hebt ook geleerd hoe u meerdere API's, inclusief mocks, kunt samenbrengen als een geïntegreerd API-gebied. U kunt deze technieken gebruiken om meer en minder complexe API's te ontwikkelen, die alle worden uitgevoerd op het serverloze rekenmodel dat wordt geleverd door Azure Functions.

De volgende informatiebronnen kunnen nuttig zijn als u uw API verder ontwikkelt:

- [HTTP-bindingen in Azure Functions](./functions-bindings-http-webhook.md)
- [Werken met Azure Functions-proxy's]
- [Een Azure Functions-API documenteren (preview)](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Werken met Azure Functions-proxy's]: ./functions-proxies.md
