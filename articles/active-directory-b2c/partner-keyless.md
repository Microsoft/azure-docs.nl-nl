---
title: Zelf studie voor het configureren van de waarde voor de verkorte Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Zelf studie voor het configureren van de Azure Active Directory B2C voor wachtwoord verificatie zonder wacht woord
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b817cfc347ee79ff7c9cbb4124e3f2b7e4d2b7ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644252"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Zelf studie: met Azure Active Directory B2C configureren

In deze voorbeeld zelfstudie bieden we richt lijnen voor het configureren van Azure Active Directory (AD) B2C met een [lagere](https://keyless.io/)waarde. Met Azure AD B2C als een id-provider kunt u de functie voor het verkorten van uw klant toepassingen integreren met een wille keurig wacht woordloze verificatie voor uw gebruikers.

De oplossing voor minder strenge **Zero-Knowledge biometrische™ (ZKB)** van minder dan een wacht woordloze multi-factor Authentication voor het elimineren van fraude, phishing en hergebruik van referenties: alle voor het verbeteren van de gebruikers ervaring en het beschermen van de privacy.

## <a name="pre-requisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md). De Tenant moet worden gekoppeld aan uw Azure-abonnement.

- Een onverminderde Cloud Tenant, een gratis [proef account](https://keyless.io/go)ophalen.

- De op het apparaat van de gebruiker geïnstalleerde verificator-app.

## <a name="scenario-description"></a>Scenariobeschrijving

De verminderd met de strenge integratie waarde bevat de volgende onderdelen:

- Azure AD B2C: de autorisatie server die verantwoordelijk is voor het verifiëren van de referenties van de gebruiker, ook wel bekend als de ID-provider.

- Web-en mobiele toepassingen: uw mobiele of webtoepassingen die u wilt beveiligen met een verminderd en Azure AD B2C.

- De mobiele app met minder apparaten: de Mobile-App die wordt uitgevoerd, wordt gebruikt voor verificatie voor de Azure AD B2C ingeschakelde toepassingen.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Afbeelding toont het diagram met de verkorte architectuur](./media/partner-keyless/keyless-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt op een aanmeldings pagina. Gebruikers selecteren aanmelden/registreren en voert de gebruikers naam in
| 2. | De toepassing stuurt de gebruikers kenmerken naar Azure AD B2C voor identiteits verificatie.
| 3. | Azure AD B2C verzamelt de gebruikers kenmerken en verzendt de kenmerken naar de waarde ' minder ' om de gebruiker te verifiëren via de mobiele app voor minder dan.
| 4. | Met deze opdracht verzendt een push melding naar het mobiele apparaat van de geregistreerde gebruiker voor een privacy-behoud van de verificatie in de vorm van een biometrische scan.
| 5. | Nadat de gebruiker op de push melding heeft gereageerd, wordt de gebruiker toegang verleend of geweigerd op basis van de verificatie resultaten.

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Een nieuwe ID-provider toevoegen

Voer de volgende stappen uit om een nieuwe ID-provider toe te voegen:

1. Meld u aan bij de **[Azure Portal](https://portal.azure.com/#home)** als globale beheerder van uw Azure AD B2C Tenant.

2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Tenant bevat.

3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.

4. Navigeren naar **dash board**-  >  **Azure Active Directory B2C**-  >   **id-providers**

5. Selecteer **id-providers**.

6. Selecteer **Toevoegen**.

### <a name="configure-an-identity-provider"></a>Een id-provider configureren

Als u een id-provider wilt configureren, volgt u deze stappen:

1. Selecteer een **ID-provider type**  >  **OpenID Connect Connect (preview-versie)**
2. Vul het formulier in om de ID-provider in te stellen:

   |Eigenschap | Waarde |
   |:-----| :-----------|
   | Naam   | Minder dan |
   | Metagegevens-URL | Voeg de URI van de gehoste verificatie-app met de ondervallende toepassing toe, gevolgd door het specifieke pad, bijvoorbeeld https://keyless.auth/.well-known/openid-configuration |
   | Clientgeheim | Het geheim dat is gekoppeld aan het exemplaar met de strenge verificatie sleutel, niet hetzelfde als het certificaat dat eerder is geconfigureerd. Voeg een complexe teken reeks van uw keuze toe. Dit geheim wordt later gebruikt in de configuratie van de sleutel zonder container.|
   | Client-id | De ID van de client. Deze ID wordt later gebruikt in de configuratie met de sleutel zonder container.|
   | Bereik | OpenID Connect |
   | Antwoord type | id_token |
   | Antwoord modus | form_post|

3. Selecteer **OK**.

4. Selecteer **de claims van deze id-provider toewijzen**.

5. Vul het formulier in om de ID-provider toe te wijzen:

   |Eigenschap | Waarde |
   |:-----| :-----------|
   | UserID    | Uit abonnement |
   | Weergavenaam | Uit abonnement |
   | Antwoord modus | Uit abonnement |

6. Selecteer **Opslaan** om de installatie te volt ooien voor uw nieuwe ID-provider (OIDC) voor open-id's.

### <a name="create-a-user-flow-policy"></a>Een beleid voor gebruikers stromen maken

U ziet nu een nieuwe OIDC-ID-provider die wordt vermeld in uw B2C-id-providers.

1. Selecteer in uw Azure AD B2C-Tenant onder **beleids regels** **gebruikers stromen**.

2. Selecteer **nieuwe** gebruikers stroom.

3. Selecteer **Aanmelden en aanmelden**, selecteer een **versie** en selecteer vervolgens **maken**.

4. Voer een **naam** in voor uw beleid.

5. Selecteer in de sectie id-providers de zojuist gemaakte ID-provider voor de niet-ingestelde waarde.

6. Stel de para meters van uw gebruikers stroom in. Voer een naam in en selecteer de ID-provider die u hebt gemaakt. U kunt ook een e-mail adres toevoegen. In dit geval stuurt Azure de aanmeldings procedure niet rechtstreeks naar de functie met de naam minder. er wordt dan een scherm weer gegeven waarin de gebruiker de optie kan kiezen die ze willen gebruiken.

7. Zorg ervoor dat het veld **multi-factor Authentication** ongewijzigd blijft.

8. Selecteer **beleid voor voorwaardelijke toegang afdwingen**

9. Selecteer bij **gebruikers kenmerken en Token claims** **e-mail adres** in de optie kenmerk verzamelen. U kunt alle kenmerken toevoegen die Azure Active Directory over de gebruiker kunt verzamelen naast de claims die Azure AD B2C kunnen worden teruggestuurd naar de client toepassing.

10. Selecteer **Maken**.

11. Nadat het maken is voltooid, selecteert u de nieuwe **gebruikers stroom**.

12. Selecteer **toepassings claims** in het linkerdeel venster. Tik onder opties op het selectie vakje **e-mail** en selecteer **Opslaan**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C Tenant en selecteer onder beleids regels het Framework identiteits ervaring selecteren.

2. Selecteer uw eerder gemaakte SignUpSignIn.

3. Selecteer gebruikers stroom uitvoeren en selecteer de instellingen:

   a. Toepassing: Selecteer de geregistreerde app (voor beeld is JWT)

   b. Antwoord-URL: de omleidings-URL selecteren

   c. Selecteer Gebruikersstroom uitvoeren.

4. Ga door naar de registratie stroom en maak een account

5. Wanneer het gebruikers kenmerk is gemaakt, wordt er tijdens de stroom minder dan de waarde aangeroepen. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)