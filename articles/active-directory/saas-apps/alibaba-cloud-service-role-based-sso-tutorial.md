---
title: 'Zelfstudie: Eenmalige aanmelding via Azure Active Directory integreren met Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Alibaba Cloud Service (eenmalige aanmelding op basis van rollen).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: c9db5ab471f743ffbf3ae4e140ad4cb32d0a3d6c
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670189"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Zelfstudie: Eenmalige aanmelding via Azure Active Directory integreren met Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)

In deze zelfstudie leert u hoe u Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) kunt integreren met Azure Active Directory (Azure AD). Wanneer u Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Alibaba Cloud Service (eenmalige aanmelding op basis van rollen).
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Alibaba Cloud Service (eenmalige aanmelding op basis van rollen).
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) toevoegen via de galerie

Om de integratie van Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) met Azure AD te configureren, moet u Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)** in het zoekvak.
1. Selecteer **Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)** vanuit het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.
5. Klik op de pagina **Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)** op **Eigenschappen** in het navigatievenster aan de linkerkant en kopieer de **Object-id** en sla deze op uw computer op voor later gebruik.

    ![Configuratie van eigenschappen](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>Eenmalige aanmelding via Azure AD configureren en testen voor Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)

Configureer en test eenmalige aanmelding via Azure AD bij Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Alibaba Cloud Service (eenmalige aanmelding op basis van rollen).

Voer de volgende stappen uit om eenmalige aanmelding via Azure AD bij Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding op basis van rollen in Alibaba Cloud Service configureren](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** : om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Eenmalige aanmelding voor Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)](#configure-alibaba-cloud-service-role-based-sso-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) maken](#create-alibaba-cloud-service-role-based-sso-test-user)** : een tegenhanger voor Britta Simon maken in Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina van de toepassing **Alibaba Cloud Service (op rollen gebaseerde eenmalige aanmelding)** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.


    >[!NOTE]
    >1. Download de metagegevens van de serviceprovider vanaf [deze](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) koppeling voor Alibaba Cloud International Site.
    > 1. Download de metagegevens van de serviceprovider vanaf [deze](https://signin.aliyun.com/saml-role/sp-metadata.xml) koppeling voor Alibaba Cloud Service (CN) Site.

    c. Zodra het bestand met metagegevens is geüpload, worden de waarde voor **Id** en **Antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie voor Alibaba Cloud Service (eenmalige aanmelding op basis van rollen):

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) vereist dat rollen worden geconfigureerd in Azure AD. De rolclaim is vooraf geconfigureerd, zodat u deze niet hoeft te configureren, maar u moet deze wel in Azure AD maken. Lees daarvoor dit [artikel](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in het gedeelte **Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) instellen** de juiste URL('s) op basis van wat u nodig hebt.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Alibaba Cloud Service (eenmalige aanmelding op basis van rollen).

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)** .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer op het tabblad **Gebruikers en groepen** de optie u2 in de lijst met gebruikers en klik op **Selecteren**. Klik vervolgens op **Toewijzen**.

    ![Wijs testgebruiker1 voor Azure AD toe](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Bekijk de toegewezen rol en test Alibaba Cloud Service (eenmalige aanmelding op basis van rollen).

    ![Wijs testgebruiker2 voor Azure AD toe](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Nadat u de gebruiker (u2) hebt toegewezen, wordt de gemaakte rol automatisch aan de gebruiker gekoppeld. Als u meerdere rollen hebt gemaakt, moet u de juiste rol aan de gebruiker koppelen, indien nodig. Als u eenmalige aanmelding op basis van rollen wilt implementeren vanuit Azure AD naar meerdere Alibaba Cloud-accounts, herhaalt u de voorgaande stappen.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Eenmalige aanmelding op basis van rollen configureren in Alibaba Cloud Service

1. Meld u aan bij de [RAM-console](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) van Alibaba Cloud met behulp van Account1.

2. Selecteer **Eenmalige aanmelding** in het navigatiedeelvenster aan de linkerkant.

3. Klik op het tabblad **Eenmalige aanmelding op basis van rollen** op **IdP maken**.

4. Voer `AAD` in het veld IdP-naam op de weergegeven pagina in en voer een beschrijving in het veld **Notitie** in. Klik op **Uploaden** om het bestand met federatieve metagegevens te uploaden dat u eerder hebt gedownload en klik op **OK**.

5. Nadat de IdP is gemaakt, klikt u op **RAM-rol maken**.

6. In het veld **RAM-rolnaam** voert u `AADrole` in. Selecteer `AAD` in de vervolgkeuzelijst **IdP selecteren** en klik op OK.

    >[!NOTE]
    >U kunt de rol zo nodig machtigen. Nadat u de IdP en de bijbehorende rol hebt gemaakt, raden we u aan de ARN’s van de IdP en de rol voor later gebruik op te slaan. U kunt de ARN’s verkrijgen op de pagina’s met IdP-informatie en rolinformatie.

7. Koppel de Alibaba Cloud RAM-rol (AAD-rol) aan de Azure AD-gebruiker (u2): Als u de RAM-functie wilt koppelen aan de Azure AD-gebruiker, moet u een rol in Azure AD maken door de volgende stappen uit te voeren:

    a. Meld u aan bij de [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

    b. Klik op **Machtigingen wijzigen** om de vereiste machtigingen te verkrijgen voor het maken van een rol.

    ![Configuratie1 voor Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecteer de volgende machtigingen in de lijst en klik op **Machtigingen wijzigen**, zoals wordt weergegeven in de volgende afbeelding.

    ![Configuratie2 voor Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Nadat de machtigingen zijn verleend, meldt u zich opnieuw aan bij de Graph Explorer.

    d. Selecteer op de pagina Graph Explorer de optie **GET** in de eerste vervolgkeuzelijst en **Bèta** in de tweede vervolgkeuzelijst. Voer `https://graph.microsoft.com/beta/servicePrincipals` in het veld naast de vervolgkeuzelijsten in en klik op **Query uitvoeren**.

    ![Configuratie3 voor Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Als u meerdere directory's gebruikt, kunt u `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` invoeren in het veld van de query.

    e. Extraheer de eigenschap appRoles van de ‘service-principal’ in de sectie **Preview van reactie** voor later gebruik.

    ![Configuratie4 voor Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >U kunt de eigenschap appRoles vinden door `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` in te voeren in het veld van de query. Houd er rekening mee dat `objectID` de object-id is die u hebt gekopieerd van de Azure AD-pagina **Eigenschappen**.

    f. Ga terug naar de Graph Explorer, wijzig de methode van **GET** naar **PATCH**, plak de volgende inhoud in het gedeelte **Aanvraagbody** en klik op **Query uitvoeren**:
    ```
    { 
      "appRoles": [
        { 
          "allowedMemberTypes":[
            "User"
          ],
          "description": "msiam_access",
          "displayName": "msiam_access",
          "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
          "isEnabled": true,
          "origin": "Application",
          "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
      ]
    }
    ```
    > [!NOTE]
    > De `value` is de ARN’s van de IdP en de rol die u hebt gemaakt in de RAM-console. Hier kunt u meerdere rollen toevoegen als dat nodig is. Azure AD verzendt de waarde van deze rollen als de claimwaarde in het SAML-antwoord. U kunt echter alleen nieuwe rollen toevoegen na de tegel `msiam_access` voor de patchbewerking. Om het aanmaakproces te vereenvoudigen, raden we aan om een id-generator, zoals GUID Generator, te gebruiken om id's in realtime te genereren.

    g. Wanneer de service-principal is geïnstalleerd met de vereiste rol, koppelt u de rol aan de Azure AD-gebruiker (u2) door de stappen te volgen van **De Azure AD-testgebruiker toewijzen** in deze zelfstudie.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) configureren

Als u eenmalige aanmelding aan de zijde van **Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste gekopieerde URL's uit Azure Portal verzenden naar het [ondersteuningsteam van Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)](https://www.aliyun.com/service/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Een testgebruiker maken voor Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)

In deze sectie maakt u een gebruiker met de naam Britta Simon in Alibaba Cloud Service (eenmalige aanmelding op basis van rollen). Werk met het ondersteuningsteam van [Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)](https://www.aliyun.com/service/) om gebruikers toe te voegen aan Alibaba Cloud Service (eenmalige aanmelding op basis van rollen). Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

Nadat de voorgaande configuraties zijn voltooid, test u Alibaba Cloud Service (eenmalige aanmelding op basis van rollen) door de volgende stappen uit te voeren:

1. Ga in Azure Portal naar de pagina **Alibaba Cloud Service (eenmalige aanmelding op basis van rollen)** , selecteer **Eenmalige aanmelding** en klik op **Testen**.

    ![Configuratie1 testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klik op **Aanmelden als huidige gebruiker**.

    ![Configuratie2 testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Selecteer u2 op de pagina voor het selecteren van accounts.

    ![Configuratie3 testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. De volgende pagina wordt weergegeven, waarmee wordt aangegeven dat eenmalige aanmelding op basis van rollen is geslaagd.

    ![Configuratie4 testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>Volgende stappen

Zodra u Alibaba Cloud Service (op rollen gebaseerde eenmalige aanmelding) configureert, kunt u sessiecontrole afdwingen om in real time de vertrouwelijke gegevens van uw organisatie te beschermen tegen exfiltratie en infiltratie. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).