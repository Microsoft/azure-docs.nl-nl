---
title: Zelf studie voor het configureren van Azure Active Directory B2C met HYPR
titleSuffix: Azure AD B2C
description: Zelf studie voor het configureren van Azure Active Directory B2C met Hypr voor een echte, strikte gebruikers verificatie met een wacht woord
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953760"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van HYPR met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het configureren van Azure AD B2C met [HYPR](https://get.hypr.com). Met Azure AD B2C als een id-provider kunt u HYPR integreren met uw klant toepassingen, zodat u een echte verificatie zonder wacht woord kunt opgeven voor uw gebruikers. HYPR vervangt wacht woorden met een open bare sleutel versleuteling, waardoor fraude, phishing en hergebruik van referenties worden vermeden.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md). De Tenant is gekoppeld aan uw Azure-abonnement.

- Een HYPR-Cloud Tenant, een gratis [proef account](https://get.hypr.com/free-trial)ophalen.

- Het mobiele apparaat van een gebruiker is geregistreerd met behulp van de HYPR rest-Api's of de HYPR-Apparaatbeheer in uw HYPR-Tenant. U kunt bijvoorbeeld de [HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) gebruiken om deze taak uit te voeren.

## <a name="scenario-description"></a>Scenariobeschrijving

De HYRP-integratie bevat de volgende onderdelen:

- Azure AD B2C: de autorisatie server die verantwoordelijk is voor het verifiëren van de referenties van de gebruiker, ook wel bekend als de ID-provider

- Webtoepassingen en mobiele toepassingen: uw mobiele of webtoepassingen die u wilt beveiligen met HYPR en Azure AD B2C. HYPR biedt een robuuste mobiele SDK, ook een mobiele app die u kunt gebruiken op iOS-en Android-platforms om echte wacht woordloze verificatie uit te voeren.

- De HYPR-app voor mobiele apparaten: de mobiele HYPR-app kan worden gebruikt om dit voor beeld uit te voeren als u de mobiele Sdk's niet wilt gebruiken in uw eigen mobiele toepassingen.

- Rest-Api's van HYPR: u kunt de HYPR-Api's gebruiken om zowel registratie van gebruikers apparaten als verificatie uit te voeren. Deze Api's kunt u [hier](https://apidocs.hypr.com)vinden.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Scherm afbeelding voor hypr-Architecture-diagram](media/partner-hypr/hypr-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt op een aanmeldings pagina. Gebruikers selecteren aanmelden/registreren en voeren de gebruikers naam in op de pagina.
| 2. | De toepassing stuurt de gebruikers kenmerken naar Azure AD B2C om verificatie te identificeren.
| 3. | Azure AD B2C verzamelt de gebruikers kenmerken en verzendt de kenmerken naar HYPR om de gebruiker te verifiëren via de mobiele HYPR-app.
| 4. | HYPR verzendt een push melding naar het mobiele apparaat van de geregistreerde gebruiker voor een FIDO-gecertificeerde verificatie (Fast Identity online). Dit kan een vinger afdruk van een gebruiker, biometrische of gedecentraliseerde pincode zijn.  
| 5. | Nadat de gebruiker de push melding heeft bevestigd, wordt de gebruiker toegang verleend of geweigerd aan de klant toepassing op basis van de verificatie resultaten.

## <a name="configure-the-azure-ad-b2c-policy"></a>Het Azure AD B2C-beleid configureren

1. Ga naar het [Azure AD B2C HYPR-beleid](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) in de map beleid.

2. Volg dit [document](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) om [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) te downloaden

3. Configureer het beleid voor de Azure AD B2C Tenant.

>[!NOTE]
>Werk het opgegeven beleid bij om te koppelen aan uw specifieke Tenant.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C Tenant en selecteer onder beleids regels het **Framework identiteits ervaring** selecteren.

2. Selecteer uw eerder gemaakte **SignUpSignIn**.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   a. **Toepassing**: Selecteer de geregistreerde app (voor beeld is JWT)

   b. **Antwoord-URL**: de **omleidings-URL** selecteren

   c. Selecteer **Gebruikersstroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account

5. HYPR wordt aangeroepen tijdens de stroom, nadat het gebruikers kenmerk is gemaakt. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)