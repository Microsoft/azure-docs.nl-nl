---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Onit | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Onit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 40ed33021a892e140dfff47417137046557c0aef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522356"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Onit

In deze zelfstudie leert u hoe u Onit kunt integreren met Azure Active Directory (Azure AD). Wanneer u Onit integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Onit.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Onit.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Onit waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Onit biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-onit-from-the-gallery"></a>Onit toevoegen vanuit de galerie

Voor het configureren van de integratie van Onit in Azure AD moet u Onit vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Onit**.
1. Selecteer **Onit** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Onit

Configureer en test eenmalige aanmelding van Azure AD met Onit met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Onit.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Onit te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Onit configureren](#configure-onit-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Onit maken](#create-onit-test-user)** : als u een tegenhanger van Britta Simon in Onit wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Onit** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<sub-domain>.onit.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<sub-domain>.onit.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Onit](https://www.onit.com/support) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-handtekeningcertificaat** de **Vingerafdrukwaarde** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. In de sectie **Onit instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding via Azure door haar toegang te geven tot Onit.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Onit** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-onit-sso"></a>Eenmalige aanmelding voor Onit configureren

1. Meld u als administrator in een ander browservenster aan bij de bedrijfssite van Onit.

2. Klik in het menu bovenaan op **Administration** (Beheer).
   
    ![Schermopname met het menu bovenaan de pagina 'MS SSO Test' met de actie 'Administration' geselecteerd.](./media/onit-tutorial/IC791174.png "Beheer")

3. Klik op **Edit Corporation** (Bedrijf bewerken).
   
    ![Edit Corporation](./media/onit-tutorial/IC791175.png "Bedrijf bewerken")
   
4. Klik op het tabblad **Beveiliging**.
    
    ![Bedrijfsgegevens bewerken](./media/onit-tutorial/IC791176.png "Bedrijfsgegevens bewerken")

5. Voer de volgende stappen uit op het tabblad **Security** (Beveiliging):

    ![Single Sign-On](./media/onit-tutorial/IC791177.png "Eenmalige aanmelding")

    a. Selecteer **Single Sign On and Password** (eenmalige aanmelding en wachtwoord) als **Authentication Strategy** (verificatiemethode).
    
    b. Plak in het tekstvak **Idp Target URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Idp logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Idp Cert Fingerprint (SHA1)** de waarde van de **Vingerafdruk** van het certificaat die u hebt gekopieerd uit de Azure-portal.

### <a name="create-onit-test-user"></a>Testgebruiker voor Onit maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Onit, moeten ze in Onit worden ingericht. In het geval van Onit, moet het inrichten handmatig worden uitgevoerd.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u als beheerder aan bij de bedrijfssite van **Onit**.

2. Klik op **Add User**.

    ![Beheer](./media/onit-tutorial/IC791180.png "Beheer")

3. Voer de volgende stappen uit in het dialoogvenster **Add User**:

    ![Gebruiker toevoegen](./media/onit-tutorial/IC791181.png "Gebruiker toevoegen")

    a. Typ in de betreffende tekstvakken de **naam** en het **e-mailadres** van een geldig Azure AD-account dat u wilt inrichten.

    b. Klik op **Create**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Onit in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Onit waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Onit met Azure AD uitproberen](https://aad.portal.azure.com/)