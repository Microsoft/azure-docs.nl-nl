---
title: "Zelfstudie: REST API's gebruiken"
description: In deze zelfstudie wordt beschreven hoe u de Azure Purview REST API's gebruikt om toegang te krijgen tot de inhoud van uw catalogus.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98951240"
---
# <a name="tutorial-use-the-rest-apis"></a>Zelfstudie: REST API's gebruiken

In deze zelfstudie leert u hoe u de Azure Purview REST API's kunt gebruiken. Iedereen die gegevens wil verzenden naar een Azure Purview-catalogus, neemt de catalogus op als onderdeel van een geautomatiseerd proces of bouwt een eigen gebruikerservaring op basis van de catalogus. Hiervoor kunnen de REST API's worden gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Service-principal (toepassing) maken.
> * Uw catalogus configureren om een vertrouwensrelatie met de service-principal (toepassing) te verkrijgen.
> * De documentatie over REST API's weergeven.
> * De benodigde waarden voor het gebruik van de REST-API's verzamelen.
> * De Postman-client gebruiken om de REST API's aan te roepen.
> * Een .NET-client genereren om de REST API's aan te roepen.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* U moet een bestaand Azure Purview-account hebben om aan de slag te gaan. Als u geen catalogus hebt, raadpleegt u de [snelstartgids voor het maken van een Azure Purview-account](create-catalog-portal.md).

* Als u gegevens aan uw catalogus wilt toevoegen, raadpleegt u de [zelfstudie voor het uitvoeren van de starterkit en het scannen van gegevens](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Service-principal (toepassing) maken

Een REST API-client kan toegang tot de catalogus krijgen als de client over een service-principal (toepassing) beschikt en over een identiteit die de catalogus herkent en die zodanig is geconfigureerd dat de service-principal wordt vertrouwd. Wanneer u REST API-aanroepen naar de catalogus uitvoert, wordt gebruikgemaakt van de identiteit van de service-principal.

Klanten die bestaande service-principals (toepassings-id's) hebben gebruikt, kennen een hoge mate van uitval. Daarom wordt u aangeraden een nieuwe service-principal te maken om API's aan te roepen.

Ga als volgt te werk als u een nieuwe service-principal wilt maken:

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Azure Active Directory**.
1. Selecteer op de pagina **Azure Active Directory** de optie **App-registraties** in het linkerdeelvenster.
1. Selecteer **Nieuwe registratie**.
1. Op de pagina **Een toepassing registreren** gaat u als volgt te werk:
    1. Voer een **naam** in voor de toepassing (de naam voor de service-principal).
    1. Selecteer **Alleen accounts in deze organisatiemap (alleen _&lt;de naam van de tenant&gt;_ - één tenant)** .
    1. Voor **Omleidings-URI (optioneel)** selecteert u **Web** en voert u een waarde in. Deze waarde hoeft geen geldige URI te zijn.
    1. Selecteer **Registreren**.
1. Kopieer op de pagina voor de service-principal de waarden van de **weergavenaam** en de **toepassings(client)-id** om deze voor later te bewaren.

   De toepassings-id is de waarde `client_id` in de voorbeeldcode.

Als u de service-principal (toepassing) wilt gebruiken, moet u het wachtwoord ervoor ophalen. U doet dit als volgt:

1. Selecteer in Azure Portal de optie **Azure Active Directory** en selecteer vervolgens **App-registraties** in het linkerdeelvenster.
1. Selecteer de service-principal (toepassing) in de lijst.
1. Selecteer **Certificaten en geheimen** in het linkerdeelvenster.
1. Selecteer **Nieuw clientgeheim**.
1. Voer op de pagina **Een clientgeheim toevoegen** een **beschrijving** in, selecteer een vervaltijd onder **Verloopt** en selecteer vervolgens **Toevoegen**.

   Op de pagina **Clientgeheimen** is de tekenreeks in de kolom **Waarde** van het nieuwe geheim uw wachtwoord. Sla deze waarde op.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Schermopname van een clientgeheim.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Uw catalogus configureren om een vertrouwensrelatie met de service-principal (toepassing) te verkrijgen

Azure Purview configureren om een vertrouwensrelatie met de nieuwe service-principal te verkrijgen:

1. Ga naar uw Purview-account

1. Selecteer op de pagina **Purview-account** het tabblad **Toegangsbeheer (IAM)**

1. Klik op **+ Toevoegen**

1. Selecteer **Roltoewijzing toevoegen**

1. Typ **Purview-gegevenscurator** voor de rol

    > [!Note]
    > Zie [Catalogusmachtigingen](catalog-permissions.md) voor meer informatie over de machtigingen voor de Azure Purview-gegevenscatalogus. Als u bijvoorbeeld toestemming nodig hebt om de scan te activeren, moet het roltype **Purview-gegevensbronbeheerder** zijn.

1. Laat bij **Toegang toewijzen aan** de standaardwaarde **Gebruiker, groep of service-principal** staan

1. Voer bij **selecteren** de naam in van de Previosly die u wilt toewijzen en klik vervolgens op de naam van de service in het resultaten venster.

1. Klik op **Opslaan**

U hebt de service-principal nu als een toepassingsbeheerder geconfigureerd, waardoor deze de inhoud naar de catalogus kan verzenden.

## <a name="view-the-rest-apis-documentation"></a>De documentatie over REST API's weergeven

Als u de API Swagger-documentatie wilt weergeven, downloadt u [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip), pakt u de bestanden uit en opent u index.html.

Als u meer wilt weten over de geavanceerde zoek- en suggestie-API van Azure Purview, raadpleegt u de documentatie over het REST API-zoekfilter. De door AutoRest gegenereerde client ondersteunt momenteel geen aangepaste zoekparameters. Als tijdelijke oplossing kunt u het document over het zoekfilter volgen om filterklassen in code te definiëren als API-aanroepparameters. Het document index.html bevat voorbeelden van deze API's.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Verzamel de benodigde waarden om de REST-API's te gebruiken

Zoek de volgende waarden en sla ze op:

* Tenant-id:
  * Selecteer in [Azure Portal](https://portal.azure.com) de optie **Azure Active Directory**.
  * In de sectie **Beheren** in het linkerdeelvenster, selecteert u **Eigenschappen**, zoek de **tenant-id** en selecteer het pictogram **Naar Klembord kopiëren** om de waarde op te slaan.
* Atlas-eindpunt:
  * Selecteer op de [pagina Azure Purview-accounts](https://aka.ms/purviewportal) in Azure Portal het Purview-account in de lijst.
  * Selecteer **Eigenschappen**, zoek **Atlas-eindpunt** en selecteer het pictogram **Naar Klembord kopiëren** om de waarde op te slaan. Verwijder het gedeelte *https://* van de tekenreeks wanneer u deze later gebruikt.
* Accountnaam:
  * Extraheer de naam van uw catalogus uit de tekenreeks van het Atlas-eindpunt. Als uw Atlas-eindpunt bijvoorbeeld `https://ThisIsMyCatalog.catalog.purview.azure.com`is, is `ThisIsMyCatalog` uw accountnaam.

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>De Postman-client gebruiken om de REST API's aan te roepen

1. Installeer de [Postman-client](https://www.getpostman.com/).
1. Selecteer **Import** vanuit de client en gebruik [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Selecteer **Collections** en vervolgens **Test**.
1. Selecteer **Get Token**:
    1. In de URL naast POST vervangt u *&lt;your-tenant-id&gt;* door de tenant-id die u in de vorige sectie hebt gekopieerd.
    1. Selecteer het tabblad **Body** en vervang de tijdelijke aanduidingen in het pad en de hoofdtekst uit de vorige stap.

       Nadat u **Send** hebt geselecteerd, bevat de antwoordtekst een JSON-structuur met inbegrip van de naam *access_token* en een tekenreekswaarde tussen aanhalingstekens. 
    1. Kopieer de waarde van het Bearer-token (zonder aanhalingstekens) voor gebruik in de volgende stap.

1. Selecteer **/v2/types/typedefs**:
    1. Vervang de tijdelijke aanduiding in het pad door de waarde van het Atlas-eindpunt. Deze waarde kan worden verkregen door naar de catalogus in de Ibiza-portal te gaan en op overview te klikken. 

       Het Bearer-token is vanaf de eerste stap ingesteld (u kunt het ook handmatig instellen op het tabblad Authorization).

    1. Selecteer **Send** om het antwoord op te halen.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Een .NET-client genereren om de REST API's aan te roepen

### <a name="install-autorest"></a>AutoRest installeren



1. [Install Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md) (Node.js installeren).
1. Open PowerShell en voer de volgende opdracht uit:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>rest-api-specs.zip downloaden en de client maken

1. Download [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) en extraheer de bestanden.
1. Voer vanuit de uitgepakte map rest-api-specs de volgende opdracht uit in PowerShell:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   De uitvoer van dit proces genereert een map PurviewCatalogClient en een submap CSharp in de map rest-api-specs.

### <a name="create-a-sample-net-console-application"></a>Een .NET-voorbeeldconsoletoepassing maken

1. Open Visual Studio 2019. Deze instructies zijn getest met de gratis communityeditie.
1. Op de pagina **Een nieuw project maken** maakt u een **Console-app (.NET Core)** -project in C#.
1. Kopieer en plak de gegeven [voorbeeldcode](#sample-code-for-the-console-application).
1. Vervang *accountName*, *servicePrincipalId*, *servicePrincipalKey* en *tenantId* door de waarden die u eerder hebt verzameld.
1. Gebruik **Solution Explorer** om een map met de naam **Generated** aan het Visual Studio-project toe te voegen.
1. Kopieer de map rest-api-specs\PurviewCatalogClient\CSharp die u eerder hebt gemaakt naar de map \Generated. Gebruik Verkenner of de opdrachtregelprompt voor de kopieerbewerking. Hiermee wordt Visual Studio geactiveerd en worden de bestanden automatisch aan het project toegevoegd.
1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **NuGet-pakketten beheren**.
1. Selecteer het tabblad **Browse**. Selecteer **Microsoft.Rest.ClientRuntime**.
1. Controleer of de versie ten minste 2.3.21 is en selecteer **Installeren**.
1. Maak de toepassing en voer deze uit.

De voorbeeldcode retourneert een telling van het aantal typedefs in de catalogus en laat zien hoe roltoewijzingen moeten worden afgehandeld. Zie `DoRoleAssignmentOperations()` in de voorbeeldcode voor meer informatie. Zie [Project Setup](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md) (Projectinstallatie) voor meer informatie over het project.

### <a name="sample-code-for-the-console-application"></a>Voorbeeldcode voor de consoletoepassing

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevensbronnen beheren](manage-data-sources.md)
