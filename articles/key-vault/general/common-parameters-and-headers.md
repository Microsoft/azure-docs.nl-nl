---
title: Algemene parameters en headers
description: De parameters en headers die gemeenschappelijk zijn voor alle bewerkingen die u kunt uitvoeren met betrekking tot Key Vault resources.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 616b6061b08258d465b09902556de6903b873199
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749866"
---
# <a name="common-parameters-and-headers"></a>Algemene parameters en headers

De volgende informatie is gebruikelijk voor alle bewerkingen die u kunt uitvoeren met betrekking tot Key Vault resources:

- De `Host` HTTP-header moet altijd aanwezig zijn en moet de hostnaam van de kluis opgeven. Bijvoorbeeld: `Host: contoso.vault.azure.net`. Houd er rekening mee dat de meeste clienttechnologieën de `Host` header van de URI vullen. Stelt bijvoorbeeld `GET https://contoso.vault.azure.net/secrets/mysecret{...}` de `Host` in als `contoso.vault.azure.net` . Dit betekent dat als u toegang hebt tot Key Vault met behulp van raw IP-adres, zoals , de automatische waarde van header onjuist is en u handmatig moet ervoor zorgen dat de header de hostnaam van de kluis `GET https://10.0.0.23/secrets/mysecret{...}` `Host` `Host` bevat.
- Vervang `{api-version}` door de API-versie in de URI.
- Vervang `{subscription-id}` door uw abonnements-id in de URI
- Vervang `{resource-group-name}` door de resourcegroep. Zie Resourcegroepen gebruiken om Azure-resources te beheren voor meer informatie.
- Vervang `{vault-name}` door de naam van uw sleutelkluis in de URI.
- Stel de Content-Type-header in op application/json.
- Stel de autorisatie-header in op een JSON Web Token die u van Azure Active Directory (AAD) hebt ontvangen. Zie Authenticating Azure Resource Manager requests [(Aanvragen voor Azure Resource Manager](authentication-requests-and-responses.md) authenticeren) voor meer informatie.

## <a name="common-error-response"></a>Veelvoorkomende foutreactie
De service gebruikt HTTP-statuscodes om aan te geven of de service is geslaagd of mislukt. Bovendien bevatten fouten een antwoord in de volgende indeling:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elementnaam | Type | Description |
|---|---|---|
| code | tekenreeks | Het type fout dat is opgetreden.|
| message | tekenreeks | Een beschrijving van de oorzaak van de fout. |



## <a name="see-also"></a>Zie ook
 [Azure Key Vault REST API referentie](/rest/api/keyvault/)
