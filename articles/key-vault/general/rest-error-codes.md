---
title: REST API-foutcodes - Azure Key Vault
description: Deze fout codes kunnen worden geretourneerd door een bewerking op een Azure Key Vault-webservice.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631219"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API fout codes
 
De volgende fout codes kunnen worden geretourneerd door een bewerking op een Azure Key Vault-webservice.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: niet-geverifieerde aanvraag

401 betekent dat de aanvraag niet is geverifieerd voor Key Vault. 

Een aanvraag wordt geverifieerd als:

- De sleutel kluis kent de identiteit van de aanroeper; maar
- De aanroeper mag proberen om toegang te krijgen tot Key Vault resources. 

Er zijn verschillende redenen waarom een aanvraag 401 kan retour neren.

### <a name="no-authentication-token-attached-to-the-request"></a>Er is geen verificatie token aan de aanvraag gekoppeld. 

Hier volgt een voor beeld van een aanvraag PUT, waarbij de waarde van een geheim wordt ingesteld:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

De header ' autorisatie ' is het toegangs token dat vereist is voor elke aanroep van de Key Vault voor gegevens vlak bewerkingen. Als de header ontbreekt, moet het antwoord 401 zijn.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Het token heeft niet de juiste resource die eraan is gekoppeld. 

Bij het aanvragen van een toegangs token van het Azure OAUTH-eind punt is een para meter met de naam ' resource ' verplicht. De waarde is belang rijk voor de token provider, omdat deze het token voor het beoogde gebruik van het bereik. De resource voor **alle** tokens voor toegang tot een Key Vault is *https://Vault.keyvault.net* (zonder afsluitende slash).

### <a name="the-token-is-expired"></a>Het token is verlopen

Tokens zijn base64-gecodeerd en de waarden kunnen worden gedecodeerd op websites, zoals [http://jwt.calebb.net](http://jwt.calebb.net) . Dit is het bovenstaande token dat is gedecodeerd:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Er kunnen veel belang rijke onderdelen in dit token worden weer geven:

- AUD (doel groep): de bron van het token. Zoals u ziet, is dit `https://vault.azure.net` . Dit token werkt niet voor resources die niet expliciet overeenkomen met deze waarde, zoals Graph.
- IAT (uitgegeven op): het aantal maat streepjes sinds het begin van de Epoch wanneer het token is uitgegeven.
- NBF (niet vóór): het aantal maat streepjes sinds het begin van de epoche wanneer dit token geldig wordt.
- EXP (verval datum): het aantal maat streepjes sinds het begin van de epoche wanneer dit token verloopt.
- AppID (toepassings-ID): de GUID voor de toepassings-ID die deze aanvraag indient.
- TID (Tenant-ID): de GUID voor de Tenant-ID van de principal die deze aanvraag indient

Het is belang rijk dat alle waarden correct worden geïdentificeerd in het token om de aanvraag te laten werken. Als alles correct is, resulteert de aanvraag niet in 401.

### <a name="troubleshooting-401"></a>Problemen oplossen 401

401s moet worden onderzocht vanaf het moment dat het token wordt gegenereerd, voordat de aanvraag wordt gedaan aan de sleutel kluis. In de meeste gevallen wordt code gebruikt om het token aan te vragen. Zodra het token is ontvangen, wordt het door gegeven aan de Key Vault aanvraag. Als de code lokaal wordt uitgevoerd, kunt u Fiddler gebruiken voor het vastleggen van de aanvraag/reactie op `https://login.microsoftonline.com` . Een aanvraag ziet er als volgt uit:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

De volgende door de gebruiker opgegeven informatie moet juist zijn:

- De Tenant-ID van de sleutel kluis
- Bron waarde ingesteld op https %3 A %2 F %2 F kluis. Azure. net (URL-code ring)
- Client-id
- Clientgeheim

Zorg ervoor dat de rest van de aanvraag bijna identiek is.

Als u alleen het toegangs token voor de reactie kunt ophalen, kunt u dit decoderen (zoals hierboven wordt weer gegeven) om ervoor te zorgen dat de Tenant-ID, de client-id (App-ID) en de bron.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: onvoldoende machtigingen

HTTP 403 betekent dat de aanvraag is geverifieerd (de aangevraagde identiteit kent), maar de identiteit heeft geen machtiging voor toegang tot de aangevraagde resource. Er zijn twee oorzaken:

- Er is geen toegangs beleid voor de identiteit.
- Het IP-adres van de aanvragende bron is niet goedgekeurd in de firewall instellingen van de sleutel kluis.

HTTP 403 treedt vaak op wanneer de toepassing van de klant niet de client-ID gebruikt die de klant denkt dat deze is. Dit betekent meestal dat het toegangs beleid niet juist is ingesteld voor de daad werkelijke aanroep-id.

### <a name="troubleshooting-403"></a>Problemen oplossen 403

Schakel eerst logboek registratie in. Zie [Azure Key Vault logboek registratie](logging.md)voor instructies over hoe u dit doet.

Zodra logboek registratie is ingeschakeld, kunt u bepalen of de 403 is vanwege het toegangs beleid of het firewall beleid.

#### <a name="error-due-to-firewall-policy"></a>Fout vanwege het firewall beleid

' Client adres (00.00.00.00) is niet geautoriseerd en de aanroeper is geen vertrouwde service '

Er is een beperkte lijst met ' vertrouwde ' Azure-Services. Azure-websites zijn **geen** vertrouwde Azure-service. Zie het blog bericht [Key Vault firewall toegang door Azure-app Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)voor meer informatie.

U moet het IP-adres van de Azure-website toevoegen aan de Key Vault om het te kunnen gebruiken.

Als gevolg van het toegangs beleid: Zoek de object-ID voor de aanvraag en zorg ervoor dat de object-ID overeenkomt met het object waarnaar de gebruiker het toegangs beleid probeert toe te wijzen. Er zijn vaak meerdere objecten in de AAD die dezelfde naam hebben, zodat het juiste belang rijk is. Door het toegangs beleid te verwijderen en opnieuw toe te voegen, is het mogelijk om te zien of er meerdere objecten met dezelfde naam bestaan.

Daarnaast is het gebruik van de ' geautoriseerde toepassing ' niet vereist voor het meeste toegangs beleid, zoals weer gegeven in de portal. Een geautoriseerde toepassing wordt gebruikt voor verificatie scenario's ' namens ' die zeldzaam zijn. 


## <a name="http-429-too-many-requests"></a>HTTP 429: te veel aanvragen

Beperking treedt op wanneer het aantal aanvragen het opgegeven maximum voor de periode overschrijdt. Als er sprake is van beperking, wordt het antwoord van de Key Vault HTTP 429. Er worden maximum waarden opgegeven voor typen aanvragen. Bijvoorbeeld: het maken van een HSM 2048-bits sleutel is 5 aanvragen per 10 seconden, maar alle andere HSM-trans acties hebben een limiet van 1000 aanvragen/tien seconden. Daarom is het belang rijk om te begrijpen welke typen aanroepen er worden gemaakt bij het bepalen van de oorzaak van de beperking.
Over het algemeen zijn aanvragen voor de Key Vault beperkt tot 2000 aanvragen/tien seconden. Uitzonde ringen zijn belang rijke bewerkingen, zoals beschreven in [Key Vault service limieten](service-limits.md)

### <a name="troubleshooting-429"></a>Problemen oplossen 429
Het gebruik van deze technieken verloopt als volgt:

- Verminder het aantal aanvragen voor de Key Vault door te bepalen of er patronen zijn naar een aangevraagde resource en te proberen deze op te slaan in de aanroepende toepassing. 

- Wanneer Key Vault beperking optreedt, past u de aanvraag code aan om een exponentiële uitstel te gebruiken om het opnieuw te proberen. De algoritme wordt hier uitgelegd: [uw app beperken](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Als het aantal aanvragen niet kan worden verkleind door caching en tijdgebonden uitstel niet werkt, kunt u de sleutels in meerdere sleutel kluizen splitsen. De service limiet voor één abonnement is 5x de limiet voor individuele Key Vault. Als u meer dan 5 sleutel kluizen gebruikt, moet aandacht worden besteed aan het gebruik van meerdere abonnementen. 

Gedetailleerde richt lijnen, waaronder aanvragen voor het verhogen van limieten, vindt u hier: [Key Vault beperkings richtlijnen](overview-throttling.md)
