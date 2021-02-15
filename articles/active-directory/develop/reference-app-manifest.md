---
title: Informatie over het Azure Active Directory app-manifest
titleSuffix: Microsoft identity platform
description: Gedetailleerde dekking van het app-manifest van de Azure Active Directory, dat de identiteits configuratie van een toepassing vertegenwoordigt in een Azure AD-Tenant, en wordt gebruikt om de OAuth-autorisatie, de toestemmings ervaring en nog veel meer te vergemakkelijken.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 02/02/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 47215070893c6fefd42554fdf4514e4aa8b45047
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103342"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-app-manifest

Het manifest van de toepassing bevat een definitie van alle kenmerken van een toepassings object in het micro soft Identity-platform. Het fungeert ook als mechanisme voor het bijwerken van het toepassings object. Zie de documentatie van de [Graph API toepassings entiteit](/graph/api/resources/application)voor meer informatie over de toepassings entiteit en het bijbehorende schema.

U kunt de kenmerken van een app configureren via de Azure Portal of programmatisch met behulp van [rest API](/graph/api/resources/application) of [Power shell](/powershell/module/azuread#applications). Er zijn echter enkele scenario's waarin u het app-manifest moet bewerken om het kenmerk van een app te configureren. Deze scenario's omvatten:

* Als u de app als Azure AD-multi tenant en persoonlijke micro soft-accounts hebt geregistreerd, kunt u de ondersteunde micro soft-accounts in de gebruikers interface niet wijzigen. In plaats daarvan moet u de Application manifest editor gebruiken om het ondersteunde account type te wijzigen.
* Als u machtigingen en rollen wilt definiëren die door uw app worden ondersteund, moet u het toepassings manifest wijzigen.

## <a name="configure-the-app-manifest"></a>Het app-manifest configureren

Het toepassings manifest configureren:

1. Ga naar de <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>. Zoek en selecteer de **Azure Active Directory** service.
1. Selecteer **App-registraties**.
1. Selecteer de app die u wilt configureren.
1. Selecteer op de pagina **Overzicht** van de app de sectie **Manifest**. Een manifest editor op het web wordt geopend, zodat u het manifest in de portal kunt bewerken. Desgewenst kunt u **downloaden** selecteren om het manifest lokaal te bewerken en vervolgens **uploaden** gebruiken om het opnieuw toe te passen op uw toepassing.

## <a name="manifest-reference"></a>Manifest verwijzing

In deze sectie worden de kenmerken beschreven die in het toepassings manifest worden gevonden.

### <a name="id-attribute"></a>kenmerk id

| Sleutel | Waardetype |
| :--- | :--- |
| id | Tekenreeks |

De unieke id voor de app in de Directory. Deze ID is niet de id die wordt gebruikt om de app in een protocol transactie te identificeren. Het wordt gebruikt voor het verwijzen naar het object in Directory-query's.

Voorbeeld:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| accessTokenAcceptedVersion | Int32 met Null-waarde |

Hiermee geeft u de versie van het toegangs token verwacht door de resource. Met deze para meter wijzigt u de versie en indeling van de JWT die onafhankelijk is gemaakt van het eind punt of de client die wordt gebruikt om het toegangs token aan te vragen.

Het eind punt dat wordt gebruikt, v 1.0 of v 2.0, wordt gekozen door de client en heeft alleen invloed op de versie van id_tokens. Resources moeten expliciet worden geconfigureerd `accesstokenAcceptedVersion` om de ondersteunde indeling van het toegangs token aan te geven.

Mogelijke waarden voor `accesstokenAcceptedVersion` zijn 1, 2 of null. Als de waarde Null is, wordt deze para meter standaard ingesteld op 1, die overeenkomt met het eind punt v 1.0.

Als `signInAudience` dat `AzureADandPersonalMicrosoftAccount` het geval is, moet de waarde zijn `2` .

Voorbeeld:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>Invoeg kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| addIns | Verzameling |

Hiermee wordt aangepast gedrag gedefinieerd dat een verbruikte service kan gebruiken om een app in specifieke contexten aan te roepen. Bijvoorbeeld: toepassingen die bestands stromen kunnen weer geven, kunnen de `addIns` eigenschap voor de ' FileHandler-functionaliteit instellen. Met deze para meter kunnen services zoals Microsoft 365 de toepassing aanroepen in de context van een document waarmee de gebruiker werkt.

Voorbeeld:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| allowPublicClient | Booleaans |

Hiermee wordt het type terugval toepassing opgegeven. Azure AD leidt het toepassings type standaard af van de replyUrlsWithType. Er zijn bepaalde scenario's waarin het app-type van de client niet kan worden bepaald door Azure AD. Een voor beeld hiervan is de [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) -stroom waarbij een HTTP-aanvraag wordt uitgevoerd zonder URL-omleiding). In dergelijke gevallen interpreteert Azure AD het toepassings type op basis van de waarde van deze eigenschap. Als deze waarde is ingesteld op True, wordt het type terugval toepassing ingesteld als open bare client, zoals een geïnstalleerde app die wordt uitgevoerd op een mobiel apparaat. De standaard waarde is False. Dit betekent dat het type terugval toepassing vertrouwelijk is, zoals web-app.

Voorbeeld:

```json
    "allowPublicClient": false,
```

### <a name="appid-attribute"></a>appId-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| appId | Tekenreeks |

Hiermee geeft u de unieke id op voor de app die is toegewezen aan een app door Azure AD.

Voorbeeld:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| appRoles | Verzameling |

Hiermee geeft u de verzameling rollen op die een app kan declareren. Deze rollen kunnen worden toegewezen aan gebruikers, groepen of service-principals. Zie [app-rollen toevoegen in uw toepassing en deze in het token ontvangen](howto-add-app-roles-in-azure-ad-apps.md)voor meer voor beelden en informatie.

Voorbeeld:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="errorurl-attribute"></a>errorUrl-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| errorUrl | Tekenreeks |

Niet-ondersteunde.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
|groupMembershipClaims | Tekenreeks |

Hiermee wordt de `groups` claim geconfigureerd die is uitgegeven in een gebruiker of OAuth 2,0-toegangs token dat de app verwacht. Als u dit kenmerk wilt instellen, gebruikt u een van de volgende geldige teken reeks waarden:

- `"None"`
- `"SecurityGroup"` (voor beveiligings groepen en Azure AD-rollen)
- `"ApplicationGroup"` (deze optie omvat alleen groepen die zijn toegewezen aan de toepassing)
- `"DirectoryRole"` (Hiermee worden de Azure AD-adreslijst functies opgehaald waarvan de gebruiker lid is)
- `"All"` (Hiermee worden alle beveiligings groepen, distributie groepen en Azure AD-adreslijst rollen opgehaald waarvan de aangemelde gebruiker lid is).

Voorbeeld:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="optionalclaims-attribute"></a>optionalClaims-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| optionalClaims | Tekenreeks |

De optionele claims die in het token worden geretourneerd door de beveiligings token service voor deze specifieke app.

Op dit moment kunnen apps die zowel persoonlijke accounts als Azure AD ondersteunen (geregistreerd via de app registratie-Portal) geen gebruikmaken van optionele claims. Apps die zijn geregistreerd voor Azure AD met behulp van het v 2.0-eind punt kunnen echter de optionele claims ophalen die ze in het manifest hebben aangevraagd. Zie voor meer informatie [optionele claims](active-directory-optional-claims.md).

Voorbeeld:

```json
    "optionalClaims": null,
```


### <a name="identifieruris-attribute"></a>identifierUris-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| identifierUris | Teken reeks matrix |

Door de gebruiker gedefinieerde URI (s) waarmee een web-app in de Azure AD-Tenant wordt geïdentificeerd, of binnen een geverifieerd aangepast domein als de app meerdere tenants is.

Voorbeeld:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| informationalUrls | Tekenreeks |

Hiermee geeft u de koppelingen naar de service voorwaarden en de privacyverklaring van de app op. De service voorwaarden en de privacyverklaring worden aan gebruikers door gegeven via de toestemming van de gebruiker. Zie [How to: Service voorwaarden toevoegen en privacyverklaring voor geregistreerde Azure AD-apps](howto-add-terms-of-service-privacy-statement.md)voor meer informatie.

Voorbeeld:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>kenmerk voor referentie gegevens

| Sleutel | Waardetype |
| :--- | :--- |
| keyCredentials | Verzameling |

Bevat verwijzingen naar referenties die aan de app zijn toegewezen, op teken reeks gebaseerde gedeelde geheimen en X. 509-certificaten. Deze referenties worden gebruikt bij het aanvragen van toegangs tokens (wanneer de app als een client fungeert, in plaats van als een resource).

Voorbeeld:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| knownClientApplications | Teken reeks matrix |

Wordt gebruikt voor bundeling-toestemming als u een oplossing hebt die twee onderdelen bevat: een client-app en een aangepaste web-API-app. Als u de appID van de client-app in deze waarde invoert, hoeft de gebruiker slechts eenmaal toestemming te geven aan de client-app. Azure AD weet dat de toestemming van de client impliciet wordt gestemd op de Web-API. De service-principals worden automatisch voor zowel de client als de Web-API ingericht. Zowel de client als de Web-API-app moeten zijn geregistreerd in dezelfde Tenant.

Voorbeeld:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| logoUrl | Tekenreeks |

Alleen-lezen waarde die verwijst naar de CDN-URL naar het logo dat is geüpload in de portal.

Voorbeeld:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| logoutUrl | Tekenreeks |

De URL voor het afmelden bij de app.

Voorbeeld:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>naam kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| naam | Tekenreeks |

De weergave naam voor de app.

Voorbeeld:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| oauth2AllowImplicitFlow | Booleaans |

Hiermee geeft u op of deze web-app OAuth 2.0 impliciet flow-toegangs tokens kan aanvragen. De standaardwaarde is false. Deze markering wordt gebruikt voor apps die zijn gebaseerd op de browser, zoals Java script-apps met één pagina. Als u meer wilt weten, voert u `OAuth 2.0 implicit grant flow` in de inhouds opgave in en raadpleegt u de onderwerpen over impliciete stroom.

Voorbeeld:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Booleaans |

Hiermee geeft u op of deze web-app OAuth 2.0 impliciete stroom-ID-tokens kan aanvragen. De standaardwaarde is false. Deze markering wordt gebruikt voor apps die zijn gebaseerd op de browser, zoals Java script-apps met één pagina.

Voorbeeld:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| oauth2Permissions | Verzameling |

Hiermee geeft u de verzameling OAuth 2,0-machtigings bereik op die de Web-API (resource)-app beschikbaar maakt voor client-apps. Deze machtigings bereiken kunnen worden verleend aan client-apps tijdens toestemming.

Voorbeeld:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| oauth2RequiredPostResponse | Booleaans |

Hiermee geeft u op of door Azure AD POST-aanvragen worden toegestaan als onderdeel van OAuth 2,0-token aanvragen. De standaard waarde is False. Hiermee wordt aangegeven dat alleen GET-aanvragen worden toegestaan.

Voorbeeld:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| parentalControlSettings | Tekenreeks |

- `countriesBlockedForMinors` Hiermee geeft u de landen/regio's op waarin de app is geblokkeerd voor minder jarigen.
- `legalAgeGroupRule` Hiermee geeft u de regel voor de juridische leeftijds groep op die van toepassing is op gebruikers van de app. Kan worden ingesteld op `Allow` , `RequireConsentForPrivacyServices` , `RequireConsentForMinors` , `RequireConsentForKids` of `BlockMinors` .

Voorbeeld:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| passwordCredentials | Verzameling |

Zie de beschrijving van de `keyCredentials` eigenschap.

Voorbeeld:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| preAuthorizedApplications | Verzameling |

Een lijst met toepassingen en aangevraagde machtigingen voor impliciete toestemming. Vereist dat een beheerder toestemming heeft gegeven voor de toepassing. de gebruiker is niet verplicht om toestemming te geven voor de aangevraagde machtigingen. De machtigingen die worden vermeld in preAuthorizedApplications, vereisen geen toestemming van de gebruiker. Andere aangevraagde machtigingen die niet worden vermeld in preAuthorizedApplications, vereisen echter toestemming van de gebruiker.

Voorbeeld:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publisherdomain-attribute"></a>publisherDomain-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| publisherDomain | Tekenreeks |

Het geverifieerde uitgevers domein voor de toepassing. Alleen-lezen.

Voorbeeld:

```json
    "publisherDomain": "{tenant}.onmicrosoft.com",
```

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| replyUrlsWithType | Verzameling |

Deze eigenschap met meerdere waarden bevat de lijst met geregistreerde redirect_uri waarden die Azure AD accepteert als doelen bij het retour neren van tokens. Elke URI-waarde moet een gekoppelde app-type waarde bevatten. Ondersteunde type waarden zijn:

- `Web`
- `InstalledClient`
- `Spa`

Zie [replyUrl-beperkingen en-beperkingen](./reply-url.md)voor meer informatie.

Voorbeeld:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| requiredResourceAccess | Verzameling |

Met dynamische toestemming `requiredResourceAccess` verstuurt de beheerder toestemming en wordt de gebruikers toestemming verleend voor gebruikers die een statische toestemming gebruiken. Deze para meter is echter niet van toepassing op de gebruikers toestemming voor algemeen gebruik.

- `resourceAppId` is de unieke id voor de resource waartoe de app toegang vereist. Deze waarde moet gelijk zijn aan de appId die is gedeclareerd voor de doel resource-app.
- `resourceAccess` is een matrix met de OAuth 2.0-machtigings bereiken en app-rollen die voor de app van de opgegeven resource zijn vereist. Bevat de `id` en- `type` waarden van de opgegeven resources.

Voorbeeld:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| samlMetadataUrl | Tekenreeks |

De URL naar de SAML-meta gegevens voor de app.

Voorbeeld:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| signInUrl | Tekenreeks |

Hiermee geeft u de URL naar de start pagina van de app op.

Voorbeeld:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience-kenmerk

| Sleutel | Waardetype |
| :--- | :--- |
| signInAudience | Tekenreeks |

Hiermee geeft u op welke micro soft-accounts voor de huidige toepassing worden ondersteund. Ondersteunde waarden zijn:
- `AzureADMyOrg` -Gebruikers met een werk-of school account van de Azure AD-Tenant van mijn organisatie (bijvoorbeeld één Tenant)
- `AzureADMultipleOrgs` -Gebruikers met een micro soft-werk-of school account in de Azure AD-Tenant van elke organisatie (bijvoorbeeld multi tenant)
- `AzureADandPersonalMicrosoftAccount` -Gebruikers met een persoonlijk Microsoft-account, of een werk-of school account in de Azure AD-Tenant van een organisatie
- `PersonalMicrosoftAccount` -Persoonlijke accounts die worden gebruikt om u aan te melden bij services zoals Xbox en Skype.

Voorbeeld:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>kenmerk Tags

| Sleutel | Waardetype |
| :--- | :--- |
| tags | Teken reeks matrix  |

Aangepaste teken reeksen die kunnen worden gebruikt voor het categoriseren en identificeren van de toepassing.

Voorbeeld:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Algemene problemen

### <a name="manifest-limits"></a>Limieten voor manifest

Een toepassings manifest heeft meerdere kenmerken die als verzamelingen worden aangeduid. bijvoorbeeld appRoles, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess en oauth2Permissions. In het volledige toepassings manifest voor elke toepassing is het totale aantal vermeldingen in alle verzamelingen gecombineerd op 1200. Als u in het manifest van de toepassing eerder 100 omleidings-Uri's hebt opgegeven, bent u nog niet meer met 1100 resterende vermeldingen voor gebruik in alle andere verzamelingen gecombineerd waaruit het manifest wordt gemaakt.

> [!NOTE]
> Als u probeert meer dan 1200 vermeldingen toe te voegen aan het toepassings manifest, ziet u mogelijk een fout **' kan de toepassing xxxxxx niet bijwerken. Fout Details: de limiet voor de grootte van het manifest is overschreden. Verminder het aantal waarden en probeer de aanvraag opnieuw uit te voeren. "**

### <a name="unsupported-attributes"></a>Niet-ondersteunde kenmerken

Het manifest van de toepassing vertegenwoordigt het schema van het onderliggende toepassings model in azure AD. Naarmate het onderliggende schema zich ontwikkelt, wordt de manifest editor bijgewerkt zodat het nieuwe schema van tijd tot tijd wordt weer gegeven. Als gevolg hiervan ziet u mogelijk nieuwe kenmerken die worden weer gegeven in het manifest van de toepassing. In zeldzame gevallen kunt u een syntactische of semantische wijziging in de bestaande kenmerken opmerken of een kenmerk dat eerder bestond niet meer wordt ondersteund. U ziet bijvoorbeeld nieuwe kenmerken in de [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908), die bekend zijn met een andere naam in de app-registraties (verouderde) ervaring.

| App-registraties (verouderd)| App-registraties           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Zie de sectie [manifest verwijzing](#manifest-reference) voor beschrijvingen van deze kenmerken.

Wanneer u een eerder gedownload manifest probeert te uploaden, ziet u mogelijk een van de volgende fouten. Deze fout is waarschijnlijk omdat de manifest editor nu ondersteuning biedt voor een nieuwere versie van het schema, wat niet overeenkomt met het bestand dat u wilt uploaden.

* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: ongeldige object-id niet gedefinieerd. []."
* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: een of meer opgegeven eigenschaps waarden zijn ongeldig. []."
* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: het is niet toegestaan om availableToOtherTenants in te stellen in deze API-versie voor de update. []."
* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: het bijwerken van de eigenschap replyUrls is niet toegestaan voor deze toepassing. Gebruik in plaats daarvan de eigenschap replyUrlsWithType. []."
* Het bijwerken van de xxxxxx-toepassing is mislukt. Fout Details: er is een waarde zonder type naam gevonden en er is geen verwacht type beschikbaar. Wanneer het model is opgegeven, moet elke waarde in de payload een type hebben dat kan worden opgegeven in de payload, expliciet door de aanroeper of impliciet afgeleid van de bovenliggende waarde. []"

Wanneer u een van deze fouten ziet, raden we u aan de volgende acties uit te voeren:

1. Bewerk de kenmerken afzonderlijk in de manifest editor in plaats van een eerder gedownload manifest te uploaden. Gebruik de [verwijzings](#manifest-reference) tabel van het manifest om inzicht te krijgen in de syntaxis en semantiek van oude en nieuwe kenmerken, zodat u de kenmerken kunt bewerken waarin u bent geïnteresseerd.
1. Als uw werk stroom vereist dat u de manifesten in uw bron opslagplaats opslaat voor later gebruik, wordt u aangeraden om de opgeslagen manifesten in uw opslag plaats te herbaseren met de map die u ziet in de **app-registraties** -ervaring.

## <a name="next-steps"></a>Volgende stappen

* Zie [Application and Service Principal Objects in azure AD](app-objects-and-service-principals.md)(Engelstalig) voor meer informatie over de relatie tussen de toepassing en Service-Principal-objecten van een app.
* Zie de [verklarende woorden lijst voor ontwikkel aars van micro soft Identity platform](developer-glossary.md) voor definities van sommige basis concepten voor ontwikkel aars van micro soft Identity platform.

Gebruik de volgende opmerkingen sectie om uw inhoud te verfijnen en vorm te geven.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
