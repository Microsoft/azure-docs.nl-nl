---
title: De sessie beheer REST API
description: Hierin wordt beschreven hoe u sessies beheert
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: d957c5d6521010c7393e2297be16cd7bef41c35f
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724065"
---
# <a name="use-the-session-management-rest-api"></a>De REST API voor sessiebeheer gebruiken

Als u de functionaliteit voor externe rendering van Azure wilt gebruiken, moet u een *sessie* maken. Elke sessie komt overeen met een virtuele machine (VM) die wordt toegewezen in Azure en er wordt gewacht tot een client apparaat verbinding maakt. Wanneer een apparaat verbinding maakt, worden de aangevraagde gegevens door de VM weer gegeven en wordt het resultaat als een video stroom beschouwd. Tijdens het maken van de sessie hebt u gekozen voor welk type server u wilt uitvoeren, waarmee de prijzen worden bepaald. Zodra de sessie niet meer nodig is, moet deze worden gestopt. Als deze niet hand matig wordt gestopt, wordt deze automatisch afgesloten wanneer de *lease tijd* van de sessie verloopt.

We bieden een Power shell-script in de voor [beelden-opslag plaats](https://github.com/Azure/azure-remote-rendering) in de map *Scripts* , genaamd *RenderingSession.ps1*, waarin het gebruik van onze service wordt gedemonstreerd. Het script en de bijbehorende configuratie worden hier beschreven: [voor beelden van Power shell-scripts](../samples/powershell-example-scripts.md)

> [!TIP]
> De Power shell-opdrachten die op deze pagina worden weer gegeven, zijn bedoeld om elkaar aan te vullen. Als u alle scripts in volg orde uitvoert binnen dezelfde Power shell-opdracht prompt, worden ze boven op elkaar gebouwd.

## <a name="regions"></a>Regio's

Bekijk de [lijst met beschik bare regio's](../reference/regions.md) voor de basis-url's om de aanvragen naar te verzenden.

Voor de voorbeeld scripts hieronder hebt u de regio *westus2* gekozen.

### <a name="example-script-choose-an-endpoint"></a>Voorbeeld script: een eind punt kiezen

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Accounts

Als u geen externe rendering-account hebt, [maakt u er een](create-an-account.md). Elke resource wordt geïdentificeerd door een *accountId*, dat wordt gebruikt tijdens de sessie-api's.

### <a name="example-script-set-accountid-accountkey-and-account-domain"></a>Voorbeeld script: accountId instellen, accountKey en account domein

Account domein is de locatie van het externe rendering-account. In dit voor beeld is de locatie van het account regio *Oost*.

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
$accountDomain = "eastus.mixedreality.azure.com"
```

## <a name="common-request-headers"></a>Algemene aanvraag headers

* De *autorisatie* -header moet de waarde " `Bearer TOKEN` " hebben, waarbij " `TOKEN` " het verificatie token dat [door de Secure token service wordt geretourneerd](tokens.md).

### <a name="example-script-request-a-token"></a>Voorbeeld script: een token aanvragen

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Algemene antwoord headers

* De kop *MS-CV* kan door het product team worden gebruikt om de oproep binnen de service te traceren.

## <a name="create-a-session"></a>Een sessie maken

Met deze opdracht maakt u een sessie. Hiermee wordt de ID van de nieuwe sessie geretourneerd. U hebt de sessie-ID nodig voor alle andere opdrachten.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountId*/Sessions/Create | POST |

**Hoofd tekst van aanvraag:**

* maxLeaseTime (time span): een time-outwaarde wanneer de sessie automatisch wordt buiten gebruik gesteld
* modellen (matrix): URL van de Asset-container om vooraf te laden
* grootte (teken reeks): de te configureren server grootte ([**"Standard"**](../reference/vm-sizes.md) of [**"Premium"**](../reference/vm-sizes.md)). Bekijk de specifieke beperkingen voor de [grootte](../reference/limits.md#overall-number-of-polygons).

**Rapporten**

| Statuscode | JSON-nettolading | Opmerkingen |
|-----------|:-----------|:-----------|
| 202 | -sessionId: GUID | Success |

### <a name="example-script-create-a-session"></a>Voorbeeld script: een sessie maken

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Voorbeeld script: sessionId Store

Het antwoord van de bovenstaande aanvraag bevat een **SessionID**, die u nodig hebt voor alle follow-aanvragen.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="modify-and-query-session-properties"></a>Sessie-eigenschappen wijzigen en opvragen

Er zijn enkele opdrachten om de para meters van bestaande sessies op te vragen of te wijzigen.

> [!CAUTION]
> Net als bij alle REST-aanroepen wordt het verzenden van deze opdrachten te vaak vertraagd en wordt de server uiteindelijk niet meer geretourneerd. De status code in dit geval is 429 ("te veel aanvragen"). Als vuistregel moet er een vertraging van **5-10 seconden tussen opeenvolgende aanroepen** in acht worden genomen.

### <a name="update-session-parameters"></a>Sessie parameters bijwerken

Met deze opdracht worden de para meters van een sessie bijgewerkt. Op dit moment kunt u alleen de lease tijd van een sessie verlengen.

> [!IMPORTANT]
> De lease tijd wordt altijd gegeven als een totale tijd sinds het begin van de sessie. Dit betekent dat als u een sessie hebt gemaakt met een lease tijd van één uur en u de lease tijd voor een ander uur wilt verlengen, dan moet u de maxLeaseTime bijwerken naar twee uur.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountID*/Sessions/*SessionID* | VERZENDEN |

**Hoofd tekst van aanvraag:**

* maxLeaseTime (time span): een time-outwaarde wanneer de sessie automatisch wordt buiten gebruik gesteld

**Rapporten**

| Statuscode | JSON-nettolading | Opmerkingen |
|-----------|:-----------|:-----------|
| 200 | | Success |

#### <a name="example-script-update-a-session"></a>Voorbeeld script: een sessie bijwerken

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

### <a name="get-active-sessions"></a>Actieve sessies ophalen

Met deze opdracht wordt een lijst met actieve sessies geretourneerd.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountId*/Sessions | GET |

**Rapporten**

| Statuscode | JSON-nettolading | Opmerkingen |
|-----------|:-----------|:-----------|
| 200 | -sessies: matrix van sessie-eigenschappen | Zie de sectie ' sessie-eigenschappen ophalen ' voor een beschrijving van de sessie-eigenschappen |

#### <a name="example-script-query-active-sessions"></a>Voorbeeld script: actieve sessies opvragen

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

### <a name="get-sessions-properties"></a>Eigenschappen van de sessie ophalen

Met deze opdracht wordt informatie over een sessie geretourneerd, zoals de hostnaam van de VM.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountId*/Sessions/*SessionID*/Properties | GET |

**Rapporten**

| Statuscode | JSON-nettolading | Opmerkingen |
|-----------|:-----------|:-----------|
| 200 | -bericht: teken reeks<br/>-sessionElapsedTime: time span<br/>-sessionHostname: teken reeks<br/>-sessionId: teken reeks<br/>-sessionMaxLeaseTime: time span<br/>-sessionSize: Enum<br/>-sessionStatus: Enum | inventarisatie van sessionStatus {starten, gereed, stoppen, gestopt, verlopen, fout}<br/>Als de status ' fout ' of ' verlopen ' is, bevat het bericht meer informatie |

#### <a name="example-script-get-session-properties"></a>Voorbeeld script: sessie-eigenschappen ophalen

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Een sessie stoppen

Met deze opdracht wordt een sessie gestopt. De toegewezen VM wordt kort na geclaimd.

| URI | Methode |
|-----------|:-----------|
| /v1/accounts/*accountId*/Sessions/*SessionID* | DELETE |

**Rapporten**

| Statuscode | JSON-nettolading | Opmerkingen |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>Voorbeeld script: een sessie stoppen

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Voorbeelduitvoer:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Volgende stappen

* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
