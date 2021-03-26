---
title: 'Zelf studie: integratie Azure Active Directory met Amazon Web Services om verbinding te maken met meerdere accounts | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure AD en Amazon Web Services (verouderde zelf studie).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "100384109"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Zelf studie: integratie Azure Active Directory met Amazon Web Services

In deze zelf studie leert u hoe u Azure Active Directory (Azure AD) integreert met Amazon Web Services (AWS) (verouderde zelf studie).

Deze integratie biedt de volgende voor delen:

- U kunt beheren in azure AD die toegang heeft tot AWS.
- U kunt uw gebruikers in staat stellen om zich automatisch aan te melden bij AWS door eenmalige aanmelding (SSO) met hun Azure AD-accounts te gebruiken.
- U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

![Diagram van Azure AD-integratie met AWS.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> U wordt aangeraden *geen* AWS-app te verbinden met al uw AWS-accounts. In plaats daarvan raden we u aan [Azure AD SSO-integratie met AWS](./amazon-web-service-tutorial.md) te gebruiken om meerdere exemplaren van uw AWS-account te configureren voor meerdere exemplaren van AWS-apps in azure AD. 

U kunt het beste om de volgende redenen *geen* verbinding maken met een AWS-app met al uw AWS-accounts:

* U kunt deze methode alleen gebruiken als u een klein aantal AWS-accounts en-rollen hebt, omdat dit model niet schaalbaar is als het aantal AWS-accounts en de rollen binnen deze servers toenemen. De benadering maakt geen gebruik van de functie voor AWS-import met Azure AD-gebruikers inrichting, dus u moet de rollen hand matig toevoegen, bijwerken of verwijderen. 

* U moet de Microsoft Graph Explorer-benadering gebruiken om alle rollen in de app te patchen. We raden niet aan dat u de benadering met het manifestbestand gebruikt.

* Klanten rapporteren dat na het toevoegen van ~ 1.200 app-rollen voor een enkele AWS-app de fouten met betrekking tot de grootte van de app worden gestart. Er is een limiet voor de vaste grootte van het toepassings object.

* U moet de rollen hand matig bijwerken wanneer deze worden toegevoegd aan een van de accounts. Dit is een *vervangende* benadering, niet een *toevoeg* benadering. Als uw account nummers groeien, wordt dit ook een *n* &times; *n* -relatie met accounts en rollen.

* Alle AWS-accounts gebruiken hetzelfde XML-bestand met federatieve meta gegevens. Op het moment van de certificaat overschakeling, kan het certificaat op alle AWS-accounts tegelijkertijd worden bijgewerkt.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met AWS wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-abonnement hebt, kunt u een [proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/)ontvangen.
* Een AWS-abonnement dat is ingeschakeld voor SSO.

> [!NOTE]
> Het is niet raadzaam om de stappen in deze zelf studie in een productie omgeving te testen, tenzij dit nodig is.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

AWS ondersteunt door SP geïnitieerde en door IDP geïnitieerde SSO.

## <a name="add-aws-from-the-gallery"></a>AWS toevoegen vanuit de galerie

Als u de integratie van AWS in azure AD wilt configureren, voegt u AWS van de galerie toe aan uw lijst met SaaS-apps (Software as a Service).

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeel venster de Azure AD-service waarmee u wilt werken.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Amazon Web Services** in het zoekvak.
1. Selecteer in de lijst met resultaten **Amazon Web Services** en voeg vervolgens de app toe. Na enkele seconden wordt de app toegevoegd aan de tenant.

1. Ga naar het deel venster **Eigenschappen** en kopieer de waarde die wordt weer gegeven in het vak **object-id** .

    ![Scherm afbeelding van het vak object-ID in het deel venster Eigenschappen.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met AWS op basis van een test gebruiker met de naam ' Julia Simon '.

Voor gebruik van eenmalige aanmelding moet Azure AD weten wat de tegen gebruiker in AWS is voor de Azure AD-gebruiker. Met andere woorden, een koppelings relatie tussen de Azure AD-gebruiker en dezelfde gebruiker in AWS moet tot stand worden gebracht.

In AWS wijst u de waarde van de **gebruikers naam** in azure AD toe als de waarde van de AWS **username** om de koppelings relatie tot stand te brengen.

Ga als volgt te werk om eenmalige aanmelding voor Azure AD te configureren en te testen met AWS:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
1. **[CONFIGUREER AWS SSO](#configure-aws-sso)** voor het configureren van SSO-instellingen aan de kant van de toepassing.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie schakelt u Azure AD SSO in de Azure Portal en configureert u SSO in uw AWS-toepassing door het volgende te doen:

1. Selecteer in de Azure Portal in het linkerdeel venster van de pagina **Amazon Web Services (AWS)** Application Integration de optie **eenmalige aanmelding**.

    ![Scherm afbeelding van de opdracht voor eenmalige aanmelding.](common/select-sso.png)

1. In het deelvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![Scherm opname van het deel venster ' Selecteer een methode voor eenmalige aanmelding '.](common/select-saml-option.png)

1. Selecteer in het deel venster **eén Sign-On met SAML instellen** de knop  **bewerken** (potlood pictogram).

    ![Scherm afbeelding van de knop bewerken in het deel venster Eén Sign-On met SAML instellen.](common/edit-urls.png)

1. Het **basis configuratie** venster voor SAML wordt geopend. Sla deze sectie over, omdat de app vooraf is geïntegreerd met Azure. Selecteer **Opslaan**.

   De AWS-toepassing verwacht de SAML-beweringen in een specifieke indeling. U kunt de waarden van deze kenmerken beheren in de sectie **gebruikers kenmerken & claims** op de pagina voor de integratie van de **toepassing** . 
   
1. Selecteer de knop **bewerken** op de pagina **Eén Sign-On met SAML instellen** .

    ![Scherm afbeelding van de knop bewerken in het deel venster ' gebruikers kenmerken '.](common/edit-attribute.png)

1. Configureer in de sectie **gebruikers claims** van het deel venster **gebruikers kenmerken** het SAML-token kenmerk met behulp van de waarden in de volgende tabel:

    | Naam  | Bronkenmerk  | Naamruimte |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Rol | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "Geef een waarde op van 900 seconden (15 minuten) tot 43200 seconden (12 uur)" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. Selecteer **nieuwe claim toevoegen** en voer vervolgens in het deel venster **gebruikers claims beheren** de volgende handelingen uit:

   ![Scherm opname van de knoppen nieuwe claim toevoegen en opslaan in het deel venster gebruikers claims.](common/new-save-attribute.png)

   ![Scherm afbeelding van het deel venster gebruikers claims beheren.](common/new-attribute-details.png)

   b. Voer in het vak **naam** de naam van het kenmerk in.  

   c. Voer in het vak **naam ruimte** de waarde van de naam ruimte in.

   d. Als **Bron** selecteert u **Kenmerk**.

   e. Selecteer in de vervolg keuzelijst **bron kenmerk** het kenmerk.

   f. Selecteer **OK** en selecteer vervolgens **Opslaan**.

   >[!NOTE]
   >Zie [app-rollen toevoegen aan uw toepassing en deze in het token ontvangen](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)voor meer informatie over rollen in azure AD.

1. Selecteer op de pagina **eén Sign-On met SAML instellen** , in de sectie **SAML-handtekening certificaat** , **downloaden** om het XML-bestand met federatieve meta gegevens te downloaden en sla het vervolgens op uw computer op.

   ![Scherm opname van de download koppeling voor de federatieve meta gegevens in het deel venster SAML-handtekening certificaat.](common/metadataxml.png)

### <a name="configure-aws-sso"></a>AWS SSO configureren

1. Meld u in een nieuw browser venster aan bij uw AWS-bedrijfs site als beheerder.

1. Selecteer het **Start** pictogram van AWS.

   ![Scherm afbeelding van het pictogram ' AWS Home '.][11]

1. Selecteer in het deel venster **AWS Services** onder **beveiliging, identiteit & naleving** de optie **IAM (identiteit & Access Management)**.

    ![Scherm afbeelding van de koppeling ' identiteits-en toegangs beheer ' in het deel venster AWS Services.][12]

1. Selecteer in het linkerdeel venster **id-providers** en selecteer vervolgens **provider maken**.

    ![Scherm opname van de knop ' provider maken '.][13]

1. Ga als volgt te werk op het deel venster **provider configureren** :

    ![Scherm opname van het deel venster ' provider configureren '.][14]

    a. Selecteer in de vervolg keuzelijst **provider type** de optie **SAML**.

    b. Voer in het vak **naam van provider** een provider naam in (bijvoorbeeld. *WAAD*).

    c. Selecteer in het vak **meta gegevens document** de **optie bestand kiezen** om uw gedownloade federatieve meta gegevens-XML-bestand te uploaden naar de Azure Portal.

    d. Selecteer **Next Step**.

1. Selecteer **maken** in het deel venster **provider gegevens verifiëren** .

    ![Scherm opname van het deel venster provider gegevens verifiëren.][15]

1. Selecteer in het linkerdeel venster **rollen** en selecteer vervolgens **rol maken**.

    ![Scherm opname van de knop ' rol maken ' in het deel venster rollen.][16]

    > [!NOTE]
    > De gecombineerde lengte van de rol Amazon Resource Name (ARN) en de SAML-provider ARN voor een rol die wordt geïmporteerd, mogen Maxi maal 240 tekens lang zijn.

1. Ga als volgt te werk op de pagina **rol maken** :  

    ![Scherm afbeelding van de knop vertrouwde entiteit van de ' SAML 2,0-Federatie ' op de pagina ' rol maken '.][19]

    a. Selecteer **SAML 2.0 federation** onder **Select type of trusted entity**.

    b. Selecteer onder **een saml 2,0-provider kiezen** de SAML-provider die u eerder hebt gemaakt (bijvoorbeeld *WAAD*)

    c. Selecteer **Allow programmatic and AWS Management Console access**.

    d. Selecteer **Volgende: Permissions**.

1. Voer in het zoekvak **beheerders toegang** in, schakel het selectie vakje **AdministratorAccess** in en selecteer **volgende: Tags**.

    ![Scherm afbeelding van de lijst ' beleids naam ' waarvoor het AdministratorAccess-beleid is geselecteerd.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Ga als volgt te werk in het deel venster **Tags toevoegen (optioneel)** :

    ![Scherm afbeelding van het deel venster Tags toevoegen (optioneel).](./media/aws-multi-accounts-tutorial/config2.png)

    a. Voer in het vak **sleutel** de sleutel naam in (bijvoorbeeld *Azureadtest*).

    b. Voer in het vak **waarde (optioneel)** de sleutel waarde in de volgende indeling in: `<accountname-aws-admin>` . De account naam mag alleen uit kleine letters bestaan.

    c. Selecteer **Volgende: Review**.

1. Ga als volgt te werk in het deel venster **controleren** :

    ![Scherm opname van het deel venster revisie, met de vakken "rolnaam" en "beschrijving van rol" gemarkeerd.][34]

    a. In het vak **rolnaam** typt u de waarde in de volgende notatie: `<accountname-aws-admin>` .

    b. Voer in het vak **Beschrijving van rol** de waarde in die u hebt gebruikt voor de naam van de rol.

    c. Selecteer **Create role**.

    d. Maak zoveel rollen als u nodig hebt en wijs ze toe aan de ID-provider.

    > [!NOTE]
    > Op dezelfde manier kunt u andere rollen maken, zoals *AccountName-Finance-Administrator*, *AccountName-alleen-lezen-gebruiker*, *AccountName-devops-User* of *AccountName-TPM-gebruiker*, elk met een ander beleid dat eraan is gekoppeld. U kunt deze beleids regels later wijzigen op basis van de vereisten voor elk AWS-account. Het is een goed idee om hetzelfde beleid voor elke rol over de AWS-accounts te gebruiken.

1. Noteer de account-ID voor het AWS-account in het eigenschappen venster van de Amazon Elastic Compute-Cloud (Amazon EC2) of het IAM-dash board, zoals wordt weer gegeven in de volgende scherm afbeelding:

    ![Scherm afbeelding die laat zien waar de account-ID wordt weer gegeven in het deel venster identiteits-en toegangs beheer.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Meld u aan bij de Azure Portal en ga vervolgens naar **groepen**.

1. Maak nieuwe groepen met dezelfde naam als die van de IAM-rollen die u eerder hebt gemaakt en noteer de waarde in het vak **object-id** van elk van deze nieuwe groepen.

    ![Scherm afbeelding van de account Details voor een nieuwe groep.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Meld u af bij het huidige AWS-account en meld u vervolgens aan bij een ander account waarvoor u SSO wilt configureren met Azure AD.

1. Nadat u alle rollen in de accounts hebt gemaakt, worden deze weer gegeven in de lijst **rollen** voor die accounts.

    ![Scherm afbeelding van de lijst met rollen, met de naam, beschrijving en vertrouwde entiteiten van elke rol.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

U moet de functie ARNs en vertrouwde entiteiten voor alle rollen in alle accounts vastleggen. U moet ze hand matig toewijzen met de Azure AD-toepassing. Hiervoor doet u het volgende:

1. Selecteer elke rol om de ARN van de rol en de vertrouwde entiteits waarden te kopiëren. U hebt deze nodig voor alle rollen die u maakt in azure AD.

    ![Scherm opname van het deel venster samen vatting voor de functie ARNs en vertrouwde entiteiten.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Herhaal de vorige stap voor alle rollen in alle accounts en sla ze vervolgens op in een tekst bestand met de volgende indeling: `<Role ARN>,<Trusted entities>` .

1. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)en voer de volgende handelingen uit:

    a. Meld u aan bij de Microsoft Graph Explorer-site met de referenties van de globale beheerder of co-beheerder voor uw Tenant.

    b. U hebt voldoende machtigingen om de rollen te maken. Selecteer **machtigingen voor wijzigen**.

      ![Scherm afbeelding van de koppeling machtigingen wijzigen in het deel venster Microsoft Graph Explorer-verificatie.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Als u nog niet beschikt over de machtigingen die worden weer gegeven op de volgende scherm afbeelding, selecteert u deze in de lijst met machtigingen en selecteert u vervolgens **machtigingen wijzigen**. 

      ![Scherm afbeelding van de lijst met machtigingen van Microsoft Graph Explorer met de juiste machtigingen gemarkeerd.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Meld u opnieuw aan bij Graph Explorer en accepteer de gebruiks voorwaarden voor de site. 

    e. Selecteer boven in het deel venster **ophalen** voor de methode, selecteer **beta** voor de versie en voer in het vak query een van de volgende handelingen uit: 
    
    * Gebruik om alle service-principals van uw Tenant op te halen `https://graph.microsoft.com/beta/servicePrincipals` . 
    * Gebruik `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` , dat uw primaire domein bevat, als u meerdere directory's gebruikt.

    ![Scherm afbeelding van het deel venster Microsoft Graph Explorer-query voor aanvraag tekst.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Ga in de lijst met Service-principals naar het account dat u wilt wijzigen. 
    
    U kunt de toepassing ook zoeken naar alle vermelde service-principals door op CTRL + F te drukken. Als u een specifieke Service-Principal wilt ophalen, neemt u in de query de object-ID van de Service-Principal op die u eerder hebt gekopieerd in het deel venster met Azure AD-eigenschappen, zoals hier wordt weer gegeven:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Scherm opname van een Service-Principal-query die de object-ID bevat.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extraheer de eigenschap appRoles uit het service-principalobject.

    ![Scherm afbeelding van de code voor het extra heren van de eigenschap appRoles van het Service-Principal-object.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Nu moet u nieuwe rollen genereren voor uw toepassing. 

    i. De volgende JSON-code is een voor beeld van een appRoles-object. Maak een vergelijkbaar object om de rollen toe te voegen die u voor uw toepassing wilt.

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
    > U kunt pas nieuwe rollen toevoegen nadat u *msiam_access* voor de patch bewerking hebt toegevoegd. U kunt ook zoveel functies toevoegen als u wilt, afhankelijk van de behoeften van uw organisatie. Azure AD verzendt de *waarde* van deze rollen als de claim waarde in het SAML-antwoord.

    j. Wijzig in Microsoft Graph Explorer de methode van **down load** to **patch**. Patch het Service-Principal-object met de gewenste rollen door de eigenschap appRoles bij te werken, zoals in het vorige voor beeld. Selecteer **query uitvoeren** om de patch bewerking uit te voeren. Met een geslaagd bericht wordt het maken van de rol voor uw AWS-toepassing bevestigd.

      ![Scherm afbeelding van het deel venster Microsoft Graph Explorer, waarbij de methode is gewijzigd in PATCH.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Wanneer de Service-Principal is bijgewerkt met meer rollen, kunt u gebruikers en groepen toewijzen aan hun respectieve rollen. U kunt dit doen in de Azure Portal door naar de toepassing AWS te gaan en vervolgens het tabblad **gebruikers en groepen** bovenaan te selecteren.

1. We raden u aan om voor elke AWS-rol een nieuwe groep te maken, zodat u die specifieke rol in de groep kunt toewijzen. Deze een-op-een-toewijzing houdt in dat één groep wordt toegewezen aan één rol. U kunt vervolgens de leden toevoegen die bij die groep horen.

1. Nadat u de groepen hebt gemaakt, selecteert u de groep en wijst u deze toe aan de toepassing.

    ![Scherm afbeelding van het deel venster gebruikers en groepen.](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Geneste groepen worden niet ondersteund wanneer u groepen toewijst.

1. Als u de rol aan de groep wilt toewijzen, selecteert u de rol en selecteert u vervolgens **toewijzen**.

    ![Scherm afbeelding van het deel venster toewijzing toevoegen.](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Nadat u de functies hebt toegewezen, kunt u deze bekijken door uw Azure Portal-sessie te vernieuwen.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van micro soft mijn apps.

Wanneer u de tegel **AWS** in mijn apps selecteert, wordt de pagina AWS-toepassing geopend met een optie om de rol te selecteren.

![Scherm afbeelding van de AWS-pagina voor het testen van SSO.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

U kunt ook de SAML-reactie verifiëren om de rollen te bekijken die als claims worden doorgevoerd.

![Scherm opname van het SAML-antwoord.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Zie voor meer informatie over mijn apps [Aanmelden en apps starten vanuit de portal mijn apps](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Volgende stappen

Nadat u AWS hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor de exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. Zie [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad) voor meer informatie.

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
