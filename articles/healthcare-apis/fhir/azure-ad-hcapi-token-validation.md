---
title: Azure API voor FHIR-toegangs token validatie
description: Token validatie door lopen en tips geven over het oplossen van toegangs problemen
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018257"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API voor FHIR-toegangs token validatie

Hoe Azure API voor FHIR het toegangs token valideert, is afhankelijk van de implementatie en configuratie. In dit artikel gaan we de validatie stappen door lopen. Dit kan handig zijn bij het oplossen van toegangs problemen.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Token valideren heeft geen problemen met ID-provider

De eerste stap bij het valideren van tokens is om te controleren of het token is uitgegeven door de juiste ID-provider en niet is gewijzigd. De FHIR-server wordt geconfigureerd voor het gebruik van een specifieke ID-provider die bekend staat als de instantie `Authority` . De FHIR-server haalt informatie over de ID-provider op uit het `/.well-known/openid-configuration` eind punt. Wanneer u Azure AD gebruikt, is de volledige URL:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

waar `<TENANT-ID>` is de specifieke Azure AD-Tenant (een Tenant-id of een domein naam).

Azure AD retourneert een document zoals hieronder wordt weer gegeven op de FHIR-server.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
De belangrijkste eigenschappen voor de FHIR-server zijn `jwks_uri` , waarmee de server wordt geïnformeerd over het ophalen van de versleutelings sleutels die nodig zijn voor het valideren van de token handtekening en `issuer` , waarmee wordt aangegeven dat de server de Issuer claim ( `iss` ) van tokens heeft uitgegeven door deze server. De FHIR-server kan deze gebruiken om te controleren of er een authentiek token wordt ontvangen.

## <a name="validate-claims-of-the-token"></a>Claims van het token valideren

Zodra de server de authenticiteit van het token heeft geverifieerd, wordt door de FHIR-server gecontroleerd of de client de vereiste claims heeft voor toegang tot het token.

Wanneer u de Azure API voor FHIR gebruikt, wordt door de server gevalideerd:

1. Het token heeft het recht `Audience` ( `aud` claim).
1. De gebruiker of Principal waarvoor het token is uitgegeven, is gemachtigd om toegang te krijgen tot het FHIR Server-gegevens vlak. De `oid` claim van het token bevat een identiteits object-id, waarmee de gebruiker of Principal uniek wordt geïdentificeerd.

We raden u aan de FHIR-service [zo te configureren dat deze gebruikmaakt van Azure RBAC](configure-azure-rbac.md) voor het beheren van roltoewijzingen voor het gegevens vlak. U kunt ook [lokale RBAC configureren](configure-local-rbac.md) als uw FHIR-service gebruikmaakt van een externe of secundaire Azure Active Directory Tenant. 

Wanneer u de OSS micro soft FHIR-server voor Azure gebruikt, wordt door de server gevalideerd:

1. Het token heeft het recht `Audience` ( `aud` claim).
1. Het token heeft een rol in de `roles` claim, die toegang heeft tot de FHIR-server.

Raadpleeg de informatie over het [definiëren van rollen op de FHIR-server](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

Een FHIR-server kan ook valideren dat een toegangs token de scopes (in token claim `scp` ) heeft om toegang te krijgen tot het deel van de FHIR-API dat een client probeert te openen. Momenteel valideert de Azure-API voor FHIR en de FHIR-server voor Azure geen token bereik.

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u de token validatie doorloopt, kunt u de zelf studie volt ooien om een Java script-toepassing te maken en FHIR-gegevens te lezen.

>[!div class="nextstepaction"]
>[Zelf studie voor webtoepassingen](tutorial-web-app-fhir-server.md)