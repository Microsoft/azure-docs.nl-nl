---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Profit.co | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Profit.co.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.openlocfilehash: 027fb66538dfcead24470507b8080f2e0aacb469
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515199"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Profit.co

In deze zelfstudie leert u hoe u Profit.co integreert met Azure Active Directory (Azure AD). Wanneer u Profit.co integreert met Azure AD, kunt u het volgende doen:

* In Azure AD regelen wie toegang tot Profit.co heeft.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Profit.co.
* Uw accounts op een centrale locatie beheren, namelijk de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Profit.co waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Profit.co ondersteunt eenmalige aanmelding via IDP.

* Nadat u Profit.co hebt geconfigureerd, kunt u sessiebeheer afdwingen. Dit biedt realtime bescherming tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-from-the-gallery"></a>Profit.co toevoegen vanuit de galerie

Als u de integratie van Profit.co met Azure AD wilt configureren, voegt u Profit.co vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Profit.co** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Profit.co** in het venster met resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Profit.co

Configureer en test eenmalige aanmelding van Azure AD met Profit.co met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Profit.co.

Hier volgen de algemene stappen voor het configureren en testen van eenmalige aanmelding van Azure AD met Profit.co:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Profit.co configureren](#configure-profitco-sso)** : als u de instellingen voor eenmalige aanmelding voor de toepassing wilt configureren.
    1. **[Een testgebruiker voor Profit.co maken](#create-a-profitco-test-user)** als tegenhanger van B.Simon in Profit.co. Deze tegenhanger is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Profit.co** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van de pagina Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld in Azure. De gebruiker moet de configuratie opslaan door **Opslaan** te selecteren.

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en selecteer in de sectie **SAML-handtekeningcertificaat** de knop **Kopiëren**. Hiermee wordt de URL van de **App-URL voor federatieve metagegevens** gekopieerd en op uw computer opgeslagen.

    ![Schermopname van het SAML-handtekeningcertificaat, met de kopieerknop gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het veld **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Profit.co.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Profit.co** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers **B.Simon**. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-profitco-sso"></a>Eenmalige aanmelding voor Profit.co configureren

Als u eenmalige aanmelding wilt configureren voor Profit.co, moet u de App-URL voor federatieve metagegevens verzenden naar het [ondersteuningsteam van Profit.co](mailto:support@profit.co). Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.

### <a name="create-a-profitco-test-user"></a>Een testgebruiker voor Profit.co maken

In deze sectie maakt u in Profit.co een gebruiker met de naam B.Simon. Neem contact op met het [ondersteuningsteam van Profit.co](mailto:support@profit.co) om de gebruikers toe te voegen aan het Profit.co-platform. U kunt eenmalige aanmelding pas gebruiken nadat u gebruikers hebt gemaakt en geactiveerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster de tegel Profit.co selecteert, wordt u automatisch aangemeld bij het Profit.co-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Profit.co proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Profit.co beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)