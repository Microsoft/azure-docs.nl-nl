---
title: 'Een HTTP-aanvraag ondertekenen met C #'
description: In deze zelf studie wordt de C#-versie van het ondertekenen van een HTTP-aanvraag met een HMAC-hand tekening voor Azure Communication Services uitgelegd.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 03/10/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 34c7df2b0e61536c0b5f0bc1e4a97d58d0d9c6a4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490502"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- Installeer [Visual Studio](https://visualstudio.microsoft.com/downloads/).
- Maak een Azure Communication Services-resource. Zie [een Azure Communication Services-resource maken](../../quickstarts/create-communication-resource.md)voor meer informatie. U moet uw **resourceEndpoint** en **resourceAccessKey** voor deze zelf studie vastleggen.

## <a name="sign-an-http-request-with-c"></a>Een HTTP-aanvraag ondertekenen met C #

Toegangs sleutel verificatie maakt gebruik van een gedeelde geheime sleutel voor het genereren van een HMAC-hand tekening voor elke HTTP-aanvraag. Deze hand tekening wordt gegenereerd met het SHA256-algoritme en wordt in de header verzonden met `Authorization` behulp van het `HMAC-SHA256` schema. Bijvoorbeeld:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

De `hmac-sha256-signature` bestaat uit:

- HTTP-term (bijvoorbeeld `GET` of `PUT` )
- HTTP-aanvraag pad
- Datum
- Host
- x-MS-content-sha256

## <a name="setup"></a>Instellen

In de volgende stappen wordt beschreven hoe u de autorisatie-header bouwt.

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

In een console venster, zoals cmd, Power shell of bash, gebruikt u de `dotnet new` opdracht om een nieuwe console-app met de naam te maken `SignHmacTutorial` . Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: **Program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Wijzig uw map in de zojuist gemaakte app-map. Gebruik de `dotnet build` opdracht voor het compileren van uw toepassing.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Het pakket installeren

Installeer het pakket `Newtonsoft.Json` dat wordt gebruikt voor de serialisatie van de hoofd tekst.

```console
dotnet add package Newtonsoft.Json
```

Update de `Main` methode declaratie ter ondersteuning van async-code. Gebruik de volgende code om te beginnen.

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>Een aanvraag bericht maken

Voor dit voor beeld ondertekenen we een aanvraag om een nieuwe identiteit te maken met behulp van de API voor verificatie van Communication Services (versie `2021-03-07` ).

Voeg de volgende code toe aan de methode `Main`.

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create a uri you are going to call.
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Vervang door `resourceEndpoint` de werkelijke waarde van het resource-eind punt.

## <a name="create-a-content-hash"></a>Een hash voor inhoud maken

De hash voor inhoud maakt deel uit van uw HMAC-hand tekening. Gebruik de volgende code om de hash van de inhoud te berekenen. U kunt deze methode toevoegen aan `Progam.cs` de `Main` methode.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Een hand tekening berekenen

Gebruik de volgende code om een methode te maken voor het berekenen van uw HMAC-hand tekening.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Vervang door `resourceAccessKey` een toegangs sleutel van uw werkelijke communicatie Services-resource.

## <a name="create-an-authorization-header-string"></a>Een teken reeks voor een autorisatie-header maken

We maken nu de teken reeks die we gaan toevoegen aan onze autorisatie-header.

1. Een hash voor inhoud berekenen.
1. Geef de UTC-tijds tempel (Coordinated Universal Time) op.
1. Een teken reeks voorbereiden om te ondertekenen.
1. De hand tekening te berekenen.
1. De teken reeks die wordt gebruikt in de autorisatie-header samen voegen.
 
Voeg de volgende code toe aan de methode `Main`.

```csharp
// Compute a content hash.
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp.
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature.
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Headers toevoegen aan requestMessage

Gebruik de volgende code om de vereiste kopteksten toe te voegen aan uw `requestMessage` .

```csharp
//Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//Add a date header.
requestMessage.Headers.Add("Date", date);
//Add an authorization header.
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>De client testen

Roep het eind punt aan met en `HttpClient` Controleer het antwoord.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
