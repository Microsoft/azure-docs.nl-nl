---
title: 'Zelfstudie: Integratie van Azure Active Directory met Printix | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Printix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: dfde9bbbeb7f6b349ecbdc4c2da605d39a0708da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357875"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Zelfstudie: Azure Active Directory-integratie met Printix

In deze zelfstudie leert u hoe u Printix kunt integreren met Azure Active Directory (Azure AD).

De integratie van Printix met Azure AD biedt de volgende voordelen:

- U kunt in Azure AD regelen wie toegang tot Printix heeft
- U kunt uw gebruikers zich automatisch laten aanmelden bij Printix (eenmalige aanmelding) met hun Azure AD-account
- U kunt uw accounts vanaf één locatie beheren: de Azure Portal

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Printix hebt u de volgende items nodig:

- Een Azure AD-abonnement
- Een Printix-abonnement waarvoor eenmalige aanmelding is ingeschakeld

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u nog geen proefversie van Azure AD hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een proefversie van één maand aanvragen.

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie gaat u eenmalige aanmelding van Azure AD in een testomgeving testen. Het scenario dat in deze zelfstudie wordt beschreven, bestaat uit twee belangrijke bouwstenen:

1. Printix toevoegen vanuit de galerie
1. Eenmalige aanmelding van Azure AD configureren en testen

## <a name="adding-printix-from-the-gallery"></a>Printix toevoegen vanuit de galerie
Voor het configureren van de integratie van Printix in Azure AD moet u Printix vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Printix uit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram. 

    ![Active Directory][1]

1. Ga naar **Bedrijfstoepassingen**. Ga vervolgens naar **Alle toepassingen**.

    ![Schermopname toont de Azure Portal-bedrijfstoepassingen onder 'Beheren', waarbij 'Alle toepassingen' is geselecteerd.][2]
    
1. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![Schermopname waarin Nieuwe toepassing is geselecteerd.][3]

1. Voer in het zoekvak **Printix** in.

    ![De schermopname toont het zoeken naar Printix in het dialoogvenster Toevoegen vanuit de galerie.](./media/printix-tutorial/tutorial_printix_search.png)

1. Selecteer in het resultatenvenster **Printix** en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Schermopname met de optie Printix geselecteerd.](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Eenmalige aanmelding van Azure AD configureren en testen
In deze sectie gaat u eenmalige aanmelding bij Azure AD met Printix configureren en testen op basis van een testgebruiker met de naam ‘Britta Simon’.

Voor gebruik van eenmalige aanmelding moet Azure AD weten wie de tegenhangende gebruiker in Printix is voor een gebruiker in Azure AD. Met andere woorden, er moet een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Printix tot stand worden gebracht.

Wijs in Printix de waarde van de **gebruikersnaam** in Azure AD toe als de waarde van de **Gebruikersnaam** om de relatie vast te leggen.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Printix, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configuring-azure-ad-single-sign-on)** - zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Een Azure AD-testgebruiker maken](#creating-an-azure-ad-test-user)** - voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
1. **[Een Printix-testgebruiker maken](#creating-a-printix-test-user)** - als u een tegenhanger van Britta Simon in Printix wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[De Azure AD-testgebruiker toewijzen](#assigning-the-azure-ad-test-user)** - zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
1. **[Eenmalige aanmelding testen](#testing-single-sign-on)** - om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In dit gedeelte schakelt u eenmalige aanmelding van Azure AD in de Azure Portal in en configureert u eenmalige aanmelding in uw Printix-toepassing.

**Voer de volgende stappen uit om Azure AD-eenmalige aanmelding bij Printix te configureren:**

1. In de Azure Portal selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Printix**.

    ![Schermopname met Eenmalige aanmelding geselecteerd onder Beheren in de Azure Portal.][4]

1. Selecteer in het dialoogvenster **Eenmalige aanmelding** **Modus** als **op SAML gebaseerde aanmelding** om eenmalige aanmelding in te schakelen.
 
    ![Schermopname met op SAML gebaseerde aanmeldingsmodus geselecteerd.](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Voer de volgende stappen uit in de sectie **Printix-domein en -URL's**:

    ![Schermopname met de sectie Printix-domein en -URL’s geselecteerd, waar u een U R L voor aanmelden kunt opgeven.](./media/printix-tutorial/tutorial_printix_url.png)

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://<subdomain>.printix.net`

    > [!NOTE] 
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Printix](mailto:support@printix.net) om de waarde op te halen. 
 
1. Klik in de sectie **SAML-handtekeningcertificaat** op **XML-bestand met metagegevens**, en sla het bestand met metagegevens op de computer op.

    ![Schermopname van het deelvenster SAML-handtekeningcertificaat waar u een certificaat kunt downloaden.](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Klik op de knop **Save**.

    ![Schermopname toont de knop 'Opslaan'.](./media/printix-tutorial/tutorial_general_400.png)

1. Meld u als beheerder aan bij uw Printix-tenant.

1. Klik in het menu aan de bovenkant op het pictogram in de rechterbovenhoek en selecteer ‘**Verificatie**’.
   
    ![Schermopname met Verificatie geselecteerd in het menu.](./media/printix-tutorial/tutorial_printix_06.png)

1. Selecteer op het tabblad **Instellen** de optie **Azure/Office 365-verificatie inschakelen**
   
    ![Schermopname met de Printix.net-pagina waar u Azure/Office 365-verificatie inschakelen kunt selecteren.](./media/printix-tutorial/tutorial_printix_07.png)

1. Voer op het tabblad **Azure** de URL voor de federatieve metagegevens in bij het tekstvak van ‘**Document met federatieve metagegevens**’. 

    Voeg het XML-bestand met metagegevens dat u hebt gedownload van Azure AD toe voor het [Ondersteuningsteam van Printix](mailto:support@printix.net). Zij uploaden het XML-bestand vervolgens en leveren een URL voor federatieve metagegevens.
   
    ![Schermopname met de Printix.net-pagina waar u een document met federatieve metagegevens kunt opgeven.](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Klik op de knop ‘**Test**’ en klik op de knop ‘**OK**’ als de test is geslaagd.
   
     De pagina Azure Active Directory wordt weergegeven nadat u op de knop **test** hebt geklikt. ‘De test is geslaagd’ houdt in dat na het invoeren van de referenties van uw Azure-testaccount een bericht wordt weergegeven met de tekst ‘Geteste instellingen zijn OK’. Klik vervolgens op de knop **OK**.
   
    ![Schermopname toont de resultaten van de test.](./media/printix-tutorial/tutorial_printix_09.png)

1. Klik op de pagina ‘**Verificatie**’ op de knop **Opslaan**.


> [!TIP]
> U kunt nu een beknopte versie van deze instructies in [Azure Portal](https://portal.azure.com) lezen terwijl u de app instelt!  Klik nadat u deze app onder **Active Directory > Bedrijfstoepassingen** hebt toegevoegd op het tabblad **Eenmalige aanmelding** en open de ingesloten documentatie via het gedeelte **Configuratie** onderaan. Hier leest u meer over de functie voor ingesloten documentatie: [Ingesloten documentatie in Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

![Een Azure AD-gebruiker maken][100]

**Voer de volgende stappen uit om een testgebruiker in Azure AD te maken:**

1. Klik in het linker navigatiemenu in de **Azure Portal** op het **Azure Active Directory**-pictogram.

    ![Schermopname toont een naam en gebruikersnaam die moeten worden gemaakt.](./media/printix-tutorial/create_aaduser_01.png) 

1. Als u de lijst met gebruikers wilt weergeven, gaat u naar **Gebruikers en groepen** en klikt u op **Alle gebruikers**.
    
    ![Schermopname met het Azure A D-pictogram in de Azure Portal.](./media/printix-tutorial/create_aaduser_02.png) 

1. Als u het dialoogvenster **Gebruiker** wilt openen, klikt u op **Toevoegen** aan de bovenkant van het dialoogvenster.
 
    ![Schermopname met Gebruikers en groepen geselecteerd in het menu Beheren, met Alle gebruikers geselecteerd.](./media/printix-tutorial/create_aaduser_03.png) 

1. Voer de volgende stappen uit in het dialoogvenster **Gebruiker**:
 
    ![Schermopname met het dialoogvenster Gebruiker, waar u de beschreven waarden kunt invoeren.](./media/printix-tutorial/create_aaduser_04.png) 

    a. Typ, in het veld **Naam**, **Britta Simon**.

    b. In het tekstvak **Gebruikersnaam** voert u het **e-mailadres** van Britta Simon in.

    c. Selecteer **Wachtwoord weergeven** en noteer de weergegeven waarde van het **Wachtwoord**.

    d. Klik op **Create**.
 
### <a name="creating-a-printix-test-user"></a>Een Printix-testgebruiker maken

Het doel van deze sectie is om een gebruiker met de naam Britta Simon te maken in Printix. Printix biedt ondersteuning voor Just-In-Time-inrichting; dit is standaard ingeschakeld.

Er is geen actie-item voor u in deze sectie. Tijdens een poging om Printix te openen, wordt er een nieuwe gebruiker gemaakt als deze nog niet bestaat. 

> [!NOTE]
> Als u handmatig een gebruiker moet maken, moet u contact opnemen met het [ondersteuningsteam van Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van Azure-eenmalige aanmelding door haar toegang te geven tot Printix.

![Schermopname toont een gebruiker met standaardtoegang.][200] 

**Voer de volgende stappen uit om Britta Simon toe te wijzen aan Printix:**

1. Open in de Azure Portal de toepassingenweergave, navigeer naar de mapweergave en ga naar **Bedrijfstoepassingen**, klik vervolgens op **Alle toepassingen**.

    ![Schermopname toont de Bedrijfstoepassingen onder Beheren, waarbij Alle toepassingen is geselecteerd.][201] 

1. Selecteer **Printix** in de lijst met toepassingen.

    ![Schermopname van de lijst met toepassingen waar u Printix kunt selecteren.](./media/printix-tutorial/tutorial_printix_app.png) 

1. Klik, in het menu aan de linkerkant, op **Gebruikers en groepen**.

    ![Schermopname met Gebruikers en groepen geselecteerd in het menu Beheren.][202] 

1. Klik op de knop **Add**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname toont de knop Toevoegen en de pagina Toewijzing toevoegen waar u Gebruikers en Groepen kunt selecteren.][203]

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met Gebruikers.

1. Klik op de knop **Selecteren** in het dialoogvenster **Gebruikers en groepen**.

1. Klik, in het dialoogvenster **Toewijzing toevoegen**, op de knop **Toewijzen**.
    
### <a name="testing-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Printix klikt, wordt u automatisch aangemeld bij uw Printix-toepassing.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

