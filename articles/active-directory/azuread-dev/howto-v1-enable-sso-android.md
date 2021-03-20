---
title: SSO van meerdere apps inschakelen op Android met behulp van ADAL | Microsoft Docs
description: De functies van de ADAL SDK gebruiken om eenmalige aanmelding in te scha kelen voor uw toepassingen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev, devx-track-java
ROBOTS: NOINDEX
ms.openlocfilehash: 9d1faf7aed5cf4c4975925c7a56d169dc99a7bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87313588"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Procedure: cross-app SSO inschakelen op Android met behulp van ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Eenmalige aanmelding (SSO) stelt gebruikers in staat om hun referenties één keer in te voeren en deze referenties automatisch te laten werken in toepassingen en op verschillende platforms die andere toepassingen mogelijk gebruiken (zoals micro soft-accounts of een werk account van Microsoft 365), ongeacht de uitgever.

Het identiteits platform van micro soft, samen met de Sdk's, maakt het eenvoudig om eenmalige aanmelding in te scha kelen in uw eigen suite van apps of met de Broker-en verificator-toepassingen, op het hele apparaat.

In deze procedure leert u hoe u de SDK in uw toepassing kunt configureren om eenmalige aanmelding te bieden voor uw klanten.

## <a name="prerequisites"></a>Vereisten

In deze procedure wordt ervan uitgegaan dat u weet hoe u:

- Richt uw app in met behulp van de verouderde portal voor Azure Active Directory (Azure AD). Zie [een app registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie
- Integreer uw toepassing met de [Azure AD Android-SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Concepten van eenmalige aanmelding

### <a name="identity-brokers"></a>Identiteits Brokers

Micro soft biedt toepassingen voor elk mobiel platform dat de bridging van referenties voor toepassingen van verschillende leveranciers mogelijk maakt, en voor verbeterde functies waarvoor een enkele veilige locatie is vereist voor het valideren van referenties. Deze worden **makelaars** genoemd.

Op iOS en Android worden makelaars geleverd via download bare toepassingen die door klanten afzonderlijk of naar het apparaat worden gepusht door een bedrijf dat sommige of alle apparaten beheert voor hun werk nemers. Makelaars ondersteunen het beheer van beveiliging alleen voor sommige toepassingen of het hele apparaat op basis van de IT-beheerders configuratie. In Windows wordt deze functionaliteit geleverd door een account kiezer die is ingebouwd in het besturings systeem, technisch aangeduid als de Web authentication Broker.

#### <a name="broker-assisted-login"></a>Door Broker geassisteerde aanmelding

Aanmeldingen met Broker ondersteuning zijn aanmeldings ervaringen die zich binnen de Broker-toepassing voordoen en gebruiken de opslag en beveiliging van de Broker om referenties te delen in alle toepassingen op het apparaat waarop het identiteits platform wordt toegepast. De implicatie van uw toepassingen is afhankelijk van de Broker voor het ondertekenen van gebruikers in. Op iOS en Android worden deze makelaars geleverd door Download bare toepassingen die onafhankelijk van elkaar worden geïnstalleerd of kunnen worden gepusht naar het apparaat door een bedrijf dat het apparaat beheert voor hun gebruiker. Een voor beeld van dit type toepassing is de Microsoft Authenticator toepassing op iOS. In Windows wordt deze functionaliteit geleverd door een account kiezer die is ingebouwd in het besturings systeem, technisch aangeduid als de Web authentication Broker.
De ervaring is afhankelijk van het platform en kan soms storend zijn voor gebruikers als ze niet correct worden beheerd. U bent waarschijnlijk het meest vertrouwd met dit patroon als u de Facebook-toepassing hebt geïnstalleerd en Facebook Connect van een andere toepassing gebruikt. Het identiteits platform gebruikt hetzelfde patroon.

Op Android wordt de account kiezer weer gegeven boven op de toepassing, die minder storend is voor de gebruiker.

#### <a name="how-the-broker-gets-invoked"></a>Hoe de Broker wordt aangeroepen

Als er een compatibele Broker op het apparaat is geïnstalleerd, zoals de toepassing Microsoft Authenticator, wordt door de identiteits-Sdk's automatisch het werk van de Broker opgeroepen wanneer een gebruiker aangeeft dat hij of zij zich wil aanmelden met een account van het identiteits platform.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hoe micro soft garandeert dat de toepassing geldig is

De nood zaak om ervoor te zorgen dat de identiteit van een toepassing die de Broker aanroept, cruciaal is voor de beveiliging die wordt geleverd in door Broker gesteunde aanmeldingen. iOS en Android afdwingen geen unieke id's die alleen geldig zijn voor een bepaalde toepassing, waardoor kwaadwillende toepassingen een geldige toepassings-id kunnen ' spoofen ' en de tokens ontvangen die voor de legitieme toepassing bedoeld zijn. Om ervoor te zorgen dat micro soft altijd communiceert met de juiste toepassing tijdens de uitvoering, wordt de ontwikkelaar gevraagd een aangepaste redirectURI op te geven wanneer ze hun toepassing registreren bij micro soft. **Hoe ontwikkel aars deze omleidings-URI moeten bebouwen, worden hieronder gedetailleerd beschreven.** Deze aangepaste redirectURI bevat de vinger afdruk van het certificaat van de toepassing en wordt gegarandeerd uniek voor de toepassing door de Google Play Store. Wanneer een toepassing de Broker aanroept, vraagt de Broker het Android-besturings systeem om het te voorzien van de vinger afdruk van het certificaat dat de Broker wordt genoemd. De broker biedt deze vinger afdruk van het certificaat naar micro soft in de aanroep van het identiteits systeem. Als de vinger afdruk van de toepassing niet overeenkomt met de vinger afdruk van het certificaat dat door de ontwikkelaar tijdens de registratie is opgegeven, wordt de toegang geweigerd tot de tokens voor de resource die de toepassing aanvraagt. Met deze controle wordt gegarandeerd dat alleen de toepassing die door de ontwikkelaar is geregistreerd, tokens ontvangt.

Voor brokered-SSO-aanmeldingen gelden de volgende voor delen:

* Gebruikers ervaringen SSO voor al hun toepassingen, ongeacht de leverancier.
* Uw toepassing kan meer geavanceerde zakelijke functies gebruiken, zoals voorwaardelijke toegang en ondersteuning voor intune-scenario's.
* Uw toepassing kan verificatie op basis van certificaten ondersteunen voor zakelijke gebruikers.
* Veiliger aanmeldings ervaring als de identiteit van de toepassing en de gebruiker worden geverifieerd door de Broker-toepassing met aanvullende beveiligings algoritmen en versleuteling.

Hier volgt een weer gave van de manier waarop de Sdk's samen werken met de Broker-toepassingen om SSO in te scha kelen:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>EENMALIGe aanmelding inschakelen voor SSO met Broker-ondersteuning

De mogelijkheid voor een toepassing voor het gebruik van elke Broker die op het apparaat is geïnstalleerd, is standaard uitgeschakeld. Als u uw toepassing met de Broker wilt gebruiken, moet u een extra configuratie uitvoeren en wat code toevoegen aan uw toepassing.

De stappen die u moet volgen:

1. De Broker modus inschakelen in de aanroepende toepassings code van de MS SDK
2. Een nieuwe omleidings-URI tot stand brengen en opgeven dat zowel de app als uw app-registratie
3. De juiste machtigingen instellen in het Android-manifest

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Stap 1: de Broker modus inschakelen in uw toepassing

De mogelijkheid om uw toepassing te gebruiken voor het gebruik van de Broker is ingeschakeld wanneer u de instellingen of de eerste installatie van uw verificatie-exemplaar maakt. U doet dit als volgt in uw app:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Stap 2: een nieuwe omleidings-URI tot stand brengen met uw URL-schema

Om ervoor te zorgen dat de juiste toepassing de geretourneerde referentie tokens ontvangt, moet u ervoor zorgen dat de aanroep terugkeert naar uw toepassing op een manier die het Android-besturings systeem kan controleren. Het Android-besturings systeem gebruikt de hash van het certificaat in het Google Play-archief. Deze hash van het certificaat kan niet worden vervalst met een Rogue-toepassing. Naast de URI van de Broker-toepassing zorgt micro soft ervoor dat de tokens worden geretourneerd naar de juiste toepassing. Er moet een unieke omleidings-URI zijn geregistreerd voor de toepassing.

De omleidings-URI moet de volgende vorm hebben:

`msauth://packagename/Base64UrlencodedSignature`

bijvoorbeeld: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

U kunt deze omleidings-URI in de registratie van uw app registreren met behulp van de [Azure Portal](https://portal.azure.com/). Zie [integreren met Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie over Azure AD-App-registratie.

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Stap 3: de juiste machtigingen instellen in uw toepassing

In de Broker-toepassing in Android wordt de functie Accounts Manager van het Android-besturings systeem gebruikt voor het beheren van referenties tussen toepassingen. Als u de broker in Android wilt gebruiken, moet uw app-manifest machtigingen hebben voor het gebruik van AccountManager-accounts. Deze machtigingen worden gedetailleerd beschreven in de [Google-documentatie voor account manager](https://developer.android.com/reference/android/accounts/AccountManager.html)

Deze machtigingen zijn met name:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>U hebt SSO geconfigureerd.

Nu zal de identiteits-SDK automatisch referenties delen in uw toepassingen en de Broker aanroepen als deze aanwezig is op het apparaat.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
