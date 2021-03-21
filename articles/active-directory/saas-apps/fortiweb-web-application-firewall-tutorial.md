---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met FortiWeb Web Application Firewall | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en FortiWeb Web Application Firewall.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731933"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met FortiWeb Web Application Firewall

In deze zelfstudie leert u hoe u FortiWeb Web Application Firewall integreert met Azure Active Directory (Azure AD). Wanneer u FortiWeb Web Application Firewall integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot FortiWeb Web Application Firewall.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij FortiWeb Web Application Firewall.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op FortiWeb Web Application Firewall waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FortiWeb Web Application Firewall ondersteunt door **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>FortiWeb Web Application Firewall toevoegen vanuit de galerie

Als u de integratie van FortiWeb Web Application Firewall in Azure AD wilt configureren, moet u FortiWeb Web Application Firewall vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **FortiWeb Web Application Firewall** in het zoekvak.
1. Selecteer **FortiWeb Web Application Firewall** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Eenmalige aanmelding van Azure AD voor FortiWeb Web Application Firewall configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met FortiWeb Web Application Firewall met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in FortiWeb Web Application Firewall.

Als u eenmalige aanmelding van Azure AD met FortiWeb Web Application Firewall wilt testen, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor FortiWeb Web Application Firewall configureren](#configure-fortiweb-web-application-firewall-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor FortiWeb Web Application Firewall maken](#create-fortiweb-web-application-firewall-test-user)** : als u een tegenhanger van B. Simon in FortiWeb Web Application Firewall wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **FortiWeb Web Application Firewall** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

   a. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://www.<CUSTOMER_DOMAIN>.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.<CUSTOMER_DOMAIN>.com`

    d. Typ in het tekstvak **Afmeldings-URL** een URL met het volgende patroon: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` is een naam-id die later wordt gebruikt wanneer de configuratie wordt opgegeven voor FortiWeb.
    > Neem contact op met het [ondersteuningsteam van FortiWeb Web Application Firewall](mailto:support@fortinet.com) om de echte URL-waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)


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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot FortiWeb Web Application Firewall.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **FortiWeb Web Application Firewall** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Eenmalige aanmelding voor FortiWeb Web Application Firewall configureren

1.  Ga naar `https://<address>:8443`, waarin `<address>` de FQDN of het openbare IP-adres is dat is toegewezen aan de FortiWeb-VM.

2.  Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiWeb-VM.

1. Voer de volgende stappen uit op de volgende pagina.

    ![Pagina SAML-server](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  Klik in het linkermenu op **Gebruiker**.

    b.  Klik onder Gebruiker op **Externe server**.

    c.  Klik op **SAML-server**.

    d.  Klik op **Nieuwe maken**.

    e.  Geef in het veld **Naam** de waarde voor `<fwName>` op die is gebruikt in de sectie Azure AD configureren.

    f.  Plak in het tekstvak **Entiteits-id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    g. Klik naast **Metagegevens** op **Bestand kiezen** en selecteer het **XML-bestand met federatieve metagegevens** dat u in Azure Portal hebt gedownload.

    h.  Klik op **OK**.

### <a name="create-a-site-publishing-rule"></a>Een regel voor het publiceren van een site maken

1.  Ga naar `https://<address>:8443`, waarin `<address>` de FQDN of het openbare IP-adres is dat is toegewezen aan de FortiWeb-VM.

1.  Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiWeb-VM.
1. Voer de volgende stappen uit op de volgende pagina.

    ![Regel voor het publiceren van een site](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  Klik in het linkermenu op **Application Delivery**.
    
    b.  Klik onder **Application Delivery** op **Site Publish**.
    
    c.  Klik onder **Site Publish** op **Site Publish**.
    
    d.  Klik op **Site Publish Rule**.
    
    e.  Klik op **Nieuwe maken**.
    
    f.  Geef een naam op voor de regel voor het publiceren van de site.
    
    g.  Klik naast **Published Site Type** op **Regular Expression**.
    
    i.  Geef naast **Published Site** een tekenreeks op die overeenkomt met de host-header van de website die u publiceert.
    
    j.  Geef naast **Path** een / op.
    
    k.  Selecteer naast **Client Authentication Method** de optie **SAML Authentication**.
    
    l.  Selecteer in de vervolgkeuzelijst **SAML Server** de SAML-server die u eerder hebt gemaakt.
    
    m.  Klik op **OK**.


### <a name="create-a-site-publishing-policy"></a>Een beleidsregel voor het publiceren van een site maken

1.  Ga naar `https://<address>:8443`, waarin `<address>` de FQDN of het openbare IP-adres is dat is toegewezen aan de FortiWeb-VM.

2.  Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiWeb-VM.

1. Voer de volgende stappen uit op de volgende pagina.

    ![Een beleidsregel voor het publiceren van een site](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  Klik in het linkermenu op **Application Delivery**.

    b.  Klik onder **Application Delivery** op **Site Publish**.

    c.  Klik onder **Site Publish** op **Site Publish**.

    d.  Klik op **Site Publish Policy**.

    e.  Klik op **Nieuwe maken**.

    f.  Geef een naam op voor de beleidsregel voor het publiceren van de site.

    g.  Klik op **OK**.

    h.  Klik op **Nieuwe maken**.

    i.  Selecteer in de vervolgkeuzelijst **Rule** de regel voor het publiceren van een site die u eerder hebt gemaakt.

    j.  Klik op **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Een profiel voor webbeveiliging maken en toewijzen

1.  Ga naar `https://<address>:8443`, waarin `<address>` de FQDN of het openbare IP-adres is dat is toegewezen aan de FortiWeb-VM.

2.  Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiWeb-VM.
3.  Klik in het linkermenu op **Policy**.
4.  Klik onder **Policy** op **Web Protection Profile**.
5.  Klik op **Inline Standard Protection** en op **Clone**.
6.  Geef een naam op voor het nieuwe profiel voor webbeveiliging en klik op **OK**.
7.  Selecteer het nieuwe profiel voor webbeveiliging en klik op **Edit**.
8.  Selecteer naast **Site Publish** de beleidsregel voor het publiceren van een site die u eerder hebt gemaakt.
9.  Klik op **OK**.
 
    ![site publiceren](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. Klik in het linkermenu op **Policy**.
11. Klik onder **Policy** op **Server Policy**.
12. Selecteer het serverbeleid dat wordt gebruikt om de website te publiceren waarvoor u Azure Active Directory voor verificatie wilt gebruiken.
13. Klik op **Bewerken**.
14. Selecteer in de vervolgkeuzelijst **Web Protection Profile** het profiel voor webbeveiliging dat u zojuist hebt gemaakt.
15. Klik op **OK**.
16. Klik op de externe URL waaronder FortiWeb de website publiceert. U wordt doorgestuurd naar Azure Active Directory voor verificatie.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Testgebruiker voor FortiWeb Web Application Firewall maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in FortiWeb Web Application Firewall. Werk met het [ondersteuningsteam van FortiWeb Web Application Firewall](mailto:support@fortinet.com) om de gebruikers toe te voegen aan het FortiWeb Web Application Firewall-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van FortiWeb Web Application, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van FortiWeb Web Application Firewall en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel FortiWeb Web Application klikt, wordt u omgeleid naar de aanmeldings-URL van FortiWeb Web Application. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u FortiWeb Web Application Firewall hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).