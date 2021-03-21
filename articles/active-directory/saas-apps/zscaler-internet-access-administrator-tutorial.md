---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zscaler Internet Access Administrator | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler Internet Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 70afa0a02f4e303105aec1884b966796854c6f49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449314"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Zelfstudie: Integratie van Azure Active Directory met Zscaler Internet Access Administrator

In deze zelf studie leert u hoe u Zscaler Internet Access Administrator integreert met Azure Active Directory (Azure AD). Wanneer u Zscaler Internet Access-beheerder integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Zscaler Internet Access-beheerder.
* Stel in dat uw gebruikers automatisch worden aangemeld voor Zscaler Internet Access Administrator met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Zscaler-abonnement met eenmalige aanmelding (SSO) voor Internet toegang beheerder.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Internet Access-beheerder ondersteunt door **IDP** geïnitieerde SSO.

## <a name="add-zscaler-internet-access-administrator-from-the-gallery"></a>Zscaler Internet Access Administrator toevoegen vanuit de galerie

Als u de integratie van Zscaler Internet Access Administrator met Azure AD wilt configureren, dient u Zscaler Internet Access Administrator vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Zscaler Internet Access Administrator** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **Zscaler Internet Access Administrator** in het resultaten paneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Azure AD SSO configureren en testen voor Zscaler Internet Access-beheerder

Configureer en test Azure AD SSO met Zscaler Internet Access-beheerder met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler Internet Access Administrator.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Zscaler Internet Access-beheerder:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[CONFIGUREER SSO van Zscaler Internet Access Administrator](#configure-zscaler-internet-access-administrator-sso)** -om de instellingen voor één Sign-On te configureren aan de kant van de toepassing.
    1. **[Testgebruiker van Zscaler Internet Access Administrator maken](#create-zscaler-internet-access-administrator-test-user)** : als u een equivalent van Britta Simon in Zscaler Internet Access Administrator wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **Zscaler Internet Access Administrator** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. Typ in het tekstvak **id** een van de volgende url's volgens uw vereiste:

    | Id |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Typ in het tekstvak **antwoord-URL** een van de volgende url's volgens uw vereiste:

    | Antwoord-URL |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. De Zscaler Internet Access Administrator-toepassing verwacht dat de SAML-beweringen in een bepaalde indeling staan. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken en claims** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken en claims** te openen.

    ![Koppeling Kenmerk](./media/zscaler-internet-access-administrator-tutorial/attributes.png)

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    | Naam  | Bronkenmerk  |
    | ---------| ------------ |
    | Rol | user.assignedroles |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. Selecteer de kenmerkwaarde in de lijst **Bronkenmerk**.

    c. Klik op **OK**.

    d. Klik op **Opslaan**.

    > [!NOTE]
    > Klik [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) als u wilt weten hoe u rollen in Azure AD moet configureren.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. In de sectie **Zscaler Internet Access Administrator instellen** kopieert u de juiste URL('s) overeenkomstig wat u nodig hebt.

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot Zscaler Internet Access-beheerder.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Zscaler Internet Access-beheerder**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-zscaler-internet-access-administrator-sso"></a>Eenmalige aanmelding voor Zscaler Internet Access-beheerder configureren

1. Meld u in een ander browservenster als beheerder aan bij de beheerinterface van Zscaler Internet Access Administrator.

2. Ga naar **Administration > Administrator Management**, voer de volgende stappen uit en klik op Save:

    ![Schermopname van Administrator Management met opties voor het inschakelen van SAML-verificatie, het uploaden van een SSL-certificaat en het opgeven van een certificaatverlener.](./media/zscaler-internet-access-administrator-tutorial/management.png "Beheer")

    a. Selecteer **Enable SAML Authentication** (vinkje).

    b. Klik op **Uploaden** om het Azure SAML-ondertekeningscertificaat te uploaden dat u in de Azure-portal in het **openbare SSL-certificaat** hebt gedownload.

    c. Voor extra beveiliging voegt u desgewenst in het vak **Issuer** gegevens toe om de uitgever van het SAML-antwoord te controleren.

3. Voer de volgende stappen uit in de beheerinterface:

    ![Schermopname van de beheerdersinterface, waar u de stappen kunt uitvoeren.](./media/zscaler-internet-access-administrator-tutorial/activation.png)

    a. Beweeg de muisaanwijzer boven het menu **Activering** linksonder.

    b. Klik op **Activeren**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Testgebruiker van Zscaler Internet Access Administrator maken

Het doel van deze sectie is om in Zscaler Internet Access Administrator een gebruiker te maken met de naam Britta Simon. TZscaler Internet Access Administrator biedt geen ondersteuning voor Just-In-Time inrichting voor eenmalige aanmelding bij Administrator. U moet handmatig een Administrator-account maken.
Instructies hiervoor vindt u in deze documentatie van Zscaler:

https://help.zscaler.com/zia/adding-admins

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en meld u automatisch aan bij de Zscaler Internet Access-beheerder waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Zscaler Internet toegangs beheer in de mijn apps klikt, moet u automatisch worden aangemeld bij de Zscaler Internet Access-beheerder waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Zscaler Internet Access-beheerder hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
