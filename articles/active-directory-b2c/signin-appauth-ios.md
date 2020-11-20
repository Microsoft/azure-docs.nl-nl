---
title: AppAuth gebruiken in een iOS-toepassing
titleSuffix: Azure AD B2C
description: Een iOS-app maken die gebruikmaakt van AppAuth met Azure Active Directory B2C om gebruikers identiteiten te beheren en gebruikers te verifiëren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6064bd2c62922abea44508b8bf6cdfa3e7ecbc92
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953301"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: aanmelden met een iOS-toepassing

Op het Microsoft Identity-platform wordt gebruikgemaakt van open standaarden, zoals OAuth2 en OpenID Connect. Het gebruik van een open standaard-protocol biedt meer keuze voor ontwikkel aars bij het selecteren van een bibliotheek om te integreren met onze services. We hebben deze walkthrough en andere gebruikers hiervan op de hulp om ontwikkel aars te helpen bij het schrijven van toepassingen die verbinding maken met het micro soft-identiteits platform. De meeste bibliotheken die [de RFC6749 OAuth2-spec](https://tools.ietf.org/html/rfc6749) implementeren, kunnen verbinding maken met het micro soft-identiteits platform.

> [!WARNING]
> Micro soft biedt geen oplossingen voor bibliotheken van derden en heeft geen beoordeling uitgevoerd van die bibliotheken. In dit voor beeld wordt een bibliotheek van derden gebruikt met de naam AppAuth die is getest op compatibiliteit in basis scenario's met de Azure AD B2C. Problemen en functie aanvragen moeten worden omgeleid naar het open-source project van de bibliotheek. Zie [dit artikel](../active-directory/develop/reference-v2-libraries.md)voor meer informatie.
>
>

Als u geen ervaring hebt met OAuth2 of OpenID Connect, is het mogelijk dat veel van deze voorbeeld configuratie niet veel zinvol is voor u. U wordt geadviseerd eerst een beknopt [overzicht van het hier gedocumenteerde protocol te bekijken](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Een Azure AD B2C-directory maken
Voordat u Azure AD B2C kunt gebruiken, moet u een directory, of tenant, maken. Een directory is een container voor al uw gebruikers, apps, groepen en meer. Als u nog geen directory hebt, [maakt u een B2C-directory](tutorial-create-tenant.md) voordat u verdergaat.

## <a name="create-an-application"></a>Een app maken

Registreer vervolgens een toepassing in uw Azure AD B2C-Tenant. Dit geeft Azure AD de informatie die nodig is om veilig te communiceren met uw app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Noteer de **Toepassings-id (client)** voor gebruik in een latere stap.

Neem ook uw aangepaste omleidings-URI op voor gebruik in een latere stap. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Uw gebruikers stromen maken
In Azure AD B2C wordt elke gebruikers ervaring gedefinieerd door een [gebruikers stroom](user-flow-overview.md). Deze toepassing bevat één identiteits ervaring: een gecombineerde aanmelding en registratie. Wanneer u de gebruikers stroom maakt, moet u het volgende doen:

* Selecteer de **weergave naam** van het kenmerk onder **aanmeldings kenmerken**.  U kunt ook andere kenmerken selecteren.
* Selecteer onder **toepassings claims** de **weergave naam** van de claims en de **object-id van de gebruiker**. U kunt ook andere claims selecteren.
* Kopieer de **naam** van elke gebruikers stroom nadat u deze hebt gemaakt. De naam van de gebruikers stroom wordt voorafgegaan door `b2c_1_` Wanneer u de gebruikers stroom opslaat.  U hebt de naam van de gebruikers stroom later nodig.

Nadat u de gebruikers stromen hebt gemaakt, bent u klaar om uw app te bouwen.

## <a name="download-the-sample-code"></a>De voorbeeld code downloaden
We hebben een werkend voor beeld gegeven dat gebruikmaakt van AppAuth met Azure AD B2C [op github](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). U kunt de code downloaden en uitvoeren. Als u uw eigen Azure AD B2C Tenant wilt gebruiken, volgt u de instructies in de [README.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Dit voor beeld is gemaakt door de Leesmij-instructies te volgen van het [IOS AppAuth-project op github](https://github.com/openid/AppAuth-iOS). Raadpleeg het Leesmij-bestand voor AppAuth op GitHub voor meer informatie over de werking van het voor beeld en de bibliotheek.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Uw app wijzigen om Azure AD B2C te gebruiken met AppAuth

> [!NOTE]
> AppAuth biedt ondersteuning voor iOS 7 en hoger.  Voor het ondersteunen van sociale aanmeldingen op Google is SFSafariViewController echter vereist, waarvoor iOS 9 of hoger vereist is.
>

### <a name="configuration"></a>Configuratie

U kunt communicatie met Azure AD B2C configureren door zowel het autorisatie-eind punt als de token-eind punt-Uri's op te geven.  Als u deze Uri's wilt genereren, hebt u de volgende gegevens nodig:
* Tenant-ID (bijvoorbeeld contoso.onmicrosoft.com)
* Gebruikers stroom naam (bijvoorbeeld B2C \_ 1 \_ SignUpIn)

De URI van het token-eind punt kan worden gegenereerd door de Tenant \_ -id en de \_ naam van het beleid in de volgende URL te vervangen:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

De URI van het autorisatie-eind punt kan worden gegenereerd door de Tenant \_ -id en de \_ naam van het beleid in de volgende URL te vervangen:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Voer de volgende code uit om uw AuthorizationServiceConfiguration-object te maken:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autoriseren

Na het configureren of ophalen van een configuratie van een autorisatie service kan een autorisatie aanvraag worden samengesteld. Als u de aanvraag wilt maken, hebt u de volgende gegevens nodig:

* De client-ID (toepassings-ID) die u eerder hebt vastgelegd. Bijvoorbeeld `00000000-0000-0000-0000-000000000000`.
* Aangepaste omleidings-URI die u eerder hebt vastgelegd. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Beide items moeten zijn opgeslagen bij [het registreren van uw app](#create-an-application).

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Als u uw toepassing wilt instellen voor het afhandelen van de omleiding naar de URI met het aangepaste schema, moet u de lijst met URL-Schema's bijwerken in uw info. pList:
* Open info. pList.
* Beweeg de muis aanwijzer over een rij zoals ' bundel OS type code ' en klik op het \+ symbool.
* Wijzig de naam van de nieuwe rij ' URL-typen '.
* Klik op de pijl links van ' URL-typen ' om de structuur te openen.
* Klik op de pijl links van ' item 0 ' om de structuur te openen.
* Wijzig de naam van het eerste item onder item 0 in ' URL-Schema's '.
* Klik op de pijl links van ' URL-Schema's ' om de structuur te openen.
* In de kolom waarde bevindt zich links van ' item 0 ' onder ' URL-Schema's ' een leeg veld.  Stel de waarde in op het unieke schema van uw toepassing.  De waarde moet overeenkomen met het schema dat in redirectURL wordt gebruikt bij het maken van het OIDAuthorizationRequest-object.  In het voor beeld wordt het schema ' com. onmicrosoft. fabrikamb2c. exampleapp ' gebruikt.

Raadpleeg de [AppAuth-hand leiding](https://openid.github.io/AppAuth-iOS/) voor informatie over hoe u de rest van het proces kunt volt ooien. Bekijk [het voor beeld](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)als u snel aan de slag wilt gaan met een werkende app. Volg de stappen in de [README.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) om uw eigen Azure AD B2C configuratie in te voeren.

We zijn altijd te openen voor feedback en suggesties. Als u problemen ondervindt met dit artikel of aanbevelingen hebt voor het verbeteren van deze inhoud, zouden we uw feedback onder aan de pagina waarderen. Voor functie aanvragen voegt u deze toe aan [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).