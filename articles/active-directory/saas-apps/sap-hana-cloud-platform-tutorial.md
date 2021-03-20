---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Cloud Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97964045"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform

In deze zelfstudie leert u hoe u SAP Cloud Platform met Azure Active Directory (Azure AD) kunt integreren. Wanneer u SAP Cloud Platform met Azure AD integreert, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot SAP Cloud Platform.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SAP Cloud Platform.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van de Azure AD-integratie met SAP Cloud Platform hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op SAP Cloud Platform waarvoor eenmalige aanmelding is ingeschakeld

Na het voltooien van deze zelfstudie kunnen de Azure AD-gebruikers die u hebt toegewezen aan SAP Cloud Platform zich aanmelden bij de toepassing met behulp van [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster).

>[!IMPORTANT]
>U moet uw eigen toepassing implementeren of zich abonneren op een toepassing van uw SAP Cloud Platform-account voor het testen van de eenmalige aanmelding. In deze zelfstudie wordt een toepassing geïmplementeerd in het account.
> 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Cloud Platform ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>SAP Cloud Platform toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP Cloud Platform met Azure AD moet u SAP Cloud Platform uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **SAP Cloud Platform** in het zoekvak.
1. Selecteer **SAP Cloud Platform** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SAP Cloud Platform

Configureer en test eenmalige aanmelding van Azure AD met SAP Cloud Platform met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Cloud Platform.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor SAP Cloud Platform:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding voor SAP Cloud Platform configureren](#configure-sap-cloud-platform-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor SAP Cloud Platform maken](#create-sap-cloud-platform-test-user)** : als u een equivalent van Britta Simon in SAP Cloud Platform wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **SAP Cloud Platform** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    ![Informatie over eenmalige aanmelding bij het SAP Cloud Platform-domein en URL's](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **Aanmeldings-URL** de URL die wordt gebruikt door uw gebruikers om zich aan te melden bij uw **SAP Cloud Platform**-toepassing. Dit is de accountspecifieke URL van een beveiligde resource in uw SAP Cloud Platform-toepassing. De URL is gebaseerd op de volgende notatie: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Dit is de URL in uw SAP Cloud Platform-toepassing die vereist is voor verificatie van de gebruiker.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. In het tekstvak **Id** voert u in uw SAP Cloud Platform een URL in met een van de volgende notaties: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie:

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [SAP Cloud Platform-clientondersteuningsteam](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) om de aanmeldings-URL en id te verkrijgen. De antwoord-URL kunt u ophalen in de sectie vertrouwensbeheer die verderop in de zelfstudie wordt uitgelegd.
    > 
4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

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

In deze sectie zorgt u ervoor dat B.Simon gebruik kan maken van eenmalige aanmelding met Azure door haar toegang te verlenen tot SAP Cloud Platform.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SAP Cloud Platform** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-cloud-platform-sso"></a>Eenmalige aanmelding voor SAP Cloud Platform configureren

1. Meld u in een ander browservenster aan bij SAP Cloud Platform Cockpit op `https://account.<landscape host>.ondemand.com/cockpit`(bijvoorbeeld: https://account.hanatrial.ondemand.com/cockpit).

2. Klik op het tabblad **Vertrouwen**.
   
    ![Vertrouwen](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Vertrouwen")

3. Voer in de sectie Vertrouwensbeheer, onder **Lokale serviceprovider**, de volgende stappen uit:

    ![Schermopname van de sectie Trust Management, waarin het tabblad Local Service Provider is geselecteerd en alle tekstvakken zijn gemarkeerd.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Vertrouwensbeheer")
   
    a. Klik op **Bewerken**.

    b. Selecteer als **configuratietype** de optie **Aangepast**.

    c. Laat bij **Lokale providernaam** de standaardwaarde staan. Kopieer de waarde en plak deze in het **id**-veld in de Azure AD-configuratie voor SAP Cloud Platform.

    d. Klik voor het genereren van een sleutelpaar voor een **ondertekeningssleutel** en **ondertekeningscertificaat** op **Sleutelpaar genereren**.

    e. Selecteer bij **Principal-doorgifte** de optie **Uitgeschakeld**.

    f. Selecteer bij **Geforceerde verificatie** de optie **Uitgeschakeld**.

    g. Klik op **Opslaan**.

4. Nadat de **Lokale serviceprovider**-instellingen zijn opgeslagen, kunt u de antwoord-URL op de volgende manier verkrijgen:
   
    ![Metagegevens ophalen](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Metagegevens ophalen")

    a. Download het metagegevensbestand van SAP Cloud Platform door te klikken op **Metagegevens ophalen**.

    b. Open het gedownloade SAP Cloud Platform metagegevens-XML-bestand en zoek vervolgens de tag **ns3:AssertionConsumerService**.
 
    c. Kopieer de waarde van het kenmerk **Locatie** en plak deze in het veld **Antwoord-URL** in de Azure AD-configuratie voor SAP Cloud Platform.

5. Klik op het tabblad **Vertrouwde id-provider** en klik vervolgens op **Vertrouwde id-provider toevoegen**.
   
    ![Schermopname van de pagina Trust Management, waarop het tabblad Trusted Identity Provider is geselecteerd.](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Vertrouwensbeheer")
   
    >[!NOTE]
    >Voor het beheren van de lijst met vertrouwde id-providers moet u het type Aangepaste configuratie hebben gekozen in de sectie Lokale serviceprovider. Voor het standaard configuratietype hebt u een niet-bewerkbare en impliciete vertrouwensrelatie met de SAP-id-service. Voor Geen hebt u geen vertrouwensrelatie-instellingen.
    > 
    > 

6. Klik op het tabblad **Algemeen** en vervolgens op **Bladeren** om het gedownloade metagegevensbestand te uploaden.
    
    ![Vertrouwensbeheer](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Vertrouwensbeheer")
    
    >[!NOTE]
    >Na het uploaden van het metagegevensbestand worden de waarden voor **Eenmalige aanmeldings-URL**, **Eenmalige afmeldings-URL** en **Ondertekeningscertificaat** automatisch ingevuld.
    > 
     
7. Klik op het tabblad **Kenmerken**.

8. Voer op het tabblad **Kenmerken** de volgende stap uit:
    
    ![Kenmerken](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Kenmerken") 

    a. Klik op **Op bevestiging gebaseerd kenmerk toevoegen** en voeg de volgende kenmerken toe op basis van een bevestiging:
       
    | Bevestigingskenmerk | Principal-kenmerk |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >De configuratie van de kenmerken is afhankelijk van hoe de toepassingen op SCP worden ontwikkeld. Dat wil zeggen, welke kenmerken in het SAML-antwoord worden verwacht en onder welke naam (Principal-kenmerk) toegang wordt verleend tot dit kenmerk in de code.
     > 
    
    b. Het **Standaardkenmerk** in de schermafbeelding dient slechts ter illustratie. Het is niet vereist om het scenario te laten werken.  
 
    c. De weergegeven namen en waarden voor **Principal-kenmerk** in de schermafbeelding zijn afhankelijk van hoe de toepassing is ontwikkeld. Het is mogelijk dat uw toepassing is vereist voor andere toewijzingen.

### <a name="assertion-based-groups"></a>Op bevestiging gebaseerde groepen

Als optionele stap kunt u groepen op basis van een bevestiging configureren voor uw identiteitsprovider Azure Active Directory.

Met behulp van groepen op SAP Cloud Platform kunt u een of meer gebruikers dynamisch toewijzen aan een of meer rollen in uw SAP Cloud Platform-toepassingen, die worden bepaald door de waarden van kenmerken in de SAML 2.0-verklaring. 

Als de bevestiging bijvoorbeeld het kenmerk *contract=tijdelijk* bevat, kunt u alle betrokken gebruikers toevoegen aan de groep *TIJDELIJK*. De groep *TIJDELIJK* kan een of meer rollen bevatten uit een of meer toepassingen die zijn geïmplementeerd in uw SAP Cloud Platform-account.
 
Gebruik op bevestiging gebaseerde groepen wanneer u veel gebruikers gelijktijdig wilt toewijzen aan een of meer rollen van toepassingen in uw SAP Cloud Platform-account. Als u slechts één of een klein aantal gebruikers aan specifieke rollen wilt toewijzen, dan kunt u deze het beste rechtstreeks in het tabblad **Autorisaties** van de SAP Cloud Platform-cockpit toewijzen.

### <a name="create-sap-cloud-platform-test-user"></a>Testgebruiker voor SAP Cloud Platform maken

Als u wilt dat Azure AD-gebruikers zich aanmelden bij SAP Cloud Platform moet u de rollen in SAP Cloud Platform aan hen toewijzen.

**Als u een rol wilt toewijzen aan een gebruiker, moet u de volgende stappen uitvoeren:**

1. Meld u aan bij uw **SAP Cloud Platform**-cockpit.

2. Voer het volgende uit:
   
    ![Autorisaties](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorisaties")
   
    a. Klik op **Autorisatie**.

    b. Klik op het tabblad **Gebruikers**.

    c. Typ in het tekstvak **Gebruiker** het e-mailadres van de gebruiker.

    d. Klik op **Toewijzen** om de gebruiker toe te wijzen aan een rol.

    e. Klik op **Opslaan**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van SAP Cloud Platform, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van SAP Cloud Platform en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel SAP Cloud Platform klikt in Mijn apps, wordt u automatisch aangemeld bij de instantie van SAP Cloud Platform waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u SAP Cloud Platform hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).