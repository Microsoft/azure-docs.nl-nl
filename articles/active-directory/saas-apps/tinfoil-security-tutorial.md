---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met TINFOIL SECURITY | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TINFOIL SECURITY.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 5c2ac2c7bb1b60c87075a1bc62241edab2fc310e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516287"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met TINFOIL SECURITY

In deze zelfstudie leert u hoe u TINFOIL SECURITY integreert met Azure Active Directory (Azure AD). Wanneer u TINFOIL SECURITY integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot TINFOIL SECURITY.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij TINFOIL SECURITY.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op TINFOIL SECURITY waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TINFOIL SECURITY biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-tinfoil-security-from-the-gallery"></a>TINFOIL SECURITY toevoegen vanuit de galerie

Als u de integratie van TINFOIL SECURITY in Azure AD wilt configureren, moet u TINFOIL SECURITY vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **TINFOIL SECURITY** in het zoekvak.
1. Selecteer **TINFOIL SECURITY** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Eenmalige aanmelding van Azure AD configureren en testen voor TINFOIL SECURITY

Configureer en test eenmalige aanmelding van Azure AD met TINFOIL SECURITY met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TINFOIL SECURITY.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met TINFOIL SECURITY te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor TINFOIL SECURITY configureren](#configure-tinfoil-security-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker maken voor TINFOIL SECURITY](#create-tinfoil-security-test-user)** : als u een tegenhanger van Britta Simon in TINFOIL SECURITY wilt hebben die is gekoppeld aan de weergave van de gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/), op de integratiepagina van de **TINFOIL SECURITY**-toepassing, naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. In de Visitly-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Visitly-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > De waarde voor accountid wordt verderop in de zelfstudie uitgelegd.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-handtekeningcertificaat** de **Vingerafdrukwaarde** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. In de sectie **TINFOIL SECURITY instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot TINFOIL SECURITY.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **TINFOIL SECURITY** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tinfoil-security-sso"></a>Eenmalige aanmelding voor TINFOIL SECURITY configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in TINFOIL SECURITY.

1. Klik in de werkbalk bovenaan op **My account**.

    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

1. Klik op **Beveiliging**.

    ![Beveiliging](./media/tinfoil-security-tutorial/ic798972.png "Beveiliging")

1. Voer op de configuratiepagina **Single Sign-On** de volgende stappen uit:

    ![Single Sign-On](./media/tinfoil-security-tutorial/ic798973.png "Eenmalige aanmelding")

    a. Selecteer **SAML inschakelen**.

    b. Klik op **Handmatige configuratie**.

    c. Plak in het tekstvak **SAML Post URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd vanuit Azure Portal

    d. Plak in het tekstvak **SAML Certificate Fingerprint** de waarde van de **vingerafdruk** die u hebt gekopieerd vanuit de sectie **SAML-handtekeningcertificaat**.
  
    e. Kopieer de waarde van uw **account-id** en plak deze in het tekstvak **Bronkenmerk** onder het gedeelte **Gebruikerskenmerken en claims** in Azure Portal.

    f. Klik op **Opslaan**.

### <a name="create-tinfoil-security-test-user"></a>Testgebruiker maken voor TINFOIL SECURITY

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij TINFOIL SECURITY, moeten ze worden ingericht in TINFOIL SECURITY. In het geval van TINFOIL SECURITY wordt het inrichten handmatig uitgevoerd.

**Voer de volgende stappen uit als u een gebruiker wilt inrichten:**

1. Als de gebruiker deel uitmaakt van een Enterprise-account, moet u [contact opnemen met het ondersteuningsteam van TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) om het gebruikersaccount te laten maken.

1. Als de gebruiker een gewone SaaS-gebruiker van TINFOIL SECURITY is, kan de gebruiker een samenwerker toevoegen aan een van de sites van de gebruiker. Hiermee wordt een proces geactiveerd om een uitnodiging naar het opgegeven e-mailadres te verzenden om een nieuw TINFOIL SECURITY-gebruikersaccount te maken.

> [!NOTE]
> U kunt andere hulpprogramma's of API's van TINFOIL SECURITY gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel TINFOIL SECURITY klikt, wordt u automatisch aangemeld bij de instantie van TINFOIL SECURITY waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [TINFOIL SECURITY met Azure AD uitproberen](https://aad.portal.azure.com/)