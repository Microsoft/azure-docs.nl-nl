---
title: 'Zelfstudie: Azure Active Directory-integratie met Amazon Business | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Amazon Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 42da33fe874b6dea43bc16009dd27b70e6c3f535
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589891"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Zelfstudie: Amazon Business integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Amazon Business kunt integreren met Azure Active Directory (Azure AD). Wanneer u [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) integreert met Azure Active Directory, kunt u:

* In Azure AD beheren wie toegang heeft tot Amazon Business.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Amazon Business.
* Uw accounts op één centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement dat geschikt is voor eenmalige aanmelding (SSO) voor Amazon Business. Ga naar de pagina [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) om een Amazon Business-account te maken.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u Azure AD-eenmalige aanmelding voor een bestaand Amazon Business-account configureren en testen.

* Amazon Business ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Amazon Business ondersteunt het **Just In Time** inrichten van gebruikers

## <a name="adding-amazon-business-from-the-gallery"></a>Amazon Business toevoegen vanuit de galerie

Voor het configureren van de integratie van Amazon Business in Azure AD moet u Amazon Business uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Amazon Business** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Amazon Business** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met Amazon Business met behulp van een testgebruiker met de naam **B.Simon**.

Voltooi de volgende compilatiestappen om Azure AD-eenmalige aanmelding met Amazon Business te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Amazon Business configureren](#configure-amazon-business-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een testgebruiker maken in Amazon Business](#create-amazon-business-test-user)** : om in Amazon Business een tegenhanger van B. Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Amazon Business** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u in de door **IDP** geïnitieerde modus wilt configureren:

    1. In het tekstvak **Id (Entiteits-id)** typt u een URL met één van de volgende patronen:
    
       | URL | Regio |
       |-|-|
       | `https://www.amazon.com`| Noord-Amerika |
       | `https://www.amazon.co.jp`| Azië - oost |
       | `https://www.amazon.de`| Europa |

    1. In het tekstvak **Antwoord-URL** typt u een URL met één van de volgende patronen:
    
       | URL | Regio |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Noord-Amerika |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Azië - oost |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Europa |

       > [!NOTE]
       > De waarde van de antwoord-URL is niet de echte waarde. Werk deze waarde bij met de werkelijke antwoord-URL. U krijgt de waarde `<idpid>` uit het configuratiegedeelte voor eenmalige aanmelding voor Amazon Business. Deze wordt verderop in de zelfstudie uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Als u de toepassing in de door **SP** geïnitieerde modus wilt configureren, moet u de volledige URL die is opgegeven in de Amazon Business-configuratie toevoegen aan de **aanmeldings-URL** in het gedeelte **Extra URL's instellen**.

1. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Bewerk de kenmerken door op het pictogram **Bewerken** te klikken in het gedeelte **Gebruikerskenmerken en -claims**.

    ![Schermopname van User Attributes & Claims (Gebruikerskenmerken en claims) met standaardwaarden zoals Givenname user.givenname en Emailaddress user.mail.](media/amazon-business-tutorial/map-attribute3.png)

1. Bewerk de kenmerken en kopieer de waarde **Naamruimte** van deze kenmerken naar het kladblok.

    ![Schermopname van User Attributes & Claims (Gebruikerskenmerken en claims) met kolommen voor claimnaam en waarde.](media/amazon-business-tutorial/map-attribute4.png)

1. Bovendien verwacht de Amazon Business-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Voer in het gedeelte **Gebruikerskenmerken en -claims** in het dialoogvenster **Groepsclaims** de volgende stappen uit:

    1. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

        ![Schermopname van User Attributes & Claims (Gebruikerskenmerken en claims) met pictogram voor Groups returned in claim (In claim geretourneerde groepen) geselecteerd.](./media/amazon-business-tutorial/config04.png)

        ![Schermopname van Group Claims (Groepclaims) met waarden zoals beschreven in deze procedure.](./media/amazon-business-tutorial/config05.png)

    1. Selecteer **Alle groepen** in de lijst met keuzerondjes.

    1. Selecteer **Groeps-id** als **Bronkenmerk**.

    1. Schakel het selectievakje **De naam van de groepsclaim aanpassen** in en voer de groepsnaam in overeenstemming met de vereisten van uw organisatie in.

    1. Klik op **Opslaan**.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Amazon Business instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Eenmalige aanmelding voor Amazon Business configureren

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Amazon Business.

1. Klik op het **Gebruikersprofiel** en selecteer **Bedrijfsinstellingen**.

    ![Gebruikersprofiel](media/amazon-business-tutorial/user-profile.png)

1. Selecteer in de wizard **Systeemintegraties** **Eenmalige aanmelding (SSO)** .

    ![Eenmalige aanmelding (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. Selecteer in de wizard **Eenmalige aanmelding instellen** de provider volgens de vereisten van uw organisatie en klik op **Volgende**.

    ![Schermopname van Set up S S O, (S S O instellen) met Microsoft Azure A D en Next (Volgende) geselecteerd.](media/amazon-business-tutorial/default-group1.png)
    
    > [!NOTE]
    > Hoewel Microsoft ADFS een vermelde optie is, werkt het niet met Azure AD-eenmalige aanmelding.

1. Selecteer in de wizard **Standaardinstellingen voor het nieuwe gebruikersaccount** de **Standaardgroep** en selecteer vervolgens **Standaardaankooprol** op basis van de gebruikersrol in uw organisatie en klik op **Volgende**.

    ![Schermopname van de standaardinstellingen voor een nieuw gebruikersaccount, met Microsoft S S O, Requisitioner (Aanvrager) en Next (Volgende) geselecteerd.](media/amazon-business-tutorial/dafault-group2.png)

1. Klik in de wizard **Uw metagegevensbestand uploaden** op **Bladeren** om het **XML-bestand met metagegevens** te uploaden dat u hebt gedownload uit Azure Portal. Klik dan op **Uploaden**.

    ![Schermopname van Upload your metadata file (Metagegevensbestand uploaden) waarmee u naar een x m l-bestand kunt bladeren en dit uploaden.](media/amazon-business-tutorial/connection-data1.png)

1. Wanneer u het gedownloade metagegevensbestand hebt geüpload, worden de velden in de sectie **Verbindingsgegevens** automatisch ingevuld. Klik daarna op **Volgende**.

    ![Schermopname van Connection data (Verbindingsgegevens), waar u een Azure A D i d, aanmeldings-U R L en SAML-ondertekeningscertificaat kunt opgeven.](media/amazon-business-tutorial/connection-data2.png)

1. Klik in de wizard **Uw kenmerkinstructie uploaden** op **Overslaan**.

    ![Schermopname van Upload your Attribute statement, waarmee u naar een kenmerkinstructie kunt bladeren maar in dit geval Skip (overslaan) selecteert.](media/amazon-business-tutorial/map-attribute1.png)

1. Voeg in de wizard **Kenmerktoewijzing** de velden voor vereisten toe door te klikken op de optie **+ Een veld toevoegen**. Voeg de kenmerkwaarden toe, met inbegrip van de naamruimte die u hebt gekopieerd uit de sectie **Gebruikerskenmerken en -claims** van Azure Portal naar het veld **SAML AttributeName** en klik op **Volgende**.

    ![Schermopname van Attribute mapping (Kenmerktoewijzing), waar u de SAML-kenmerknamen voor uw Amazon-gegevens kunt wijzigen.](media/amazon-business-tutorial/map-attribute2.png)

1. Klik in de wizard **Amazon-verbindingsgegevens** op **Volgende**.

    ![Schermopname van Amazon-verbindingsgegevens, waar u op Next (Volgende) kunt klikken om door te gaan.](media/amazon-business-tutorial/amazon-connect.png)

1. Controleer de **status** van de stappen die zijn geconfigureerd en klik op **Beginnen met testen**.

    ![Schermopname van S S O Connection Details (S S O-verbindingsdetails) met de optie Start testing (Testen starten).](media/amazon-business-tutorial/sso-connection1.png)

1. Klik in de wizard **SSO-verbinding testen** op **Testen**.

    ![Schermopname van Test S S O Connection (S S O-verbinding testen) met de knop Test.](media/amazon-business-tutorial/sso-connection2.png)

1. Kopieer, voordat u op **Activeren** klikt, in de wizard **Door IDP geïnitieerde URL** de waarde die is toegewezen aan **idpid** en plak deze in de parameter **idpid** in de **antwoord-URL** in het gedeelte **Standaard SAML-configuratie** in Azure Portal.

    ![Schermopname van I D P initiated U R L (Door IPD geïnitieerde U R L) waar u een U R L kunt ophalen om te testen en vervolgens Activate (Activeren) selecteren.](media/amazon-business-tutorial/sso-connection3.png)

1. Schakel in de wizard **bent u klaar om over te schakelen naar de actieve SSO?** het selectievakje **Ik heb SSO volledig getest en ben klaar om live te gaan** in en klik op **Overschakelen naar actief**.

    ![Schermopname van het bevestigingsvenster Are you ready to switch to active S S O (Bent u klaar om over te schakelen naar actieve SSO) waar u Switch to active (Overschakelen naar actief) kunt selecteren.](media/amazon-business-tutorial/sso-connection4.png)

1. Ten slotte wordt in de sectie **SSO-verbindingsgegevens** de **status** weergegeven als **Actief**.

    ![Schermopname van S S O Connection Details (S S O-verbindingsdetails) met de status Active (Actief).](media/amazon-business-tutorial/sso-connection5.png)
    
    > [!NOTE]
    > Als u de toepassing wilt configureren in de door **SP** geïnitieerde modus, voert u de volgende stap uit en plakt u de aanmeldings-URL van de bovenstaande schermopname in het tekstvak **Aanmeldings-URL** van de sectie **Extra URL's instellen** in Azure Portal. Gebruik de volgende indeling:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`
    
### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

> [!NOTE]
> Beheerders moeten de testgebruikers zo nodig in hun tenant maken. De volgende stappen laten zien hoe u een testgebruiker maakt.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Een Azure AD-beveiligingsgroep maken in Azure Portal

1. Klik op **Azure Active Directory > Alle groepen**.

    ![Schermopname van het Azure-portalmenu met Azure Active Directory geselecteerd en Alle groepen geselecteerd in het deelvenster Groepen.](./media/amazon-business-tutorial/all-groups-tab.png)

1. Klik op **Nieuwe groep**:

    ![Schermopname van de knop Nieuwe groep.](./media/amazon-business-tutorial/new-group-tab.png)

1. Vul **Groepstype**, **Groepsnaam**, **Groepsbeschrijving**, **Lidmaatschapstype** in. Klik op de pijl om leden te selecteren, zoek vervolgens naar of klik op het lid dat u aan de groep wilt toevoegen. Klik op **Selecteren** om de geselecteerde leden toe te voegen en klik vervolgens op **Maken**.

    ![Schermopname van het deelvenster Groep met opties, waaronder het selecteren van leden en het uitnodigen van externe gebruikers.](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Amazon Business.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Amazon Business** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de knop Gebruiker toevoegen.](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

    >[!NOTE]
    > Als u de gebruikers niet toewijst in Azure AD, wordt de volgende fout weergeven.

    ![Schermopname van een foutbericht met de mededeling dat de gebruiker niet kan worden aangemeld.](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>De Azure AD-beveiligingsgroep toewijzen in Azure Portal

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Amazon Business**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Amazon Business** in de lijst met toepassingen.

    ![De koppeling Amazon Business in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de **Gebruiker toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Zoek naar de beveiligingsgroep die u wilt gebruiken en klik vervolgens op de groep die u wilt toevoegen aan de sectie Leden selecteren. Klik op **Selecteren** en vervolgens op **Toewijzen**.

    ![Beveiligingsgroep doorzoeken](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Controleer de meldingen in de menubalk om te zien of de melding is ontvangen dat de groep is toegewezen aan de bedrijfstoepassing in Azure Portal.

### <a name="create-amazon-business-test-user"></a>Een testgebruiker voor Amazon Business maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Amazon Business. Amazon Business ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Amazon Business bestaat, wordt er een nieuwe gemaakt na verificatie.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Amazon Business in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Amazon Business waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)