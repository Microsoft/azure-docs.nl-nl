---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met Otsuka Shokai | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Otsuka Shokai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: ef2151c7f3c03ff8996bae1814e4df638b25490f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92513732"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-otsuka-shokai"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Otsuka Shokai

In deze zelfstudie leert u hoe u Otsuka Shokai kunt integreren met Azure Active Directory (Azure AD). Wanneer u Otsuka Shokai integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Otsuka Shokai.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Otsuka Shokai.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Otsuka Shokai waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Otsuka Shokai ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-otsuka-shokai-from-the-gallery"></a>Otsuka Shokai toevoegen vanuit de galerie

Voor het configureren van de integratie van Otsuka Shokai met Azure AD moet u Otsuka Shokai uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Otsuka Shokai** in het zoekvak in de sectie **Toevoegen vanuit de galerie**.
1. Selecteer **Otsuka Shokai** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-otsuka-shokai"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Otsuka Shokai

Configureer en test eenmalige aanmelding van Azure AD met Otsuka Shokai met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Otsuka Shokai.

Voltooi de volgende stappen om eenmalige aanmelding bij Otsuka Shokai met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Otsuka Shokai configureren](#configure-otsuka-shokai-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Otsuka Shokai maken](#create-otsuka-shokai-test-user)** : als u een tegenhanger van B.Simon in Otsuka Shokai wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Otsuka Shokai** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. In de Otsuka Shokai-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de Otsuka Shokai-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de PureCloud by Genesys-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | Appid | `<Application ID>` |

    >[!NOTE]
    >`<Application ID>` is de waarde die u hebt gekopieerd van het tabblad **eigenschappen** in de Azure-portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Otsuka Shokai.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Otsuka Shokai**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-otsuka-shokai-sso"></a>Eenmalige aanmelding voor Otsuka Shokai configureren

1. Wanneer u verbinding maakt met de pagina van de klant vanuit de SSO-app, wordt de wizard voor SSO-instelling gestart.

2. Als de Otsuka-ID niet is geregistreerd, registreert u een nieuwe Otsuka-ID.   Als u de Otsuka-ID hebt geregistreerd, stelt u de koppeling in.

3. Ga door naar het einde en wanneer het bovenste scherm wordt weergegeven nadat u zich hebt aangemeld bij de pagina van de klant, zijn de SSO-instellingen voltooid.

4. De volgende keer dat u vanuit de SSO-app verbinding maakt met de pagina van de klant, wordt het bovenste scherm weergegeven nadat u zich hebt aangemeld bij de pagina van de klant.

### <a name="create-otsuka-shokai-test-user"></a>Otsuka Shokai-testgebruiker maken

Nieuwe registratie van SaaS-account wordt uitgevoerd na de eerste keer aanmelden bij Otsuka Shokai. Daarnaast koppelen we ook de Azure AD-account en SaaS-account op het moment dat er een nieuwe wordt gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Otsuka Shokai klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Otsuka Shokai waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Otsuka Shokai met Azure AD](https://aad.portal.azure.com/)