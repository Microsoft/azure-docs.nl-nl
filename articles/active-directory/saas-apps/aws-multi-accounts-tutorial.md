---
title: 'Zelfstudie: Azure Active Directory-integratie met Amazon Web Services (AWS) om meerdere accounts te verbinden | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure AD en Amazon Web Services (AWS) (verouderde zelfstudie).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: 7e71058e1899cf83e712025b534e51a1be1f6bdb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591766"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Zelfstudie: Azure Active Directory-integratie met Amazon Web Services (AWS) (verouderde zelfstudie)

In deze zelfstudie leert u hoe u Azure Active Directory (Azure AD) integreert met Amazon Web Services (AWS) (verouderde zelfstudie).

De integratie van Amazon Web Services (AWS) met Azure AD heeft de volgende voordelen:

- U kunt in Azure AD beheren wie toegang heeft tot Amazon Web Services (AWS).
- U kunt uw gebruikers zich automatisch laten aanmelden bij Amazon Web Services (AWS) (eenmalige aanmelding) met hun Azure AD-account.
- U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

![Amazon Web Services (AWS) in de resultatenlijst](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Onthoud dat het niet onze aanbevolen benadering is om één AWS-app aan al uw AWS-accounts te koppelen. In plaats daarvan raden we aan dat u [deze](./amazon-web-service-tutorial.md) benadering gebruikt om meerdere instanties van een AWS-account te configureren met meerdere instanties van AWS-apps in Azure AD. U moet deze benadering alleen gebruiken als u er weinig AWS-accounts en -rollen in hebt. Dit model is niet schaalbaar naarmate er meer AWS-accounts en -rollen in deze accounts komen. Deze benadering maakt geen gebruik van AWS-functionaliteit om rollen te importeren met Azure AD-gebruikersinrichting. Het kan dus zijn dat u handmatig rollen moet toevoegen, bijwerken of verwijderen. Voor andere beperkingen voor deze benaderingen raadpleegt u de onderstaande informatie.

**Onthoud dat we deze benadering om de volgende redenen niet aanraden:**

* U moet de Microsoft Graph Explorer-benadering gebruiken om alle rollen in de app te patchen. We raden niet aan dat u de benadering met het manifestbestand gebruikt.

* Er zijn klanten die melden dat er nadat er ~1200 app-rollen voor één AWS-app zijn toegevoegd, bewerkingen in de app fouten geven met betrekking tot de grootte. Er is een harde limiet voor de grootte in het toepassingsobject.

* U moet de rol handmatig bijwerken nadat rollen worden toegevoegd in de accounts. Dat is helaas een vervangingsbenadering en geen toevoegingsbenadering. En naarmate uw accounts groeien, wordt dit een n-x-n-relatie met accounts en rollen.

* Alle AWS-accounts gebruiken hetzelfde Federation Metadata XML-bestand en op het moment van certificaatoverschakeling moet u deze enorme oefening sturen om het certificaat in alle AWS-accounts op hetzelfde moment bij te werken

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met Amazon Web Services (AWS):

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een Amazon Web Services (AWS) abonnement met eenmalige aanmelding ingeschakeld

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Amazon Web Services (AWS) ondersteunt door **SP en IDP** gestarte SSO

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) toevoegen uit de galerie

Om de integratie van Amazon Web Services (AWS) met Azure AD te configureren, moet u Amazon Web Services (AWS) vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Amazon Web Services (AWS)** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Amazon Web Services (AWS)** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

1. Nadat de toepassing is geladen, gaat u naar de pagina **Eigenschappen** en kopieert u het **Object-id**.

    ![Object-id](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Amazon Web Services (AWS) op basis van een testgebruiker met de naam "Britta Simon".

Voor gebruik van eenmalige aanmelding moet Azure AD weten wie de tegenhangende gebruiker in Amazon Web Services (AWS) is voor een gebruiker in Azure AD. In andere woorden, er moet een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Amazon Web Services (AWS) tot stand worden gebracht.

Wijs in Amazon Web Services (AWS) de waarde van de **gebruikersnaam** in Azure AD toe als de waarde van de **Gebruikersnaam** om de relatie vast te leggen.

Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren en testen voor Amazon Web Services (AWS):

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Amazon Web Services (AWS) configureren](#configure-amazon-web-services-aws-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In dit gedeelte schakelt u eenmalige aanmelding van Azure AD in de Azure Portal in en configureert u eenmalige aanmelding in uw Amazon Web Services-toepassing (AWS).

**Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren voor Amazon Web Services (AWS):**

1. Ga in de Azure-portal naar de overzichtspagina van de integratie voor **Amazon Web Services (AWS)** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren of op **Opslaan** te klikken, omdat de app al vooraf is geïntegreerd met Azure.

5. Amazon Web Services (AWS) verwacht de SAML-asserties in een specifieke notatie. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken en claims** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken en claims** te openen.

    ![Schermopname met Gebruikerskenmerken en een bijschrift bij het pictogram Bewerken.](common/edit-attribute.png)

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    | Naam  | Bronkenmerk  | Naamruimte |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Rol | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "Geef een waarde op tussen 900 seconden (15 minuten) en 43200 seconden (12 uur)" |  `https://aws.amazon.com/SAML/Attributes` |

    1. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

        ![Schermopname met Gebruikersclaims met een bijschrift voor de opties Nieuwe claim toevoegen en Opslaan.](common/new-save-attribute.png)

        ![Schermopname met het dialoogvenster Gebruikersclaims beheren, waarin u de waarden kunt invoeren die worden beschreven in deze stap.](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Typ de naamruimtewaarde voor die rij in het tekstvak **Naamruimte**.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Eenmalige aanmelding bij Amazon Web Services (AWS) configureren

1. Meld u in een ander browservenster aan als beheerder bij uw Amazon Web Services (AWS) bedrijfssite.

1. Klik op **AWS Home**.

    ![Startpagina voor configureren van eenmalige aanmelding][11]

1. Klik op **Identity and Access Management**.

    ![Identiteit voor eenmalige aanmelding configureren][12]

1. Klik op **Identity Providers** en vervolgens op **Create Provider**.

    ![Provider voor eenmalige aanmelding configureren][13]

1. Voer op de dialoogpagina **Configure Provider** de volgende stappen uit:

    ![Dialoogvenster Configure Single Sign-On][14]

    a. Selecteer **SAML** als **Provider Type**.

    b. Typ in het tekstvak **Provider Name** de naam van een provider (bijvoorbeeld *WAAD*).

    c. Klik op **Choose File** om het uit de Azure-portal gedownloade **metagegevensbestand** te uploaden.

    d. Klik op **Next Step**.

1. Klik op de dialoogpagina **Verify Provider Information** op **Create**.

    ![Verificatie voor het configureren van eenmalige aanmelding][15]

1. Klik op **Roles** en vervolgens op **Create role**.

    ![Rollen voor eenmalige aanmelding configureren][16]

    > [!NOTE]
    > De gecombineerde lengte van de rol-ARN en de ARN van de SAML-provider voor een rol die wordt geïmporteerd, mag maximaal 240 tekens bevatten.

1. Voer op de pagina **Create role** de volgende stappen uit:  

    ![Vertrouwensrelatie voor eenmalige aanmelding configureren][19]

    a. Selecteer **SAML 2.0 federation** onder **Select type of trusted entity**.

    b. Selecteer onder **Choose a SAML 2.0 Provider section** de **SAML-provider** die u eerder hebt gemaakt (bijvoorbeeld *WAAD*)

    c. Selecteer **Allow programmatic and AWS Management Console access**.

    d. Klik op **Next: Permissions**.

1. Zoek **Beheerderstoegang** in de zoekbalk en selecteer het vakje **Beheerderstoegang**. Klik dan op **Volgende: Tags**.

    ![Schermopname die Beheerderstoegang geselecteerd heeft als beleidsnaam.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Voer in het gedeelte **Tags toevoegen (optioneel)** de volgende stappen uit:

    ![Tags toevoegen](./media/aws-multi-accounts-tutorial/config2.png)

    a. Voer in het tekstvak **Sleutel** de sleutelnaam naam in voor ex: Azureadtest.

    b. Voer in het tekstvak **Waarde (optioneel)** de sleutelwaarde in de volgende indeling in: `accountname-aws-admin`. De accountnaam moet volledig in kleine letters staan.

    c. Klik op **Volgende: Review**.

1. Voer in het dialoogvenster **Review** de volgende stappen uit:

    ![Configuratie van eenmalige aanmelding controleren][34]

    a. Voer in het tekstvak **Rolnaam** de waarde in de volgende indeling in: `accountname-aws-admin`.

    b. Voer in het tekstvak **Rolbeschrijving** dezelfde waarde in die u ook hebt gebruikt voor de rolnaam.

    c. Klik op **Create Role**.

    d. Maak zoveel rollen als nodig is en wijs ze toe aan de id-provider.

    > [!NOTE]
    > Maak ook resterende rollen als accountname-finance-admin, accountname-read-only-user, accountname-devops-user en accountname-tpm-user waar verschillende beleidsregels aan moeten worden gekoppeld. Deze rolbeleidsregels kunnen ook worden veranderd overeenkomstig de vereisten per AWS-account, maar het is altijd beter om hetzelfde beleid voor elke rol te behouden in AWS-accounts.

1. Schrijf de account-id voor die AWS-account op. Dat kan via EC2-eigenschappen of het IAM-dashboard, zoals hieronder is weergegeven:

    ![Schermopname die laat zien waar de account-id zich in het AWS-venster bevindt.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Meld u nu aan bij [Azure Portal](https://portal.azure.com/) en navigeer naar **Groepen**.

1. Maak nieuwe groepen met dezelfde naam als de IAM-rollen die eerder zijn gemaakt en noteer de **Object-id's** van deze nieuwe groepen.

    ![Selecteer Beheerderstoegang1](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Meld u af bij het huidige AWS-account en meld u aan bij een ander account waarvoor u eenmalige aanmelding met Azure AD wilt configureren.

1. Nadat alle rollen in de accounts zijn gemaakt, worden ze weergegeven in de lijst **Rollen** voor deze accounts.

    ![Schermopname die de lijst Rollen met Rolnaam, Beschrijving en Vertrouwde entiteiten weergeeft.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. We moeten alle Rol-ARN's en Vertrouwde entiteiten vastleggen voor alle rollen in de accounts, die we handmatig moeten toewijzen met de Azure AD-toepassing.

1. Klik op de rollen om de waarden **Rol-ARN** en **Vertrouwde entiteiten** te kopiëren. U hebt deze waarden nodig voor alle rollen die u moet maken in azure AD.

    ![Rollen instellen2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Voer de bovenstaande stap uit voor alle rollen in alle accounts en bewaar deze in de indeling **Rol-ARN,Vertrouwde entiteiten** in een kladblok.

1. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in een ander venster.

    1. Meld u aan bij de Microsoft Graph Explorer-site met de referenties voor uw tenant.

    1. U hebt de juiste machtigingen nodig om de rollen te maken. Klik op **machtigingen wijzigen** om de vereiste machtigingen op te halen.

        ![Microsoft Graph Explorer-dialoogvenster1](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    1. Selecteer de volgende machtigingen uit de lijst (als u die nog niet hebt) en klik op 'Machtigingen wijzigen' 

        ![Microsoft Graph Explorer-dialoogvenster2](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    1. U wordt gevraagd u opnieuw aan te melden en toestemming te geven. Nadat u de toestemming hebt gegeven, wordt u opnieuw aangemeld bij Microsoft Graph Explorer.

    1. Wijzig het verzievervolgkeuzemenu naar **bèta**. Gebruik de volgende query om alle service-principals van uw tenant op te halen: `https://graph.microsoft.com/beta/servicePrincipals`. Als u meerdere mappen gebruikt, kunt u gebruikmaken van het volgende patroon (dat het primaire domein bevat): `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.

        ![Microsoft Graph Explorer-dialoogvenster3](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    1. In de lijst met opgehaalde service-principals, selecteert u de principal die u moet bewerken. U kunt ook met CtrL+F zoeken naar de toepassing in alle vermelde service-principals. U kunt de volgende query gebruiken door het **object-id van de service-principal** te gebruiken, die u hebt gekopieerd op de eigenschappenpagina van Azure AD, om de bijbehorende service-principal op te halen.

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

        ![Microsoft Graph Explorer-dialoogvenster4](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    1. Extraheer de eigenschap appRoles uit het service-principalobject.

        ![Microsoft Graph Explorer-dialoogvenster5](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    1. Nu moet u nieuwe rollen genereren voor uw toepassing. 

    1. De onderstaande JSON is een voorbeeld van het object appRoles. Maak een vergelijkbaar object om de rollen toe te voegen die u voor uw toepassing wilt.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > U kunt alleen nieuwe rollen toevoegen na **msiam_acces** voor de patchbewerking. U kunt zoveel rollen toevoegen als u per organisatiebehoefte wilt. Azure AD verzendt de **waarde** van deze rollen als de claimwaarde in het SAML-antwoord.

    1. Ga terug naar uw Microsoft Graph Explorer en wijzig de methode van **GET** naar **PATCH**. Patch het service-principal-object dat de gewenste rollen moet krijgen door de eigenschap appRoles bij te werken zoals in het bovenstaande voorbeeld is weergegeven. Klik op **Query uitvoeren** om de patchbewerking uit te voeren. Een slagingsbericht bevestigt het maken van de rol voor uw Amazon Web Services-toepassing.

        ![Microsoft Graph Explorer-dialoogvenster6](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Wanneer de service-principal is bijgewerkt met meer rollen, kunt u gebruikers/groepen toevoegen aan de bijbehorende rollen. Dit doet u door naar de portal te gaan en vervolgens naar de Amazon Web Services-toepassing te navigeren. Klik boven op het tabblad **Gebruikers en groepen**.

1. We raden u aan nieuwe groepen te maken voor elke AWS-rol, zodat u die bepaalde rol in die groep kunt toewijzen. Onthoud dat dit één-op-één-toewijzing is voor een groep naar een rol. U kunt vervolgens de leden toevoegen die bij die groep horen.

1. Nadat de groepen zijn gemaakt, selecteert u de groep en wijst u de toepassing toe.

    ![Eenmalige aanmelding configureren1](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Geneste groepen worden niet ondersteund wanneer u groepen toewijst.

1. Als u de rol aan de groep toewijst, selecteert u de rol en klikt u onderaan de pagina op de knop **Toewijzen**.

    ![Eenmalige aanmelding configureren2](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Onthoud dat u uw sessie in Azure Portal moet vernieuwen om de nieuwe rollen te bekijken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Amazon Web Services (AWS) klik in het toegangsvenster, moet u de toepassingspagina Amazon Web Services (AWS) krijgen met de optie om de rol te selecteren.

![Eenmalige aanmelding testen1](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

U kunt ook de SAML-reactie verifiëren om de rollen te bekijken die als claims worden doorgevoerd.

![Eenmalige aanmelding testen2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Amazon Web Services (AWS) hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/