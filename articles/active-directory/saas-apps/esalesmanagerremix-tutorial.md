---
title: 'Zelfstudie: Azure Active Directory-integratie met E Sales Manager Remix | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en E Sales Manager Remix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c06595b683092abf52300481068daab26394c4cb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995638"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Azure Active Directory integreren met E Sales Manager Remix

In deze zelfstudie leert u hoe u Azure Active Directory (Azure AD) kunt integreren met E Sales Manager Remix.

Door Azure AD te integreren met E Sales Manager Remix, profiteert u van de volgende voordelen:

- U kunt in Azure AD beheren wie toegang heeft tot E Sales Manager Remix.
- U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld (eenmalige aanmelding, of SSO) bij E Sales Manager Remix.
- U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende zaken nodig om de integratie van Azure AD met E Sales Manager Remix te configureren:

- Een Azure AD-abonnement
- Een E Sales Manager Remix-abonnement met eenmalige aanmelding ingeschakeld

> [!NOTE]
> Wanneer u de stappen in deze zelfstudie wilt testen, raden wij u aan *geen* productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie gaat u eenmalige aanmelding van Azure AD in een testomgeving testen. 

Het scenario dat in deze zelfstudie wordt beschreven, bestaat uit twee belangrijke bouwstenen:

* E Sales Manager Remix toevoegen vanuit de galerie
* Eenmalige aanmelding van Azure AD configureren en testen

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>E Sales Manager Remix toevoegen vanuit de galerie
Om de integratie van Azure AD met E Sales Manager Remix te configureren, moet u E Sales Manager Remix vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps. U doet dit als volgt:

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**. 

    ![De knop Azure Active Directory][1]

1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Het venster 'Bedrijfstoepassingen'][2]
    
1. Om een nieuwe toepassing toe te voegen, selecteert u **Nieuwe toepassing** bovenin het venster.

    ![De knop Nieuwe toepassing][3]

1. Typ **E Sales Manager Remix** in het zoekvak, selecteer **E Sales Manager Remix** in de lijst met resultaten en selecteer **Toevoegen**.

    ![E Sales Manager Remix in de lijst met resultaten](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met E Sales Manager Remix. Dit doet u op basis van een testgebruiker met de naam Britta Simon.

Voor gebruik van eenmalige aanmelding moet Azure AD de E Sales Manager Remix-gebruiker en de bijbehorende tegenhanger identificeren in Azure AD. In andere woorden, er moet een koppelingsrelatie tussen een Azure AD-gebruiker en dezelfde gebruiker in E Sales Manager Remix tot stand worden gebracht.

Om eenmalige aanmelding voor Azure AD te configureren en testen met E Sales Manager Remix, voltooit u de bouwstenen in de volgende vijf secties:

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

Schakel eenmalige aanmelding van Azure AD in de Azure Portal in. Configureer vervolgens eenmalige aanmelding in uw E Sales Manager Remix-toepassing door het volgende te doen:

1. Ga naar de Azure Portal. Ga vervolgens naar de integratiepagina van de toepassing **E Sales Manager Remix**. Selecteer hier de optie **Eenmalige aanmelding**.

    ![De link voor 'Eenmalige aanmelding'][4]

1. In het vak **Modus voor eenmalige aanmelding**, dat in het venster **Eenmalige aanmelding** te vinden is, selecteert u **Op SAML gebaseerde aanmelding**.
 
    ![Het venster 'Eenmalige aanmelding'](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Ga als volgt te werk onder **E Sales Manager Remix-domein en -URL's**:

    ![Informatie over eenmalige aanmelding voor E Sales Manager Remix-domein en -URL's](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Typ, in het tekstvak **Aanmeldings-URL** een URL met de volgende indeling: *https://\<Server-Based-URL>/\<sub-domain>/esales-pc*.

    b. Typ, in het tekstvak **Id** een URL met de volgende indeling: *https://\<Server-Based-URL>/\<sub-domain>/* .

    c. Noteer de **Id**-waarde voor later gebruik in deze zelfstudie.
    
    > [!NOTE] 
    > De bovenstaande waarden zijn niet echt. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. U kunt de waarden verkrijgen door contact op te nemen met [het klantondersteuningsteam van E Sales Manager Remix](mailto:esupport@softbrain.co.jp).

1. Onder **SAML-handtekeningcertificaat** selecteert u **Certificaat (Base64)** . Sla het certificaatbestand vervolgens op uw computer op.

    ![De link om het certificaat (Base64) te downloaden](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Zet een vinkje in het selectievakje **Alle andere gebruikerskenmerken weergeven en bewerken** en selecteer vervolgens het kenmerk **E-mailadres**.
    
    ![Het venster 'Gebruikerskenmerken'](./media/esalesmanagerremix-tutorial/configure1.png)

    Het venster **Kenmerk bewerken** wordt geopend.

1. Kopieer de **Naamruimte** en de waarden van **Naam**. Genereer de waarde in het patroon *\<Namespace>/\<Name>* . Sla deze waarde vervolgens op voor later gebruik in deze zelfstudie.

    ![Het venster 'Kenmerk bewerken'](./media/esalesmanagerremix-tutorial/configure2.png)

1. Selecteer, onder **Configuratie van E Sales Manager Remix**, de optie **E Sales Manager Remix configureren**.

    ![Schermopname van de sectie 'Configuratie van E Sales Manager Remix' met 'E Sales Manager Remix configureren' geselecteerd.](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Het venster **Aanmelden configureren** wordt geopend.

1. Kopieer de afmeldings-URL en de service-URL voor eenmalige aanmelding met SAML in de sectie **Snelzoekgids**.

1. Selecteer **Opslaan**.

    ![De knop 'Opslaan'](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Meld u aan bij uw E Sales Manager Remix-toepassing als een beheerder.

1. Selecteer rechtsboven **Naar het beheerdersmenu**.

    ![De opdracht 'Naar het beheerdersmenu'](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecteer in het linkerdeelvenster de optie **Systeeminstellingen** > **Samenwerking met extern systeem**.

    ![De koppelingen 'Systeeminstellingen' en 'Samenwerking met extern systeem'](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Selecteer **SAML** in het venster **Samenwerking met extern systeem**.

    ![Het venster 'Samenwerking met extern systeem'](./media/esalesmanagerremix-tutorial/configure6.png)

1. Ga als volgt te werk onder **SAML-verificatie-instellingen**:

    ![De sectie 'SAML-verificatie-instellingen'](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Zet een vinkje in het selectievakje **PC-versie**.
    
    b. Selecteer **E-mail** in de vervolgkeuzelijst **Samenwerkingsitem**.

    c. Plak in het tekstvak **Samenwerkingsitem** de claimwaarde die u eerder hebt gekopieerd in de Azure Portal (dus **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** ).

    d. Plak in het vak **Verlener (entiteits-id)** de id-waarde die u eerder hebt gekopieerd uit de sectie **E Sales Manager Remix-domein en -URL's** van de Azure Portal.

    e. Selecteer **Bestandsselectie** om uw certificaat, dat u uit de Azure Portal gedownload hebt, te uploaden.

    f. In het vak **Aanmeldings-URL van id-provider** plakt u de SAML-service-URL voor eenmalige aanmelding die u eerder in de Azure Portal hebt gekopieerd.

    g. In het vak **Afmeldings-URL van id-provider** plakt u de URL voor afmelding die u eerder in de Azure Portal hebt gekopieerd.

    h. Selecteer **Instelling voltooid**.

> [!TIP]
> U kunt nu een beknopte versie van deze instructies in de [Azure Portal](https://portal.azure.com) lezen terwijl u de app instelt. Nadat u deze app in de sectie **Active Directory** > **Bedrijfstoepassingen** hebt toegevoegd, selecteert u het tabblad **Eenmalige aanmelding**. Vervolgens opent u de ingesloten documentatie via de sectie **Configuratie**, die onderin het venster staat. Zie [Ingesloten documentatie van Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985) voor meer informatie over de functie voor ingesloten documentatie.
> 

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure Portal door het volgende te doen:

![Een Azure AD-testgebruiker maken][100]

1. In de Azure Portal, in het linkerdeelvenster, selecteert u **Azure Active Directory**.

    ![De Azure Active Directory-link](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Als u een lijst met huidige gebruikers wilt weergeven, selecteert u **Gebruikers en groepen** > **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Selecteer bovenin het venster **Alle gebruikers** de optie **Toevoegen**.

    ![De knop 'Toevoegen'](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Het venster **Gebruiker** wordt geopend.

1. Ga in het venster **Gebruiker** als volgt te werk:

    ![Het venster 'Gebruiker'](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Typ in het veld **Naam** **Britta Simon**.

    b. Typ in het tekstvak **Gebruikersnaam** het e-mailadres van de gebruiker Britta Simon.

    c. Zet een vinkje in het selectievakje **Wachtwoord weergeven** en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    d. Selecteer **Maken**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Een testgebruiker voor E Sales Manager Remix maken

1. Meld u aan bij uw E Sales Manager Remix-toepassing als een beheerder.

1. Selecteer rechtsboven **Naar het beheerdersmenu** in het menu.

    ![Configuratie voor E Sales Manager Remix](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecteer **Instellingen van uw bedrijf** > **Onderhoud van afdelingen en werknemers**. Selecteer vervolgens **Geregistreerde werknemers**.

    ![Het tabblad 'Geregistreerde werknemers'](./media/esalesmanagerremix-tutorial/user1.png)

1. Voer de volgende handelingen uit in de sectie **Nieuwe werknemer registreren**:
    
    ![De sectie 'Nieuwe werknemer registreren'](./media/esalesmanagerremix-tutorial/user2.png)

    a. Typ in het vak **Naam van de werknemer** de naam van de gebruiker (bijvoorbeeld **Britta**).

    b. Voltooi de verdere verplichte velden.
    
    c. Als u SAML inschakelt, kan de beheerder zich niet aanmelden vanaf de aanmeldingspagina. U kunt beheerdersmachtigingen voor de gebruiker toekennen door een vinkje in het selectievakje **Als beheerder aanmelden** te zetten.

    d. Selecteer **Registratie**.

1. Als u zich in de toekomst wilt aanmelden als beheerder, meldt u zich aan als de gebruiker met beheerdersmachtigingen en selecteert u, in de rechterbovenhoek, de optie **Naar het beheerdersmenu**.

    ![De opdracht 'Naar het beheerdersmenu'](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding via Azure. Dit doet u door haar toegang te geven tot E Sales Manager Remix. U kunt dit als volgt doen: 

![De gebruikersrol toewijzen][200] 

1. Open, in de Azure Portal, de weergave **Toepassingen**, ga naar de weergave **Directory** en selecteer vervolgens **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![De links 'Bedrijfstoepassingen' en 'Alle toepassingen'][201] 

1. Selecteer **E Sales Manager Remix** in de lijst **Toepassingen**.

    ![De link 'E Sales Manager Remix'](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. Selecteer, in het linkerdeelvenster, **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen][202]

1. Selecteer **Toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen][203]

1. Selecteer **Britta Simon** in de lijst **Gebruikers** in het deelvenster **Gebruikers en groepen**.

1. Selecteer de knop **Selecteren**.

1. Selecteer **Toewijzen** in het venster **Toewijzing toevoegen**.
    
### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel 'E Sales Manager Remix' selecteert in het toegangsvenster, moet u zich automatisch aanmelden bij uw E Sales Manager Remix-toepassing.

Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster. 

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png