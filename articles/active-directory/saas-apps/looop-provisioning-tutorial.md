---
title: 'Zelfstudie: Looop configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten in Looop, of de inrichting van gebruikersaccounts ongedaan te maken.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835013"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Zelfstudie: Looop configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in Looop en Azure AD (Azure Active Directory) om automatisch gebruikers en/of groepen van Azure AD in te richten in Looop, of de inrichting ervan ongedaan te maken.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Looop-tenant](https://www.looop.co/pricing/)
* Een gebruikersaccount in Looop met beheerdersmachtigingen.

## <a name="assign-users-to-looop"></a>Gebruikers toewijzen aan Looop

Azure AD gebruikt een concept met de naam Toewijzingen, om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Looop. Eenmaal besloten, kunt u deze gebruikers toewijzen aan de Looop-app door deze instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Belangrijke tips voor het toewijzen van gebruikers aan Looop

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Looop om de configuratie van de automatische inrichting van gebruikers te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker toewijst aan Looop, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoogvenster Toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-looop-for-provisioning"></a>Looop instellen voor inrichting

Voordat u Looop configureert voor automatische inrichting van gebruikers met Azure AD, moet u bepaalde inrichtingsgegevens ophalen bij Looop.

1. Meld u aan bij de [Looop-beheerconsole](https://app.looop.co/#/login) en selecteer **Account**. Selecteer bij **Accountinstellingen** de optie **Verificatie**.

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Schermopname van de Looop-beheerconsole. Het tabblad Account is gemarkeerd en open. Bij Accountinstellingen is de optie Verificatie gemarkeerd." border="false":::

2. Genereer een nieuw token door bij **SCIM-integratie** te klikken op **Token opnieuw instellen**.

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Schermopname van de sectie SCIM-integratie van een pagina in de Looop-beheerconsole. De knop Token opnieuw instellen is gemarkeerd." border="false":::

3. Kopieer het **SCIM-eindpunt** en het **Token**. Deze waarden worden ingevoerd in de velden **Tenant-URL** en **Token voor geheim** op het tabblad Inrichten van de Looop-toepassing in Azure Portal. 

    ![Token maken in Looop](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Looop toevoegen vanuit de galerie

Als u Looop wilt configureren voor automatische inrichting van gebruikers met Azure AD, moet u Looop vanuit de Azure AD-toepassingsgalerie toevoegen aan de lijst met beheerde SaaS-toepassingen.

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Looop** in en selecteer **Looop** in het resultatenpaneel. 

    ![Looop in de resultatenlijst](common/search-new-app.png)

5. Selecteer de knop **Registreren voor Looop** om naar de aanmeldingspagina van Looop te gaan. 

    ![Looop OIDC toevoegen](media/looop-provisioning-tutorial/signup.png)

6. Aangezien Looop een OpenIDConnect-app is, meldt u zich aan bij Looop met uw Microsoft-werkaccount.

    ![Looop OIDC-aanmelding](media/looop-provisioning-tutorial/msftlogin.png)

7. Wanneer de verificatie is voltooid, accepteert u de instemmingsprompt op de instemmingspagina. De toepassing wordt vervolgens automatisch toegevoegd aan de tenant, en u wordt doorgestuurd naar uw Looop-account.

    ![Looop OIDC-instemming](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Automatische inrichting van gebruiker configureren in Looop 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in Looop, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Looop in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Looop** in de lijst met toepassingen.

    ![De Looop-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://<organisation_domain>.looop.co/scim/v2` in bij **Tenant-URL**. Bijvoorbeeld `https://demo.looop.co/scim/v2`. Voer de waarde die u eerder hebt opgehaald uit Looop en opgeslagen, in bij **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Looop. Als de verbinding mislukt, controleert u of het Looop-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Looop**.

    ![Gebruikerstoewijzingen in Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met Looop. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Looop te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |externalId|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:area|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_1|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_2|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_3|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:employee_id|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:location|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:position|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:startAt|Tekenreeks|

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Meta Networks Connector**.

    ![Groepstoewijzingen in Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die zijn gesynchroniseerd vanuit Azure AD naar Meta Networks Connector. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Meta Networks Connector te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    |Kenmerk|Type|Ondersteund voor filteren|
    |---|---|---|
    |displayName|Tekenreeks|&check;|
    |leden|Naslaginformatie|
    |externalId|Tekenreeks|


10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor Looop.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt toevoegen aan Looop, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit, waarin alle acties worden beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in Looop.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)


