---
title: 'Zelfstudie: Meta Networks Connector configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Meta Networks Connector.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: b6a8f192cd26639431cc9fcb6b43e1bc5e8e2843
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353626"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Zelfstudie: Meta Networks Connector configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Meta Networks Connector en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Meta Networks Connector.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Meta Networks Connector-tenant](https://www.metanetworks.com/)
* Een gebruikersaccount in Meta Networks Connector met beheerdersmachtigingen.

## <a name="assigning-users-to-meta-networks-connector"></a>Gebruikers toewijzen aan Meta Networks Connector

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Meta Networks Connector. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Meta Networks Connector toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Belangrijke tips voor het toewijzen van gebruikers aan Meta Networks Connector

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Meta Networks Connector om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

* Als u een gebruiker aan Meta Networks Connector toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Meta Networks Connector instellen voor inrichting

1. Meld u aan bij [Meta Networks Connector-beheerconsole](https://login.metanetworks.com/login/) met uw organisatienaam. Navigeer naar **Beheer > API-sleutels**.

    ![Meta Networks Connector-beheerconsole](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Klik op het plusteken in de rechterbovenhoek van het scherm om een nieuwe **API-sleutel** te maken.

    ![Meta Networks Connector-pluspictogram](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Stel de **API-sleutelnaam** en de **API-sleutelbeschrijving** in.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="Schermopname van de Meta Networks Connector-beheerconsole met gemarkeerde API-sleutelnaam en API-sleutelbeschrijving van Azure AD en API-sleutel." border="false":::

4.  Schakel **Schrijf**-bevoegdheden in voor **Groepen** en **Gebruikers**.

    ![Meta Networks Connector-bevoegdheden](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Klik op **Toevoegen**. Kopieer het **GEHEIM** en sla het op omdat dit de enige keer is dat u het kunt bekijken. Deze waarde wordt ingevoerd in het veld Token voor geheim op het tabblad Inrichten van uw Meta Networks Connector-toepassing in Azure Portal.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="Schermopname van een venster waarin gebruikers wordt verteld dat de API-sleutel is toegevoegd. Het vak Geheim bevat een onleesbare waarde en is gemarkeerd." border="false":::

6.  Voeg een IdP toe door te navigeren naar **Beheer > Instellingen > IdP > Nieuwe maken**.

    ![Meta Networks Connector IdP toevoegen](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Op de pagina **IdP-configuratie** kunt u uw IdP-configuratie een **naam** geven en een **Pictogram** kiezen.

    ![Meta Networks Connector IdP-naam](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Meta Networks Connector IdP-pictogram](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Onder **SCIM configureren** selecteert u de naam van de API-sleutel die u in de vorige stappen hebt gemaakt. Klik op **Opslaan**.

    ![Meta Networks Connector SCIM configureren](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Ga naar **Beheer > Instellingen > tabblad IdP**. Klik op de naam van de IdP-configuratie die in de vorige stappen is gemaakt om de **IdP-id** weer te geven. Deze **ID** wordt toegevoegd aan het einde van de **Tenant-URL** terwijl u de waarde in het veld **Tenant-URL** invoert op het tabblad Inrichten van uw Meta Networks Connector-toepassing in de Azure Portal.

    ![Meta Networks Connector IdP-id](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Meta Networks Connector toevoegen vanuit de galerie

Voordat u Meta Networks Connector configureert voor het automatisch inrichten van gebruikers met Azure AD, moet Meta Networks Connector vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Meta Networks Connector toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Meta Networks Connector** in het zoekvak in, selecteer **Meta Networks Connector** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Meta Networks Connector in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Automatische gebruikersinrichting configureren voor Meta Networks Connector 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Meta Networks Connector te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Meta Networks Connector, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor Meta Networks Connector](./metanetworksconnector-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, ook al vullen deze twee functies elkaar aan

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Meta Networks Connector in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Meta Networks Connector** in de lijst met toepassingen.

    ![De koppeling Meta Networks Connector in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://api.metanetworks.com/v1/scim/<IdP ID>` in bij **Tenant-URL**. Voer de waarde van het **SCIM-verificatietoken** in die eerder in **Token voor geheim** is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Meta Networks Connector. Als de verbinding mislukt, moet u controleren of uw Meta Networks Connector-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Meta Networks Connector**.

    ![Meta Networks Connector-gebruikerstoewijzingen](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Meta Networks Connector worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Meta Networks Connector te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Meta Networks Connector-gebruikerskenmerken](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Meta Networks Connector**.

    ![Meta Networks Connector-groepstoewijzingen](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Meta Networks Connector worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Meta Networks Connector te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Meta Networks Connector-groepskenmerken](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Meta Networks Connector.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Meta Networks Connector wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Meta Networks Connector worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)