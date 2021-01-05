---
title: Tokens voor servicetoegang ophalen
description: Hierin wordt beschreven hoe u tokens maakt voor toegang tot de ARR REST-Api's
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830697"
---
# <a name="get-service-access-tokens"></a>Tokens voor servicetoegang ophalen

Toegang tot de ARR REST-Api's wordt alleen verleend voor gemachtigde gebruikers. Als u uw autorisatie wilt bewijzen, moet u een *toegangs token* verzenden in combi natie met rest-aanvragen. Deze tokens worden uitgegeven door de *Secure token service* (STS) in Exchange voor een account sleutel. Tokens hebben een **levens duur van 24 uur** en kunnen daarom worden verleend aan gebruikers zonder dat ze volledige toegang tot de service geven.

In dit artikel wordt beschreven hoe u een dergelijk toegangs token maakt.

## <a name="prerequisites"></a>Vereisten

[Maak een ARR-account](create-an-account.md)als u er nog geen hebt.

## <a name="token-service-rest-api"></a>Token Service REST API

Voor het maken van toegangs tokens biedt de *Secure token service* een enkele rest API. De URL voor de STS-service is afhankelijk van het account domein van het externe rendering-account. Deze bevindt zich in de vorm https://sts . [ account domein], bijvoorbeeld `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>Aanvraag voor Token ophalen

| URI | Methode |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| Header | Waarde |
|--------|:------|
| Autorisatie | "Bearer- **accountId**:**accountKey**" |

Vervang *accountId* en *accountKey* door uw respectieve gegevens.

### <a name="get-token-response"></a>Het antwoord van het token ophalen

| Statuscode | JSON-nettolading | Opmerkingen |
|-----------|:-----------|:-----------|
| 200 | AccessToken: teken reeks | Success |

| Header | Doel |
|--------|:------|
| MS-CV | Deze waarde kan worden gebruikt om de aanroep binnen de service te traceren |

## <a name="getting-a-token-using-powershell"></a>Een Token ophalen met behulp van Power shell

De Power shell-code hieronder laat zien hoe u de benodigde REST-aanvraag naar de STS verzendt. Vervolgens wordt het token afgedrukt op de Power shell-prompt.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Met het script wordt alleen het token naar de uitvoer afgedrukt, waaruit u & plakken kunt kopiëren. Voor een echt project moet u dit proces automatiseren.

## <a name="next-steps"></a>Volgende stappen

* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
* [Azure front-end-Api's](../how-tos/frontend-apis.md)
* [REST API voor sessie beheer](../how-tos/session-rest-api.md)
