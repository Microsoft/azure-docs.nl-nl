---
title: 'Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met AWS Single-Account Access | Microsoft Docs'
description: Ontdek hoe u een aanmelding configureert tussen Azure Active Directory en AWS Single-Account Access.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: jeedes
ms.openlocfilehash: eb469c757e2898a9925dd7d3358cfe95734cb2e9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537733"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>Zelfstudie: Azure Active Directory integratie van eenmalige aanmelding (SSO) met AWS Single-Account Access

In deze zelfstudie leert u hoe u AWS Single-Account Access integreert met Azure Active Directory (Azure AD). Wanneer u AWS Single-Account Access integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot AWS Single-Account Access.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen Single-Account bij AWS Single-Account Access.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>Inzicht in de verschillende AWS-toepassingen in de Azure AD-toepassingsgalerie
Gebruik de onderstaande informatie om een beslissing te nemen tussen het gebruik van de AWS Single Sign-On- en AWS Single-Account Access-toepassingen in de Azure AD-toepassingsgalerie.

**AWS Single Sign-On**

[AWS Single Sign-On](./aws-single-sign-on-tutorial.md) is in februari 2021 toegevoegd aan de Azure AD-toepassingsgalerie. Het maakt het eenvoudig om de toegang tot meerdere AWS-accounts en AWS-toepassingen centraal te beheren, met aanmelding via Microsoft Azure AD. Federerer Microsoft Azure AD met AWS SSO en gebruik AWS SSO om machtigingen voor al uw AWS-accounts vanaf één plek te beheren. AWS SSO zorgt ervoor dat machtigingen automatisch worden gebruikt en dat de machtigingen actueel blijven wanneer u beleidsregels en toegangstoewijzingen bijhoudt. Eindgebruikers kunnen zich verifiëren met hun Azure AD-referenties voor toegang tot de geïntegreerde toepassingen AWS Console, Command Line Interface en AWS SSO.

**AWS Single-Account Access**

[AWS Single-Account Access]() is in de afgelopen jaren gebruikt door klanten en stelt u in staat om Azure AD te federeren naar één AWS-account en Azure AD te gebruiken om de toegang tot AWS IAM-rollen te beheren. AWS IAM-beheerders definiëren rollen en beleidsregels in elk AWS-account. Voor elk AWS-account federeren Azure AD-beheerders naar AWS IAM, wijzen ze gebruikers of groepen toe aan het account en configureren ze Azure AD voor het verzenden van asserties die roltoegang autoreren.  

| Functie | AWS Single Sign-On | AWS Single-Account Access |
|:--- |:---:|:---:|
|Voorwaardelijke toegang| Ondersteunt één beleid voor voorwaardelijke toegang voor alle AWS-accounts. | Ondersteunt één beleid voor voorwaardelijke toegang voor alle accounts of aangepaste beleidsregels per account|
| CLI-toegang | Ondersteund | Ondersteund|
| Privileged Identity Management | Nog niet ondersteund | Nog niet ondersteund |
| Accountbeheer centraliseren | Accountbeheer centraliseren in AWS. | Accountbeheer centraliseren in Azure AD (vereist waarschijnlijk een Azure AD-bedrijfstoepassing per account). |
| SAML-certificaat| Eén certificaat| Afzonderlijke certificaten per app/account | 

## <a name="aws-single-account-access-architecture"></a>AWS Single-Account Access-architectuur
![Diagram van de relatie tussen Azure Active Directory en AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

U kunt meerdere id's voor meerdere instanties configureren. Bijvoorbeeld:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Met deze waarden verwijdert Azure Active Directory de waarde van **#** en stuurt de juiste waarde `https://signin.aws.amazon.com/saml` als de doelgroep-URL in het SAML-token.

We raden u om de volgende redenen aan deze methode te gebruiken:

- Elke toepassing biedt u een uniek x509-certificaat. Elk exemplaar van een AWS-app-instantie kan vervolgens een andere verloopdatum voor het certificaat hebben, die kan worden beheerd op basis van een afzonderlijk AWS-account. In dit geval is een algemene certificaatrollover gemakkelijker.

- U kunt het inrichten van gebruikers inschakelen met de AWS-app in Azure Active Directory. Onze service haalt dan alle rollen van dat AWS-account op. U hoeft de AWS-rollen in de app niet handmatig toe te voegen of bij te werken.

- U kunt de app-eigenaar voor de app afzonderlijk toewijzen. Deze persoon kan de app rechtstreeks in Azure Active Directory beheren.

> [!Note]
> Zorg ervoor dat u alleen een galerietoepassing gebruikt.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een AWS-abonnement dat geschikt is voor eenmalige aanmelding.

> [!Note]
> Rollen mogen niet handmatig worden bewerkt in Azure AD bij het importeren van rollen.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* AWS Single-Account Access ondersteunt door **SP en IDP geïnitieerde** eenmalige aanmelding.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-aws-single-account-access-from-the-gallery"></a>AWS Single-Account Access toevoegen vanuit de galerie

Voor het configureren van de integratie van AWS Single-Account Access met Azure AD moet u AWS Single-Account Access vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.
1. Kies in het overzichtsmenu van Azure Active Directory **Ondernemingstoepassingen** > **alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie **AWS Single-Account Access** in het zoekvak.
1. Selecteer **AWS Single-Account Access in het** resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>Eenmalige aanmelding van Azure AD voor AWS Single-Account Access configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met AWS Single-Account Access met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AWS Single-Account Access.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met AWS Single-Account Access te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor AWS Single-Account Access configureren](#configure-aws-single-account-access-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor AWS Single-Account Access](#create-aws-single-account-access-test-user)** maken : als u een tegenhanger van B.Simon in AWS Single-Account Access wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
    1. **[Het inrichten van rollen configureren in AWS Single-Account Access](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de integratiepagina van de **toepassing AWS Single-Account Access** de sectie Beheren en selecteer Een **aanmelding.** 
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Werk in de sectie **Standaard-SAML-configuratie** zowel **Id (entiteits-id)** als **Antwoord-URL** bij met dezelfde standaardwaarde: `https://signin.aws.amazon.com/saml`. U moet **Opslaan** selecteren om de configuratiewijzigingen op te slaan.

1. Geef de id-waarde op als u meer dan één instantie configureert. Gebruik vanaf de tweede instantie de volgende indeling, met inbegrip van een **#** -teken om een unieke SPN-waarde op te geven.

    `https://signin.aws.amazon.com/saml#2`

1. In de AWS-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing AWS nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam  | Bronkenmerk  | Naamruimte |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Rol | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | "Geef een waarde op tussen 900 seconden (15 minuten) en 43200 seconden (12 uur)" |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > AWS verwacht rollen voor gebruikers die zijn toegewezen aan de toepassing. Stel deze rollen in Azure AD in zodat gebruikers de juiste rollen toegewezen kunnen krijgen. Kijk [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)voor meer informatie over het configureren van rollen in Azure AD

1. Ga naar de pagina **Eenmalige aanmelding instellen met SAML** en selecteer in het dialoogvenster **SAML-handtekeningcertificaat** (Stap 3) de optie **Een certificaat toevoegen**.

    ![Nieuw SAML-certificaat maken](common/add-saml-certificate.png)

1. Genereer een nieuw SAML-handtekeningcertificaat en selecteer vervolgens **Nieuw certificaat**. Voer een e-mailadres voor certificaatmeldingen in.
   
    ![Nieuw SAML-certificaat](common/new-saml-certificate.png) 

1. Ga in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve gegevens** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](./media/amazon-web-service-tutorial/certificate.png)

1. In de **sectie AWS Single-Account Access** kopieert u de juiste URL('s) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.
1. Kies in het overzichtsmenu van Azure Active Directory **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot AWS Single-Account Access.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer AWS Single-Account **Access in de lijst met toepassingen.**
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-aws-single-account-access-sso"></a>Eenmalige aanmelding voor AWS Single-Account Access configureren

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van AWS.

2. Selecteer **AWS Home**.

    ![Schermopname van de AWS-bedrijfssite, met het AWS Home-pictogram gemarkeerd][11]

3. Klik op **Identity and Access Management**.

    ![Schermopname van de pagina AWS Services, met IAM gemarkeerd][12]

4. Selecteer **Identity Providers** > **Create Provider**.

    ![Schermafbeelding van IAM-pagina met Identity Providers en Create provider gemarkeerd][13]

5. Voer op de pagina **Configure Provider** de volgende stappen uit:

    ![Schermopname van Configure Provider][14]

    a. Selecteer **SAML** als **Provider Type**.

    b. Typ bij **Provider Name** de naam van een provider (bijvoorbeeld: *WAAD*).

    c. Selecteer **Choose File** om het uit de Azure Portal gedownloade **metagegevensbestand** te uploaden.

    d. Selecteer **Next Step**.

6. Klik op de pagina **Verify Provider Information** op **Create**.

    ![Schermopname van Verify Provider Information, met Create gemarkeerd][15]

7. Selecteer **Roles** > **Create role**.

    ![Schermopname van de pagina Roles][16]

8. Voer op de pagina **Create role** de volgende stappen uit:  

    ![Schermopname van de pagina Create role][19]

    a. Selecteer **SAML 2.0 federation** onder **Select type of trusted entity**.

    b. Selecteer onder **Choose a SAML 2.0 Provider** de **SAML-provider** die u eerder hebt gemaakt (bijvoorbeeld: *WAAD*).

    c. Selecteer **Allow programmatic and AWS Management Console access**.
  
    d. Selecteer **Volgende: Permissions**.

9. Voeg in het dialoogvenster **Attach permissions policies** het juiste beleid voor uw organisatie toe. Selecteer vervolgens **Volgende: Review**.  

    ![Schermopname van het dialoogvenster Attach permissions policy][33]

10. Voer in het dialoogvenster **Review** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Review][34]

    a. In **Role name** voert u de naam van uw rol in.

    b. Voer bij **Role description** de omschrijving van de rol in.

    c. Selecteer **Create role**.

    d. Maak zoveel rollen als nodig is en wijs ze toe aan de id-provider.

11. Gebruik de aanmeldingsgegevens van uw AWS-serviceaccount voor het ophalen van de rollen van het AWS-account voor het inrichten van gebruikers in Azure Active Directory. Open hiervoor de startpagina van de AWS-console.

12. Selecteer **Services**. Selecteer onder **Security, Identity & Compliance** de optie **IAM**.

    ![Schermopname van de startpagina van de AWS-console, met Services and IAM gemarkeerd](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Selecteer in de sectie IAM **Policies**.

    ![Schermopname van de sectie IAM met Policies gemarkeerd](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Maak een nieuw beleid door op **Create policy** te klikken voor het ophalen van de rollen van het AWS-account voor het inrichten van gebruikers in Azure Active Directory.

    ![Schermopname van de pagina Create role, met Create policy gemarkeerd](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Maak uw eigen beleid om alle rollen van AWS-accounts op te halen.

    ![Schermopname van de pagina Create policy, met JSON gemarkeerd](./media/amazon-web-service-tutorial/policy1.png)

    a. Selecteer in **Create policy** het tabblad **JSON**.

    b. Voeg de volgende JSON toe in het beleidsdocument:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Selecteer **Review policy** om het beleid te valideren.

    ![Schermopname van de pagina Create policy](./media/amazon-web-service-tutorial/policy5.png)

16. Definieer het nieuwe beleid.

    ![Schermopname van de pagina Create policy, waarin de velden Name en Description zijn gemarkeerd](./media/amazon-web-service-tutorial/policy2.png)

    a. Voer **AzureAD_SSOUserRole_Policy** in bij **Name**.

    b. Voer bij **Description** het volgende in: **Met dit beleid kunnen de rollen van AWS-accounts worden opgehaald**.

    c. Selecteer **Beleid maken**.

17. Maak een nieuw gebruikersaccount in de AWS IAM-service.

    a. Selecteer in de AWS IAM-console de optie **Users**.

    ![Schermopname van de AWS IAM-console, met Users gemarkeerd](./media/amazon-web-service-tutorial/policy3.png)

    b. Als u een nieuwe gebruiker wilt maken, selecteert u **Add user**.

    ![Schermopname van de knop Add user](./media/amazon-web-service-tutorial/policy4.png)

    c. In de sectie **Add user**:

    ![Schermopname van de pagina Add user, met User name en Access type gemarkeerd](./media/amazon-web-service-tutorial/adduser1.png)

    * Voer de naam van de gebruiker in als **AzureADRoleManager**.

    * Selecteer bij Access type de optie **Programmatic access**. Op deze manier kan de gebruiker de API's aanroepen en de rollen van het AWS-account ophalen.

    * Selecteer **Next Permissions**.

18. Maak een nieuw beleid voor deze gebruiker.

    ![Schermopname met de pagina Gebruiker toevoegen, waar u een beleid voor de gebruiker kunt maken.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Selecteer **Attach existing policies directly**.

    b. Zoek het zojuist gemaakte beleid op in de filtersectie **AzureAD_SSOUserRole_Policy**.

    c. Selecteer het beleid en klik vervolgens op de knop **Next: Review**.

19. Controleer het beleid bij de gekoppelde gebruiker.

    ![Schermopname van de pagina Add user, met Create user gemarkeerd](./media/amazon-web-service-tutorial/adduser3.png)

    a. Controleer de gebruikersnaam, het toegangstype en het beleid dat is toegewezen aan de gebruiker.

    b. Selecteer **Create user**.

20. Download de referenties van een gebruiker.

    ![Schermopname met de pagina Gebruiker toevoegen met de knop C S V downloaden om gebruikersreferenties op te halen.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopieer de **Access key ID** en **Secret access key** van de gebruiker.

    b. Voer deze referenties in in de sectie voor het inrichten van gebruikers van Azure Active Directory om de rollen op te halen uit de AWS-console.

    c. Selecteer **Sluiten**.

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>Het inrichten van rollen configureren in AWS Single-Account Access

1. Ga in de Azure Active Directory-portal in de AWS-app naar **Inrichten** in de AWS-app.

    ![Schermopname van de AWS-app, met Inrichten gemarkeerd](./media/amazon-web-service-tutorial/provisioning.png)

2. Voer de toegangssleutel en het geheim in het veld **Clientgeheim** respectievelijk **Token voor geheim** in.

    ![Schermopname van het dialoogvenster Beheerdersreferenties](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Voer de gebruikerstoegangssleutel van AWS in het veld **Clientgeheim** in.

    b. Voer het gebruikersgeheim van AWS in het veld **Token voor geheim** in.

    c. Selecteer **Verbinding testen**.

    d. Sla de instelling op door **Opslaan** te selecteren.

3. Selecteer in de sectie **Instellingen** bij **Inrichtingsstatus** de optie **Aan**. Selecteer vervolgens **Opslaan**.

    ![Schermopname van de sectie Instellingen, met Aan gemarkeerd](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> De inrichtingsservice importeert rollen alleen van AWS naar Azure Active Directory. Met de service worden geen gebruikers en groepen van Azure Active Directory naar AWS geïmporteerd.

> [!NOTE]
> Nadat u de inrichtingsreferenties hebt opgeslagen, moet u wachten tot de eerste synchronisatiecyclus is uitgevoerd. Het voltooien van de synchronisatie duurt meestal ongeveer 40 minuten. U kunt de status onder aan de pagina **Inrichten** zien, bij **Huidige status**.

### <a name="create-aws-single-account-access-test-user"></a>Testgebruiker voor AWS Single-Account Access maken

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in AWS Single-Account Access. AWS Single-Account Access hoeft geen gebruiker te maken in het systeem voor eenmalige aanmelding, dus u hoeft hier geen actie uit te voeren.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL Single-Account AWS, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar AWS Single-Account Access Sign-on URL en initieer de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de AWS Single-Account Access waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel AWS Single-Account Access in de Mijn apps klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de AWS Single-Account Access waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="known-issues"></a>Bekende problemen

* Integratie van AWS Single-Account Access-inrichting kan alleen worden gebruikt om verbinding te maken met openbare AWS-cloud-eindpunten. AWS Single-Account Access-inrichtingsintegratie kan niet worden gebruikt voor toegang tot AWS Government-omgevingen.
 
* In de sectie **Inrichten** onder de subsectie **Toewijzingen**, wordt het bericht 'Laden...' weergeven, maar nooit de kenmerktoewijzingen. De enige inrichtingswerkstroom die tegenwoordig wordt ondersteund is het importeren van de rollen van AWS naar Azure Active Directory voor selectie tijdens de toewijzing van de gebruiker of groep. De kenmerktoewijzingen hiervoor zijn vooraf bepaald en kunnen niet worden geconfigureerd.

* De sectie **Inrichten** ondersteunt alleen de invoer van één set referenties per AWS-tenant tegelijk. Alle geïmporteerde rollen worden geschreven naar de eigenschap `appRoles` van het Azure Active Directory [`servicePrincipal`-object](/graph/api/resources/serviceprincipal) voor de AWS-tenant.

  Meerdere AWS-tenants (vertegenwoordigd door `servicePrincipals`) kunnen worden toegevoegd aan Azure Active Directory vanuit de galerie voor het inrichten. Er is echter een bekend probleem waarbij niet alle geïmporteerde rollen automatisch kunnen worden geschreven uit meerdere AWS-`servicePrincipals` die worden gebruikt voor het inrichten van de `servicePrincipal` die wordt gebruikt voor eenmalige aanmelding.

  Als tijdelijke oplossing kunt u de [Microsoft Graph API](/graph/api/resources/serviceprincipal) gebruiken om alle `appRoles` op te halen die zijn geïmporteerd in elke AWS-`servicePrincipal` waar de inrichting is geconfigureerd. U kunt deze tekenreeksen vervolgens toevoegen aan de AWS-`servicePrincipal` waar eenmalige aanmelding is geconfigureerd.

* Rollen moeten voldoen aan de volgende vereisten om in aanmerking te komen voor importeren vanuit AWS in Azure Active Directory:

  * Rollen moeten precies één SAML-provider hebben gedefinieerd in AWS
  * De gecombineerde lengte van de ARN (Amazon-resourcenaam) voor de rol en de ARN voor de bijbehorende SAML-provider moet korter zijn dan 240 tekens.

## <a name="change-log"></a>Wijzigingenlogboek

* 01-12-2020 - Limiet voor rollengte is verhoogd van 119 tekens naar 239 tekens. 

## <a name="next-steps"></a>Volgende stappen

Zodra u AWS Single-Account Access hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png