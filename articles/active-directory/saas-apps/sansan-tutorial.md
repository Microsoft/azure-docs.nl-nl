---
title: 'Zelfstudie: Integratie van Azure Active Directory met Sansan | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Sansan.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 6c35ce5e4b420b7f203b33b640a3175ba4c2739b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181506"
---
# <a name="tutorial-integrate-sansan-with-azure-active-directory"></a>Zelfstudie: Sansan met Azure Active Directory integreren

In deze zelfstudie leert u hoe u Sansan integreert met Azure Active Directory (Azure AD). Wanneer u Sansan integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Sansan.
* U kunt instellen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Sansan.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Sansan waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.
* Sansan biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding.
* Zodra u Sansan hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sansan-from-the-gallery"></a>Sansan toevoegen vanuit de galerie

Als u de integratie van Sansan in Azure AD wilt configureren, moet u Sansan vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Sansan** in het zoekvak.
1. Selecteer **Sansan** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Sansan met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sansan.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Sansan te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
   * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
   * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
1. **[Configureer Sansan](#configure-sansan)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
   * **[Een testgebruiker voor Sansan maken](#create-sansan-test-user)** : als u in Sansan een tegenhanger van Britta Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) naar de toepassingsintegratiepagina van **Sansan**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://ap.sansan.com/`

   1. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL:  
   `https://ap.sansan.com/saml2/<company name>`

   1. In het tekstvak **Antwoord-URL** typt u een van de URL's met de volgende notaties:

    
       | Omgeving | URL |
      |:--- |:--- |
      | Pc |`https://ap.sansan.com/v/saml2/<company name>/acs` |
      | Smartphone-app |`https://internal.api.sansan.com/<company name>/acs` |
      | Smartphone Web |`https://ap.sansan.com/s/saml2/<company name>/acs` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Controleer de werkelijke waarden in de **Sansan-beheerinstellingen**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Sansan instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Sansan.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Sansan**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sansan"></a>Sansan configureren

Volg de onderstaande stappen volgens uw vereisten om de **instellingen voor eenmalige aanmelding** aan de **Sansan** -zijde uit te voeren.

   * [Japanse](https://jp-help.sansan.com/hc/ja/articles/900001551383 ) versie.

   * [Engelse](https://jp-help.sansan.com/hc/en-us/articles/900001551383 ) versie.


### <a name="create-sansan-test-user"></a>Een Sansan-testgebruiker maken

In deze sectie gaat u in Sansan een gebruiker maken met de naam Britta Simon. Raadpleeg [deze](https://jp-help.sansan.com/hc/en-us/articles/206508997-Adding-users) stappen voor meer informatie over het maken van een gebruiker.

## <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel Sansan selecteert, wordt u automatisch aangemeld bij de instantie van Sansan waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)