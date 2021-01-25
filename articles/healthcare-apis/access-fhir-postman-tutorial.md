---
title: Postman FHIR-server in Azure - Azure API for FHIR
description: In deze zelfstudie doorlopen we de stappen die nodig zijn om Postman te gebruiken voor toegang tot een FHIR-server. Postman is handig voor het opsporen van fouten in toepassingen die toegang hebben tot API's.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/07/2019
ms.openlocfilehash: b0acfbf9636c2ad1df7df3197a0a1a83ec91b5c3
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747337"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Azure API for FHIR openen met Postman

Een clienttoepassing wil toegang tot een FHIR-API via een [REST API](https://www.hl7.org/fhir/http.html). Mogelijk wilt u ook rechtstreeks met de FHIR-server communiceren tijdens het bouwen van toepassingen, zoals voor foutopsporing. In deze zelfstudie doorlopen we de stappen die nodig zijn om [Postman](https://www.getpostman.com/) te gebruiken voor toegang tot een FHIR-server. Postman is een hulpprogramma dat vaak wordt gebruikt voor foutopsporing bij het bouwen van toepassingen die toegang tot API's hebben.

## <a name="prerequisites"></a>Vereisten

- Een FHIR-eindpunt in Azure. U kunt dat instellen met behulp van de beheerde Azure API for FHIR of de Open Source FHIR-server voor Azure. Stel de beheerde Azure API for FHIR in met behulp van [Azure Portal](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md)of [Azure CLI](fhir-paas-cli-quickstart.md).
- Een [ clienttoepassing](register-confidential-azure-ad-client-app.md) die u gebruikt om toegang te krijgen tot de FHIR-service.
- Postman is geïnstalleerd. U kunt het programma downloaden op [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>Details van FHIR-server en verificatie

U hebt de volgende gegevens nodig om Postman te gebruiken:

- De URL van uw FHIR-server, bijvoorbeeld `https://MYACCOUNT.azurehealthcareapis.com`
- De id-provider `Authority` voor uw FHIR-server, bijvoorbeeld `https://login.microsoftonline.com/{TENANT-ID}`
- De geconfigureerde `audience`. Dit is doorgaans de URL van de FHIR-server, bijvoorbeeld `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` of alleen `https://azurehealthcareapis.com` .
- De `client_id` (of toepassings-id) van de [clienttoepassing](register-confidential-azure-ad-client-app.md) die u gebruikt om toegang te krijgen tot de FHIR-service.
- De `client_secret` (of het toepassingsgeheim) van de clienttoepassing.

Controleer ten slotte of `https://www.getpostman.com/oauth2/callback` een geregistreerde antwoord-URL voor uw clienttoepassing is.

## <a name="connect-to-fhir-server"></a>Verbinding maken met de FHIR-server

Voer met behulp van Postman een `GET`-aanvraag uit naar `https://fhir-server-url/metadata`:

![Mogelijkheidsinstructie voor Postman-metagegevens](media/tutorial-postman/postman-metadata.png)

De metagegevens-URL voor de Azure API for FHIR is `https://MYACCOUNT.azurehealthcareapis.com/metadata`. In dit voorbeeld is de URL van de FHIR-server `https://fhirdocsmsft.azurewebsites.net`. De mogelijkheidsinstructie van de server is beschikbaar op `https://fhirdocsmsft.azurewebsites.net/metadata`. Dit eindpunt moet toegankelijk zijn zonder verificatie.

Als u toegang probeert te krijgen tot beperkte resources, wordt de reactie De verificatie is mislukt weergegeven:

![De verificatie is mislukt](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Een toegangstoken verkrijgen

Selecteer Autorisatie en kies TYPE OAuth 2.0 als u een geldig toegangstoken wilt verkrijgen:

![OAuth 2.0 instellen](media/tutorial-postman/postman-select-oauth2.png)

Klik op Nieuw toegangstoken ophalen. Er wordt dan een dialoogvenster weergegeven:

![Nieuw toegangstoken aanvragen](media/tutorial-postman/postman-request-token.png)

U hebt de volgende informatie nodig:

| Veld                 | Voorbeeldwaarde                                                                                                   | Opmerking                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Tokennaam            | MYTOKEN                                                                                                         | Een door u gekozen naam          |
| Toekenningstype            | Autorisatiecode                                                                                              |                            |
| URL voor aanroep          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| URL van autorisatie              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` is `https://MYACCOUNT.azurehealthcareapis.com` voor Azure API for FHIR |
| URL van toegangstoken      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| Client-id             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | Toepassings-id             |
| Clientgeheim         | `XXXXXXXX`                                                                                                        | Geheime clientsleutel          |
| Bereik | `<Leave Blank>` |
| Status                 | `1234`                                                                                                            |                            |
| Clientauthenticatie | Clientreferenties in hoofdtekst verzenden                                                                                 |                 

Klik op Token aanvragen. U wordt dan door de verificatiestroom voor Azure Active Directory geleid en er wordt een token geretourneerd naar Postman. Open de Postman-console (via het menu-item Weergave -> Postman-console weergeven) als u problemen ondervindt.

Schuif omlaag in het scherm met het geretourneerde token en Selecteer Token gebruiken:

![Token gebruiken](media/tutorial-postman/postman-use-token.png)

Het token moet nu worden ingevuld in het veld Toegangstoken. U kunt tokens selecteren onder Beschikbare tokens. Als u gegevens opnieuw verzendt om de zoekopdracht naar `Patient`-resources te herhalen, moet dit resulteren in Status `200 OK`:

![200 OK](media/tutorial-postman/postman-200-OK.png)

In dit geval bevat de database geen patiënten en is de zoekopdracht leeg.

Als u het toegangstoken inspecteert met een hulpprogramma zoals [https://jwt.ms](https://jwt.ms), ziet u inhoud zoals:

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Als u problemen probeert op te lossen, kunt u het beste beginnen met een validatie of u de juiste doelgroep hebt (`aud`-claim). Als uw token van de juiste certificaatverlener (`iss`-claim) afkomstig is en de juiste doelgroep (`aud`-claim) heeft, maar u nog steeds geen toegang tot de FHIR-API hebt, is het waarschijnlijk dat de gebruiker of service-principal (`oid`-claim) geen toegang heeft tot het FHIR-gegevensvlak. U wordt aangeraden om [op rollen gebaseerd toegangsbeheer van Azure](configure-azure-rbac.md) (Azure RBAC) te gebruiken om gegevenslaagrollen toe te wijzen aan gebruikers. Als u een externe, secundaire Azure Active Directory-tenant voor uw gegevensvlak gebruikt, moet u [lokale RBAC-toewijzingen configureren](configure-local-rbac.md).

Het is ook mogelijk om [een token voor de Azure API for FHIR op te halen met behulp van de Azure CLI](get-healthcare-apis-access-token-cli.md). Als u een token gebruikt dat met de Azure CLI is verkregen, moet u het autorisatietype Bearer-token gebruiken en het token rechtstreeks plakken.

## <a name="inserting-a-patient"></a>Een patiënt invoegen

Nu u een geldig toegangstoken hebt, kunt u een nieuwe patiënt invoegen. Schakel over naar de methode POST en voeg het volgende JSON-document toe aan de hoofdtekst van de aanvraag:

[!code-json[](samples/sample-patient.json)]

Klik op Verzenden. U ziet dan dat de patiënt is gemaakt:

![Schermopname waarop te zien is dat het maken van de patiënt is geslaagd.](media/tutorial-postman/postman-patient-created.png)

Als u de zoekopdracht voor de patiënt herhaalt, ziet u nu de patiëntrecord:

![De patiënt is gemaakt](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een FHIR-API geopend met behulp van Postman. Meer informatie over de ondersteunde API-functies vindt u in de sectie over ondersteunde functies.
 
>[!div class="nextstepaction"]
>[Ondersteunde functies](fhir-features-supported.md)
