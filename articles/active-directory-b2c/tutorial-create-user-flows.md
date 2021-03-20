---
title: 'Zelfstudie: Gebruikersstromen maken - Azure Active Directory B2C'
description: Volg deze zelfstudie voor meer informatie over het maken van gebruikersstromen in Azure Portal voor het registreren, aanmelden en bewerken van gebruikersprofielen voor uw toepassingen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6b0bdc5a5b58c205d888c8892a4333225a9b316f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100557139"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Zelfstudie: Gebruikersstromen maken in Azure Active Directory B2C

In uw toepassingen kunt u [gebruikersstromen](user-flow-overview.md) hebben waarmee gebruikers zich kunnen registreren, aanmelden of hun profiel kunnen beheren. U kunt meerdere gebruikersstromen van verschillende typen in uw Azure Active Directory B2C-tenant (Azure AD B2C) maken en deze naar behoefte gebruiken in uw toepassingen. Gebruikersstromen kunnen opnieuw worden gebruikt in verschillende toepassingen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een gebruikersstroom voor registratie en aanmelding maken
> * Een gebruikersstroom voor profielbewerking maken
> * Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

In deze zelfstudie ziet u hoe u enkele aanbevolen gebruikersstromen kunt maken met behulp van de Azure-portal. Zie [De stroom voor referenties voor wachtwoord van resource-eigenaar configureren in Azure AD B2C](add-ropc-policy.md) voor informatie over het instellen van een stroom voor referenties voor het wachtwoord van resource-eigenaar (ROPC) in uw toepassing.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> We hebben de manier veranderd waarop we verwijzen naar gebruikersstroomversies. Eerder boden we V1-versies (klaar voor productie) en V1.1- en V2-versies (preview) aan. Nu hebben we gebruikersstromen samengevoegd in **Aanbevolen** (preview van volgende generatie) en **Standaard** (algemeen beschikbaar) versies. Alle verouderde V1.1- en V2-previewgebruikersstromen worden afgeschaft op **1 augustus 2021**. Raadpleeg [Gebruikersstroomversies in Azure AD B2C](user-flow-versions.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

[Registreer uw toepassingen](tutorial-register-applications.md) die deel uitmaken van de gebruikersstromen die u wilt maken.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Een gebruikersstroom voor registratie en aanmelding maken

Met de gebruikersstroom voor registratie en aanmelding worden zowel registratie als aanmelding met een enkele configuratie afgehandeld. Gebruikers van uw toepassing worden naar het juiste pad geleid, afhankelijk van de context.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

    ![B2C-tenant, deelvenster Map + Abonnement, Azure-portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer onder **Beleid** de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.

    ![De pagina Gebruikersstromen in de portal met de knop Nieuwe gebruikersstroom gemarkeerd](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Selecteer op de pagina **Een gebruikersstroom maken** de gebruikersstroom **Registreren en aanmelden**.

    ![Een gebruikersstroompagina selecteren met de stroom voor registratie en aanmelding gemarkeerd](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**. ([Meer informatie](user-flow-versions.md) over gebruikersstroomversies.)

    ![De pagina Gebruikersstroom maken in de Azure-portal met eigenschappen gemarkeerd](./media/tutorial-create-user-flows/select-version.png)

1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *signupsignin1*.
1. Selecteer voor **Id-providers** de optie **E-mailregistratie**.
1. Kies voor **Gebruikerskenmerken en claims** de claims en kenmerken van de gebruiker die u tijdens de registratie wilt verzamelen en verzenden. Selecteer bijvoorbeeld **Meer weergeven** en kies vervolgens kenmerken en claims voor **Land/regio**, **Weergavenaam** en **Postcode**. Klik op **OK**.

    ![Selectiepagina voor gebruikerskenmerken en claims met drie claims geselecteerd](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klik op **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch voor de naam geplaatst.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **Gebruikersstroom uitvoeren** en selecteer **Nu registreren**.

    ![De pagina Gebruikersstroom uitvoeren in de portal met de knop Gebruikersstroom uitvoeren gemarkeerd](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Voer een geldig e-mailadres in, klik op **Verificatiecode verzenden**, voer de verificatiecode in die u ontvangt en selecteer **Code verifiëren**.
1. Voer een nieuw wachtwoord in en bevestig het wachtwoord.
1. Selecteer uw land en regio, voer de naam in die u wilt weergeven, voer een postcode in en klik vervolgens op **Maken**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.
1. U kunt de gebruikersstroom nu opnieuw uitvoeren en u moet zich kunnen aanmelden met het account dat u hebt gemaakt. Het geretourneerde token bevat de claims die u hebt geselecteerd voor land/regio, naam en postcode.

> [!NOTE]
> De 'Gebruikersstroom uitvoeren'-ervaring is momenteel niet compatibel met de SPA-antwoord-URL met autorisatiecodestroom. Als u de 'Gebruikersstroom uitvoeren'-ervaring wilt gebruiken met deze typen apps, moet u een antwoord-URL van het type 'Web' registreren en de impliciete stroom inschakelen zoals [hier](tutorial-register-spa.md) beschreven.

## <a name="create-a-profile-editing-user-flow"></a>Een gebruikersstroom voor profielbewerking maken

Als u gebruikers in staat wilt stellen hun profiel te bewerken in uw toepassing, gebruikt u een gebruikersstroom voor het bewerken van profielen.

1. Selecteer in het menu van het paginaoverzicht van de Azure AD B2C-tenant de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.
1. Selecteer op de pagina **Een gebruikersstroom maken** de gebruikersstroom **Profiel bewerken**. 
1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**.
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *profileediting1*.
1. Klik op **Id-providers** en selecteer **Aanmelding met lokaal account**.
2. Kies voor **Gebruikerskenmerken** de kenmerken waarvan u wilt dat de klant deze in zijn profiel kan bewerken. Selecteer bijvoorbeeld **Meer weergeven** en kies vervolgens kenmerken en claims voor **Weergavenaam** en **Functie**. Klik op **OK**.
3. Klik op **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch aan de naam toegevoegd.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **Gebruikersstroom uitvoeren** en meld u vervolgens aan met het account dat u eerder hebt gemaakt.
1. U hebt nu de mogelijkheid om de weergavenaam en de functie voor de gebruiker te wijzigen. Klik op **Doorgaan**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.

## <a name="create-a-password-reset-user-flow"></a>Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Als u gebruikers van uw toepassing de mogelijkheid wilt bieden hun wachtwoord opnieuw in te stellen, gebruikt u een gebruikersstroom voor het opnieuw instellen van wachtwoorden.

1. Selecteer in het overzichtsmenu van de Azure AD B2C-tenant de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.
1. Selecteer op de pagina **Een gebruikersstroom maken** de gebruikersstroom **Wachtwoord opnieuw instellen**. 
1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**.
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *passwordreset1*.
1. Schakel voor de optie **Id-providers** de optie **Het wachtwoord opnieuw instellen met e-mailadres** in.
2. Klik onder Toepassingsclaims op **Meer weergeven** en kies de claims die u wilt retourneren in de autorisatietokens die worden teruggestuurd naar uw toepassing. Selecteer bijvoorbeeld **Object-ID van gebruiker**.
3. Klik op **OK**.
4. Klik op **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch aan de naam toegevoegd.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **Gebruikersstroom uitvoeren**, controleer het e-mailadres van het account dat u eerder hebt gemaakt en selecteer **Doorgaan**.
1. U hebt nu de mogelijkheid om het wachtwoord voor de gebruiker te wijzigen. Wijzig het wachtwoord en selecteer **Doorgaan**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een gebruikersstroom voor registratie en aanmelding maken
> * Een gebruikersstroom voor profielbewerking maken
> * Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Lees vervolgens hoe u Azure AD B2C kunt gebruiken om u aan te melden en gebruikers in een toepassing te registreren. Volg de koppeling van de ASP.NET-webtoepassing hieronder of navigeer naar een andere toepassing in de inhouds opgave onder **gebruikers verifiëren**.

> [!div class="nextstepaction"]
> [Zelf studie: verificatie inschakelen in een webtoepassing met behulp van Azure AD B2C >](tutorial-web-app-dotnet.md)
