---
title: Postman FHIR-server in Azure - Azure API for FHIR
description: In deze zelf studie gaan we de stappen door lopen die nodig zijn om postman te gebruiken voor toegang tot een FHIR-server. Postman is handig voor het opsporen van fouten in toepassingen die toegang hebben tot API's.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 03/16/2021
ms.openlocfilehash: e9031dc77054a2bbac8015bbbdd7b9ed2a35e84f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043339"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Azure API for FHIR openen met Postman

Een client toepassing kan toegang krijgen tot de Azure API voor FHIR via een [rest API](https://www.hl7.org/fhir/http.html). Als u aanvragen wilt verzenden, reacties wilt weer geven en fouten wilt opsporen in uw toepassing terwijl deze wordt gebouwd, gebruikt u een API-test programma van uw keuze. In deze zelf studie begeleidt u de stappen voor het openen van de FHIR-server met behulp van [postman](https://www.getpostman.com/). 

## <a name="prerequisites"></a>Vereisten

- Een FHIR-eindpunt in Azure. 

   Als u de Azure API voor FHIR (een beheerde service) wilt implementeren, kunt u de [Azure Portal](fhir-paas-portal-quickstart.md), [Power shell](fhir-paas-powershell-quickstart.md)of [Azure cli](fhir-paas-cli-quickstart.md)gebruiken.
- Een geregistreerde [vertrouwelijke client toepassing](register-confidential-azure-ad-client-app.md) voor toegang tot de FHIR-service.
- U hebt machtigingen verleend aan de vertrouwelijke client toepassing, bijvoorbeeld ' FHIR data contributor ', om toegang te krijgen tot de FHIR-service. Zie [Azure RBAC configureren voor FHIR](./configure-azure-rbac.md)voor meer informatie.
- Postman is geïnstalleerd. 
    
    Zie [aan de slag met postman](https://www.getpostman.com)voor meer informatie over postman.

## <a name="fhir-server-and-authentication-details"></a>Details van FHIR-server en verificatie

Als u postman wilt gebruiken, zijn de volgende verificatie parameters vereist:

- De URL van uw FHIR-server, bijvoorbeeld `https://MYACCOUNT.azurehealthcareapis.com`

- De id-provider `Authority` voor uw FHIR-server, bijvoorbeeld `https://login.microsoftonline.com/{TENANT-ID}`

- De geconfigureerde `audience` die meestal de URL van de FHIR-server is, bijvoorbeeld `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` of `https://azurehealthcareapis.com` .

- De `client_id` of toepassings-id van de [vertrouwelijke client toepassing](register-confidential-azure-ad-client-app.md) die wordt gebruikt voor toegang tot de FHIR-service.

- Het `client_secret` of toepassings geheim van de vertrouwelijke client toepassing.

Controleer ten slotte of `https://www.getpostman.com/oauth2/callback` een geregistreerde antwoord-URL voor uw clienttoepassing is.

## <a name="connect-to-fhir-server"></a>Verbinding maken met de FHIR-server

Open postman en selecteer vervolgens **ophalen** om een aanvraag in te stellen `https://fhir-server-url/metadata` .

![Mogelijkheidsinstructie voor Postman-metagegevens](media/tutorial-postman/postman-metadata.png)

De metagegevens-URL voor de Azure API for FHIR is `https://MYACCOUNT.azurehealthcareapis.com/metadata`. 

In dit voor beeld is de URL van de FHIR `https://fhirdocsmsft.azurewebsites.net` -Server en is de instructie Capability van de server beschikbaar op `https://fhirdocsmsft.azurewebsites.net/metadata` . Dit eind punt is zonder verificatie toegankelijk.

Als u toegang probeert te krijgen tot beperkte bronnen, wordt het antwoord ' verificatie is mislukt ' weer gegeven.

![De verificatie is mislukt](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Een toegangstoken verkrijgen
Als u een geldig toegangs token wilt verkrijgen, selecteert u **autorisatie** en selecteert u **OAuth 2,0** in de vervolg keuzelijst **type** .

![OAuth 2.0 instellen](media/tutorial-postman/postman-select-oauth2.png)

Selecteer **Nieuw toegangstoken ophalen**.

![Nieuw toegangstoken aanvragen](media/tutorial-postman/postman-request-token.png)

Voer in het dialoog venster **nieuw toegangs Token ophalen** de volgende gegevens in:

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
| Status                |  `1234`                                                                                                           |                            |
| Clientauthenticatie | Clientreferenties in hoofdtekst verzenden                                                                                 |                 

Selecteer een **aanvraag token** om door de Azure Active Directory verificatie stroom te worden geleid en een token wordt geretourneerd naar de Postman. Als er een verificatie fout optreedt, raadpleegt u de Postman-console voor meer informatie. **Opmerking**: Selecteer in het lint **weer gave** en selecteer vervolgens de optie **postman console weer geven**. De sneltoets voor de Postman-console is **ALT + C**.

Schuif omlaag om het scherm geretourneerde token weer te geven en selecteer vervolgens **token gebruiken**.

![Token gebruiken](media/tutorial-postman/postman-use-token.png)

Raadpleeg het veld **toegangs token** om het zojuist ingevulde token weer te geven. Als u **verzenden** selecteert om het zoeken naar resources te herhalen `Patient` , wordt een **status** `200 OK` geretourneerd. Een geretourneerde status `200 OK` geeft aan dat er een geslaagde HTTP-aanvraag is.

![200 OK](media/tutorial-postman/postman-200-OK.png)

In het *Zoek* voorbeeld van patiënten zijn er geen patiënten in de data base, zodat het Zoek resultaat leeg is.

U kunt het toegangs token controleren met een hulp programma zoals [JWT.MS](https://jwt.ms). Hieronder ziet u een voor beeld van de inhoud.

```json
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

Als u problemen probeert op te lossen, kunt u het beste beginnen met een validatie of u de juiste doelgroep hebt (`aud`-claim). Als uw token afkomstig is van de juiste verlener ( `iss` claim) en de juiste doel groep ( `aud` claim) heeft, maar u nog steeds geen toegang hebt tot de FHIR-API, is het waarschijnlijk dat de gebruiker of Service-Principal ( `oid` claim) geen toegang heeft tot het gegevens vlak FHIR. U wordt aangeraden [Azure RBAC (op rollen gebaseerd toegangs beheer)](configure-azure-rbac.md) te gebruiken om gegevenslaag rollen toe te wijzen aan gebruikers. Als u een externe, secundaire Azure Active Directory-Tenant gebruikt voor uw gegevens vlak, moet u [lokale RBAC configureren voor FHIR](configure-local-rbac.md) -toewijzingen.

Het is ook mogelijk om een token voor de [Azure-API voor FHIR op te halen met behulp van de Azure cli](get-healthcare-apis-access-token-cli.md). Als u een token gebruikt dat is verkregen met de Azure CLI, moet u het token voor autorisatie type *Bearer* gebruiken. Plak het token rechtstreeks in.

## <a name="inserting-a-patient"></a>Een patiënt invoegen

Met een geldig toegangs token kunt u nu een nieuwe patiënt invoegen. Wijzig in postman de methode door **post** te selecteren en voeg vervolgens het volgende JSON-document toe aan de hoofd tekst van de aanvraag.

[!code-json[](../samples/sample-patient.json)]

Selecteer **verzenden** om te bepalen of de patiënt is gemaakt.

![Schermopname waarop te zien is dat het maken van de patiënt is geslaagd.](media/tutorial-postman/postman-patient-created.png)

Als u de zoekopdracht voor de patiënt herhaalt, ziet u nu de patiëntrecord:

![De patiënt is gemaakt](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de Azure API voor FHIR geopend met behulp van postman. Zie voor meer informatie over de Azure API voor FHIR-functies.
 
>[!div class="nextstepaction"]
>[Ondersteunde functies](fhir-features-supported.md)
