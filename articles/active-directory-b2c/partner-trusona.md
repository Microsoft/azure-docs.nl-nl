---
title: Trusona en Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Meer informatie over het toevoegen van Trusona als een id-provider op Azure AD B2C voor het inschakelen van verificatie met een wacht woord.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99368da8b4018e93ad537e4722ffefd476e61291
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573684"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Trusona integreren met Azure Active Directory B2C

Trusona is een ISV-provider (Independent Software Vendor) die helpt bij het beveiligen van de aanmelding door verificatie zonder wacht woord, multi-factor Authentication en digitale licentie controle in te scha kelen. In dit artikel leert u hoe u Trusona kunt toevoegen als een id-provider in Azure AD B2C om verificatie met een wacht woordloos in te scha kelen.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* [Een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.
* Een [proef account](https://www.trusona.com/) op Trusona

## <a name="scenario-description"></a>Scenariobeschrijving

In dit scenario fungeert Trusona als een id-provider voor Azure AD B2C voor het inschakelen van verificatie met een wacht woord. De volgende onderdelen vormen de oplossing:

* Een Azure AD B2C gecombineerde aanmeldings-en registratie beleid
* Trusona toegevoegd aan Azure AD B2C als een id-provider
* De Download bare Trusona-app

![Diagram van Trusona-architectuur](media/partner-trusona/trusona-architecture-diagram.png)

| Stap | Beschrijving |
|------|------|
|1     | Een gebruiker probeert zich aan te melden bij of zich aan te melden bij de toepassing. De gebruiker wordt geverifieerd via het Azure AD B2C registratie-en aanmeldings beleid. Tijdens het aanmelden wordt het eerder geverifieerde e-mail adres van de gebruiker uit de Trusona-app gebruikt.     |
|2     | De gebruiker wordt door Azure B2C omgeleid naar de Trusona OpenID Connect Connect (OIDC)-ID-provider met behulp van de impliciete stroom.     |
|3     | Voor aanmeldingen op basis van bureaublad computers geeft Trusona een unieke, stateless, geanimeerde en dynamische QR-code weer voor het scannen met de Trusona-app. Voor mobiele aanmeldingen gebruikt Trusona een ' diepe koppeling ' om de Trusona-app te openen. Deze twee methoden worden gebruikt voor apparaten en uiteindelijk gebruikers detectie.     |
|4     | De gebruiker scant de weer gegeven QR-code met de Trusona-app.     |
|5     | Het account van de gebruiker is te vinden in de Trusona-Cloud service en de verificatie wordt voor bereid.     |
|6     | De Trusona-Cloud service geeft een authenticatie vraag aan de gebruiker via een push melding verzonden naar de Trusona-app:<br>a. De gebruiker wordt gevraagd de verificatie vraag op te vragen. <br> b. De gebruiker kiest ervoor de vraag te accepteren of af te wijzen. <br> c. De gebruiker wordt gevraagd de beveiliging van het besturings systeem (bijvoorbeeld biometrisch, wachtwoord code, pincode of patroon) te gebruiken om de uitdaging te bevestigen en te ondertekenen met een persoonlijke sleutel in de beveiligde enclave/vertrouwde uitvoerings omgeving. <br> d. De Trusona-app genereert een dynamische, anti-replay Payload op basis van de para meters van de verificatie in realtime. <br> e. Het hele antwoord is ondertekend (een tweede keer) door een persoonlijke sleutel in de beveiligde enclave/Trusted Execution Environment en geretourneerd naar de Trusona-Cloud service voor verificatie.      |
|7     |  De Trusona-Cloud service leidt de gebruiker terug naar de initiatie toepassing met een id_token. Azure AD B2C controleert de id_token met de gepubliceerde OpenID Connect-configuratie van Trusona, zoals geconfigureerd tijdens de installatie van de identiteits provider.    |
|  |  |

## <a name="onboard-with-trusona"></a>Onboard met Trusona

1. Vul het [formulier](https://www.trusona.com/) in om een Trusona-account te maken en aan de slag te gaan.

2. Down load de mobiele Trusona-app uit de App Store. Installeer de app en registreer uw e-mail adres.

3. Controleer uw e-mail via de beveiligde ' Magic link ' die door de software is verzonden.  

4. Ga naar het [Trusona-dash board van ontwikkel aars](https://dashboard.trusona.com) voor Self-service.

5. Selecteer **Ik ben klaar** en verifieer jezelf met je Trusona-app.

6. Kies **OIDC-integraties** in het navigatie deel venster aan de linkerkant.

7. Selecteer **OpenID Connect Connect-integratie maken**.

8. Geef een **naam** op voor uw keuze en gebruik de eerder verstrekte domein gegevens (bijvoorbeeld Contoso) in het **veld host van client omleiden**.  

   > [!NOTE]
   > De initiële domein naam van Azure Active Directory wordt gebruikt als host voor client omleiding.

9. Volg de instructies in de [Trusona-integratie handleiding](https://docs.trusona.com/integrations/aad-b2c-integration/). Als u hierom wordt gevraagd, gebruikt u de eerste domein naam (bijvoorbeeld Contoso) waarnaar wordt verwezen in de vorige stap.  

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Een nieuwe ID-provider toevoegen

> [!NOTE]
> Als u nog geen account hebt, [maakt u een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).

2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.

3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.

4. Navigeer naar **dash board**  >  **Azure Active Directory B2C**  >  **id-providers**.

3. Selecteer **id-providers**.

4. Selecteer **Toevoegen**.

### <a name="configure-an-identity-provider"></a>Een id-provider configureren  

1. Selecteer een **ID-provider type**  >  **OpenID Connect Connect (preview-versie)**.

2. Vul het formulier in om de ID-provider in te stellen:  

   | Eigenschap | Waarde  |
   | :--- | :--- |
   | Metagegevens-URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | Client-id | Wordt per e-mail naar u verzonden vanuit Trusona |
   | Bereik | E-mail van OpenID Connect-profiel |
   | Antwoord type | Id_token |
   | Antwoord modus  | Form_post |

3. Selecteer **OK**.  

4. Selecteer **de claims van deze id-provider toewijzen**.  

5. Vul het formulier in om de ID-provider toe te wijzen:

   | Eigenschap | Waarde  |
   | :--- | :--- |
   | UserID | Chronische  |
   | Weergavenaam | bijnaam |
   | Voornaam | given_name |
   | Achternaam | Family_name |
   | Antwoord modus | e-mail |

6. Selecteer **OK** om de installatie van de nieuwe OIDC-ID-provider te volt ooien.

### <a name="create-a-user-flow-policy"></a>Een beleid voor gebruikers stromen maken

U ziet nu Trusona als een **nieuwe OpenID Connect Connect ID-provider** die wordt weer gegeven in uw B2C-id-providers.

1. Selecteer in uw Azure AD B2C-Tenant onder **beleids regels** **gebruikers stromen**.

1. Selecteer **nieuwe gebruikers stroom**.

1. Selecteer **Aanmelden en aanmelden**, selecteer een versie en selecteer vervolgens **maken**.

1. Voer een **naam** in voor uw beleid.

1. Selecteer in de sectie **id-providers** de zojuist gemaakte **Trusona-ID-provider**.

   > [!NOTE]
   > Omdat Trusona is inherent aan meerdere factoren, is het het beste om multi-factor Authentication uitgeschakeld te laten.

1. Selecteer **Maken**.

1. Onder **gebruikers kenmerken en claims**, kiest u **meer weer geven**. Selecteer in het formulier ten minste één kenmerk dat u hebt opgegeven tijdens de installatie van uw ID-provider in de vorige sectie.

1. Selecteer **OK**.  

### <a name="test-the-policy"></a>Het beleid testen

1. Selecteer het zojuist gemaakte beleid.

2. Selecteer **Gebruikersstroom uitvoeren**.

3. Voer in het formulier de antwoord-URL in.

4. Selecteer **Gebruikersstroom uitvoeren**. U moet worden omgeleid naar de Trusona OIDC-gateway. Scan op de Trusona-gateway de weer gegeven beveiligde QR-code met de Trusona-app of met een aangepaste app met de Trusona Mobile SDK.

5. Nadat u de beveiligde QR-code hebt gescand, moet u worden omgeleid naar de antwoord-URL die u in stap 3 hebt gedefinieerd.

## <a name="next-steps"></a>Volgende stappen  

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](custom-policy-overview.md)

- [Aan de slag met aangepast beleid in AAD B2C](custom-policy-get-started.md?tabs=applications)
