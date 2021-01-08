---
title: App-rollen toevoegen en ophalen uit een token | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het toevoegen van app-rollen aan een toepassing die is geregistreerd in Azure Active Directory, het toewijzen van gebruikers en groepen aan deze rollen en het ontvangen ervan in de claim ' roles ' in het token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: b2ac90334ade52d68c775d9db5a84545774f3844
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013699"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Procedure: app-rollen toevoegen aan uw toepassing en deze ontvangen in het token

Op rollen gebaseerd toegangs beheer (RBAC) is een populair mechanisme voor het afdwingen van autorisatie in toepassingen. Wanneer u RBAC gebruikt, verleent een beheerder machtigingen aan rollen en niet aan afzonderlijke gebruikers of groepen. De beheerder kan vervolgens rollen toewijzen aan verschillende gebruikers en groepen om te bepalen wie toegang heeft tot de inhoud en functionaliteit.

Met RBAC met toepassings rollen en functie claims kunnen ontwikkel aars veilig autorisaties in hun apps afdwingen met minder inspanning.

Een andere benadering is het gebruik van Azure AD-groepen en groepclaims, zoals wordt weer gegeven in het code voorbeeld [Active-Directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) op github. Azure AD-groepen en toepassings rollen sluiten elkaar niet uit. ze kunnen samen worden gebruikt om nog nauw keuriger toegangs beheer te bieden.

## <a name="declare-roles-for-an-application"></a>Rollen voor een toepassing declareren

U kunt app-rollen definiëren met behulp van de [Azure Portal](https://portal.azure.com). App-rollen worden meestal gedefinieerd voor een toepassings registratie die een service, app of API vertegenwoordigt. Wanneer een gebruiker zich aanmeldt bij de toepassing, verzendt Azure AD een `roles` claim voor elke rol dat de gebruiker of Service-Principal afzonderlijk is verleend aan de gebruiker en aan het groepslid maatschap. Dit kan worden gebruikt voor het implementeren van autorisatie op basis van claims. App-rollen kunnen worden toegewezen [aan een gebruiker of een groep gebruikers](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app). App-rollen kunnen ook worden toegewezen aan de service-principal voor een andere toepassing, of [aan de service-principal voor een beheerde identiteit](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md).

> [!IMPORTANT]
> Als u op dit moment een service-principal aan een groep toevoegt en vervolgens een app-rol aan die groep toewijst, voegt Azure AD de claim niet toe `roles` aan tokens die IT-problemen.

Er zijn twee manieren om app-rollen te declareren met behulp van de Azure Portal:

* [Gebruikers interface voor app-rollen](#app-roles-ui--preview) | Voorbeeld
* [App-manifest editor](#app-manifest-editor)

Het aantal rollen dat u toevoegt, telt de toepassings manifest limieten die worden afgedwongen door Azure Active Directory. Voor informatie over deze limieten, zie de sectie  [manifest limieten](./reference-app-manifest.md#manifest-limits) van [Azure Active Directory app-manifest verwijzing](reference-app-manifest.md).

### <a name="app-roles-ui--preview"></a>Gebruikers interface voor app-rollen | Voorbeeld

> [!IMPORTANT]
> De UI-functie van de portal voor app-rollen [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Een app-rol maken met behulp van de gebruikers interface van de Azure Portal:

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies vervolgens de Azure Active Directory Tenant met de app-registratie waaraan u een app-functie wilt toevoegen.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **beheren** de optie **app-registraties** en selecteer vervolgens de toepassing waarvoor u de app-rollen wilt definiëren.
1. **App-rollen selecteren | Preview** en selecteer app- **functie maken**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Het deel venster app-rollen van een app-registratie in de Azure Portal":::
1. Voer in het deel venster **app-rollen maken** de instellingen voor de rol in. In de tabel die volgt op de afbeelding worden de instellingen en de bijbehorende para meters beschreven.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Met de app-rollen van een app-registratie wordt een context deel venster gemaakt in het Azure Portal":::

    | Veld | Beschrijving | Voorbeeld |
    |-------|-------------|---------|
    | **Weergavenaam** | Weergave naam voor de app-rol die wordt weer gegeven in de ervaringen beheerder en app-toewijzing. Deze waarde mag spaties bevatten. | `Survey Writer` |
    | **Toegestane leden typen** | Hiermee geeft u op of deze app-rol kan worden toegewezen aan gebruikers, toepassingen of beide.<br/><br/>Wanneer `applications` de app-rollen beschikbaar zijn voor, worden deze als toepassings machtigingen weer gegeven in de sectie **beheren** van een app-registratie > **API-machtigingen > een machtiging > mijn API'S toevoegen > een API > toepassings machtigingen te kiezen**. | `Users/Groups` |
    | **Waarde** | Hiermee geeft u de waarde van de functie claim op die de toepassing moet verwachten in het token. De waarde moet exact overeenkomen met de teken reeks waarnaar wordt verwezen in de code van de toepassing. De waarde mag geen spaties bevatten. | `Survey.Create` |
    | **Beschrijving** | Een gedetailleerde beschrijving van de app-rol die wordt weer gegeven tijdens de toewijzing van de beheer-app en de toestemming. | `Writers can create surveys.` |
    | **Wilt u deze app-functie inschakelen?** | Hiermee wordt opgegeven of de functie van de app is ingeschakeld. Als u een app-functie wilt verwijderen, schakelt u dit selectie vakje uit en past u de wijziging toe voordat u probeert de bewerking verwijderen uit te voeren. | *Ingeschakeld* |

1. Selecteer **Toepassen** om uw wijzigingen op te slaan.

### <a name="app-manifest-editor"></a>App-manifest editor

Als u rollen wilt toevoegen door het manifest rechtstreeks te bewerken:

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies vervolgens de Azure Active Directory Tenant met de app-registratie waaraan u een app-functie wilt toevoegen.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **beheren** de optie **app-registraties** en selecteer vervolgens de toepassing waarvoor u de app-rollen wilt definiëren.
1. Klik opnieuw onder **beheren** op **manifest**.
1. Bewerk het app-manifest door de `appRoles` instelling te zoeken en uw toepassings rollen toe te voegen. U kunt de app-rollen definiëren die het doel `users` hebben, `applications` of beide. De volgende JSON-fragmenten bevatten voor beelden van beide.
1. Sla het manifest op.

Elke functie definitie van een app in het manifest moet een unieke GUID hebben voor de `id` waarde.

De `value` eigenschap van elke roldefinitie van de app moet exact overeenkomen met de teken reeksen die worden gebruikt in de code in de toepassing. De `value` eigenschap mag geen spaties bevatten. Als dit het geval is, ontvangt u een fout melding wanneer u het manifest opslaat.

#### <a name="example-user-app-role"></a>Voor beeld: rol van gebruikers-app

In dit voor beeld wordt een app-functie gedefinieerd met de naam `Writer` die u kunt toewijzen aan een `User` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Voor beeld: app-rol van toepassing

Wanneer `applications` de app-rollen beschikbaar zijn voor, worden deze als toepassings machtigingen weer gegeven in de sectie **beheren** van een app-registratie > **API-machtigingen > een machtiging > mijn API'S toevoegen > een API > toepassings machtigingen te kiezen**.

In dit voor beeld ziet u een app-functie die is gericht op een `Application` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Gebruikers en groepen toewijzen aan rollen

Wanneer u app-rollen in uw toepassing hebt toegevoegd, kunt u gebruikers en groepen toewijzen aan de rollen. De toewijzing van gebruikers en groepen aan rollen kan worden uitgevoerd via de gebruikers interface van de portal of via een programma met behulp van [Microsoft Graph](/graph/api/user-post-approleassignments). Wanneer de gebruikers die zijn toegewezen aan de verschillende app-rollen zich aanmelden bij de toepassing, krijgen hun tokens hun toegewezen rollen in de `roles` claim.

Gebruikers en groepen toewijzen aan rollen met behulp van de Azure Portal:

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
1. In **Azure Active Directory** selecteert u **bedrijfs toepassingen** in het navigatie menu aan de linkerkant.
1. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven. Als uw toepassing niet wordt weer gegeven in de lijst, gebruikt u de filters boven aan de lijst **alle toepassingen** om de lijst te beperken, of schuif omlaag in de lijst om de toepassing te zoeken.
1. Selecteer de toepassing waarin u gebruikers of beveiligings groep aan rollen wilt toewijzen.
1. Selecteer onder **Beheren** de optie **Gebruikers en groepen**.
1. Selecteer **gebruiker toevoegen** om het deel venster **toewijzing toevoegen** te openen.
1. Selecteer de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** . Er wordt een lijst met gebruikers en beveiligings groepen weer gegeven. U kunt zoeken naar een bepaalde gebruiker of groep en meerdere gebruikers en groepen selecteren die in de lijst worden weer gegeven.
1. Zodra u gebruikers en groepen hebt geselecteerd, selecteert u de knop **selecteren** om door te gaan.
1. Selecteer **een rol selecteren** in het deel venster **toewijzing toevoegen** . Alle rollen die u voor de toepassing hebt gedefinieerd, worden weer gegeven.
1. Kies een rol en selecteer de knop **selecteren** .
1. Selecteer de knop **toewijzen** om de toewijzing van gebruikers en groepen aan de app te volt ooien.

Controleer of de gebruikers en groepen die u hebt toegevoegd, worden weer gegeven in de lijst **gebruikers en groepen** .

## <a name="assign-app-roles-to-applications"></a>App-rollen toewijzen aan toepassingen

Wanneer u app-rollen hebt toegevoegd in uw toepassing, kunt u een app-rol toewijzen aan een client-app met behulp van de Azure Portal of programmatisch met behulp van [Microsoft Graph](/graph/api/user-post-approleassignments).

Wanneer u app-rollen toewijst aan een toepassing, maakt u *toepassings machtigingen*. Toepassings machtigingen worden meestal gebruikt door daemon-apps of back-end-services die als zichzelf moeten worden geverifieerd en geautoriseerde API-aanroepen moeten maken, zonder de interactie van een gebruiker.

App-rollen toewijzen aan een toepassing met behulp van de Azure Portal:

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
1. Selecteer in **Azure Active Directory** **app-registraties** in het navigatie menu aan de linkerkant.
1. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven. Als uw toepassing niet wordt weer gegeven in de lijst, gebruikt u de filters boven aan de lijst **alle toepassingen** om de lijst te beperken, of schuif omlaag in de lijst om de toepassing te zoeken.
1. Selecteer de toepassing waaraan u een app-functie wilt toewijzen.
1. Selecteer **API-machtigingen** > **Een machtiging toevoegen**.
1. Selecteer het tabblad **mijn api's** en selecteer vervolgens de app waarvoor u app-rollen hebt gedefinieerd.
1. Selecteer **Toepassingsmachtigingen**.
1. Selecteer de rol (len) die u wilt toewijzen.
1. Selecteer de knop machtigingen toevoegen om de rol (len) **toe te voegen** .

De nieuw toegevoegde rollen moeten worden weer gegeven in het deel venster **API-machtigingen** van uw app-registratie.

#### <a name="grant-admin-consent"></a>Toestemming van beheerder verlenen

Omdat dit *machtigingen voor toepassingen* zijn, niet de gedelegeerde machtigingen, moet een beheerder toestemming verlenen om de app-rollen te gebruiken die aan de toepassing zijn toegewezen.

1. Selecteer in het deel venster **API-machtigingen** voor app-registratie de optie **beheerder toestemming geven voor \<tenant name\>**.
1. Selecteer **Ja** wanneer u wordt gevraagd toestemming te geven voor de aangevraagde machtigingen.

De kolom **status** moet overeenkomen met de **toegestane toestemming voor \<tenant name\>**.

## <a name="use-app-roles-in-your-web-api"></a>App-rollen gebruiken in uw web-API

Wanneer u app-rollen hebt gedefinieerd en deze hebt toegewezen aan een gebruiker, groep of toepassing, is de volgende stap het toevoegen van code aan uw web-API die controleert op die rollen wanneer de API wordt aangeroepen. Dat wil zeggen dat wanneer een client-app een API-bewerking aanvraagt die u hebt gekozen, autorisatie vereist, uw API-code moet controleren of de scopes zich in het toegangs token bevinden dat wordt weer gegeven in de aanroep van de client-app.

Zie voor meer informatie over het toevoegen van autorisatie aan uw web-API de [beveiligde web-API: scopes en app-rollen verifiëren](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>App-rollen versus groepen

Hoewel u app-rollen of-groepen kunt gebruiken voor autorisatie, zijn de belangrijkste verschillen tussen de functies die u besluit te gebruiken voor uw scenario.

| App-rollen                                                                          | Groepen                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Ze zijn specifiek voor een toepassing en worden gedefinieerd in de app-registratie. Ze worden met de toepassing verplaatst. | Ze zijn niet specifiek voor een app, maar voor een Azure AD-Tenant. |
| App-rollen worden verwijderd wanneer de app-registratie wordt verwijderd.                      | Groepen blijven intact, zelfs als de app wordt verwijderd.            |
| Gegeven in de `roles` claim.                                                     | Vermeld in `groups` claim.                                 |

Ontwikkel aars kunnen app-rollen gebruiken om te bepalen of een gebruiker zich kan aanmelden bij een app of een app kan een toegangs token verkrijgen voor een web-API. Als u dit beveiligings beheer wilt uitbreiden naar groepen, kunnen ontwikkel aars en beheerders ook beveiligings groepen toewijzen aan app-rollen.

App-rollen worden aanbevolen door ontwikkel aars wanneer ze de para meters van autorisatie in hun app zelf willen beschrijven en beheren. Een app die gebruikmaakt van groepen voor autorisatie, verbreekt bijvoorbeeld de volgende Tenant, aangezien de groeps-ID en de naam verschillend kunnen zijn. Een app die app-rollen gebruikt, blijft veilig. Het toewijzen van groepen aan app-rollen is in feite voor dezelfde redenen populair bij SaaS-apps.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over app-rollen met de volgende resources.

* Codevoorbeelden op GitHub
  * [Autorisatie toevoegen met behulp van groepen en groepclaims aan een ASP.NET Core web-app](https://aka.ms/groupssample)
  * [Hoek-toepassing met één pagina (SPA) die een .NET core web-API aanroept en app-rollen en beveiligings groepen gebruikt](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Referentiedocumentatie
  * [Azure AD-App-manifest](./reference-app-manifest.md)
  * [Azure AD-toegangs tokens](access-tokens.md)
  * [Azure AD ID-tokens](id-tokens.md)
  * [Optionele claims voor uw app opgeven](active-directory-optional-claims.md)
* Video: [autorisatie in uw toepassingen implementeren met het micro soft Identity platform](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
