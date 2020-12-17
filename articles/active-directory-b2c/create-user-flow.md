---
title: Een gebruikersstroom maken - Azure Active Directory B2C
description: Meer informatie over het maken van gebruikersstromen in Azure Portal voor het registreren, aanmelden en bewerken van gebruikersprofielen voor uw toepassingen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 418446e0d465b606b8d580297cebd73c466d4841
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109009"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Een gebruikersstroom maken in Azure Active Directory B2C

U kunt [gebruikersstromen](user-flow-overview.md) van verschillende typen in uw Azure Active Directory B2C-tenant (Azure AD B2C) maken en deze naar behoefte gebruiken in uw toepassingen. Gebruikersstromen kunnen opnieuw worden gebruikt in verschillende toepassingen.

> [!IMPORTANT]
> We hebben de manier veranderd waarop we verwijzen naar gebruikersstroomversies. Eerder boden we V1-versies (klaar voor productie) en V1.1- en V2-versies (preview) aan. Nu hebben we gebruikersstromen samengevoegd in **Aanbevolen** (preview van volgende generatie) en **Standaard** (algemeen beschikbaar) versies. Alle verouderde V1.1- en V2-previewgebruikersstromen worden afgeschaft op **1 augustus 2021**. Raadpleeg [Gebruikersstroomversies in Azure AD B2C](user-flow-versions.md) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

- **Registreer de toepassing** die u wilt gebruiken om de nieuwe gebruikersstroom te testen. Zie bijvoorbeeld [Zelfstudie: een webtoepassing registreren in Azure AD B2C](tutorial-register-applications.md).
- **Voeg externe id-providers toe** als u wilt dat gebruikers zich kunnen aanmelden bij providers als Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft en Twitter. Zie [Zelfstudie: id-providers toevoegen aan uw apps in Azure AD B2C](tutorial-add-identity-providers.md).
- **Configureer de id-provider van het lokale account** om de identiteitstypen (e-mailadres, gebruikersnaam, telefoonnummer) op te geven die u wilt ondersteunen voor lokale accounts in uw tenant. Vervolgens kunt u uit deze ondersteunde identiteitstypen kiezen wanneer u afzonderlijke gebruikersstromen maakt. Wanneer een gebruiker de gebruikersstroom voltooit, wordt er een lokaal account in uw Azure AD B2C-map gemaakt en worden de gegevens van de gebruiker geverifieerd door de id-provider van uw **lokale account**. Configureer de id-provider van het lokale account van uw tenant met de volgende stappen:

   1. Meld u aan bij de [Azure-portal](https://portal.azure.com/). 
   2. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.
   3. Zoek en selecteer **Azure AD B2C** in de zoekbalk boven in Azure Portal.
   4. Onder **Beheren** selecteert u **Id-providers**.
   5. In de lijst met id-providers selecteert u **Lokaal account**.
   6. Op de pagina **Lokale IDP configureren** selecteert u alle identiteitstypen die u wilt ondersteunen. Als u hier opties selecteert, maakt u deze beschikbaar voor de gebruikersstromen die u later maakt:
      - **Telefoon** (preview): hiermee kan een gebruiker een telefoonnummer invoeren, dat wordt geverifieerd bij het registreren en dat vervolgens de gebruikers-id wordt.
      - **E-mailadres** (standaard): hiermee kan een gebruiker een e-mailadres invoeren, dat wordt geverifieerd bij het registreren en dat vervolgens de gebruikers-id wordt.
      - **Gebruikersnaam**: hiermee kan een gebruiker een eigen unieke gebruikers-id maken. Er wordt een e-mailadres van de gebruiker gecontroleerd.
    7. Selecteer **Opslaan**.

## <a name="create-a-user-flow"></a>Een gebruikersstroom maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

    ![B2C-tenant, deelvenster Map + Abonnement, Azure-portal](./media/create-user-flow/directory-subscription-pane.png)

3. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
4. Selecteer onder **Beleid** de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.

    ![De pagina Gebruikersstromen in de portal met de knop Nieuwe gebruikersstroom gemarkeerd](./media/create-user-flow/signup-signin-user-flow.png)

5. Op de pagina **Gebruikersstroom maken** selecteert u het type gebruikersstroom dat u wilt maken (zie [Gebruikersstromen in Azure AD B2C](user-flow-overview.md)).

    ![Een gebruikersstroompagina selecteren waarop registratie en aanmelding is gemarkeerd](./media/create-user-flow/select-user-flow-type.png)

6. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**. ([Meer informatie](user-flow-versions.md) over gebruikersstroomversies.)

    ![De pagina Gebruikersstroom maken in de Azure-portal met eigenschappen gemarkeerd](./media/create-user-flow/select-version.png)

7. Voer een **naam** in voor de gebruikersstroom (bijvoorbeeld *signupsignin1*, *profileediting1*, *passwordreset1*).
8. Onder **Id-providers** kiest u de opties die afhangen van het type gebruikersstroom dat u wilt maken:

   - **Lokaal account**. Als u wilt toestaan dat gebruikers lokale accounts maken in uw Azure AD B2C-tenant, selecteert u het type id dat ze moeten gebruiken (bijvoorbeeld e-mailadres, gebruikers-id of telefoonnummer). Alleen de identiteitstypen die in de instellingen voor de [id-provider van het lokale account](#before-you-begin) worden geconfigureerd, worden vermeld.

   - **Id-providers voor sociale netwerken**. Als u wilt dat gebruikers zich aanmelden met [id-providers voor sociale netwerken die u hebt toegevoegd](tutorial-add-identity-providers.md), zoals Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft of Twitter, selecteert u de providers in de lijst.

9. Kies voor **Gebruikerskenmerken en claims** de claims en kenmerken van de gebruiker die u tijdens de registratie wilt verzamelen en verzenden. Selecteer **Meer weergeven**. Selecteer de kenmerken en claims en selecteer vervolgens **OK**.

    ![Selectiepagina voor gebruikerskenmerken en claims met drie claims geselecteerd](./media/create-user-flow/signup-signin-attributes.png)

10. Selecteer **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch voor de naam geplaatst.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer **Beleid** > **Gebruikersstromen** en selecteer vervolgens de gebruikersstroom die u hebt gemaakt. Op de overzichtspagina van de gebruikersstroom selecteert u **Gebruikersstroom uitvoeren**.
1. Voor **Toepassing** selecteert u de webtoepassing die u in stap 1 hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer **Gebruikersstroom uitvoeren**.
2. Afhankelijk van het type gebruikersstroom dat u wilt testen, registreert u zich met een geldig e-mailadres en volgt u de registratiestroom of meldt u zich aan met een account dat u eerder hebt gemaakt.

    ![De pagina Gebruikersstroom uitvoeren in de portal met de knop Gebruikersstroom uitvoeren gemarkeerd](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Volg de aanwijzingen in de gebruikersstroom. Wanneer u de gebruikersstroom hebt voltooid, wordt het token geretourneerd aan `https://jwt.ms` en moet het worden weergegeven.

> [!NOTE]
> De 'Gebruikersstroom uitvoeren'-ervaring is momenteel niet compatibel met de SPA-antwoord-URL met autorisatiecodestroom. Als u de 'Gebruikersstroom uitvoeren'-ervaring wilt gebruiken met deze typen apps, moet u een antwoord-URL van het type 'Web' registreren en de impliciete stroom inschakelen zoals [hier](tutorial-register-spa.md) beschreven.

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang toevoegen aan Azure AD B2C-gebruikersstromen](conditional-access-user-flow.md)
- [Pas de gebruikersinterface aan in een Azure AD B2C-gebruikersstroom](customize-ui-with-html.md)
