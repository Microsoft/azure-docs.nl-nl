---
title: Maak uw OpenAPI-functies beschikbaar met Azure API Management
description: Maak een definitie van een OpenAPI waarmee andere apps en services uw functie in Azure aanroepen.
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47, references_regions
ms.openlocfilehash: 9083ff7d8f65c68ce8d173973a4eda650ac355aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212914"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Een OpenAPI-definitie voor een serverloze API maken met Azure API Management

REST-API's worden vaak beschreven met behulp van de definitie van een OpenAPI. Deze definitie bevat informatie over welke bewerkingen beschikbaar zijn in een API en hoe de gegevens van de aanvraag en respons voor de API moeten worden opgebouwd.

In deze zelfstudie maakt u een functie waarmee wordt bepaald of een noodherstelproces op een windturbine rendabel is. Vervolgens maakt u m.b.v. [Azure API Management](../api-management/api-management-key-concepts.md) een OpenAPI-definitie voor de functie-app zodat de functie kan worden aangeroepen vanuit andere apps en services.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een maken functie in Azure
> * Een OpenAPI-definitie genereren met Azure API Management
> * De definitie testen door het aanroepen van de functie
> * De OpenAPI-definitie downloaden

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>De functie maken

In deze zelfstudie wordt een door HTTP geactiveerde functie gebruikt die twee parameters nodig heeft:

* De tijd (in uren) die er naar schatting nodig is om een turbine te repareren.
* De capaciteit van de turbine in kilowatt. 

De functie berekent vervolgens hoeveel een reparatie kost en hoeveel omzet de turbine in een periode van 24 uur zou kunnen maken. Om in de [Azure-portal](https://portal.azure.com) de door HTTP geactiveerde functie te maken, gaat u als volgt te werk:

1. Selecteer in het linkermenu van uw functie-app **Functies** en selecteer in het bovenste menu vervolgens **Toevoegen**.

1. Selecteer **HTTP-trigger** in het venster **Nieuwe functie**.

1. Bij **Nieuwe functie** voert u `TurbineRepair` in. 

1. Kies **Functie** in de vervolgkeuzelijst **[Autorisatieniveau](functions-bindings-http-webhook-trigger.md#http-auth)** en selecteer vervolgens **Functie maken**.

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="HTTP-functie maken voor OpenAPI":::

1. Selecteer **Code + testen** en selecteer in het vervolgkeuzemenu **run.csx**. Vervang de inhoud van het scriptbestand run.csx C# door de volgende code en klik vervolgens op **Opslaan**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Deze functiecode retourneert het bericht `Yes` of `No` om aan te geven of een noodherstelproces al dan niet rendabel is. Daarnaast retourneert de code de kans op omzet van de turbine en de reparatiekosten.

1. Als u de functie wilt testen, selecteert u **Testen** en het tabblad **Invoer**, voert u de volgende invoer voor de **Hoofdtekst** in en selecteert u **Uitvoeren**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="Test de functie in de Azure Portal":::

    De volgende uitvoer wordt in het tabblad **Uitvoer** geretourneerd:

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

U hebt nu een functie die de kosteneffectiviteit van noodreparaties bepaalt. Vervolgens genereert u de definitie van een OpenAPI voor de functie-app.

## <a name="generate-the-openapi-definition"></a>De OpenAPI-definitie genereren

Om de OpenAPI-definitie te genereren, gaat u als volgt te werk:

1. Selecteer de functie-app, kies in het linkermenu **API Management** en selecteer vervolgens **Nieuwe maken** onder **API Management**.

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="API Management kiezen":::


1. Gebruik de API Management-instellingen zoals die in de volgende tabel zijn opgegeven:

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Wereldwijd unieke naam | Er wordt een naam gegenereerd op basis van de naam van uw functie-app. |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe resource is gemaakt. |  
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Dezelfde resource als uw functie-app, die voor u moet worden ingesteld. |
    | **Locatie** | VS - west | Kies de locatie VS - west. |
    | **Naam van de organisatie** | Contoso | De organisatienaam die in de ontwikkelaarsportal en voor e-mailmeldingen wordt gebruikt. |
    | **E-mailadres van de beheerder** | uw e-mailadres | E-mailadres waarop systeemmeldingen van API Management werden ontvangen. |
    | **Prijscategorie** | Verbruik | De verbruikscategorie is niet in alle regio's beschikbaar. Raadpleeg voor alle prijzen de [pagina met API Management-prijzen](https://azure.microsoft.com/pricing/details/api-management/) |

    ![Nieuwe API Management-service maken](media/functions-openapi-definition/new-apim-service-openapi.png)

1. Kies **Maken** om het API Management-exemplaar te maken. Dit kan enkele minuten duren.

1. Nadat Azure het exemplaar heeft gemaakt, wordt de optie **Application Insights inschakelen** op de pagina ingeschakeld. Selecteer deze optie om logboeken naar dezelfde plek als de functie-app te verzenden en selecteer vervolgens **API koppelen**.

1. **Azure Functions importeren** wordt geopend, waarbij de functie **TurbineRepair** is gemarkeerd. Klik op **Selecteren** om door te gaan.

    ![Azure Functions in API Management importeren](media/functions-openapi-definition/import-function-openapi.png)

1. Accepteer op de pagina **Maken op basis van functie-app** de standaardinstellingen en selecteer vervolgens **Maken**.

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="Maken op basis van functie-app":::

    Azure maakt de API voor de functie.

## <a name="test-the-api"></a>De API testen

Voordat u de OpenAPI-definitie gebruikt, moet u controleren of de API het doet.

1. Selecteer op uw functie-app-pagina achtereenvolgens **API Management**, het tabblad **Testen** en **POST TurbineRepair**. 

1. Voer de volgende code in de **aanvraagbody** in:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Klik op **Verzenden** en bekijk vervolgens het **HTTP-antwoord**.

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="API-testfunctie":::

## <a name="download-the-openapi-definition"></a>De OpenAPI-definitie downloaden

Als uw API zoals verwacht functioneert, kunt u de OpenAPI-definitie downloaden.

1. Selecteer bovenaan de pagina **OpenAPI-definitie downloaden**.
   
   ![OpenAPI-definitie downloaden](media/functions-openapi-definition/download-definition.png)

2. Sla het gedownloade JSON-bestand op en open het. Controleer de definitie.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt de API Management-integratie gebruikt om een OpenAPI-definitie van uw functies te genereren. De definitie in API Management kunt u nu in de portal bewerken. Ook kunt u [meer informatie over API Management](../api-management/api-management-key-concepts.md) raadplegen.

> [!div class="nextstepaction"]
> [De OpenAPI-definitie bewerken in API Management](../api-management/edit-api.md)
