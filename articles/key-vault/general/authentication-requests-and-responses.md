---
title: Verificatie, vragen en antwoorden
description: Meer informatie over hoe Azure Key Vault aanvragen voor JSON-indeling en antwoorden en over vereiste verificatie voor het gebruik van een sleutel kluis gebruikt.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 58616b647affd33e96357e556ab61f85d1c62129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752274"
---
# <a name="authentication-requests-and-responses"></a>Verificatie, vragen en antwoorden

Azure Key Vault biedt twee typen containers voor het opslaan en beheren van geheimen voor uw Cloud toepassingen:

|Container type|Ondersteunde object typen|Data-vlak eindpunt|
|--|--|--|
| **Kluizen**|<ul><li>Met software beveiligde sleutels</li><li>Met HSM beveiligde sleutels (met Premium-SKU)</li><li>Certificaten</li><li>Opslagaccountsleutels</li></ul> | https://{vault-name}.vault.azure.net
|**Beheerde HSM** |<ul><li>HSM-beveiligde sleutels</li></ul> | https://{hsm-name}.managedhsm.azure.net

Hier vindt u de URL-achtervoegsels die worden gebruikt voor toegang tot elk type object

|Objecttype|URL-achtervoegsel|
|--|--|
|Met software beveiligde sleutels| /keys |
|HSM-beveiligde sleutels| /keys |
|Geheimen|/secrets|
|Certificaten| /certificates|
|Opslagaccountsleutels|/storageaccounts
||

Azure Key Vault ondersteunt aanvragen en antwoorden in JSON-indeling. Aanvragen voor de Azure Key Vault worden omgeleid naar een geldige Azure Key Vault URL met behulp van HTTPS met enkele URL-para meters en JSON-gecodeerde aanvraag-en antwoord teksten.

In dit onderwerp worden de Details voor de Azure Key Vault-service beschreven. Zie voor algemene informatie over het gebruik van Azure REST-interfaces, met inbegrip van verificatie/autorisatie en het verkrijgen van een toegangs token, [referentie voor Azure rest API](/rest/api/azure).

## <a name="request-url"></a>Aanvraag-URL  
 Voor sleutel beheer bewerkingen worden HTTP POST gebruikt voor het verwijderen, ophalen, PATCHen, plaatsen en HTTP POST-en cryptografische bewerkingen voor bestaande sleutel objecten. Clients die geen specifieke HTTP-woorden kunnen ondersteunen, kunnen ook HTTP POST gebruiken met de X-HTTP-REQUEST-header om de gewenste term op te geven. aanvragen waarvoor normaal gesp roken geen hoofd tekst vereist is, moeten een lege hoofd tekst bevatten wanneer u HTTP POST gebruikt, bijvoorbeeld door POST te gebruiken in plaats van verwijderen.  

 Voor het werken met objecten in de Azure Key Vault, zijn de volgende Url's:  

- Voor het maken van een sleutel met de naam TESTKEY in een Key Vault gebruik- `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Als u een sleutel met de naam IMPORTEDKEY wilt importeren in een Key Vault gebruik- `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Een geheim met de naam MYSECRET verkrijgen in een Key Vault gebruik- `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Een samen vatting ondertekenen met behulp van een sleutel met de naam TESTKEY in een Key Vault gebruik- `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

- De instantie voor een aanvraag voor een Key Vault is altijd als volgt:
  - Voor kluizen: `https://{keyvault-name}.vault.azure.net/`
  - Voor beheerde Hsm's: `https://{HSM-name}.managedhsm.azure.net/`

  Sleutels worden altijd opgeslagen onder het pad/Keys, geheimen worden altijd opgeslagen onder het pad/Secrets.  

## <a name="api-version"></a>API-versie  
 De Azure Key Vault-service ondersteunt Protocol versie beheer om compatibiliteit te bieden met downlevelclients, hoewel niet alle mogelijkheden beschikbaar zijn voor deze clients. Clients moeten de `api-version` query teken reeks parameter gebruiken om de versie op te geven van het protocol dat wordt ondersteund, omdat er geen standaard waarde is.  

 Azure Key Vault-protocol versies volgen een schema voor datum nummering met behulp van een {jjjj}. {MM}. Indeling van {DD}.  

## <a name="request-body"></a>Aanvraagbody  
 Volgens de HTTP-specificatie moeten GET-bewerkingen geen aanvraag tekst hebben en moeten POST-en PUT-bewerkingen een aanvraag tekst hebben. De hoofd tekst in DELETE-bewerkingen is optioneel in HTTP.  

 Tenzij anders vermeld in de beschrijving van de bewerking, moet het inhouds type van de aanvraag hoofdtekst application/json zijn en moet het een geserialiseerd JSON-object bevatten dat voldoet aan het inhouds type.  

 Tenzij anders vermeld in de beschrijving van de bewerking, moet de aanvraag header accepteren het media type application/json bevatten.  

## <a name="response-body"></a>Antwoord tekst  
 Tenzij anders vermeld in de beschrijving van de bewerking, is het inhouds type van de antwoord hoofdtekst van zowel geslaagde als mislukte bewerkingen van toepassing/JSON en bevat deze gedetailleerde informatie over de fout.  

## <a name="using-http-post"></a>HTTP POST gebruiken  
 Sommige clients kunnen bepaalde HTTP-woorden, zoals PATCH of verwijderen, mogelijk niet gebruiken. Azure Key Vault ondersteunt HTTP POST als alternatief voor deze clients, mits de client ook de header X-HTTP-METHOD bevat voor een specifieke HTTP-term. Ondersteuning voor HTTP POST wordt vermeld voor elk van de API'S die in dit document zijn gedefinieerd.  

## <a name="error-responses"></a>Fout berichten  
 Fout afhandeling maakt gebruik van HTTP-status codes. Typische resultaten zijn:  

- 2xx – geslaagd: wordt gebruikt voor normale bewerking. De antwoord tekst bevat het verwachte resultaat  

- 3xx-omleiding: de 304 ' niet gewijzigd ' kan worden geretourneerd om aan een voorwaardelijke GET te voldoen. Andere 3xx-codes kunnen in de toekomst worden gebruikt om DNS-en pad wijzigingen aan te duiden.  

- 4xx: client fout: wordt gebruikt voor ongeldige aanvragen, ontbrekende sleutels, syntaxis fouten, ongeldige para meters, verificatie fouten, enzovoort. De antwoord tekst bevat gedetailleerde informatie over de fout.  

- 5xx – server fout: wordt gebruikt voor interne server fouten. De antwoord tekst bevat een overzicht van de fout gegevens.  

  Het systeem is ontworpen om achter een proxy of firewall te werken. Daarom kan een client andere fout codes ontvangen.  

  Azure Key Vault retourneert ook fout informatie in de antwoord tekst wanneer er een probleem optreedt. De antwoord tekst is JSON-indeling en maakt deel uit van het volgende formulier:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Verificatie  
 Alle aanvragen voor Azure Key Vault moeten worden geverifieerd. Azure Key Vault ondersteunt Azure Active Directory toegangs tokens die kunnen worden verkregen met behulp van OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Zie [uw client toepassing registreren bij Azure AD](/rest/api/azure/index#register-your-client-application-with-azure-ad)voor meer informatie over het registreren van uw toepassing en het verifiëren van het gebruik van Azure Key Vault.
 
 Toegangs tokens moeten naar de service worden verzonden met de HTTP-autorisatie-header:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Als er geen toegangs token is opgegeven of wanneer een token niet wordt geaccepteerd door de service, wordt er een HTTP 401-fout geretourneerd naar de client en wordt de WWW-Authenticate-header opgenomen, bijvoorbeeld:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 De para meters in de WWW-Authenticate-header zijn:  

-   autorisatie: het adres van de OAuth2-autorisatie service dat kan worden gebruikt om een toegangs token voor de aanvraag te verkrijgen.  

-   resource: de naam van de resource ( `https://vault.azure.net` ) die in de autorisatie aanvraag moet worden gebruikt.

> [!NOTE]
> Key Vault SDK-clients voor geheimen, certificaten en sleutels in de eerste aanroep van Key Vault bieden geen toegangs token om Tenant gegevens op te halen. Het is naar verwachting een HTTP 401 te ontvangen met behulp van Key Vault SDK-client, waarbij de Key Vault laat zien dat de toepassing de WWW-Authenticate-header met de resource en de Tenant bevat, waar deze zich moet bezoeken en om het token te vragen. Als alles correct is geconfigureerd, zal de tweede aanroep van de toepassing naar Key Vault een geldig token bevatten en slagen. 
