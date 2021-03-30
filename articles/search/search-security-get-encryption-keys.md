---
title: Informatie over versleutelings sleutel ophalen
titleSuffix: Azure Cognitive Search
description: Haal de naam van de versleutelings sleutel en versie op die wordt gebruikt in een index of synoniemen kaart, zodat u de sleutel in Azure Key Vault kunt beheren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88932896"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>Door de klant beheerde sleutel gegevens ophalen van indexen en synoniemen kaarten

In azure Cognitive Search worden door de klant beheerde versleutelings sleutels gemaakt, opgeslagen en beheerd in Azure Key Vault. Als u wilt bepalen of een object is versleuteld of welke sleutel naam of versie is gebruikt, gebruikt u de REST API of een SDK om de eigenschap **encryptionKey** op te halen uit een definitie van een index of synoniemen kaart. 

Het is raadzaam om [logboek registratie in te scha kelen](../key-vault/general/logging.md) op Key Vault zodat u het sleutel gebruik kunt controleren.

## <a name="get-the-admin-api-key"></a>De beheer-API-sleutel ophalen

Als u object definities van een zoek service wilt ophalen, moet u zich verifiëren met beheerders rechten. De eenvoudigste manier om de beheer-API-sleutel op te halen, is via de portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en open de pagina overzicht van de zoek service.

1. Klik aan de linkerkant op **sleutels** en kopieer een beheer-API. Er is een beheerders sleutel vereist voor het ophalen van index-en synoniemen.

Voor de resterende stappen gaat u naar Power shell en de REST API. In de portal worden geen synoniemen en de eigenschappen van de versleutelings sleutel van indexen weer gegeven.

## <a name="use-powershell-and-rest"></a>Power shell en REST gebruiken

Voer de volgende opdrachten uit om de variabelen in te stellen en object definities op te halen.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet welke versleutelings sleutel en versie worden gebruikt, kunt u de sleutel in Azure Key Vault beheren of andere configuratie-instellingen controleren.

+ [Quickstart: Een geheim uit Azure Key Vault instellen en ophalen met PowerShell](../key-vault/secrets/quick-create-powershell.md)

+ [Door de klant beheerde sleutels voor gegevens versleuteling configureren in azure Cognitive Search](search-security-manage-encryption-keys.md)