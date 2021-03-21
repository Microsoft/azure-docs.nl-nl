---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 01/08/2021
ms.author: erhopf
ms.openlocfilehash: 22127f81d871fe333750020196540db17e7544f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98033441"
---
## <a name="authentication"></a>Verificatie

Elke aanvraag vereist een autorisatie-header. In deze tabel ziet u welke headers worden ondersteund voor elke service:

| Ondersteunde autorisatie headers | Spraak naar tekst | Tekst naar spraak |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Ja | Ja |
| Autorisatie: Bearer | Ja | Ja |

Wanneer u de `Ocp-Apim-Subscription-Key` koptekst gebruikt, hoeft u alleen uw abonnements sleutel op te geven. Bijvoorbeeld:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Wanneer u de `Authorization: Bearer` header gebruikt, moet u een aanvraag indienen bij het `issueToken` eind punt. In deze aanvraag verwisselt u uw abonnements sleutel voor een toegangs token die 10 minuten geldig is. In de volgende gedeelten leert u hoe u een token krijgt en een token gebruikt.

### <a name="how-to-get-an-access-token"></a>Hoe kan ik een toegangs Token ophalen?

Als u een toegangs token wilt ophalen, moet u een aanvraag indienen bij het `issueToken` eind punt met behulp van de `Ocp-Apim-Subscription-Key` en uw abonnements sleutel.

Het `issueToken` eind punt heeft de volgende indeling:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Vervang door `<REGION_IDENTIFIER>` de id die overeenkomt met de regio van uw abonnement uit deze tabel:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Gebruik deze voor beelden om uw toegangs token aanvraag te maken.

#### <a name="http-sample"></a>HTTP-voor beeld

Dit voor beeld is een eenvoudige HTTP-aanvraag om een token op te halen. Vervang door `YOUR_SUBSCRIPTION_KEY` uw abonnements sleutel voor uw spraak service. Als uw abonnement zich niet in de regio vs West bevindt, vervangt u de header door de `Host` hostnaam van uw regio.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

De hoofd tekst van het antwoord bevat de toegangs token in de indeling JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Voorbeeld van PowerShell

Dit voor beeld is een eenvoudig Power shell-script om een toegangs token op te halen. Vervang door `YOUR_SUBSCRIPTION_KEY` uw abonnements sleutel voor uw spraak service. Zorg ervoor dat u het juiste eind punt gebruikt voor de regio die overeenkomt met uw abonnement. Dit voor beeld is momenteel ingesteld op VS-West.

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>Krul voorbeeld

Krul is een opdracht regel programma dat beschikbaar is in Linux (en in het Windows-subsysteem voor Linux). Deze krul opdracht illustreert hoe u een toegangs token kunt ophalen. Vervang door `YOUR_SUBSCRIPTION_KEY` uw abonnements sleutel voor uw spraak service. Zorg ervoor dat u het juiste eind punt gebruikt voor de regio die overeenkomt met uw abonnement. Dit voor beeld is momenteel ingesteld op VS-West.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-voorbeeld

Deze C#-klasse laat zien hoe u een toegangs token kunt ophalen. Geef uw abonnements sleutel voor uw spraak service op wanneer u een instantie van de klasse maakt. Als uw abonnement zich niet in de regio vs-West bevindt, wijzigt u de waarde van `FetchTokenUri` zodat deze overeenkomt met de regio voor uw abonnement.

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Python-voor beeld

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Een toegangs token gebruiken

Het toegangs token moet worden verzonden naar de service als de `Authorization: Bearer <TOKEN>` header. Elk toegangs token is 10 minuten geldig. U kunt op elk gewenst moment een nieuw token verkrijgen, maar om het netwerk verkeer en de latentie te minimaliseren, raden we u aan hetzelfde token gedurende negen minuten te gebruiken.

Hier volgt een voor beeld van een HTTP-aanvraag voor de spraak-naar-tekst REST API voor korte audio:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
