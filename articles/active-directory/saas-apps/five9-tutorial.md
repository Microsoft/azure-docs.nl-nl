---
title: 'Zelfstudie: Azure Active Directory-integratie met Five9 Plus Adapter (CTI, contactcentermedewerkers) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Five9 Plus Adapter (CTI, contactcentermedewerkers).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: 68de5b11c131fe33252178ebecdeb9c3855fe239
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453430"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Zelfstudie: Azure Active Directory-integratie met Five9 Plus Adapter (CTI, contactcentermedewerkers)

In deze zelfstudie leert u hoe u Five9 Plus Adapter (CTI, contactcentermedewerkers) kunt integreren met Azure Active Directory (Azure AD).
Integratie van Five9 Plus Adapter (CTI, contactcentermedewerkers) met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Five9 Plus Adapter (CTI, contactcentermedewerkers).
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Five9 Plus Adapter (CTI, contactcentermedewerkers) (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Five9 Plus Adapter (CTI, contactcentermedewerkers) te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Five9 Plus Adapter (CTI, contactcentermedewerkers) waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Five9 Plus Adapter (CTI, contactcentermedewerkers) ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Five9 Plus Adapter (CTI, contactcentermedewerkers) toevoegen uit de galerie

Om de integratie van Five9 Plus Adapter (CTI, contactcentermedewerkers) te configureren in Azure AD, moet u Five9 Plus Adapter (CTI, contactcentermedewerkers) uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Five9 Plus Adapter (CTI, contactcentermedewerkers) toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Five9 Plus Adapter (CTI, contactcentermedewerkers)** in het zoekvak, selecteer **Five9 Plus Adapter (CTI, contactcentermedewerkers)** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om het toe te voegen aan de toepassing.

     ![Five9 Plus Adapter (CTI, contactcentermedewerkers) in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding met Azure AD bij Five9 Plus Adapter (CTI, contactcentermedewerkers) configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Five9 Plus Adapter (CTI, contactcentermedewerkers) tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Five9 Plus Adapter (CTI, contactcentermedewerkers) te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Five9 Plus Adapter (CTI, contactcentermedewerkers) configureren](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Five9 Plus Adapter (CTI, contactcentermedewerkers) maken](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** : als u een tegenhanger van Britta Simon in Five9 Plus Adapter (CTI, contactcentermedewerkers) wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Five9 Plus Adapter (CTI, contactcentermedewerkers) te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Five9 Plus Adapter (CTI, contactcentermedewerkers)** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Five9 Plus Adapter (CTI, contactcentermedewerkers)](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:
    
    |    Omgeving      |       URL      |
    | :-- | :-- |
    | Voor ‘Five9 Plus Adapter voor Microsoft Dynamics CRM’ | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Voor ‘Five9 Plus Adapter voor Zendesk’ | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Voor ‘Five9 Plus Adapter voor Agent Desktop Toolkit’ | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    |      Omgeving     |      URL      |
    | :--                  | :--           |
    | Voor ‘Five9 Plus Adapter voor Microsoft Dynamics CRM’ | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Voor ‘Five9 Plus Adapter voor Zendesk’ | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Voor ‘Five9 Plus Adapter voor Agent Desktop Toolkit’ | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Five9 Plus Adapter (CTI, contactcentermedewerkers)** de juiste URL(‘s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Eenmalige aanmelding bij Five9 Plus Adapter (CTI, contactcentermedewerkers) configureren

1. Als u eenmalige aanmelding aan de zijde van **Five9 Plus Adapter (CTI, contactcentermedewerkers)** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste gekopieerde URL(‘s) naar het [ondersteuningsteam van Five9 Plus Adapter (CTI, contactcentermedewerkers)](https://www.five9.com/about/contact) verzenden. Om eenmalige aanmelding verder te configureren, moet u daarnaast ook de onderstaande stappen volgen, afhankelijk van de adapter:

    a. Beheerdershandleiding ‘Five9 Plus Adapter voor Agent Desktop Toolkit’: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Beheerdershandleiding ‘Five9 Plus Adapter voor Microsoft Dynamics CRM’: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Beheerdershandleiding ‘Five9 Plus Adapter voor Zendesk’: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Five9 Plus Adapter (CTI, contactcentermedewerkers).

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Five9 Plus Adapter (CTI, contactcentermedewerkers)** .

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Five9 Plus Adapter (CTI, contactcentermedewerkers)** in de lijst met toepassingen.

    ![De koppeling Five9 Plus Adapter (CTI, contactcentermedewerkers) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Een testgebruiker voor Five9 Plus Adapter (CTI, contactcentermedewerkers) maken

In deze sectie gaat u een gebruiker maken met de naam Britta Simon in Five9 Plus Adapter (CTI, contactcentermedewerkers). Werk samen met het [ondersteuningsteam van Five9 Plus Adapter (CTI, contactcentermedewerkers)](https://www.five9.com/about/contact) om gebruikers toe te voegen op het Five9 Plus Adapter (CTI, contactcentermedewerkers)-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Five9 Plus Adapter (CTI, contactcentermedewerkers) klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Five9 Plus Adapter (CTI, contactcentermedewerkers) waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)