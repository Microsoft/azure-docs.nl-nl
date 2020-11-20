---
title: Configuratie van client toepassing (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over configuratie opties voor open bare client-en vertrouwelijke client toepassingen met behulp van de micro soft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/20/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9ec8a5fe5de751e40ebaa17629ff72c5f6b2adca
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979981"
---
# <a name="application-configuration-options"></a>Configuratie opties voor toepassingen

Als u tokens wilt verifiëren en verkrijgen, initialiseert u een nieuwe open bare of vertrouwelijke client toepassing in uw code. U kunt verschillende configuratie opties instellen wanneer u de client-app in de micro soft Authentication Library (MSAL) initialiseert. Deze opties zijn onderverdeeld in twee groepen:

- Registratie opties, waaronder:
  - [Instantie](#authority) (bestaande uit het ID-provider [exemplaar](#cloud-instance) en het [publiek](#application-audience) voor de app, en mogelijk de Tenant-id)
  - [Client ID](#client-id)
  - [Omleidings-URI](#redirect-uri)
  - [Client geheim](#client-secret) (voor vertrouwelijke client toepassingen)
- [Opties voor logboek registratie](#logging), waaronder logboek niveau, controle van persoons gegevens en de naam van het onderdeel met behulp van de bibliotheek

## <a name="authority"></a>Instantie

De instantie is een URL die een directory aangeeft waarvan MSAL tokens kan aanvragen.

Algemene instanties zijn:

| Url's van algemene instanties | Wanneer gebruikt u dit? |
|--|--|
| `https://login.microsoftonline.com/<tenant>/` | Alleen gebruikers van een specifieke organisatie aanmelden. De `<tenant>` in de URL is de Tenant-id van de Azure Active Directory (Azure AD)-Tenant (een GUID) of het Tenant domein. |
| `https://login.microsoftonline.com/common/` | Meld gebruikers aan met werk-en school accounts of persoonlijke micro soft-accounts. |
| `https://login.microsoftonline.com/organizations/` | Gebruikers aanmelden met werk-en school accounts. |
| `https://login.microsoftonline.com/consumers/` | Meld gebruikers alleen aan met persoonlijke micro soft-accounts (MSA). |

De instantie die u in uw code opgeeft moet consistent zijn met de **ondersteunde account typen** die u hebt opgegeven voor de app in **App-registraties** in het Azure Portal.

De autoriteit kan het volgende zijn:

- Een Azure AD-Cloud instantie.
- Een Azure AD B2C-instantie. Zie [B2C-specifieke](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)informatie.
- Een Active Directory Federation Services-instantie (AD FS). Zie [AD FS ondersteuning](https://aka.ms/msal-net-adfs-support).

Azure AD-Cloud instanties hebben twee onderdelen:

- Het ID-provider *exemplaar*
- De aanmeldings *doel groep* voor de app

Het exemplaar en de doel groep kunnen worden samengevoegd en als de URL van de autoriteit worden gegeven. In dit diagram ziet u hoe de URL van de instantie bestaat:

![Hoe de CA-URL is samengesteld](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Cloud exemplaar

Het *exemplaar* wordt gebruikt om op te geven of uw app gebruikers ondertekent vanuit de open bare Azure-Cloud of van nationale Clouds. Met behulp van MSAL in uw code kunt u het Azure Cloud-exemplaar instellen met behulp van een opsomming of door de URL door te geven aan het [National Cloud-exemplaar](authentication-national-cloud.md#azure-ad-authentication-endpoints) als het `Instance` lid (als u het weet).

MSAL.NET genereert een expliciete uitzonde ring als beide `Instance` en `AzureCloudInstance` zijn opgegeven.

Als u geen exemplaar opgeeft, wordt de app in het open bare Azure-Cloud exemplaar (het exemplaar van de URL `https://login.onmicrosoftonline.com` ) gericht.

## <a name="application-audience"></a>Toepassings doelgroep

De aanmeldings doelgroep is afhankelijk van de bedrijfs behoeften van uw app:

- Als u een LOB-ontwikkelaar (line-of-Business) bent, produceert u waarschijnlijk een toepassing met één Tenant die alleen in uw organisatie wordt gebruikt. In dat geval moet u de organisatie opgeven, hetzij door de Tenant-ID (de ID van uw Azure AD-instantie) ofwel door een domein naam die is gekoppeld aan de Azure AD-instantie.
- Als u een ISV bent, wilt u mogelijk gebruikers aanmelden met hun werk-en school accounts in elke organisatie of in sommige organisaties (multi tenant-app). Maar u wilt ook dat gebruikers zich aanmelden met hun persoonlijke micro soft-accounts.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>De doel groep in uw code/configuratie opgeven

Door MSAL in uw code te gebruiken, geeft u de doel groep op met behulp van een van de volgende waarden:

- De inventarisatie van de Azure AD-instantie doelgroep
- De Tenant-ID, die kan zijn:
  - Een GUID (de ID van uw Azure AD-exemplaar) voor toepassingen met één Tenant
  - Een domein naam die is gekoppeld aan uw Azure AD-exemplaar (ook voor toepassingen met één Tenant)
- Een van deze tijdelijke aanduidingen als Tenant-ID in plaats van de inventarisatie van de Azure AD-instantie doelgroep:
  - `organizations` voor een multi tenant-toepassing
  - `consumers` gebruikers alleen met hun persoonlijke accounts aanmelden
  - `common` gebruikers aanmelden met hun werk-en school account of hun persoonlijke micro soft-accounts

MSAL genereert een zinvolle uitzonde ring als u zowel de Azure AD-instantie doelgroep als de Tenant-ID opgeeft.

Als u geen doel groep opgeeft, zal uw app Azure AD en persoonlijke micro soft-accounts als doel groep richten. (Dat wil zeggen dat het zich bevindt alsof het is `common` opgegeven.)

### <a name="effective-audience"></a>Doel groep

De doel groep voor uw toepassing is het minimum (als er een snij punt is) van de doel groep die u in uw app hebt ingesteld en de doel groep die is opgegeven in de app-registratie. Met de [app-registraties](https://aka.ms/appregistrations) -ervaring kunt u de doel groep (de ondersteunde account typen) voor de app opgeven. Zie [Quick Start: een toepassing registreren bij het micro soft Identity-platform](quickstart-register-app.md)voor meer informatie.

Op dit moment kunt u een app alleen voor het aanmelden van gebruikers met persoonlijke micro soft-accounts krijgen door beide instellingen te configureren:

- Stel de app-registratie doelgroep in op `Work and school accounts and personal accounts` .
- Stel de doel groep in uw code/configuratie in op `AadAuthorityAudience.PersonalMicrosoftAccount` (of `TenantID` = ' consumenten ').

## <a name="client-id"></a>Client-id

De client-ID is de unieke toepassings-ID (client) die is toegewezen aan uw app door Azure AD wanneer de app is geregistreerd.

## <a name="redirect-uri"></a>Omleidings-URI

De omleidings-URI is de URI waarnaar de ID-provider de beveiligings tokens terugstuurt.

### <a name="redirect-uri-for-public-client-apps"></a>Omleidings-URI voor open bare client-apps

Als u een open bare client-app-ontwikkelaar bent die gebruikmaakt van MSAL:

- U wilt gebruiken `.WithDefaultRedirectUri()` in Desktop-of UWP-toepassingen (MSAL.net 4.1 +). Met deze methode wordt de eigenschap omleidings-URI van de open bare client toepassing ingesteld op de aanbevolen standaard omleidings-URI voor open bare client toepassingen.

  | Platform | Omleidings-URI |
  |--|--|
  | Bureau blad-app (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` |
  | UWP | waarde van `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` . Hiermee wordt SSO met de browser ingeschakeld door de waarde in te stellen op het resultaat van WebAuthenticationBroker. GetCurrentApplicationCallbackUri () die u moet registreren |
  | .NET Core | `https://localhost`. Op deze manier kan de gebruiker de systeem browser voor interactieve verificatie gebruiken, omdat .NET Core op het moment geen gebruikers interface voor de Inge sloten webweergave heeft. |

- U hoeft geen omleidings-URI toe te voegen als u een Xamarin Android-en iOS-toepassing bouwt die geen ondersteuning biedt voor Broker (de omleidings-URI wordt automatisch ingesteld op `msal{ClientId}://auth` voor Xamarin Android en IOS

- U moet de omleidings-URI configureren in [app-registraties](https://aka.ms/appregistrations):

   ![Omleidings-URI in App-registraties](media/msal-client-application-configuration/redirect-uri.png)

U kunt de omleidings-URI onderdrukken met behulp van de `RedirectUri` eigenschap (bijvoorbeeld als u Brokers gebruikt). Hier volgen enkele voor beelden van omleidings-Uri's voor dat scenario:

- `RedirectUriOnAndroid` = "msauth-5a434691-CCB2-4fd1-b97b-b64bcfbc03fc://com.Microsoft.Identity.client.sample";
- `RedirectUriOnIos` = $ "msauth. {Bundel. ID}://auth ";

Zie [IOS-toepassingen migreren die gebruikmaken van Microsoft Authenticator van ADAL.net naar MSAL.net](msal-net-migration-ios-broker.md) en [de Broker op Ios](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)gebruiken voor meer informatie over IOS.
Zie [brokered auth in Android](msal-android-single-sign-on.md)voor meer informatie over Android.

### <a name="redirect-uri-for-confidential-client-apps"></a>Omleidings-URI voor vertrouwelijke client-apps

Voor web-apps is de omleidings-URI (of antwoord-URL) de URI die door Azure AD wordt gebruikt om het token terug te sturen naar de toepassing. Deze URI kan de URL zijn van de web-app/Web-API als de vertrouwelijke app een van deze is. De omleidings-URI moet worden geregistreerd bij de app-registratie. Deze registratie is vooral belang rijk wanneer u een app implementeert die u eerst lokaal hebt getest. Vervolgens moet u de antwoord-URL van de geïmplementeerde app toevoegen in de portal voor toepassings registratie.

Voor daemon-apps hoeft u geen omleidings-URI op te geven.

## <a name="client-secret"></a>Clientgeheim

Met deze optie geeft u het client geheim op voor de vertrouwelijke client-app. Dit geheim (app-wacht woord) wordt geleverd door de portal voor toepassings registratie of geleverd aan Azure AD tijdens de registratie van de app met Power shell AzureAD, Power shell AzureRM of Azure CLI.

## <a name="logging"></a>Logboekregistratie

Met de andere configuratie opties kunt u logboek registratie en probleem oplossing inschakelen. Zie het artikel [logboek registratie](msal-logging.md) voor meer informatie over het gebruik ervan.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [instantiëren van client toepassingen met behulp van MSAL.net](msal-net-initializing-client-applications.md) en het [instantiëren van client toepassingen met behulp van MSAL.js](msal-js-initializing-client-applications.md).
