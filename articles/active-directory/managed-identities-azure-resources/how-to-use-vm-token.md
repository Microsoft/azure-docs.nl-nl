---
title: Beheerde identiteiten op een virtuele machine gebruiken om een toegangsken te verkrijgen - Azure AD
description: Stapsgewijs instructies en voorbeelden voor het gebruik van beheerde identiteiten voor Azure-resources op een virtuele machine om een OAuth-toegangsteken te verkrijgen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ee7739d9dbfd34190dc1e856b98fdd21be15743
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364937"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Beheerde identiteiten gebruiken voor Azure-resources op een Azure-VM om een toegangs token te verkrijgen 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

Dit artikel bevat verschillende code- en scriptvoorbeelden voor het verkrijgen van een token, evenals richtlijnen voor belangrijke onderwerpen zoals het afhandelen van verloop van token en HTTP-fouten. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Als u van plan bent om de Azure PowerShell in dit artikel te gebruiken, moet u de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Bij alle voorbeeldcode/scripts in dit artikel wordt ervan uitgenomen dat de client wordt uitgevoerd op een virtuele machine met beheerde identiteiten voor Azure-resources. Gebruik de functie 'Verbinding maken' van de virtuele machine in Azure Portal om op afstand verbinding te maken met uw virtuele machine. Zie Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure Portal of een van de variantartikelen (met behulp van PowerShell, CLI, een sjabloon of een Azure SDK) voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources op een [VM.](qs-configure-portal-windows-vm.md) 

> [!IMPORTANT]
> - De beveiligingsgrens van beheerde identiteiten voor Azure-resources is de resource die wordt gebruikt. Alle code/scripts die op een virtuele machine worden uitgevoerd, kunnen tokens aanvragen en ophalen voor alle beheerde identiteiten die op de virtuele machine beschikbaar zijn. 

## <a name="overview"></a>Overzicht

Een clienttoepassing kan beheerde identiteiten aanvragen voor een [app-token voor azure-resources](../develop/developer-glossary.md#access-token) voor toegang tot een bepaalde resource. Het token is gebaseerd [op de beheerde identiteiten voor de service-principal voor Azure-resources.](overview.md#managed-identity-types) Het is dus niet nodig dat de client zichzelf registreert om een toegangs token te verkrijgen onder een eigen service-principal. Het token is geschikt voor gebruik als bearer-token in [service-naar-service-aanroepen waarvoor clientreferenties zijn vereist.](../develop/v2-oauth2-client-creds-grant-flow.md)

| Koppeling | Beschrijving |
| -------------- | -------------------- |
| [Een token downloaden met HTTP](#get-a-token-using-http) | Protocoldetails voor beheerde identiteiten voor token-eindpunt van Azure-resources |
| [Een token downloaden met behulp van de bibliotheek Microsoft.Azure.Services.AppAuthentication voor .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Voorbeeld van het gebruik van de bibliotheek Microsoft.Azure.Services.AppAuthentication vanuit een .NET-client
| [Een token op te halen met C #](#get-a-token-using-c) | Voorbeeld van het gebruik van beheerde identiteiten voor het REST-eindpunt van Azure-resources vanaf een C#-client |
| [Een token krijgen met Java](#get-a-token-using-java) | Voorbeeld van het gebruik van beheerde identiteiten voor het REST-eindpunt van Azure-resources vanuit een Java-client |
| [Een token op halen met Go](#get-a-token-using-go) | Voorbeeld van het gebruik van beheerde identiteiten voor het REST-eindpunt van Azure-resources vanuit een Go-client |
| [Een token op te halen met Azure PowerShell](#get-a-token-using-azure-powershell) | Voorbeeld van het gebruik van beheerde identiteiten voor het REST-eindpunt van Azure-resources vanuit een PowerShell-client |
| [Een token op te halen met behulp van CURL](#get-a-token-using-curl) | Voorbeeld van het gebruik van beheerde identiteiten voor het REST-eindpunt van Azure-resources vanuit een Bash/CURL-client |
| Token-caching verwerken | Richtlijnen voor het verwerken van verlopen toegangstokens |
| [Foutafhandeling](#error-handling) | Richtlijnen voor het afhandelen van HTTP-fouten die worden geretourneerd door de beheerde identiteiten voor het token-eindpunt van Azure-resources |
| [Resource-ID's voor Azure-services](#resource-ids-for-azure-services) | Waar u resource-ID's voor ondersteunde Azure-services kunt krijgen |

## <a name="get-a-token-using-http"></a>Een token downloaden met HTTP 

De fundamentele interface voor het verkrijgen van een toegangs token is gebaseerd op REST, waardoor het toegankelijk is voor elke clienttoepassing die wordt uitgevoerd op de VM die HTTP REST-aanroepen kan uitvoeren. Dit is vergelijkbaar met het Azure AD-programmeermodel, behalve dat de client een eindpunt op de virtuele machine gebruikt (versus een Azure AD-eindpunt).

Voorbeeldaanvraag met behulp van het Azure Instance Metadata Service -eindpunt (IMDS) *(aanbevolen)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | Het HTTP-woord, waarmee wordt aangegeven dat u gegevens wilt ophalen van het eindpunt. In dit geval een OAuth-toegangsteken. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | De beheerde identiteiten voor het azure-resources-eindpunt voor de Instance Metadata Service. |
| `api-version`  | Een queryreeksparameter die de API-versie voor het IMDS-eindpunt aangeeft. Gebruik api-versie `2018-02-01` of hoger. |
| `resource` | Een queryreeksparameter die de URI van de app-id van de doelresource aangeeft. Het wordt ook weergegeven in de `aud` claim (doelgroep) van het uitgegeven token. In dit voorbeeld wordt een token voor toegang Azure Resource Manager, die een URI van de app-id `https://management.azure.com/` heeft. |
| `Metadata` | Een veld voor de header van een HTTP-aanvraag, vereist door beheerde identiteiten voor Azure-resources als een oplossing tegen een SSRF-aanval (Server Side Request Forgery). Deze waarde moet worden ingesteld op 'true', in kleine gevallen. |
| `object_id` | (Optioneel) Een queryreeksparameter die de object_id van de beheerde identiteit aangeeft waar u het token voor wilt gebruiken. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `client_id` | (Optioneel) Een queryreeksparameter die de client_id van de beheerde identiteit aangeeft waar u het token voor wilt gebruiken. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `mi_res_id` | (Optioneel) Een queryreeksparameter die de mi_res_id (Azure Resource ID) aangeeft van de beheerde identiteit waar u het token voor wilt gebruiken. Vereist als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft. |

Voorbeeldreactie:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beschrijving |
| ------- | ----------- |
| `access_token` | Het aangevraagde toegang token. Bij het aanroepen van een beveiligde REST API, wordt het token ingesloten in het aanvraagheaderveld als een bearer-token, zodat de API de aanroeper `Authorization` kan verifiëren. | 
| `refresh_token` | Niet gebruikt door beheerde identiteiten voor Azure-resources. |
| `expires_in` | Het aantal seconden dat het toegangs token geldig blijft, vóór het verlopen, vanaf het moment van uitgifte. Het tijdstip van uitgifte vindt u in de claim van het `iat` token. |
| `expires_on` | De periode waarin het toegangs token verloopt. De datum wordt weergegeven als het aantal seconden van '1970-01-01T0:0:0Z UTC' (komt overeen met de claim van het `exp` token). |
| `not_before` | De periode waarin het toegangs token van kracht wordt en kan worden geaccepteerd. De datum wordt weergegeven als het aantal seconden van '1970-01-01T0:0:0Z UTC' (komt overeen met de claim van het `nbf` token). |
| `resource` | De resource waarvoor het toegangs token is aangevraagd, die overeenkomt met de `resource` queryreeksparameter van de aanvraag. |
| `token_type` | Het type token, een Bearer-toegang token, wat betekent dat de resource toegang kan verlenen tot de bearer van dit token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Een token downloaden met behulp van de bibliotheek Microsoft.Azure.Services.AppAuthentication voor .NET

Voor .NET-toepassingen en -functies is de eenvoudigste manier om met beheerde identiteiten voor Azure-resources te werken via het pakket Microsoft.Azure.Services.AppAuthentication. Met deze bibliotheek kunt u uw code ook lokaal testen op uw ontwikkelmachine, met behulp van uw gebruikersaccount van Visual Studio, [de Azure CLI](/cli/azure)of geïntegreerde Active Directory-verificatie. Zie de Naslag voor [Microsoft.Azure.Services.AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication)voor meer informatie over lokale ontwikkelopties met deze bibliotheek. In deze sectie ziet u hoe u aan de slag gaat met de bibliotheek in uw code.

1. Voeg verwijzingen naar de [NuGet-pakketten Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) toe aan uw toepassing.

2.  Voeg de volgende code toe aan uw toepassing:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Zie het .NET-voorbeeld van [Microsoft.Azure.Services.AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication) en de App Service en [KeyVault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)met beheerde identiteiten voor Azure-resources voor meer informatie over Microsoft.Azure.Services.AppAuthentication en de bewerkingen die het beschikbaar maakt.

## <a name="get-a-token-using-c"></a>Een token op halen met C #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Een token op halen met Java

Gebruik deze [JSON-bibliotheek om](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) een token op te halen met behulp van Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Een token op halen met Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Een token op te halen met Azure PowerShell

In het volgende voorbeeld wordt gedemonstreerd hoe u de beheerde identiteiten voor het REST-eindpunt van Azure-resources van een PowerShell-client gebruikt om:

1. Een toegangstoken verkrijgt.
2. Gebruik het toegangs token om een Azure Resource Manager REST API aan te roepen en informatie over de VM op te halen. Vervang respectievelijk de abonnements-id, de naam van de resourcegroep en de naam van de virtuele machine door `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>` , en `<VM-NAME>` .

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Voorbeeld van het parseren van het toegangsken vanuit het antwoord:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Een token op te halen met behulp van CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Voorbeeld van het parseren van het toegangsken vanuit het antwoord:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Token in de caching

Hoewel de beheerde identiteiten voor het subsysteem van Azure-resources tokens cachen, raden we u ook aan tokencacheopslag in uw code te implementeren. Als gevolg hiervan moet u zich voorbereiden op scenario's waarin de resource aangeeft dat het token is verlopen. 

On-the-wire-aanroepen naar Azure AD resulteren alleen wanneer:

- Cache-missers treedt op vanwege geen token in de beheerde identiteiten voor subsysteemcache van Azure-resources.
- Het token in de cache is verlopen.

## <a name="error-handling"></a>Foutafhandeling

De beheerde identiteiten voor het eindpunt van Azure-resources signaleren fouten via het statuscodeveld van de header van het HTTP-antwoordbericht, ofwel 4xx- of 5xx-fouten:

| Statuscode | Reden van de fout | Hoe kan ik omgaan? |
| ----------- | ------------ | ------------- |
| 404 Niet gevonden. | Het IMDS-eindpunt wordt bijgewerkt. | Opnieuw proberen met exponential backoff. Zie de onderstaande richtlijnen. |
| 429 Te veel aanvragen. |  Limiet voor IMDS-vertraging is bereikt. | Opnieuw proberen met exponential backoff. Zie de onderstaande richtlijnen. |
| 4xx Fout in aanvraag. | Een of meer van de aanvraagparameters waren onjuist. | Doe het niet opnieuw.  Bekijk de foutdetails voor meer informatie.  4xx-fouten zijn ontwerpfouten.|
| 5xx Tijdelijke fout van service. | De beheerde identiteiten voor subsysteem of Azure Active Directory azure-resources hebben een tijdelijke fout geretourneerd. | Het is veilig om het opnieuw te proberen nadat u ten minste één seconde hebt gewacht.  Als u te snel of te vaak een nieuwe poging doet, kunnen IMDS en/of Azure AD een fout met een snelheidslimiet (429) retourneren.|
| timeout | HET IMDS-eindpunt wordt bijgewerkt. | Opnieuw proberen met exponential backoff. Zie de onderstaande richtlijnen. |

Als er een fout optreedt, bevat de bijbehorende HTTP-antwoord-body JSON met de foutdetails:

| Element | Beschrijving |
| ------- | ----------- |
| fout   | Fout-id. |
| error_description | Uitgebreide beschrijving van de fout. **Foutbeschrijvingen kunnen op elk moment worden gewijzigd. Schrijf geen code die vertakt op basis van waarden in de foutbeschrijving.**|

### <a name="http-response-reference"></a>Naslag voor HTTP-antwoorden

Deze sectie documenteert de mogelijke foutreacties. De status '200 OK' is een geslaagd antwoord en het toegangsteken is opgenomen in de JSON van de antwoord body, in het access_token element.

| Statuscode | Fout | Beschrijving van de fout | Oplossing |
| ----------- | ----- | ----------------- | -------- |
| 400 Ongeldige aanvraag | invalid_resource | AADSTS50001: De toepassing met de naam *\<URI\>* is niet gevonden in de tenant met de naam *\<TENANT-ID\>* . Dit kan gebeuren als de toepassing niet is geïnstalleerd door de beheerder van de tenant of als er geen toestemming voor is verleend door een gebruiker in de tenant. Mogelijk hebt u uw verificatieaanvraag verzonden naar de verkeerde tenant.\ | (alleen Linux) |
| 400 Ongeldige aanvraag | bad_request_102 | Vereiste metagegevensheader niet opgegeven | Het veld `Metadata` met de aanvraagheader ontbreekt in uw aanvraag of is onjuist opgemaakt. De waarde moet worden opgegeven als `true` , in kleine gevallen. Zie 'Voorbeeldaanvraag' in de voorgaande REST-sectie voor een voorbeeld.|
| 401 Onbevoegd | unknown_source | Onbekende bron *\<URI\>* | Controleer of uw HTTP GET-aanvraag-URI correct is opgemaakt. Het `scheme:host/resource-path` gedeelte moet worden opgegeven als `http://localhost:50342/oauth2/token` . Zie 'Voorbeeldaanvraag' in de voorgaande REST-sectie voor een voorbeeld.|
|           | invalid_request | Er ontbreekt een vereiste parameter voor de aanvraag, bevat een ongeldige parameterwaarde, bevat een parameter meer dan één keer of is anderszins ongeldig. |  |
|           | unauthorized_client | De client is niet gemachtigd om een toegangs token aan te vragen met behulp van deze methode. | Veroorzaakt door een aanvraag op een VM die geen beheerde identiteiten heeft voor Azure-resources die correct zijn geconfigureerd. Zie [Beheerde identiteiten configureren voor Azure-resources](qs-configure-portal-windows-vm.md) op een VM met behulp van de Azure Portal als u hulp nodig hebt met de VM-configuratie. |
|           | access_denied | De resource-eigenaar of autorisatieserver heeft de aanvraag geweigerd. |  |
|           | unsupported_response_type | De autorisatieserver biedt geen ondersteuning voor het verkrijgen van een toegangs token met behulp van deze methode. |  |
|           | invalid_scope | Het aangevraagde bereik is ongeldig, onbekend of ongeldig. |  |
| 500 Interne serverfout | unknown | Kan het token niet ophalen uit de Active Directory. Zie Logboeken in voor meer informatie *\<file path\>* | Controleer of beheerde identiteiten voor Azure-resources zijn ingeschakeld op de VM. Zie [Beheerde identiteiten configureren voor Azure-resources](qs-configure-portal-windows-vm.md) op een VM met behulp van de Azure Portal als u hulp nodig hebt met de VM-configuratie.<br><br>Controleer ook of uw HTTP GET-aanvraag-URI correct is opgemaakt, met name de resource-URI die is opgegeven in de queryreeks. Zie de voorbeeldaanvraag in de voorgaande REST-sectie voor een voorbeeld of [Azure-services](./services-support-managed-identities.md) die ondersteuning bieden voor Azure AD-verificatie voor een lijst met services en hun respectieve resource-ID's.

## <a name="retry-guidance"></a>Richtlijnen voor opnieuw proberen 

Het is raadzaam om het opnieuw te proberen als u een 404-, 429- of 5xx-foutcode ontvangt (zie [Foutafhandeling](#error-handling) hierboven).

Beperkingslimieten zijn van toepassing op het aantal aanroepen naar het IMDS-eindpunt. Wanneer de drempelwaarde voor beperking wordt overschreden, beperkt het IMDS-eindpunt eventuele verdere aanvragen terwijl de vertraging van kracht is. Tijdens deze periode retourneert het IMDS-eindpunt de HTTP-statuscode 429 ('Te veel aanvragen') en mislukken de aanvragen. 

Voor nieuwe poging raden we de volgende strategie aan: 

| **Strategie voor opnieuw proberen** | **Instellingen** | **Waarden** | **Uitleg** |
| --- | --- | --- | --- |
|ExponentialBackoff |Aantal pogingen<br />Min. uitstel<br />Max. uitstel<br />Delta-uitstel<br />Eerste snelle poging |5<br />0 sec.<br />60 sec.<br />2 sec.<br />onjuist |Poging 1, vertraging 0 sec.<br />Poging 2, vertraging ~2 sec.<br />Poging 3, vertraging ~6 sec.<br />Poging 4, vertraging ~14 sec.<br />Poging 5, vertraging ~30 sec. |

## <a name="resource-ids-for-azure-services"></a>Resource-ID's voor Azure-services

Zie [Azure-services die Azure AD-verificatie](./services-support-managed-identities.md) ondersteunen voor een lijst met resources die Ondersteuning bieden voor Azure AD en die zijn getest met beheerde identiteiten voor Azure-resources en hun respectieve resource-id's.


## <a name="next-steps"></a>Volgende stappen

- Zie Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure Portal om beheerde identiteiten voor [Azure-resources in](qs-configure-portal-windows-vm.md)te Azure Portal.