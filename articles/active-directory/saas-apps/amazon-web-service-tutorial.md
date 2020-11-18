---
title: 'Zelfstudie: Integratie van eenmalige aanmelding in Azure Active Directory met Amazon Web Services (AWS) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Amazon Web Services (AWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: jeedes
ms.openlocfilehash: 764342f237452d9322d44c86ebdb41691b44495d
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360714"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Zelfstudie: Integratie van eenmalige aanmelding in Azure Active Directory met Amazon Web Services (AWS)

In deze zelfstudie leert u hoe u Amazon Web Services (AWS) kunt integreren met Azure Active Directory (Azure AD). Wanneer u Amazon Web Services integreert met Azure Active Directory, kunt u:

* In Azure Active Directory beheren wie toegang heeft tot Amazon Web Services (AWS).
* Uw gebruikers zich automatisch laten aanmelden bij Amazon Web Services (AWS) met hun Azure Active Directory-account.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

> [!Note]
> Azure AD biedt geen ondersteuning voor de integratie van eenmalige aanmelding met AWS SSO, dit is een ander product van AWS. Hoewel AWS dit [hier](https://docs.aws.amazon.com/singlesignon/latest/userguide/azure-ad-idp.html) vermeldt, raadt Azure AD klanten aan om in de plaats daarvan AWS IAM-integratie te gebruiken zodat u betere beveiligingscontroles kunt bekomen met behulp van beleid voor voorwaardelijke toegang op individuele accounts, en deze toepassingen ook beter kunt beheren.

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

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Amazon Web Services (AWS) ondersteunt door **SP en IDP** gestarte SSO

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) toevoegen uit de galerie

Om de integratie van Amazon Web Services (AWS) met Azure AD te configureren, moet u Amazon Web Services (AWS) vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.
1. Kies in het overzichtsmenu van Azure Active Directory **Ondernemingstoepassingen** > **alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.
1. Typ **Amazon Web Services (AWS)** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Amazon Web Services (AWS)** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-amazon-web-services-aws"></a>Eenmalige aanmelding van Azure Active Directory voor Amazon Web Services (AWS) configureren en testen

Configureer en test eenmalige aanmelding van Azure Active Directory met Amazon Web Services (AWS) met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure Active Directory-gebruiker en de bijbehorende gebruiker in Amazon Web Services (AWS).

U moet de volgende bouwstenen voltooien om eenmalige aanmelding van Azure Active Directory voor Amazon Web Services (AWS) te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Amazon Web Services (AWS) configureren](#configure-amazon-web-services-aws-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    1. **[Testgebruiker voor Amazon Web Services (AWS) maken](#create-amazon-web-services-aws-test-user)** : als u een tegenhanger van B. Simon in Amazon Web Services (AWS) wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
    1. **[Het inrichten van rollen configureren in Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Amazon Web Services (AWS)** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
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
    | Rol            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration             | "Geef een waarde op tussen 900 seconden (15 minuten) en 43200 seconden (12 uur)" |  `https://aws.amazon.com/SAML/Attributes` |

1. Ga naar de pagina **Eenmalige aanmelding instellen met SAML** en selecteer in het dialoogvenster **SAML-handtekeningcertificaat** (Stap 3) de optie **Een certificaat toevoegen**.

    ![Nieuw SAML-certificaat maken](common/add-saml-certificate.png)

1. Genereer een nieuw SAML-handtekeningcertificaat en selecteer vervolgens **Nieuw certificaat**. Voer een e-mailadres voor certificaatmeldingen in.
   
    ![Nieuw SAML-certificaat](common/new-saml-certificate.png) 

1. Ga in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve gegevens** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](./media/amazon-web-service-tutorial/certificate.png)

1. In het gedeelte **Amazon Web Services (AWS) instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In dit gedeelte gaat u B. Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Amazon Web Services (AWS).

1. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.
1. Kies in het overzichtsmenu van Azure Active Directory **Ondernemingstoepassingen** > **alle toepassingen**.
1. Selecter **Amazon Web Services (AWS)** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-amazon-web-services-aws-sso"></a>Eenmalige aanmelding bij Amazon Web Services (AWS) configureren

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

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Het inrichten van rollen configureren in Amazon Web Services (AWS)

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

### <a name="create-amazon-web-services-aws-test-user"></a>Testgebruiker voor Amazon Web Services (AWS) maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in Amazon Web Services (AWS). Amazon Web Services (AWS) vereist niet dat een gebruiker is aangemaakt in hun systeem voor eenmalige aanmelding, dus u hoeft hier niets te doen.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit zorgt voor een omleiding naar de aanmeldings-URL voor Amazon Web Services (AWS) waar de aanmeldingsstroom gestart kan worden.  

* Ga rechtstreeks naar de aanmeldings-URL van Amazon Web Services (AWS) en start de aanmeldingsstroom daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in de Azure-portal. U wordt automatisch aangemeld bij het exemplaar van Amazon Web Services (AWS) waarvoor u eenmalige aanmelding hebt ingesteld. 

U kunt ook het Microsoft-toegangsvenster gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in het toegangsvenster op de tegel Amazon Web Services (AWS) klikt en als deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het exemplaar van Amazon Web Services (AWS) waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="known-issues"></a>Bekende problemen

 * In de sectie **Inrichten** onder de subsectie **Toewijzingen**, wordt het bericht 'Laden...' weergeven, maar nooit de kenmerktoewijzingen. De enige inrichtingswerkstroom die tegenwoordig wordt ondersteund is het importeren van de rollen van AWS naar Azure Active Directory voor selectie tijdens de toewijzing van de gebruiker of groep. De kenmerktoewijzingen hiervoor zijn vooraf bepaald en kunnen niet worden geconfigureerd.

 * De sectie **Inrichten** ondersteunt alleen de invoer van één set referenties per AWS-tenant tegelijk. Alle geïmporteerde rollen worden geschreven naar de eigenschap `appRoles` van het Azure Active Directory [`servicePrincipal`-object](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) voor de AWS-tenant.

   Meerdere AWS-tenants (vertegenwoordigd door `servicePrincipals`) kunnen worden toegevoegd aan Azure Active Directory vanuit de galerie voor het inrichten. Er is echter een bekend probleem waarbij niet alle geïmporteerde rollen automatisch kunnen worden geschreven uit meerdere AWS-`servicePrincipals` die worden gebruikt voor het inrichten van de `servicePrincipal` die wordt gebruikt voor eenmalige aanmelding.

   Als tijdelijke oplossing kunt u de [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) gebruiken om alle `appRoles` op te halen die zijn geïmporteerd in elke AWS-`servicePrincipal` waar de inrichting is geconfigureerd. U kunt deze tekenreeksen vervolgens toevoegen aan de AWS-`servicePrincipal` waar eenmalige aanmelding is geconfigureerd.

* Rollen moeten voldoen aan de volgende vereisten om in aanmerking te komen voor importeren vanuit AWS in Azure Active Directory:

  * Rollen moeten precies één SAML-provider hebben gedefinieerd in AWS
  * De gecombineerde lengte van de ARN (Amazon-resourcenaam) voor de rol en de ARN voor de bijbehorende SAML-provider moet korter zijn dan 120 tekens

## <a name="next-steps"></a>Volgende stappen

Zodra u Amazon Web Services (AWS) hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


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