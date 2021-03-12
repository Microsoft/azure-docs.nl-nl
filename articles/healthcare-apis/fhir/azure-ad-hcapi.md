---
title: Azure Active Directory identiteits configuratie voor de Azure-API voor FHIR
description: Meer informatie over de principes van identiteit, authenticatie en autorisatie voor Azure FHIR-servers.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 277838e3ce870b528f986292f88ad2b2b20f42b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018251"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Azure Active Directory identiteits configuratie voor de Azure-API voor FHIR

Een belang rijk onderdeel bij het werken met gezondheids gegevens is om ervoor te zorgen dat de gegevens veilig zijn en niet toegankelijk zijn voor onbevoegde gebruikers of toepassingen. FHIR-servers gebruiken [OAuth 2,0](https://oauth.net/2/) om deze gegevens beveiliging te garanderen. De [Azure API voor FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) is beveiligd met [Azure Active Directory](../../active-directory/index.yml), een voor beeld van een OAuth 2,0-ID-provider. Dit artikel bevat een overzicht van de FHIR-server autorisatie en de benodigde stappen voor het verkrijgen van een token voor toegang tot een FHIR-server. Hoewel deze stappen van toepassing zijn op elke FHIR-server en een id-provider, gaan we Azure API voor FHIR als de FHIR-server en Azure AD als onze ID-provider in dit artikel door lopen.

## <a name="access-control-overview"></a>Overzicht van toegangsbeheer

Om een client toepassing toegang te geven tot de Azure API voor FHIR, moet er een toegangs token worden opgegeven. Het toegangs token is een ondertekende, [Base64](https://en.wikipedia.org/wiki/Base64) -gecodeerde verzameling eigenschappen (claims) die informatie over de identiteit en rollen en bevoegdheden van de client verstrekt.

Er zijn een aantal manieren om een token op te halen, maar de Azure API voor FHIR is niet van belang om te bepalen hoe het token wordt verkregen zolang het een correct ondertekend token met de juiste claims is. 

Door gebruik te maken van [autorisatie code flow](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) als voor beeld, kunt u de volgende vier stappen gebruiken om toegang te krijgen tot een FHIR-server:

![FHIR-autorisatie](media/azure-ad-hcapi/fhir-authorization.png)

1. De client verzendt een aanvraag naar het `/authorize` eind punt van Azure AD. De client wordt door Azure AD doorgestuurd naar een aanmeldings pagina waar de gebruiker wordt geverifieerd met de juiste referenties (bijvoorbeeld gebruikers naam en wacht woord of twee ledige verificatie). Zie de details over [het verkrijgen van een autorisatie code](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Bij een geslaagde verificatie wordt een *autorisatie code* geretourneerd naar de client. In azure AD mag deze autorisatie code alleen worden geretourneerd naar een geregistreerde antwoord-URL die in de registratie van de client toepassing is geconfigureerd (zie hieronder).
1. De client toepassing wisselt de autorisatie code uit voor een *toegangs token* op het `/token` eind punt van Azure AD. Bij het aanvragen van een token moet de client toepassing mogelijk een client geheim opgeven (het wacht woord van de toepassing). Zie de details over [het verkrijgen van een toegangs token](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. De client maakt een aanvraag voor de Azure-API voor FHIR, bijvoorbeeld `GET /Patient` om alle patiënten te doorzoeken. Bij het maken van de aanvraag bevat deze het toegangs token in een header van een HTTP-aanvraag, bijvoorbeeld `Authorization: Bearer eyJ0e...` waar `eyJ0e...` het token met base64-code ring wordt aangeduid.
1. De Azure API voor FHIR valideert dat het token de juiste claims (eigenschappen in het token) bevat. Als alles wordt uitgecheckt, wordt de aanvraag voltooid en wordt een FHIR-bundel met resultaten naar de client geretourneerd.

Het is belang rijk te weten dat de Azure API voor FHIR niet betrokken is bij het valideren van de gebruikers referenties en de token niet heeft uitgegeven. De verificatie en het maken van het token worden uitgevoerd door Azure AD. De Azure API voor FHIR valideert gewoon of het token correct is ondertekend (het is authentiek) en of het juiste claims heeft.

## <a name="structure-of-an-access-token"></a>Structuur van een toegangs token

Het ontwikkelen van FHIR-toepassingen omvat vaak fout opsporingsgegevens. Als een client geen toegang meer heeft tot de Azure API voor FHIR, is het handig om de structuur van het toegangs token te begrijpen en hoe deze kan worden gedecodeerd om de inhoud (de claims) van het token te controleren. 

FHIR-servers verwachten doorgaans een [JSON Web token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, soms ' noteren '). Het bestaat uit drie onderdelen:

1. Een kop, die er als volgt uitziet:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. De payload (de claims), bijvoorbeeld:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Een hand tekening die wordt berekend door het samen voegen van de base64-gecodeerde inhoud van de header en de payload en het berekenen van een cryptografische hash op basis van het algoritme ( `alg` ) dat is opgegeven in de koptekst. Een server kan open bare sleutels van de identiteits provider verkrijgen en valideren dat dit token is uitgegeven door een specifieke ID-provider en er niet mee is geknoeid.

Het volledige token bestaat uit de Base64 Encoded-versies (met feitelijke base64-URL-code ring) van deze drie segmenten. De drie segmenten worden samengevoegd en gescheiden met een `.` (punt).

Hieronder vindt u een voorbeeld token:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Het token kan worden gedecodeerd en geinspectet met hulpprogram ma's zoals [https://jwt.ms](https://jwt.ms) . Het resultaat van het decoderen van het token is:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Een toegangstoken verkrijgen

Zoals hierboven vermeld, zijn er verschillende manieren om een token van Azure AD te verkrijgen. Deze worden gedetailleerd beschreven in de [documentatie voor Azure AD-ontwikkel aars](../../active-directory/develop/index.yml).

Azure AD heeft twee verschillende versies van de OAuth 2,0-eind punten, die worden aangeduid als `v1.0` and `v2.0` . Beide versies zijn OAuth 2,0-eind punten en de- `v1.0` en- `v2.0` ontwerpen verwijzen naar verschillen in de manier waarop Azure AD de standaard implementeert. 

Wanneer u een FHIR-server gebruikt, kunt u de `v1.0` of de `v2.0` eind punten gebruiken. De keuze kan afhankelijk zijn van de verificatie bibliotheken die u gebruikt in uw client toepassing.

De relevante secties van de Azure AD-documentatie zijn:

* `v1.0`-eindpunt:
    * [Autorisatie code stroom](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [Client referentie stroom](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).
* `v2.0`-eindpunt:
    * [Autorisatie code stroom](../../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [Client referentie stroom](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

Er zijn andere variaties (bijvoorbeeld voor stroom) voor het verkrijgen van een token. Raadpleeg de documentatie van Azure AD voor meer informatie. Wanneer u de Azure-API voor FHIR gebruikt, zijn er ook enkele snelkoppelingen voor het verkrijgen van een toegangs token (voor fout opsporing) [met behulp van de Azure cli](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u enkele van de basis concepten geleerd van het beveiligen van de toegang tot de Azure API voor FHIR met behulp van Azure AD. Ga verder naar de Snelstartgids voor implementatie voor meer informatie over het implementeren van een exemplaar van de Azure API voor FHIR.

>[!div class="nextstepaction"]
>[De Azure-API voor FHIR implementeren](fhir-paas-portal-quickstart.md)