---
title: Voor beeld van aangepaste vaardigheid met Bing Entiteiten zoeken-API
titleSuffix: Azure Cognitive Search
description: Demonstreert het gebruik van de Bing Entity Search-service in een aangepaste vaardigheid die is toegewezen aan een AI-verrijkte index pijplijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5755e14e53d359fd8b322939bf1325d21536d593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89020181"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Voor beeld: een aangepaste vaardigheid maken met behulp van de Bing Entiteiten zoeken-API

In dit voor beeld leert u hoe u een aangepaste web-API maakt. Deze vaardigheid accepteert locaties, open bare cijfers en organisaties en geeft beschrijvingen voor hen. In het voor beeld wordt een [Azure-functie](https://azure.microsoft.com/services/functions/) gebruikt om de [Bing entiteiten zoeken-API](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) te verpakken zodat deze de aangepaste vaardigheids interface implementeert.

## <a name="prerequisites"></a>Vereisten

+ Meer informatie over het artikel [aangepaste vaardigheden interface](cognitive-search-custom-skill-interface.md) als u niet bekend bent met de invoer-en uitvoer interface die door een aangepaste vaardigheid moet worden geïmplementeerd.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Installeer [Visual Studio 2019](https://www.visualstudio.com/vs/) of hoger, met inbegrip van de Azure Development-workload.

## <a name="create-an-azure-function"></a>Een Azure-functie maken

Hoewel in dit voor beeld een Azure-functie wordt gebruikt om een web-API te hosten, is dit niet vereist.  Als u voldoet aan de [Interface vereisten voor een cognitieve vaardigheid](cognitive-search-custom-skill-interface.md), is de aanpak die u uitvoert, niet van belang. Met Azure Functions kunt u echter eenvoudig een aangepaste vaardigheid maken.

### <a name="create-a-function-app"></a>Een functie-app maken

1. Selecteer **Nieuw** > **Project** in het menu Bestand in Visual Studio.

1. Selecteer in het dialoog venster Nieuw project **geïnstalleerd**, vouw **Visual C#**  >  **Cloud** uit, selecteer **Azure functions**, typ een naam voor uw project en selecteer **OK**. De naam van de functie-app moet geldig zijn als C#-naam ruimte, dus geen onderstrepings tekens, afbreek streepjes of andere niet-alfanumerieke karakters gebruiken.

1. Selecteer **Azure functions v2 (.net core)**. U kunt dit ook doen met versie 1, maar de hieronder geschreven code is gebaseerd op de v2-sjabloon.

1. Selecteer het type dat **http-trigger** moet worden

1. Voor Storage-account kunt u **geen** selecteren, omdat u geen opslag voor deze functie nodig hebt.

1. Selecteer **OK** om het functie project en de door http geactiveerde functie te maken.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Wijzig de code om de Bing Entity Search-service aan te roepen

Visual Studio maakt een project met daarin een klasse die standaardcode voor het gekozen functietype bevat. Met het kenmerk *FunctionName* in de methode wordt de naam van de functie ingesteld. Met het kenmerk *HttpTrigger* wordt aangegeven dat de functie wordt geactiveerd door een HTTP-aanvraag.

Vervang nu alle inhoud van het bestand *Function1. cs* door de volgende code:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// AI enrichment pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Zorg ervoor dat u uw eigen *sleutel* waarde in de `key` constante invoert op basis van de sleutel die u hebt ontvangen bij het aanmelden voor de Bing entity Search-API.

Dit voor beeld bevat alle benodigde code in één bestand voor het gemak. U kunt een iets meer gestructureerde versie van dezelfde vaardigheid vinden in [de Power skills-opslag plaats](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

U kunt de naam van het bestand natuurlijk wijzigen van `Function1.cs` in `BingEntitySearch.cs` .

## <a name="test-the-function-from-visual-studio"></a>De functie testen vanuit Visual Studio

Druk op **F5** om het programma uit te voeren en het gedrag van functies te testen. In dit geval gebruiken we de onderstaande functie om twee entiteiten op te zoeken. Gebruik postman of Fiddler om een oproep te geven zoals hieronder wordt weer gegeven:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Aanvraagbody
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>Antwoord
Er wordt een antwoord weer gegeven dat vergelijkbaar is met het volgende voor beeld:

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>De functie publiceren naar Azure

Wanneer u tevreden bent met het functie gedrag, kunt u het publiceren.

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**. Kies **nieuwe**  >  **publicatie** maken.

1. Als u Visual Studio nog niet hebt verbonden met uw Azure-account, selecteert u **een account toevoegen....**

1. Volg de aanwijzingen op het scherm. U wordt gevraagd een unieke naam op te geven voor uw app service, het Azure-abonnement, de resource groep, het hosting plan en het opslag account dat u wilt gebruiken. U kunt een nieuwe resource groep, een nieuw hosting plan en een opslag account maken als u deze nog niet hebt. Wanneer u klaar bent, selecteert u **maken**

1. Nadat de implementatie is voltooid, ziet u de URL van de site. Het is het adres van uw functie-app in Azure. 

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de resource groep en zoek naar de `EntitySearch` functie die u hebt gepubliceerd. Onder de sectie **beheren** ziet u de host-sleutels. Selecteer het **Kopieer** pictogram voor de *standaard* -host-sleutel.  

## <a name="test-the-function-in-azure"></a>De functie in azure testen

Nu u de standaardhostcode hebt, moet u de functie als volgt testen:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Aanvraagbody
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

In dit voor beeld moet u hetzelfde resultaat opleveren dat u eerder hebt gezien bij het uitvoeren van de functie in de lokale omgeving.

## <a name="connect-to-your-pipeline"></a>Verbinding maken met uw pijp lijn
Nu u een nieuwe aangepaste vaardigheid hebt, kunt u deze toevoegen aan uw vaardig heden. In het onderstaande voor beeld ziet u hoe u de vaardigheid aanroept om beschrijvingen toe te voegen aan organisaties in het document (dit kan worden uitgebreid om ook te werken met locaties en personen). Vervang door `[your-entity-search-app-name]` de naam van uw app.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Hier tellen we hoe de ingebouwde [vaardigheid van entiteits herkenning](cognitive-search-skill-entity-recognition.md) aanwezig is in de vaardig heden en om het document te verrijken met de lijst met organisaties. Ter referentie is hier een vaardigheids configuratie voor het uitpakken van entiteiten die voldoende is voor het genereren van de gegevens die we nodig hebben:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Volgende stappen
Gefeliciteerd! U hebt uw eerste aangepaste vaardigheid gemaakt. U kunt nu hetzelfde patroon volgen om uw eigen aangepaste functionaliteit toe te voegen. Klik op de volgende koppelingen voor meer informatie.

+ [Power vaardig heden: een opslag plaats met aangepaste vaardig heden](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Een aangepaste vaardigheid toevoegen aan een AI-verrijkings pijplijn](cognitive-search-custom-skill-interface.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardig heden maken (REST)](/rest/api/searchservice/create-skillset)
+ [Verrijkte velden toewijzen](cognitive-search-output-field-mapping.md)