---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Discovery Benefits SSO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Discovery Benefits SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 0e5d67e00ee56b5c4006a8422c713e3cabb32bfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454759"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Discovery Benefits SSO

In deze zelfstudie leert u hoe u Discovery Benefits SSO kunt integreren met Azure Active Directory (Azure AD). Wanneer u Discovery Benefits SSO integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Discovery Benefits SSO.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Discovery Benefits SSO.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Discovery Benefits SSO waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Discovery Benefits SSO ondersteunt door de **IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Discovery Benefits SSO toevoegen vanuit de galerie

Om de integratie van Discovery Benefits SSO met Azure AD te configureren, moet u Discovery Benefits SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Discovery Benefits SSO** in het zoekvak.
1. Selecteer **Discovery Benefits SSO** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Discovery Benefits SSO

Configureer en test eenmalige aanmelding van Azure AD met Discovery Benefits SSO met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Discovery Benefits SSO.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Discovery Benefits SSO, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Discovery Benefits SSO configureren](#configure-discovery-benefits-sso-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker maken in Discovery Benefits SSO](#create-discovery-benefits-sso-test-user)** : om een tegenhanger voor B.Simon te maken in Discovery Benefits SSO die is gekoppeld aan de voorstelling van de gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Discovery Benefits SSO** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. In Discovery Benefits SSO worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![image](common/edit-attribute.png)

    a. Klik op het pictogram **Bewerken** om het dialoogvenster **Unieke gebruikers-id (naam-id)** te openen.

    ![Schermopname met de sectie 'Gebruikerskenmerken en claims' met het beletselteken naast 'Vereiste claim' geselecteerd aan de rechterkant.](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Configuratie van Discovery Benefits SSO](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Klik op het pictogram **Bewerken** om het dialoogvenster **Transformatie beheren** te openen.

    c. Typ in het tekstvak **Transformatie** de **ToUppercase()** die wordt weergegeven voor die rij.

    d. Typ in het tekstvak **Parameter 1** de parameter, zoals `<Name Identifier value>`.

    e. Klik op **Add**.

    > [!NOTE]
    > Voor Discovery Benefits SSO moet een vaste tekenreeks waarde worden doorgegeven aan het veld **Unieke gebruikers-id (naam-id)** om deze integratie te kunnen gebruiken. Azure AD biedt momenteel geen ondersteuning voor deze functie. Als tijdelijke oplossing kunt u transformaties van het type **ToUpper** of **ToLower** van NameID gebruiken om een vaste tekenreekswaarde in te stellen zoals hierboven in de schermafbeelding wordt weergegeven.

    f. We hebben de extra claims die vereist zijn voor de configuratie van eenmalige aanmelding (`SSOInstance` en `SSOID`) automatisch ingevuld. Gebruik het pictogram **Bewerken** om de waarden toe te wijzen volgens uw organisatie.

    ![Schermopname met "User Attributes & Claims" met de waarden voor "S S O Instance" en "S S O I D" gemarkeerd.](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Discovery Benefits SSO instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Discovery Benefits SSO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Discovery Benefits SSO** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-discovery-benefits-sso-sso"></a>Configuratie van Discovery Benefits SSO

Als u eenmalige aanmelding wilt configureren in **Discovery Benefits SSO**, moet u het gedownloade **Certificaat (Base64)** en de juiste uit Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Discovery Benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-discovery-benefits-sso-test-user"></a>Testgebruiker voor Discovery Benefits SSO maken

In deze sectie gaat u in Discovery Benefits SSO een gebruiker maken met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Discovery Benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com) om de gebruikers toe te voegen in het Discovery Benefits SSO-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Discovery Benefits SSO klikt, wordt u automatisch aangemeld bij de instantie van Discovery Benefits SSO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Discovery Benefits SSO uitproberen met Azure AD](https://aad.portal.azure.com/)