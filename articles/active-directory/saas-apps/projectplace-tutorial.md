---
title: 'Zelfstudie: Azure Active Directory-integratie met Projectplace | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Projectplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: 508a3ca02ec6c0475e78d16691f6597e1a389f97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511848"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Zelfstudie: Projectplace integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Projectplace kunt integreren met Azure Active Directory (Azure AD). Wanneer u Projectplace integreert met Azure AD, kunt u het volgende:

* In Azure AD regelen wie toegang tot Projectplace heeft.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Projectplace.
* Uw accounts op een centrale locatie beheren: Azure Portal.
* Gebruikers kunnen automatisch worden ingericht in Projectplace.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* een Projectplace-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Projectplace ondersteunt met **SP en IDP** geïnitieerde eenmalige aanmelding en het **Just-In-Time** inrichten van gebruikers.

## <a name="adding-projectplace-from-the-gallery"></a>Projectplace toevoegen vanuit de galerie

Om de integratie van Projectplace te configureren in Azure AD, moet u Projectplace vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Projectplace**.
1. Selecteer **Projectplace** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Projectplace met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Projectplane.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Projectplace te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Projectplace](#configure-projectplace)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Testgebruiker voor Projectplace maken](#create-projectplace-test-user)** : als u een tegenhanger van B.Simon in Projectplace wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Projectplace** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** kunt u de toepassing configureren in de door **IDP** geïnitieerde modus. De toepassing is vooraf geconfigureerd en de benodigde URL's zijn vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://service.projectplace.com`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u in het gedeelte **SAML-handtekeningcertificaat** op het **pictogram** kopiëren om de  **App-URL voor federatieve metagegevens** zoals vereist te kopiëren en deze op te slaan in Kladblok.

   ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

1. In de sectie **Projectplace instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Projectplace configureren

Om eenmalige aanmelding te configureren bij **Projectplace**, moet u de gekopieerde  **App-URL voor federatieve metagegevens** vanuit het Azure-portaal versturen naar het [ondersteuningsteam voor Projectplace](https://success.planview.com/Projectplace/Support). Zij zorgen ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

>[!NOTE]
>De configuratie voor eenmalige aanmelding moet uitgevoerd worden door het [ondersteuningsteam voor Projectplace](https://success.planview.com/Projectplace/Support). U ontvangt een melding zodra de configuratie voltooid is. 

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B. Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Projectplace.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Projectplace** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B. Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-projectplace-test-user"></a>Projectplace-testgebruiker maken

>[!NOTE]
>U kunt deze stap overslaan als u inrichten heeft ingeschakeld in Projectplace. U kunt het [ondersteuningsteam voor Projectplace](https://success.planview.com/Projectplace/Support) vragen om inrichten in te schakelen. Wanneer dit gebeurd is, worden gebruikers in Projectplace aangemaakt bij de eerste aanmelding.

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Projectplace, moet u ze toevoegen aan Projectplace. U moet ze handmatig toevoegen.

**Voer deze stappen uit om een gebruikersaccount te maken:**

1. Meld u bij de bedrijfssite van **Projectplace** aan als beheerder.

2. Ga naar **Personen** en selecteer vervolgens **Leden**:
   
    ![Ga naar Personen en selecteer vervolgens Leden](./media/projectplace-tutorial/ic790228.png "People")

3. Selecteer **Lid toevoegen**:
   
    ![Selecteer Lid toevoegen](./media/projectplace-tutorial/ic790232.png "Leden toevoegen")

4. Voer de volgende stappen uit in de sectie **Lid toevoegen**.
   
    ![Sectie Lid toevoegen](./media/projectplace-tutorial/ic790233.png "Nieuwe leden")
   
    1. Voer in het vak **Nieuwe leden** het e-mailadres in van een geldig Azure AD-account dat u wilt toevoegen.
   
    1. Selecteer **Verzenden**.

   Er wordt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt verzonden naar de houder van het Azure AD-account.

>[!NOTE]
>Voor het toevoegen van Azure AD-gebruikersaccounts kunt u gebruikmaken van elk ander hulpprogramma of elke andere API van Projectplace.


### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel Projectplace selecteert in het toegangsvenster, wordt u automatisch aangemeld bij Projectplace waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)