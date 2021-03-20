---
title: REST API bewerkingen aanroepen met gedeelde sleutel autorisatie
titleSuffix: Discover how to call Azure Storage REST API operations with Shared Key authorization. Get detailed information about each step of the sample operation.
description: Gebruik de Azure Storage REST API om een aanvraag voor Blob-opslag te maken met behulp van gedeelde sleutel autorisatie.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: ozge
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f569fdac19c4f765828d24f4d6615fdd7bafef8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89010899"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>REST API bewerkingen aanroepen met gedeelde sleutel autorisatie

In dit artikel wordt beschreven hoe u de Azure Storage REST-Api's aanroept, met inbegrip van het formulier voor de autorisatie-header. Het is geschreven vanuit het oogpunt van een ontwikkelaar die niets weet over REST en geen idee is hoe u een REST-oproep kunt doen. Nadat u hebt leren hoe u een REST-bewerking aanroept, kunt u gebruikmaken van deze kennis om andere Azure Storage REST-bewerkingen te gebruiken.

## <a name="prerequisites"></a>Vereisten

In de voorbeeld toepassing worden de BLOB-containers voor een opslag account vermeld. Als u de code in dit artikel wilt uitproberen, hebt u de volgende items nodig:

- [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) installeren met de **Azure-ontwikkelworkload**.

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

- Een opslag account voor algemeen gebruik. Zie [een opslag account maken](storage-account-create.md)als u nog geen opslag account hebt.

- In het voor beeld in dit artikel ziet u hoe u de containers in een opslag account kunt weer geven. Als u de uitvoer wilt zien, voegt u enkele containers toe aan Blob Storage in het opslag account voordat u begint.

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De voorbeeld toepassing is een console toepassing die is geschreven in C#.

Gebruik [git](https://git-scm.com/) om een kopie van de toepassing naar uw ontwikkelomgeving te downloaden.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Met deze opdracht wordt de opslagplaats naar uw lokale git-map gekloond. Als u de Visual Studio-oplossing wilt openen, zoekt u naar de map Storage-DotNet-rest-API-with-auth, opent u deze en dubbelklikt u op StorageRestApiAuth. SLN.

## <a name="about-rest"></a>Over REST

REST staat voor *representatieve status overdracht*. Voor een specifieke definitie raadpleegt u [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST is een architectuur waarmee u kunt communiceren met een service via een Internet Protocol, zoals HTTP/HTTPS. REST is onafhankelijk van de software die op de server of de client wordt uitgevoerd. De REST API kan worden aangeroepen vanuit elk platform dat HTTP/HTTPS ondersteunt. U kunt een toepassing schrijven die wordt uitgevoerd op een Mac, Windows, Linux, een Android-telefoon of Tablet, iPhone, iPod of website, en dezelfde REST API voor al deze platformen gebruiken.

Een aanroep van de REST API bestaat uit een aanvraag, die door de client wordt gemaakt en een antwoord dat door de service wordt geretourneerd. In de aanvraag verzendt u een URL met informatie over welke bewerking u wilt aanroepen, de resource die moet worden uitgevoerd, alle query parameters en kopteksten, en afhankelijk van de bewerking die is aangeroepen, een payload van gegevens. Het antwoord van de service omvat een status code, een set antwoord headers, en afhankelijk van de bewerking die is aangeroepen, een payload van gegevens.

## <a name="about-the-sample-application"></a>Over de voorbeeld toepassing

In de voorbeeld toepassing worden de containers in een opslag account vermeld. Wanneer u begrijpt hoe de informatie in de REST API documentatie overeenkomt met uw eigen code, zijn andere REST-aanroepen eenvoudiger te vinden.

Als u de REST API van de [BLOB-service](/rest/api/storageservices/Blob-Service-REST-API)bekijkt, ziet u alle bewerkingen die u kunt uitvoeren op Blob Storage. De opslag-client bibliotheken zijn wrappers rond de REST-Api's: ze maken het eenvoudig om toegang te krijgen tot opslag zonder de REST-Api's rechtstreeks te gebruiken. Maar zoals hierboven vermeld, wilt u soms het REST API gebruiken in plaats van een Storage-client bibliotheek.

## <a name="list-containers-operation"></a>Bewerking lijst containers

Controleer de verwijzing voor de [ListContainers](/rest/api/storageservices/List-Containers2) -bewerking. Deze informatie helpt u te begrijpen waar enkele van de velden uit komen in de aanvraag en het antwoord.

**Aanvraag methode**: ophalen. Dit woord is de HTTP-methode die u opgeeft als een eigenschap van het object Request. Andere waarden voor deze term zijn onder andere HEAD, PUT en DELETE, afhankelijk van de API die u aanroept.

**Aanvraag-URI**: `https://myaccount.blob.core.windows.net/?comp=list` .  De aanvraag-URI wordt gemaakt op basis van het eind punt van het Blob-opslag account `https://myaccount.blob.core.windows.net` en de bron teken reeks `/?comp=list` .

[URI-para meters](/rest/api/storageservices/List-Containers2#uri-parameters): er zijn aanvullende query parameters die u kunt gebruiken bij het aanroepen van ListContainers. Enkele van deze para meters zijn *time-out* voor de aanroep (in seconden) en het *voor voegsel*, dat wordt gebruikt voor het filteren.

Een andere handige para meter is *maxresults:* als er meer containers beschikbaar zijn dan deze waarde, bevat de antwoord tekst een *NextMarker* -element dat aangeeft dat de volgende container moet worden geretourneerd bij de volgende aanvraag. Als u deze functie wilt gebruiken, geeft u de waarde *NextMarker* op als de *markerings* parameter in de URI wanneer u de volgende aanvraag maakt. Wanneer u deze functie gebruikt, is het vergelijkbaar met het pagineren van de resultaten.

Als u aanvullende para meters wilt gebruiken, voegt u deze toe aan de resource teken reeks met de waarde, zoals in dit voor beeld:

```
/?comp=list&timeout=60&maxresults=100
```

[Aanvraag headers](/rest/api/storageservices/List-Containers2#request-headers)**:** in deze sectie vindt u de vereiste en optionele aanvraag headers. Er zijn drie kopteksten vereist: een *autorisatie* -header, *x-MS-date* (bevat de UTC-tijd voor de aanvraag) en *x-MS-version* (Hiermee geeft u de versie op van het rest API dat moet worden gebruikt). Inclusief *x-MS-Client-Request-id* in de headers is optioneel: u kunt de waarde voor dit veld instellen op alles. het wordt naar de logboeken voor opslag analyse geschreven wanneer logboek registratie is ingeschakeld.

[Aanvraag tekst](/rest/api/storageservices/List-Containers2#request-body)**:** er is geen aanvraag tekst voor ListContainers. De aanvraag tekst wordt gebruikt voor alle PUT-bewerkingen bij het uploaden van blobs, evenals SetContainerAccessPolicy, waarmee u in een XML-lijst met opgeslagen toegangs beleid kunt verzenden. Opgeslagen toegangs beleid wordt in het artikel besproken [met Shared Access signatures (SAS)](storage-sas-overview.md).

[Status code van antwoord](/rest/api/storageservices/List-Containers2#status-code)**:** geeft een overzicht van de status codes die u moet weten. In dit voor beeld is de HTTP-status code 200 OK. Bekijk de [definities van status](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)codes voor een volledige lijst met HTTP-status codes. Zie [algemene rest API fout codes](/rest/api/storageservices/common-rest-api-error-codes) voor meer informatie over de specifieke fout codes voor de opslag rest api's.

[Antwoord headers](/rest/api/storageservices/List-Containers2#response-headers)**:** dit is het *inhouds type* include; *x-MS-Request-id*, de aanvraag-id is die u hebt opgegeven in; *x-MS-version*, waarmee wordt aangegeven welke versie van de BLOB service gebruikt; en de *datum*, in UTC en geeft aan op welke tijd de aanvraag is gedaan.

[Antwoord tekst](/rest/api/storageservices/List-Containers2#response-body): dit veld is een XML-structuur die de aangevraagde gegevens verschaft. In dit voor beeld is het antwoord een lijst met containers en de bijbehorende eigenschappen.

## <a name="creating-the-rest-request"></a>De REST-aanvraag maken

Gebruik voor beveiliging bij het uitvoeren in productie altijd HTTPS in plaats van HTTP. Voor de doel einden van deze oefening gebruikt u HTTP, zodat u de aanvraag-en antwoord gegevens kunt bekijken. Als u de aanvraag-en antwoord informatie wilt weer geven in de daad werkelijke REST-aanroepen, kunt u [Fiddler](https://www.telerik.com/fiddler) of een vergelijk bare toepassing downloaden. In de Visual Studio-oplossing zijn de naam en sleutel van het opslag account hardcoded in de-klasse. De methode ListContainersAsyncREST geeft de naam van het opslag account en de sleutel van het opslag account door aan de methoden die worden gebruikt voor het maken van de verschillende onderdelen van de REST-aanvraag. In een echte wereld toepassing zouden de naam en sleutel van het opslag account zich bevinden in een configuratie bestand, omgevings variabelen of worden opgehaald uit een Azure Key Vault.

In het voorbeeld project bevindt de code voor het maken van de autorisatie-header zich in een afzonderlijke klasse. Het is raadzaam om de hele klasse te maken en deze toe te voegen aan uw eigen oplossing en deze te gebruiken. De code van de autorisatie-header werkt voor de meeste REST API-aanroepen naar Azure Storage.

Als u de aanvraag wilt maken, wat een HttpRequestMessage-object is, gaat u naar ListContainersAsyncREST in Program. cs. De stappen voor het maken van de aanvraag zijn:

- Maak de URI die moet worden gebruikt voor het aanroepen van de service.
- Maak het HttpRequestMessage-object en stel de payload in. De nettolading is null voor ListContainersAsyncREST omdat er niets wordt door gegeven.
- Voeg de aanvraag headers voor x-MS-date en x-MS-version toe.
- Haal de autorisatie-header op en voeg deze toe.

Enkele basis informatie die u nodig hebt:

- Voor ListContainers is de **methode** `GET` . Deze waarde wordt ingesteld bij het instantiëren van de aanvraag.
- De **resource** is het query gedeelte van de URI dat aangeeft welke API wordt aangeroepen, dus de waarde is `/?comp=list` . Zoals eerder is vermeld, bevindt de resource zich op de pagina referentie documentatie waarop de informatie over de [ListContainers-API](/rest/api/storageservices/List-Containers2)wordt weer gegeven.
- De URI wordt gemaakt door het Blob service-eind punt voor dat opslag account te maken en de resource samen te voegen. De waarde voor de **aanvraag-URI** eindigt op `http://contosorest.blob.core.windows.net/?comp=list` .
- Voor ListContainers is **requestBody** Null en er zijn geen extra **headers**.

Verschillende Api's hebben mogelijk andere para meters die moeten worden door gegeven, zoals *ifMatch*. Een voor beeld van waar u ifMatch zou kunnen gebruiken bij het aanroepen van PutBlob. In dat geval stelt u ifMatch in op een eTag en wordt alleen de BLOB bijgewerkt als de eTag die u opgeeft, overeenkomt met de huidige eTag op de blob. Als iemand anders de BLOB heeft bijgewerkt sinds het ophalen van de eTag, wordt de wijziging niet overschreven.

Stel eerst de `uri` en in `payload` .

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Instantiër vervolgens de aanvraag, waarbij u de methode instelt op `GET` en de URI opgeeft.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Voeg de aanvraag headers voor `x-ms-date` en toe `x-ms-version` . Deze plaats in de code is ook waar u aanvullende aanvraag headers toevoegt die vereist zijn voor de aanroep. In dit voor beeld zijn er geen aanvullende headers. Een voor beeld van een API die in extra headers wordt door gegeven, is de container-ACL-bewerking instellen. Deze API-aanroep voegt een header met de naam "x-MS-BLOB-Public-Access" en de waarde voor het toegangs niveau toe.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Roep de methode aan waarmee de autorisatie-header wordt gemaakt en voeg deze toe aan de aanvraag headers. Verderop in dit artikel ziet u hoe u de autorisatie-header maakt. De naam van de methode is GetAuthorizationHeader, die u in dit code fragment kunt zien:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Op dit punt wordt `httpRequestMessage` de rest-aanvraag met de autorisatie headers opgenomen.

## <a name="send-the-request"></a>De aanvraag verzenden

Nu u de aanvraag hebt gemaakt, kunt u de SendAsync-methode aanroepen om deze naar Azure Storage te verzenden. Controleer of de waarde van de antwoord status code 200 is, wat betekent dat de bewerking is geslaagd. Vervolgens parseert u het antwoord. In dit geval krijgt u een XML-lijst met containers. Laten we eens kijken naar de code voor het aanroepen van de GetRESTRequest-methode om de aanvraag te maken, de aanvraag uit te voeren en het antwoord op de lijst met containers te onderzoeken.

```csharp
    // Send the request.
    using (HttpResponseMessage httpResponseMessage =
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200),
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Als u een netwerksniffer uitvoert, zoals [Fiddler](https://www.telerik.com/fiddler) bij het aanroepen van SendAsync, kunt u de aanvraag-en antwoord gegevens bekijken. We gaan eens kijken. De naam van het opslag account is *contosorest*.

**Schot**

```
GET /?comp=list HTTP/1.1
```

**Aanvraag headers:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Status code en reactie headers die zijn geretourneerd na de uitvoering:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Tekst van antwoord (XML):** Voor de lijst containers wordt de lijst met containers en de bijbehorende eigenschappen weer gegeven.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Nu u begrijpt hoe u de aanvraag maakt, de service aanroept en de resultaten parseert, gaan we kijken hoe u de autorisatie-header maakt. Het maken van die header is gecompliceerd, maar het goede nieuws is dat wanneer u de code hebt gewerkt, werkt voor alle REST Api's van de Storage-service.

## <a name="creating-the-authorization-header"></a>De autorisatie-header maken

> [!TIP]
> Azure Storage ondersteunt nu de integratie van Azure Active Directory (Azure AD) voor blobs en wacht rijen. Azure AD biedt een veel eenvoudiger ervaring voor het autoriseren van een aanvraag voor het Azure Storage. Zie [autoriseren met Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory)voor meer informatie over het gebruik van Azure AD om rest-bewerkingen te autoriseren. Zie [toegang tot Azure Storage verifiëren met behulp van Azure Active Directory](storage-auth-aad.md)voor een overzicht van Azure AD-integratie met Azure Storage.

Er is een artikel waarin conceptueel (geen code) wordt uitgelegd hoe [aanvragen voor Azure Storage worden geautoriseerd](/rest/api/storageservices/authorize-requests-to-azure-storage).

We gaan dat artikel naar precies naar wens en de code weer geven.

Gebruik eerst verificatie met gedeelde sleutel. De indeling van de autorisatie-header ziet er als volgt uit:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Het veld hand tekening is een op hash gebaseerde Message Authentication Code (HMAC) die is gemaakt op basis van de aanvraag en berekend met behulp van het SHA256-algoritme, en is gecodeerd met base64-code ring. Inderdaad? (Een ogen blik geduld, u hebt het woord nog niet *gecanonieked* gehoord.)

Dit code fragment toont de indeling van de teken reeks voor de gedeelde sleutel hand tekening:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

De meeste van deze velden worden zelden gebruikt. Voor Blob-opslag geeft u VERB, MD5, lengte van inhoud, canonieke kopteksten en canonieke bron op. U kunt de andere leeg laten (maar in dat `\n` geval de waarde kent zodat deze leeg zijn).

Wat zijn CanonicalizedHeaders en CanonicalizedResource? Goede vraag. Wat heeft eigenlijk een canonieke betekenis? Micro soft Word herkent het niet eens als een woord. Hier vindt u [informatie over standaardisatie](https://en.wikipedia.org/wiki/Canonicalization): *in computer wetenschappen, standaardisatie (soms standaardisering of normalisatie) is een proces voor het converteren van gegevens met meer dan één mogelijke representatie in een standaard-, normale of canonieke vorm.* Normaal gesp roken houdt dit in dat u de lijst met items (zoals kopteksten in het geval van canonieke kopteksten) kunt maken en deze in een vereiste indeling wilt standaardiseren. In principe heeft micro soft een indeling gekozen en moet deze overeenkomen.

Laten we beginnen met deze twee canonieke velden, omdat deze zijn vereist voor het maken van de autorisatie-header.

### <a name="canonicalized-headers"></a>Canonieke kopteksten

Als u deze waarde wilt maken, haalt u de kopteksten op die beginnen met ' x-MS-' en sorteert u deze en vervolgens maakt u deze op in een reeks `[key:value\n]` instanties, samengevoegd tot één teken reeks. In dit voor beeld zien de canonieke kopteksten er als volgt uit:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Dit is de code die wordt gebruikt om die uitvoer te maken:

```csharp
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

### <a name="canonicalized-resource"></a>Canoniek gemaakte resource

Dit deel van de teken reeks van de hand tekening vertegenwoordigt het opslag account waarop de aanvraag betrekking heeft. Houd er rekening mee dat de aanvraag `<http://contosorest.blob.core.windows.net/?comp=list>` -URI, met de daad werkelijke account naam ( `contosorest` in dit geval). In dit voor beeld wordt dit geretourneerd:

```
/contosorest/\ncomp:list
```

Als u query parameters hebt, bevat dit voor beeld ook die para meters. Hier volgt de code, waarmee ook extra query parameters en query parameters met meerdere waarden worden verwerkt. Houd er rekening mee dat u deze code bouwt voor het werken met alle REST-Api's. U wilt alle mogelijkheden gebruiken, zelfs als de methode ListContainers niet alle nodig heeft.

```csharp
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Nu de canonieke teken reeksen zijn ingesteld, gaan we kijken hoe u de autorisatie-header zelf maakt. U begint met het maken van een teken reeks van de bericht handtekening in de indeling van de StringToSign die eerder in dit artikel wordt weer gegeven. In dit concept wordt uitgelegd hoe u opmerkingen in de code kunt gebruiken. hier is de laatste methode die de autorisatie-header retourneert:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + signature);
    return authHV;
}
```

Wanneer u deze code uitvoert, ziet de resulterende MessageSignature eruit als in dit voor beeld:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Dit is de uiteindelijke waarde voor AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

De AuthorizationHeader is de laatste header die in de aanvraag headers is geplaatst voordat het antwoord wordt gepost.

Dit omvat alles wat u moet weten om een klasse samen te stellen waarmee u een aanvraag voor het aanroepen van de REST-Api's van Storage services kunt maken.

## <a name="example-list-blobs"></a>Voor beeld: blobs weer geven

Laten we eens kijken hoe u de code wijzigt om de bewerking lijst-blobs aan te roepen voor container *container-1*. Deze code is bijna identiek aan de code voor het weer geven van containers, de enige verschillen zijn de URI en hoe u het antwoord parseert.

Als u de referentie documentatie voor [ListBlobs](/rest/api/storageservices/List-Blobs)bekijkt, ziet u dat de methode *Get* is en dat de RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

Wijzig in ListContainersAsyncREST de code waarmee de URI wordt ingesteld op de API voor ListBlobs. De container naam is **container-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Wanneer u vervolgens het antwoord afhandelt, wijzigt u de code om te zoeken naar blobs in plaats van containers.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Wanneer u dit voor beeld uitvoert, krijgt u de volgende resultaten:

**Canonieke kopteksten:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Canoniek gemaakte resource:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Bericht handtekening:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Autorisatie-header:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

De volgende waarden zijn afkomstig uit [Fiddler](https://www.telerik.com/fiddler):

**Schot**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Aanvraag headers:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Status code en reactie headers die zijn geretourneerd na de uitvoering:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Tekst van antwoord (XML):** Deze XML-respons toont de lijst met blobs en hun eigenschappen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Samenvatting

In dit artikel hebt u geleerd hoe u een aanvraag kunt indienen voor de Blob Storage-REST API. Met de aanvraag kunt u een lijst met containers of een lijst met blobs in een container ophalen. U hebt geleerd hoe u de autorisatie handtekening maakt voor de REST API-aanroep en hoe u deze kunt gebruiken in de REST-aanvraag. Ten slotte hebt u geleerd hoe u het antwoord kunt onderzoeken.

## <a name="next-steps"></a>Volgende stappen

- [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
- [Bestandsservice REST API](/rest/api/storageservices/file-service-rest-api)
- [Wachtrijservice REST API](/rest/api/storageservices/queue-service-rest-api)
- [Tabelservice REST API](/rest/api/storageservices/table-service-rest-api)
