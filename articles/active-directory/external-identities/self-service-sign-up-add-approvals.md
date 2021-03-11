---
title: Aangepaste goed keuringen toevoegen aan self-service-aanmeld stromen-Azure AD
description: API-connectors toevoegen voor aangepaste goedkeurings werk stromen in externe identiteiten self-service Sign-up-Azure Active Directory (Azure AD)
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95274f42da7f6cac9b193504df834232d7c0eb90
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609979"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>Een aangepaste goedkeurings werk stroom toevoegen aan self-service registratie

Met [API-connectors](api-connectors-overview.md)kunt u integreren met uw eigen aangepaste goedkeurings werk stromen met een self-service registratie, zodat u kunt beheren welke gast gebruikers accounts in uw Tenant worden gemaakt.

Dit artikel bevat een voor beeld van hoe u kunt integreren met een goedkeurings systeem. In dit voor beeld worden tijdens het registratie proces gebruikers gegevens verzameld door de self-service-aanmeldings stroom en door gegeven aan uw goedkeurings systeem. Vervolgens kan het goedkeurings systeem:

- De gebruiker automatisch goed keuren en Azure AD toestaan om het gebruikers account te maken.
- Activeer hand matige beoordeling. Als de aanvraag is goedgekeurd, gebruikt het goedkeurings systeem Microsoft Graph om het gebruikers account in te richten. Het goedkeurings systeem kan ook de gebruiker laten weten dat hun account is gemaakt.

> [!IMPORTANT]
>**Vanaf 4 januari 2021** wordt [ondersteuning voor WebView-aanmelding afgeschaft](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) in Google. Als u gebruikmaakt van Google-federatie of selfserviceregistratie met Gmail, moet u [de compatibiliteit van uw systeemeigen Line-of-Business-toepassingen testen](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="register-an-application-for-your-approval-system"></a>Een toepassing registreren voor uw goedkeurings systeem

U moet uw goedkeurings systeem registreren als een toepassing in uw Azure AD-Tenant, zodat het kan worden geverifieerd met Azure AD en toestemming heeft om gebruikers te maken. Meer informatie over de [basis principes van verificatie en autorisatie voor Microsoft Graph](/graph/auth/auth-concepts).

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Onder **Azure-Services** selecteert u **Azure Active Directory**.
3. Selecteer in het menu links **app-registraties** en selecteer vervolgens **nieuwe registratie**.
4. Voer een **naam** in voor de toepassing, bijvoorbeeld _goed keuringen voor registratie_.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. Selecteer **Registreren**. U kunt de standaard waarden van andere velden laten staan.

   ![Scherm opname van de knop registreren.](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. Selecteer onder **beheren** in het menu links de optie **API-machtigingen** en selecteer vervolgens **een machtiging toevoegen**.
7. Selecteer op de pagina **API-machtigingen voor aanvragen** de optie **Microsoft Graph** en selecteer vervolgens **toepassings machtigingen**.
8. Onder **machtigingen selecteren**, vouwt u **gebruiker** en vervolgens het selectie vakje **gebruiker. readwrite. all** in. Met deze machtiging kan het goedkeurings systeem de gebruiker maken na goed keuring. Selecteer vervolgens **machtigingen toevoegen**.

   ![Een toepassings pagina registreren](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. Selecteer op de pagina **API-machtigingen** de optie **beheerder toestemming geven voor (uw Tenant naam)** en selecteer vervolgens **Ja**.
10. Selecteer onder **beheren** in het linkermenu **certificaten & geheimen** en selecteer vervolgens **Nieuw client geheim**.
11. Voer een **Beschrijving** in voor het geheim, bijvoorbeeld _goed keuringen client geheim_, en selecteer de duur voor wanneer het client geheim **verloopt**. Selecteer vervolgens **Toevoegen**.
12. Kopieer de waarde van het client geheim.

    ![Het client geheim kopiëren voor gebruik in het goedkeurings systeem](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. Configureer uw goedkeurings systeem om de **toepassings-id** te gebruiken als de client-id en het **client geheim** dat u hebt gegenereerd voor verificatie met Azure AD.

## <a name="create-the-api-connectors"></a>De API-connectors maken

Vervolgens maakt u [de API-connectors](self-service-sign-up-add-api-connector.md#create-an-api-connector) voor uw Self-service voor het registreren van uw gebruikers. Uw goedkeurings systeem-API heeft twee connectors en de bijbehorende eind punten nodig, zoals hieronder wordt weer gegeven. Deze API-connectors doen het volgende:

- **Goedkeurings status controleren**. Een oproep verzenden naar het goedkeurings systeem direct nadat een gebruiker zich heeft aangemeld met een id-provider om te controleren of de gebruiker een bestaande goedkeurings aanvraag heeft of al is geweigerd. Als uw goedkeurings systeem alleen automatische goedkeurings beslissingen heeft, is deze API-connector mogelijk niet nodig. Voor beeld van de API-connector "goedkeurings status controleren".

  ![De configuratie van de goedkeurings status API-connector controleren](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **Goed keuring aanvragen** : een aanroep naar het goedkeurings systeem verzenden nadat een gebruiker de pagina kenmerk verzameling heeft voltooid, maar voordat het gebruikers account is gemaakt, om goed keuring aan te vragen. De goedkeurings aanvraag kan automatisch worden verleend of hand matig worden gecontroleerd. Voor beeld van een API-connector voor het goed keuren van aanvragen. 

  ![Configuratie van API-connector voor goed keuring aanvragen](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

Als u deze connectors wilt maken, volgt u de stappen in [een API-connector maken](self-service-sign-up-add-api-connector.md#create-an-api-connector).

## <a name="enable-the-api-connectors-in-a-user-flow"></a>De API-connectors inschakelen in een gebruikers stroom

Nu voegt u de API-Connect oren toe aan een self-service voor het aanmelden van een gebruiker met de volgende stappen:

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com/).
2. Onder **Azure-Services** selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **gebruikers stromen** en selecteer vervolgens de gebruikers stroom waarvoor u de API-connector wilt inschakelen.
5. Selecteer **API-connectors** en selecteer vervolgens de API-eind punten die u wilt aanroepen met de volgende stappen in de gebruikers stroom:

   - **Nadat u zich hebt aangemeld met een id-provider**: Selecteer uw goedkeurings status API-connector, bijvoorbeeld _goedkeurings status controleren_.
   - **Voordat u de gebruiker maakt**: Selecteer de API-connector voor de goedkeurings aanvraag, bijvoorbeeld _goed keuring aanvragen_.

   ![Api's toevoegen aan de gebruikers stroom](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. Selecteer **Opslaan**.

## <a name="control-the-sign-up-flow-with-api-responses"></a>De registratie stroom beheren met API-antwoorden

Uw goedkeurings systeem kan gebruikmaken van de reacties wanneer deze worden aangeroepen om de registratie stroom te beheren. 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>Aanvraag en antwoorden voor de API-connector ' goedkeurings status controleren '

Voor beeld van de aanvraag die wordt ontvangen door de API van de API-connector ' goedkeurings status controleren ':

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

De exacte claims die worden verzonden naar de API, zijn afhankelijk van welke informatie wordt verstrekt door de ID-provider. e-mail bericht wordt altijd verzonden.

#### <a name="continuation-response-for-check-approval-status"></a>Vervolg reactie op ' goedkeurings status controleren '

Het API-eind punt van de **goedkeurings status** moet een vervolg reactie retour neren als:

- De gebruiker heeft eerder geen goed keuring aangevraagd.

Voor beeld van de vervolg reactie:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>Reactie op ' goedkeurings status controleren ' wordt geblokkeerd

Het API-eind punt van de **goedkeurings status** moet een blokkerende reactie retour neren als:

- Goed keuring van de gebruiker is in behandeling.
- De gebruiker is geweigerd en mag niet opnieuw goed keuring aanvragen.

Hier volgen enkele voor beelden van blokkerende antwoorden:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>Aanvraag en antwoorden voor de API-connector aanvraag goed keuren

Voor beeld van een HTTP-aanvraag die wordt ontvangen door de API van de API-connector ' goed keuring aanvragen ':

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

De exacte claims die worden verzonden naar de API, zijn afhankelijk van welke gegevens van de gebruiker worden verzameld of door de ID-provider worden verstrekt.

#### <a name="continuation-response-for-request-approval"></a>Vervolg reactie op ' goed keuring aanvragen '

Het API-eind punt voor het **goed keuren van aanvragen** moet een vervolg reactie retour neren als:

- De gebruiker kan **_automatisch worden goedgekeurd_**.

Voor beeld van de vervolg reactie:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> Als er een vervolg reactie wordt ontvangen, maakt Azure AD een gebruikers account en stuurt de gebruiker naar de toepassing.

#### <a name="blocking-response-for-request-approval"></a>Reactie op ' goed keuring aanvragen ' blok keren

Het API-eind punt voor het **goed keuren van aanvragen** moet een blokkerende reactie retour neren als:

- Er is een aanvraag voor het goed keuren van een gebruiker gemaakt en deze is nu in behandeling.
- Een aanvraag voor het goed keuren van een gebruiker is automatisch geweigerd.

Hier volgen enkele voor beelden van blokkerende antwoorden:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

De `userMessage` in het antwoord wordt weer gegeven voor de gebruiker, bijvoorbeeld:

![Voor beeld van goed keuring pagina in behandeling](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>Gebruikers account maken na hand matige goed keuring

Na het verkrijgen van hand matige goed keuring maakt het aangepaste goedkeurings systeem een [gebruikers](/graph/azuread-users-concept-overview) account met behulp van [Microsoft Graph](/graph/use-the-api). De manier waarop uw goedkeurings systeem de gebruikers account inricht, is afhankelijk van de ID-provider die door de gebruiker is gebruikt.

### <a name="for-a-federated-google-or-facebook-user-and-email-one-time-passcode"></a>Voor een federatieve Google-of Facebook-gebruiker en E-mail eenmalige wachtwoord code

> [!IMPORTANT]
> Het goedkeurings systeem moet expliciet controleren `identities` of `identities[0]` en `identities[0].issuer` aanwezig zijn en gelijk zijn aan `identities[0].issuer` Facebook, Google of mail om deze methode te gebruiken.

Als uw gebruiker zich heeft aangemeld met een Google-of Facebook-account of een wacht woord voor eenmalige E-mail, kunt u de API voor het [maken van gebruikers](/graph/api/user-post-users?tabs=http)gebruiken.

1. Met het goedkeurings systeem wordt de HTTP-aanvraag van de gebruikers stroom ontvangen.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Het goedkeurings systeem gebruikt Microsoft Graph om een gebruikers account te maken.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| Parameter                                           | Vereist | Beschrijving                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | Ja      | Kan worden gegenereerd door de `email` claim te verzenden naar de API, het teken te vervangen door `@` `_` en vooraf in behandeling te nemen tot `#EXT@<tenant-name>.onmicrosoft.com` . |
| accountEnabled                                      | Ja      | Moet worden ingesteld op `true`.                                                                                                                                                 |
| mail                                                | Ja      | Gelijk aan de `email` claim die wordt verzonden naar de API.                                                                                                               |
| userType                                            | Ja      | Moet zijn `Guest` . Hiermee wordt deze gebruiker aangeduid als een gast gebruiker.                                                                                                                 |
| identiteit                                          | Ja      | De gegevens voor federatieve identiteiten.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | Nee       | Andere ingebouwde kenmerken `displayName` , zoals, `city` en anderen. Parameter namen zijn hetzelfde als de para meters die worden verzonden door de API-connector.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | Nee       | Aangepaste kenmerken van de gebruiker. Parameter namen zijn hetzelfde als de para meters die worden verzonden door de API-connector.                                                            |

### <a name="for-a-federated-azure-active-directory-user-or-microsoft-account-user"></a>Voor een federatieve Azure Active Directory gebruiker of Microsoft-account gebruiker

Als een gebruiker zich aanmeldt met een federatieve Azure Active Directory account of een Microsoft-account, moet u de [API voor uitnodigingen](/graph/api/invitation-post) gebruiken om de gebruiker te maken en vervolgens de [API voor gebruikers updates](/graph/api/user-update) om meer kenmerken aan de gebruiker toe te wijzen.

1. Het goedkeurings systeem ontvangt de HTTP-aanvraag van de gebruikers stroom.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Het goedkeurings systeem maakt de uitnodiging met behulp van de `email` API-connector.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress": "johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

Voor beeld van het antwoord:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. Het goedkeurings systeem gebruikt de gebruikers-ID van de uitgenodigde gebruiker om het account van de gebruiker bij te werken met verzamelde gebruikers kenmerken (optioneel).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met onze voor [beelden van Azure function Quick](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)start.
- Het afhandelen [van de self-service registratie voor gast gebruikers met hand matige goed keuring voor beeld](code-samples-self-service-sign-up.md#custom-approval-workflows).
