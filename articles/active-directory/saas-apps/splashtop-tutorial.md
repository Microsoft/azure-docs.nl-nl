---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Splashtop | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Splashtop.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: b6dda20487caf6fe3ba49578cfdc0b65434a8dfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Splashtop

In deze zelfstudie leert u hoe u Splashtop integreert met Azure Active Directory (Azure AD). Wanneer u Splashtop integreert met Azure AD, kunt u het volgende doen:

* Bepalen in Azure AD wie er toegang heeft tot Splashtop.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Splashtop.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Splashtop waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Splashtop ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Zodra u Splashtop hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Splashtop toevoegen vanuit de galerie

Voor het configureren van de integratie van Splashtop met Microsoft Azure Active Directory moet u Splashtop vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Splashtop**.
1. Selecteer **Splashtop** in het resultatenvenster en voeg de toepassing vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Splashtop

Configureer en test eenmalige aanmelding van Azure AD met Splashtop met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Splashtop.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Splashtop te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Splashtop configureren](#configure-splashtop-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Splashtop-testgebruiker maken](#create-splashtop-test-user)** : als u een equivalent van B.Simon in Splashtop wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Splashtop**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://my.splashtop.com/login/sso`

1. In de Splashtop-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. Op de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de Splashtop-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Splashtop instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Splashtop.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Splashtop** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-splashtop-sso"></a>Eenmalige aanmelding configureren voor Splashtop

In dit gedeelte moet u een aanvraag indienen voor een nieuwe methode voor eenmalige aanmelding via de [Splashtop-webportal](https://my.splashtop.com/login).
1. Ga in de Splashtop-webportal naar het tabblad **Account info** / **Team** en schuif omlaag naar het gedeelte **Single Sign On**. Klik vervolgens op **Apply for new SSO method**.

    ![Schermopname met de pagina Eenmalige aanmelding waar u Toevoegen kunt selecteren voor een nieuwe methode voor eenmalige aanmelding.](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Geef in het venster dat wordt weergegeven een **naam voor eenmalige aanmelding** op. Geef bijvoorbeeld Nieuwe Azure op. Selecteer vervolgens **Azure** als het type IDP en voer de **aanmeldings-URL** en **Azure AD-id** die u uit de Splashtop-toepassing hebt gekopieerd in Azure Portal in.

    ![Schermopname met de pagina Methode voor eenmalige aanmelding toepassen, waar u een naam en andere informatie kunt invoeren.](media/splashtop-tutorial/azure-sso-1.png)

1. Klik voor informatie over certificaten met de rechtermuisknop op het certificaatbestand dat is gedownload uit de Splashtop-toepassing in Azure Portal, bewerk het met Kladblok, kopieer de inhoud en plak het in het veld **Download Certificate (Base64)** .

    ![Schermopname toont het selecteren van een certificaatbestand dat in Kladblok wordt geopend.](media/splashtop-tutorial/cert-1.png)
    ![Schermopname met de inhoud van het certificaatbestand.](media/splashtop-tutorial/cert-2.png)
    ![Schermopname met het tekstvak Certificaat downloaden.](media/splashtop-tutorial/azure-sso-2.png)

1. Dat is alles. Klik op **Save**. Het Splashtop-team voor validatie van eenmalige aanmelding neemt contact met u op voor de verificatiegegevens en activeert vervolgens de methode voor eenmalige aanmelding.

### <a name="create-splashtop-test-user"></a>Splashtop-testgebruiker maken

1. Nadat de methode voor eenmalige aanmelding is geactiveerd, controleert u de zojuist gemaakte methode voor eenmalige aanmelding om deze in te schakelen in het gedeelte **Single Sign On**.

    ![Schermopname van de pagina Eenmalige aanmelding, waar u de nieuwe methode kunt inschakelen.](media/splashtop-tutorial/enable.png)

1. Nodig de testgebruiker, bijvoorbeeld `B.Simon@contoso.com`, uit voor uw Splashtop-team met de zojuist gemaakte methode voor eenmalige aanmelding.

    ![Schermopname van de pagina Gebruikers uitnodigen, waar u de nieuwe methode kunt selecteren.](media/splashtop-tutorial/invite.png)

1. U kunt ook een bestaand Splashtop-account wijzigen in een account voor eenmalige aanmelding. Zie de [instructies](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Dat is alles. U kunt het account voor eenmalige aanmelding gebruiken om u aan te melden bij de Splashtop-webportal of zakelijke Splashtop-toepassing.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Splashtop in het toegangsvenster klikt, wordt u automatisch aangemeld bij het Splashtop-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Splashtop gebruiken met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Splashtop beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)