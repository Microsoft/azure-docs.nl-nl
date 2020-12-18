---
title: Beheerde identiteit gebruiken met een toepassing
description: Beheerde identiteiten gebruiken in azure Service Fabric toepassings code voor toegang tot Azure-Services.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 07f960c01367ab42a434a8c2e1e276d9c5f7bd11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86253640"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>De beheerde identiteit van een Service Fabric-toepassing gebruiken voor toegang tot Azure-Services

Service Fabric toepassingen kunnen beheerde identiteiten gebruiken om toegang te krijgen tot andere Azure-resources die ondersteuning bieden voor op Azure Active Directory gebaseerde verificatie. Een toepassing kan een [toegangs token](../active-directory/develop/developer-glossary.md#access-token) verkrijgen dat de identiteit vertegenwoordigt, die door het systeem of de gebruiker kan worden toegewezen, en dit als een Bearer-token gebruiken om zichzelf te verifiëren bij een andere service, ook wel bekend als een [beveiligde bron server](../active-directory/develop/developer-glossary.md#resource-server). Het token vertegenwoordigt de identiteit die is toegewezen aan de Service Fabric-toepassing en wordt alleen verleend aan Azure-resources (inclusief SF-toepassingen) die deze identiteit delen. Raadpleeg de documentatie van het [overzicht van beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor een gedetailleerde beschrijving van beheerde identiteiten, evenals het onderscheid tussen systeem toewijzingen en door de gebruiker toegewezen identiteiten. In dit artikel wordt gerefereerd aan een met Managed Identity geschikte Service Fabric toepassing als de [client toepassing](../active-directory/develop/developer-glossary.md#client-application) .

> [!IMPORTANT]
> Een beheerde identiteit vertegenwoordigt de koppeling tussen een Azure-resource en een Service-Principal in de bijbehorende Azure AD-Tenant die is gekoppeld aan het abonnement dat de resource bevat. In de context van Service Fabric worden beheerde identiteiten alleen ondersteund voor toepassingen die als Azure-resources worden geïmplementeerd. 

> [!IMPORTANT]
> Voordat de beheerde identiteit van een Service Fabric toepassing wordt gebruikt, moet aan de client toepassing toegang worden verleend tot de beveiligde bron. Raadpleeg de lijst met Azure- [Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) om te controleren op ondersteuning en vervolgens naar de documentatie van de betreffende service voor specifieke stappen voor het verlenen van identiteits toegang tot belang rijke bronnen. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Een toegangs Token ophalen met behulp van REST API
In clusters waarvoor een beheerde identiteit is ingeschakeld, maakt de Service Fabric-runtime gebruik van een localhost-eind punt dat toepassingen kunnen gebruiken om toegangs tokens te verkrijgen. Het eind punt is beschikbaar op elk knoop punt van het cluster en is toegankelijk voor alle entiteiten op het knoop punt. Geautoriseerde bellers kunnen toegangs tokens verkrijgen door dit eind punt aan te roepen en een verificatie code te presen teren. de code wordt gegenereerd door de Service Fabric runtime voor elke afzonderlijke activering van service code-pakket en is gebonden aan de levens duur van het proces dat als host fungeert voor het service code pakket.

Met name de omgeving van een Service Fabric service met beheerde identiteiten wordt geseedd met de volgende variabelen:
- IDENTITY_ENDPOINT: het localhost-eind punt dat overeenkomt met de beheerde identiteit van de service
- ' IDENTITY_HEADER ': een unieke verificatie code die de service op het huidige knoop punt vertegenwoordigt
- ' IDENTITY_SERVER_THUMBPRINT ': vinger afdruk van beheerde identiteits server van service Fabric

> [!IMPORTANT]
> De toepassings code moet rekening houden met de waarde van de omgevings variabele ' IDENTITY_HEADER ' als gevoelige gegevens. deze kan niet worden geregistreerd of anderszins worden verspreid. De verificatie code heeft geen waarde buiten het lokale knoop punt of nadat het proces dat als host fungeert voor de service is beëindigd, maar het de identiteit van de Service Fabric-service vertegenwoordigt, en moet dus worden behandeld met dezelfde voorzorgsmaatregelen als het toegangs token zelf.

Voor het verkrijgen van een token voert de client de volgende stappen uit:
- een URI vormt door het samen voegen van het beheerde identiteits eindpunt (IDENTITY_ENDPOINT waarde) met de API-versie en de bron (doel groep) die vereist is voor het token
- Hiermee maakt u een GET http (s)-aanvraag voor de opgegeven URI
- Hiermee worden de juiste validatie logica voor het server certificaat toegevoegd
- Hiermee wordt de verificatie code (IDENTITY_HEADER waarde) als koptekst aan de aanvraag toegevoegd
- Hiermee wordt de aanvraag verzonden

Een geslaagde reactie bevat een JSON-Payload die het resulterende toegangs token en meta gegevens beschrijft. Een mislukte reactie omvat ook een uitleg van de fout. Zie hieronder voor meer informatie over het afhandelen van fouten.

Toegangs tokens worden in de cache opgeslagen door Service Fabric op verschillende niveaus (knoop punt, cluster, resource Provider service), waardoor een geslaagde reactie niet noodzakelijkerwijs impliceert dat het token rechtstreeks is uitgegeven als reactie op de aanvraag van de gebruikers toepassing. Tokens worden voor minder dan hun levens duur in de cache opgeslagen, zodat een toepassing gegarandeerd een geldig token kan ontvangen. Het is raadzaam dat de toepassings code zichzelf bewaart alle toegangs tokens die worden opgehaald. de cache sleutel moet (een afleiding van) de doel groep bevatten. 

Voorbeeld aanvraag:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
Hierbij

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | De HTTP-term waarmee wordt aangegeven dat u gegevens wilt ophalen uit het eind punt. In dit geval een OAuth-toegangs token. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Het beheerde identiteits eindpunt voor Service Fabric toepassingen, dat wordt gegeven via de IDENTITY_ENDPOINT omgevings variabele. |
| `api-version` | Een query reeks parameter, waarmee de API-versie van de beheerde identiteits token service wordt opgegeven. Momenteel is de enige geaccepteerde waarde `2019-07-01-preview` en deze kan worden gewijzigd. |
| `resource` | Een query teken reeks parameter, waarmee de App-ID-URI van de doel resource wordt aangegeven. Dit wordt weer gegeven als de `aud` claim (publiek) van het uitgegeven token. In dit voor beeld wordt een token aangevraagd voor toegang tot Azure Key Vault, waarvan de URI van de App-ID https://Vault.Azure.net/is. |
| `Secret` | Een veld voor de HTTP-aanvraag header, dat wordt vereist door de Service Fabric Managed Identity token service voor Service Fabric Services om de oproepende functie te verifiëren. Deze waarde wordt verschaft door de SF runtime via IDENTITY_HEADER omgevings variabele. |


Voorbeeld antwoord:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
Hierbij

| Element | Beschrijving |
| ------- | ----------- |
| `token_type` | Het type token; in dit geval is het toegangs token ' Bearer ', wat betekent dat de presentator (' Bearer ') van dit token het beoogde onderwerp van het token is. |
| `access_token` | Het aangevraagde toegangs token. Wanneer u een beveiligd REST API aanroept, wordt het token in het `Authorization` veld aanvraag header Inge sloten als een Bearer-token, waardoor de API de aanroeper kan verifiëren. | 
| `expires_on` | De tijds tempel van de verval datum van het toegangs token; wordt weer gegeven als het aantal seconden van "1970-01-01T0:0: 0Z UTC" en komt overeen met de claim van het token `exp` . In dit geval verloopt het token op 2019-08-08T06:10:11 + 00:00 (in RFC 3339)|
| `resource` | De resource waarvoor het toegangs token is uitgegeven, opgegeven via de `resource` query teken reeks parameter van de aanvraag, komt overeen met de claim ' AUD ' van de token. |


## <a name="acquiring-an-access-token-using-c"></a>Een toegangs token verkrijgen met C #
De bovenstaande wordt in C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Toegang tot Key Vault vanuit een Service Fabric-toepassing met behulp van beheerde identiteit
Dit voor beeld bouwt voort op het bovenstaande om de toegang tot een geheim dat is opgeslagen in een Key Vault met beheerde identiteit te demonstreren.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Foutafhandeling
Het veld status code van de HTTP-antwoord header geeft de status van het succes van de aanvraag aan. de status ' 200 OK ' geeft aan dat geslaagd is en het antwoord bevat het toegangs token zoals hierboven is beschreven. Hieronder vindt u een korte opsomming van mogelijke fout berichten.

| Statuscode | Fout reden | Omgaan met |
| ----------- | ------------ | ------------- |
| 404 niet gevonden. | Onbekende verificatie code of de toepassing heeft geen beheerde identiteit toegewezen. | Corrigeer de toepassings instellingen of de code voor het ophalen van tokens. |
| 429 te veel aanvragen. |  De beperkings limiet is bereikt, opgelegd door AAD of SF. | Probeer het opnieuw met exponentiële uitstel. Zie de onderstaande instructies. |
| 4xx-fout in de aanvraag. | Een of meer van de aanvraag parameters zijn onjuist. | Probeer het niet opnieuw.  Raadpleeg de fout Details voor meer informatie.  4xx fouten zijn tijdens de ontwerp fase.|
| 5xx-fout van service. | Het subsysteem Managed Identity of Azure Active Directory retourneerde een tijdelijke fout. | Het is veilig om na een korte tijd opnieuw te proberen. U kunt een beperkings voorwaarde (429) bereiken wanneer u het opnieuw probeert.|

Als er een fout optreedt, bevat de bijbehorende HTTP-antwoord tekst een JSON-object met de fout Details:

| Element | Beschrijving |
| ------- | ----------- |
| code | Foutcode. |
| correlationId | Een correlatie-ID die kan worden gebruikt voor fout opsporing. |
| message | Uitgebreide beschrijving van de fout. **Fout beschrijvingen kunnen op elk gewenst moment worden gewijzigd. Niet afhankelijk van het fout bericht zelf.**|

Voorbeeld fout:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Hier volgt een lijst met typische Service Fabric fouten die specifiek zijn voor beheerde identiteiten:

| Code | Bericht | Beschrijving | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Het geheim is niet gevonden in de aanvraag headers. | De verificatie code is niet in de aanvraag opgenomen. | 
| ManagedIdentityNotFound | Beheerde identiteit niet gevonden voor de opgegeven toepassingshost. | De toepassing heeft geen identiteit, of de verificatie code is onbekend. |
| ArgumentNullOrEmpty | De para meter ' resource ' mag niet null of een lege teken reeks zijn. | De resource (doel groep) is niet in de aanvraag opgenomen. |
| InvalidApiVersion | De API-versie wordt niet ondersteund. De ondersteunde versie is 2019-07-01-preview. | Ontbrekende of niet-ondersteunde API-versie opgegeven in de aanvraag-URI. |
| InternalServerError | Er is een fout opgetreden. | Er is een fout opgetreden in het beheerde identiteits subsysteem, mogelijk buiten de Service Fabric stack. De meest waarschijnlijke oorzaak is een onjuiste waarde die is opgegeven voor de resource (Controleer op het navolgen van '/'?) | 

## <a name="retry-guidance"></a>Richt lijnen voor opnieuw proberen 

Normaal gesp roken is de fout code alleen opnieuw proberen 429 (te veel aanvragen). interne server fouten/5xx-fout codes kunnen opnieuw worden geprobeerd, hoewel de oorzaak mogelijk permanent is. 

Beperkings limieten zijn van toepassing op het aantal aanroepen van het subsysteem Managed Identity, met name de upstream-afhankelijkheden (de beheerde identiteit Azure-service of de Secure Token-Service). Service Fabric tokens op verschillende niveaus in de pijp lijn op te slaan in de cache, maar gezien de gedistribueerde aard van de betrokken onderdelen, kan de aanroeper mogelijk inconsistente beperkings reacties ondervinden (dat wil zeggen: er wordt beperkt op één knoop punt/exemplaar van een toepassing, maar niet op een ander knoop punt tijdens het aanvragen van een token voor dezelfde identiteit.) Wanneer de beperkings voorwaarde is ingesteld, kunnen volgende aanvragen van dezelfde toepassing mislukken met de HTTP-status code 429 (te veel aanvragen) totdat de voor waarde is gewist.  

Het wordt aanbevolen dat aanvragen die zijn mislukt door beperking, als volgt opnieuw worden geprobeerd met een exponentiële uitstel: 

| Index aanroepen | Actie voor het ontvangen van 429 | 
| --- | --- | 
| 1 | Wacht 1 seconde en probeer het opnieuw |
| 2 | Wacht 2 seconden en probeer het opnieuw |
| 3 | Wacht 4 seconden en probeer het opnieuw |
| 4 | Wacht 8 seconden en probeer het opnieuw |
| 4 | Wacht 8 seconden en probeer het opnieuw |
| 5 | Wacht 16 seconden en probeer het opnieuw |

## <a name="resource-ids-for-azure-services"></a>Resource-Id's voor Azure-Services
Zie [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) voor een lijst met resources die ondersteuning bieden voor Azure AD en de bijbehorende resource-id's.

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)
