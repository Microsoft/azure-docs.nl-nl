---
title: 'Zelfstudie: Integratie van Azure Active Directory met JOBHUB | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en JOBHUB.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.openlocfilehash: 276d444239cb91f1bec2975506279721e6d0008c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459353"
---
# <a name="tutorial-integrate-jobhub-with-azure-active-directory"></a>Zelfstudie: JOBHUB integreren met Azure Active Directory

In deze zelfstudie leert u hoe u JOBHUB integreert met Azure Active Directory (Azure AD). Wanneer u JOBHUB integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot JOBHUB.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij JOBHUB.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement op JOBHUB waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. JOBHUB ondersteunt met **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-jobhub-from-the-gallery"></a>JOBHUB toevoegen vanuit de galerie

Voor het configureren van de integratie van JOBHUB in Azure AD, moet u JOBHUB vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **Toevoegen uit de galerie** **JOBHUB** in het zoekvak.
1. Selecteer **JOBHUB** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met JOBHUB met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in JOBHUB.

Voltooi de volgende stappen om eenmalige aanmelding voor JOBHUB met Azure AD te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor JOBHUB configureren](#configure-jobhub-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[JOBHUB-testgebruiker maken](#create-jobhub-test-user)** : als u een tegenhanger van Britta Simon in JOBHUB wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/), op de integratiepagina van de toepassing **JOBHUB**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:  In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://pasona.jobhub.jp/saml/init`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van JOBHUB](mailto:platform@pasonagroup.co.jp) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. In het gedeelte **JOBHUB instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-jobhub-sso"></a>Eenmalige aanmelding voor JOBHUB configureren

Als u eenmalige aanmelding aan de zijde van **JOBHUB** wilt configureren, moet u de **waarde voor de vingerafdruk** en de juiste, gekopieerde URL's vanuit Azure Portal naar het [ondersteuningsteam van JOBHUB](mailto:platform@pasonagroup.co.jp) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot JOBHUB.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **JOBHUB** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-jobhub-test-user"></a>JOBHUB-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in JOBHUB. Neem contact op met het [ondersteuningsteam van JOBHUB](mailto:platform@pasonagroup.co.jp) om de gebruikers toe te voegen in het JOBHUB-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de JOBHUB-tegel in het toegangsvenster selecteert, wordt u automatisch aangemeld bij de instantie van JOBHUB waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)