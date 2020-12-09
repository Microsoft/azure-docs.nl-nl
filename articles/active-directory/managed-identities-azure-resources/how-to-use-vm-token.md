---
title: Beheerde identiteiten op een virtuele machine gebruiken om een toegangs token te verkrijgen-Azure AD
description: Stapsgewijze instructies en voor beelden voor het gebruik van beheerde identiteiten voor Azure-resources op een virtuele machines om een OAuth-toegangs token te verkrijgen.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed64df921326ad4d219f934f7a7bc6860bfc7d8
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861898"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Beheerde identiteiten voor Azure-resources gebruiken op een Azure VM om een toegangs token te verkrijgen 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel vindt u verschillende code-en script voorbeelden voor het verkrijgen van tokens, evenals richt lijnen voor belang rijke onderwerpen, zoals het verwerken van token verloopt en HTTP-fouten. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Als u van plan bent de Azure PowerShell-voor beelden in dit artikel te gebruiken, moet u ervoor zorgen dat u de meest recente versie van [Azure PowerShell](/powershell/azure/install-az-ps)installeert.


> [!IMPORTANT]
> - In alle voorbeeld code/script in dit artikel wordt ervan uitgegaan dat de-client wordt uitgevoerd op een virtuele machine met beheerde identiteiten voor Azure-resources. Gebruik de functie ' Connect ' van de virtuele machine in het Azure Portal om extern verbinding te maken met uw VM. Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele machine met behulp van de Azure Portal](qs-configure-portal-windows-vm.md), of een van de variant artikelen (met behulp van Power shell, CLI, een sjabloon of een Azure SDK), voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources op een virtuele machine. 

> [!IMPORTANT]
> - De beveiligings grens van beheerde identiteiten voor Azure-resources is de resource die wordt gebruikt op. Alle code/scripts die op een virtuele machine worden uitgevoerd, kunnen tokens aanvragen en ophalen voor beheerde beschik bare identiteiten. 

## <a name="overview"></a>Overzicht

Een client toepassing kan beheerde identiteiten aanvragen voor de Azure [-resources app-only-toegangs token](../develop/developer-glossary.md#access-token) voor toegang tot een bepaalde resource. Het token is [gebaseerd op de Service-Principal beheerde identiteiten voor Azure-resources](overview.md#managed-identity-types). Het is dus niet nodig dat de client zichzelf registreert om een toegangs token te verkrijgen onder een eigen service-principal. Het token is geschikt voor gebruik als een Bearer-token in [service-naar-service-aanroepen die client referenties vereisen](../develop/v2-oauth2-client-creds-grant-flow.md).

| Koppeling | Beschrijving |
| -------------- | -------------------- |
| [Een Token ophalen met HTTP](#get-a-token-using-http) | Protocol gegevens voor beheerde identiteiten voor het token eindpunt van Azure-resources |
| [Een Token ophalen met behulp van de micro soft. Azure. Services. AppAuthentication-bibliotheek voor .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Voor beeld van het gebruik van de bibliotheek micro soft. Azure. Services. AppAuthentication van een .NET-client
| [Een Token ophalen met C #](#get-a-token-using-c) | Voor beeld van het gebruik van beheerde identiteiten voor Azure-bronnen REST-eind punt van een C#-client |
| [Een Token ophalen met Java](#get-a-token-using-java) | Voor beeld van het gebruik van beheerde identiteiten voor Azure-bronnen REST-eind punt van een Java-client |
| [Een Token ophalen met behulp van Go](#get-a-token-using-go) | Voor beeld van het gebruik van beheerde identiteiten voor Azure-bronnen REST-eind punt van een go-client |
| [Een Token ophalen met behulp van Azure PowerShell](#get-a-token-using-azure-powershell) | Voor beeld van het gebruik van beheerde identiteiten voor Azure-bronnen REST-eind punt van een Power shell-client |
| [Een Token ophalen met behulp van krul](#get-a-token-using-curl) | Voor beeld van het gebruik van beheerde identiteiten voor Azure-bronnen REST-eind punt van een bash/krul-client |
| Token cache afhandelen | Richt lijnen voor het afhandelen van verlopen toegangs tokens |
| [Foutafhandeling](#error-handling) | Richt lijnen voor het verwerken van HTTP-fouten die zijn geretourneerd door de beheerde identiteiten voor het token eindpunt van Azure-resources |
| [Resource-Id's voor Azure-Services](#resource-ids-for-azure-services) | Bron-Id's ophalen voor ondersteunde Azure-Services |

## <a name="get-a-token-using-http"></a>Een Token ophalen met HTTP 

De fundamentele interface voor het verkrijgen van een toegangs token is gebaseerd op REST, waardoor deze toegankelijk is voor elke client toepassing die op de virtuele machine wordt uitgevoerd en die HTTP REST-aanroepen kan maken. Dit is vergelijkbaar met het Azure AD-programmeer model, met uitzonde ring van de client gebruikt een eind punt op de virtuele machine (VS een Azure AD-eind punt).

Voorbeeld aanvraag met behulp van het Azure Instance Metadata Service (IMDS)-eind punt *(aanbevolen)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | De HTTP-term waarmee wordt aangegeven dat u gegevens wilt ophalen uit het eind punt. In dit geval een OAuth-toegangs token. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | De beheerde identiteiten voor het Azure-bronnen eindpunt voor de Instance Metadata Service. |
| `api-version`  | Een query teken reeks parameter, waarmee de API-versie voor het IMDS-eind punt wordt aangegeven. Gebruik API-versie `2018-02-01` of hoger. |
| `resource` | Een query teken reeks parameter, waarmee de App-ID-URI van de doel resource wordt aangegeven. Het wordt ook weer gegeven in de `aud` claim (publiek) van het uitgegeven token. In dit voor beeld wordt een token aangevraagd voor toegang tot Azure Resource Manager, dat een app-ID-URI van heeft `https://management.azure.com/` . |
| `Metadata` | Een veld met een HTTP-aanvraag header, dat door beheerde identiteiten voor Azure-resources wordt vereist als risico op aanvallen op server zijde vervalsing (SSRF). Deze waarde moet in alle kleine letters worden ingesteld op ' True '. |
| `object_id` | Beschrijving Een query reeks parameter, waarmee de object_id van de beheerde identiteit wordt aangegeven waarvoor u het token wilt voor. Vereist als uw virtuele machine meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `client_id` | Beschrijving Een query reeks parameter, waarmee de client_id van de beheerde identiteit wordt aangegeven waarvoor u het token wilt voor. Vereist als uw virtuele machine meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `mi_res_id` | Beschrijving Een query teken reeks parameter, waarmee de mi_res_id (Azure-Resource-ID) wordt aangegeven van de beheerde identiteit waarvoor u het token wilt voor. Vereist als uw virtuele machine meerdere door de gebruiker toegewezen beheerde identiteiten heeft. |

Voorbeeld aanvraag met behulp van het eind punt voor beheerde identiteiten voor Azure-resources VM extension *(gepland voor afschaffing in januari 2019)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | De HTTP-term waarmee wordt aangegeven dat u gegevens wilt ophalen uit het eind punt. In dit geval een OAuth-toegangs token. | 
| `http://localhost:50342/oauth2/token` | Het eind punt Managed Identities voor Azure resources, waarbij 50342 de standaard poort is en kan worden geconfigureerd. |
| `resource` | Een query teken reeks parameter, waarmee de App-ID-URI van de doel resource wordt aangegeven. Het wordt ook weer gegeven in de `aud` claim (publiek) van het uitgegeven token. In dit voor beeld wordt een token aangevraagd voor toegang tot Azure Resource Manager, dat een app-ID-URI van heeft `https://management.azure.com/` . |
| `Metadata` | Een veld met een HTTP-aanvraag header, dat door beheerde identiteiten voor Azure-resources wordt vereist als risico op aanvallen op server zijde vervalsing (SSRF). Deze waarde moet in alle kleine letters worden ingesteld op ' True '.|
| `object_id` | Beschrijving Een query reeks parameter, waarmee de object_id van de beheerde identiteit wordt aangegeven waarvoor u het token wilt voor. Vereist als uw virtuele machine meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `client_id` | Beschrijving Een query reeks parameter, waarmee de client_id van de beheerde identiteit wordt aangegeven waarvoor u het token wilt voor. Vereist als uw virtuele machine meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|

Voorbeeld antwoord:

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
| `access_token` | Het aangevraagde toegangs token. Wanneer u een beveiligd REST API aanroept, wordt het token in het `Authorization` veld aanvraag header Inge sloten als een Bearer-token, waardoor de API de aanroeper kan verifiëren. | 
| `refresh_token` | Wordt niet gebruikt door beheerde identiteiten voor Azure-resources. |
| `expires_in` | Het aantal seconden dat het toegangs token geldig blijft, vóór verloop tijd, vanaf tijdstip van uitgifte. De tijd van de uitgifte vindt u in de claim van het token `iat` . |
| `expires_on` | De time span op het moment dat het toegangs token verloopt. De datum wordt weer gegeven als het aantal seconden van ' 1970-01-01T0:0: 0Z UTC ' (komt overeen met de claim van het token `exp` ). |
| `not_before` | De time span wanneer het toegangs token van kracht is en kan worden geaccepteerd. De datum wordt weer gegeven als het aantal seconden van ' 1970-01-01T0:0: 0Z UTC ' (komt overeen met de claim van het token `nbf` ). |
| `resource` | De bron waarvoor het toegangs token is aangevraagd, dat overeenkomt met de `resource` query teken reeks parameter van de aanvraag. |
| `token_type` | Het type token, een ' Bearer ' toegangs token, wat betekent dat de bron toegang kan verlenen aan de Bearer van dit token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Een Token ophalen met behulp van de micro soft. Azure. Services. AppAuthentication-bibliotheek voor .NET

Voor .NET-toepassingen en-functies is de eenvoudigste manier om te werken met beheerde identiteiten voor Azure-resources via het pakket micro soft. Azure. Services. AppAuthentication. Met deze bibliotheek kunt u uw code ook lokaal op uw ontwikkel computer testen met behulp van uw gebruikers account uit Visual Studio, de [Azure cli](/cli/azure)of Active Directory geïntegreerde verificatie. Zie de [referentie micro soft. Azure. Services. AppAuthentication](../../key-vault/general/service-to-service-authentication.md)voor meer informatie over de lokale ontwikkelings opties voor deze bibliotheek. In deze sectie wordt beschreven hoe u aan de slag kunt met de bibliotheek in uw code.

1. Voeg referenties toe aan de [micro soft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -en [micro soft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-pakketten voor uw toepassing.

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
    
Voor meer informatie over micro soft. Azure. Services. AppAuthentication en de bewerkingen die worden weer gegeven, raadpleegt u de naslag informatie over [micro soft. Azure. Services. AppAuthentication](../../key-vault/general/service-to-service-authentication.md) en de [app service en de sleutel kluis met beheerde identiteiten voor Azure resources .net-voor beeld](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Een Token ophalen met C #

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

## <a name="get-a-token-using-java"></a>Een Token ophalen met Java

Gebruik deze [JSON-bibliotheek](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) om een token op te halen met behulp van Java.

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

## <a name="get-a-token-using-go"></a>Een Token ophalen met behulp van Go

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

## <a name="get-a-token-using-azure-powershell"></a>Een Token ophalen met behulp van Azure PowerShell

In het volgende voor beeld ziet u hoe u het REST-eind punt beheerde identiteiten voor Azure-bronnen van een Power shell-client kunt gebruiken om:

1. Een toegangstoken verkrijgt.
2. Gebruik het toegangs token om een Azure Resource Manager REST API aan te roepen en informatie over de virtuele machine op te halen. Zorg ervoor dat u de abonnements-ID, de naam van de resource groep en de naam van de virtuele machine vervangt door `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>` `<VM-NAME>` respectievelijk.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Voor beeld voor het parseren van het toegangs token uit het antwoord:
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

## <a name="get-a-token-using-curl"></a>Een Token ophalen met behulp van krul

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Voor beeld voor het parseren van het toegangs token uit het antwoord:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Token cache

Hoewel de beheerde identiteiten voor het Azure-resources-subsysteem worden gebruikt (IMDS/beheerde identiteiten voor de VM-extensie van Azure-resources), worden cache tokens ook aanbevolen voor het implementeren van token caching in uw code. Als gevolg hiervan moet u voorbereiden op scenario's waarin de bron aangeeft dat het token is verlopen. 

Alleen on-the-Wire-aanroepen naar Azure AD result wanneer:
- cache-Misser treedt op als gevolg van een token in de beheerde identiteiten voor het Azure-resources-subsysteem cache
- het token in de cache is verlopen

## <a name="error-handling"></a>Foutafhandeling

De beheerde identiteiten voor Azure resources-eind punt signaleert fouten via het veld status code van de header van het HTTP-antwoord bericht, als 4xx-of 5xx-fouten:

| Statuscode | Fout reden | Omgaan met |
| ----------- | ------------ | ------------- |
| 404 niet gevonden. | Het IMDS-eind punt wordt bijgewerkt. | Probeer het opnieuw met exponentiële uitstel. Zie de onderstaande instructies. |
| 429 te veel aanvragen. |  De IMDS-beperkings limiet is bereikt. | Probeer het opnieuw met exponentiële uitstel. Zie de onderstaande instructies. |
| 4xx-fout in de aanvraag. | Een of meer van de aanvraag parameters zijn onjuist. | Probeer het niet opnieuw.  Raadpleeg de fout Details voor meer informatie.  4xx fouten zijn tijdens de ontwerp fase.|
| 5xx tijdelijke fout van de service. | De beheerde identiteiten voor het subsysteem van Azure-resources of Azure Active Directory een tijdelijke fout geretourneerd. | Het is veilig om het opnieuw te proberen nadat u ten minste één seconde hebt gewacht.  Als u niet snel of te vaak opnieuw probeert, IMDS en/of Azure AD kan een frequentie limiet fout (429) retour neren.|
| timeout | Het IMDS-eind punt wordt bijgewerkt. | Probeer het opnieuw met exponentiële uitstel. Zie de onderstaande instructies. |

Als er een fout optreedt, bevat de bijbehorende HTTP-antwoord tekst JSON met de fout Details:

| Element | Beschrijving |
| ------- | ----------- |
| fout   | Fout-id. |
| error_description | Uitgebreide beschrijving van de fout. **Fout beschrijvingen kunnen op elk gewenst moment worden gewijzigd. Code die vertakkingen niet schrijven op basis van waarden in de fout beschrijving.**|

### <a name="http-response-reference"></a>HTTP-antwoord referentie

In deze sectie worden de mogelijke fout reacties gedocumenteerd. De status ' 200 OK ' is een geslaagd antwoord en het toegangs token bevindt zich in de JSON van de antwoord tekst in het element access_token.

| Statuscode | Fout | Beschrijving van de fout | Oplossing |
| ----------- | ----- | ----------------- | -------- |
| 400 Ongeldige aanvraag | invalid_resource | AADSTS50001: de toepassing met de naam *\<URI\>* is niet gevonden in de Tenant met de naam *\<TENANT-ID\>* . Dit kan gebeuren als de toepassing niet is geïnstalleerd door de beheerder van de tenant of als er geen toestemming voor is verleend door een gebruiker in de tenant. U hebt uw verificatie aanvraag mogelijk naar de verkeerde Tenant verzonden. \ | (Alleen Linux) |
| 400 Ongeldige aanvraag | bad_request_102 | De vereiste meta gegevens header is niet opgegeven | Het `Metadata` veld met de aanvraag header ontbreekt in uw aanvraag of heeft een onjuiste indeling. De waarde moet worden opgegeven als `true` , in kleine letters. Zie ' voorbeeld aanvraag ' in de voor gaande REST sectie voor een voor beeld.|
| 401 Onbevoegd | unknown_source | Onbekende bron *\<URI\>* | Controleer of de URI van de HTTP GET-aanvraag juist is geformatteerd. Het `scheme:host/resource-path` gedeelte moet worden opgegeven als `http://localhost:50342/oauth2/token` . Zie ' voorbeeld aanvraag ' in de voor gaande REST sectie voor een voor beeld.|
|           | invalid_request | Er ontbreekt een vereiste para meter in de aanvraag, bevat een ongeldige parameter waarde, bevat een para meter van meer dan een keer of is anders misvormd. |  |
|           | unauthorized_client | De client is niet gemachtigd om een toegangs token aan te vragen met behulp van deze methode. | Dit wordt veroorzaakt door een aanvraag die geen lokale loop back heeft gebruikt voor het aanroepen van de extensie, of op een virtuele machine die geen beheerde identiteiten voor de juiste configuratie van Azure-resources heeft. Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele machine met](qs-configure-portal-windows-vm.md) behulp van de Azure portal als u hulp nodig hebt bij de configuratie van de virtuele machine. |
|           | access_denied | De resource-eigenaar of autorisatie server heeft de aanvraag geweigerd. |  |
|           | unsupported_response_type | De autorisatie server biedt geen ondersteuning voor het verkrijgen van een toegangs token met behulp van deze methode. |  |
|           | invalid_scope | Het aangevraagde bereik is ongeldig, onbekend of onjuist gevormd. |  |
| 500 Interne serverfout | unknown | Kan geen Token ophalen uit Active Directory. Zie Logboeken in voor meer informatie *\<file path\>* | Controleer of de beheerde identiteiten voor Azure-resources zijn ingeschakeld op de VM. Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele machine met](qs-configure-portal-windows-vm.md) behulp van de Azure portal als u hulp nodig hebt bij de configuratie van de virtuele machine.<br><br>Controleer ook of uw HTTP GET-aanvraag-URI correct is geformatteerd, met name de bron-URI die is opgegeven in de query reeks. Zie de ' voorbeeld aanvraag ' in de voor gaande REST sectie voor een voor beeld of [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](./services-support-managed-identities.md) voor een lijst met Services en de bijbehorende resource-id's.

## <a name="retry-guidance"></a>Richt lijnen voor opnieuw proberen 

Het is raadzaam om het opnieuw te proberen als u de fout code 404, 429 of 5xx ontvangt (zie bovenstaande [fout afhandeling](#error-handling) ).

Beperkings limieten zijn van toepassing op het aantal aanroepen naar het IMDS-eind punt. Wanneer de drempel waarde voor bandbreedte beperking wordt overschreden, beperkt IMDS-eind punt alle verdere aanvragen terwijl de vertraging van kracht is. Tijdens deze periode retourneert het IMDS-eind punt de HTTP-status code 429 ("te veel aanvragen") en worden de aanvragen mislukt. 

Voor opnieuw proberen wordt de volgende strategie aanbevolen: 

| **Strategie voor opnieuw proberen** | **Instellingen** | **Waarden** | **Uitleg** |
| --- | --- | --- | --- |
|ExponentialBackoff |Aantal pogingen<br />Min. uitstel<br />Max. uitstel<br />Delta-uitstel<br />Eerste snelle poging |5<br />0 sec.<br />60 sec.<br />2 sec.<br />onjuist |Poging 1, vertraging 0 sec.<br />Poging 2, vertraging ~2 sec.<br />Poging 3, vertraging ~6 sec.<br />Poging 4, vertraging ~14 sec.<br />Poging 5, vertraging ~30 sec. |

## <a name="resource-ids-for-azure-services"></a>Resource-Id's voor Azure-Services

Zie [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](./services-support-managed-identities.md) voor een lijst met resources die ondersteuning bieden voor Azure AD en die zijn getest met beheerde identiteiten voor Azure-resources en de bijbehorende resource-id's.


## <a name="next-steps"></a>Volgende stappen

- Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele machine met behulp van de Azure Portal](qs-configure-portal-windows-vm.md)om beheerde identiteiten voor Azure-resources in te scha kelen op een Azure VM.
