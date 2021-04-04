---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Drift | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Drift.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 5e78ca99d3f339e6b4e9c8bc00fd9e2c68c50777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454561"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-drift"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Drift

In deze zelfstudie leert u hoe u Drift integreert met Azure AD (Active Directory). Wanneer u Drift integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Drift.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Drift.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Drift-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Drift ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Drift biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-drift-from-the-gallery"></a>Drift toevoegen vanuit de galerie

Voor het configureren van de integratie van Drift in Azure AD moet u Drift vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Drift**.
1. Selecteer **Drift** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-drift"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Drift

Configureer en test eenmalige aanmelding van Azure AD met Drift met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Drift.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Drift te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Drift configureren](#configure-drift-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Drift-testgebruiker maken](#create-drift-test-user)** : als u een equivalent van B.Simon in Drift wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Drift** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

    a. Klik op **Extra URL's instellen**.
 
    b. In het tekstvak **Relaystatus** typt u een URL: `https://app.drift.com` 

    c. Als u de toepassing wilt configureren in door **SP** geïnitieerde modus, voert u de volgende stap uit:

    d. Typ een URL in het tekstvak **Aanmeldings-URL**: `https://start.drift.com`

6. In de Drift-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

7. Bovendien worden in de Drift-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten. 

    | Naam | Bronkenmerk|
    | ---------------| --------------- |    
    | Naam | user.displayname |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Drift instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Drift.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Drift** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-drift-sso"></a>Eenmalige aanmelding van Drift configureren

1. Als u de configuratie in Drift wilt automatiseren, moet u de **browserextensie voor veilig aanmelden bij mijn apps** installeren door te klikken op **De extensie installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Drift instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Drift-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Drift. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 en 4 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Drift handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Drift-bedrijfssite. Voer hierna de volgende stappen uit:

4. Klik links in de menubalk op het pictogram **Instellingen** > **App-instellingen** > **Verificatie** en voer de volgende stappen uit:

    ![De koppeling Beheerder](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Upload de **Federatieve metagegevens-XML** die u hebt gedownload vanuit Azure-portal naar het tekstvak **Metagegevensbestand uploaden vanuit uw identiteitsprovider**.

    b. Na het uploaden van het bestand met metagegevens worden de resterende waarden automatisch op de pagina ingevuld.

    c. Klik op **Enable SAML**.

### <a name="create-drift-test-user"></a>Testgebruiker voor Drift maken

In dit gedeelte wordt er een gebruiker met de naam Britta Simon gemaakt in Drift. Drift biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Drift bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neem dan contact op met het [ondersteuningsteam van Drift](mailto:integrations@drift.com).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Drift in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Drift waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Drift uitproberen met Azure AD](https://aad.portal.azure.com/)