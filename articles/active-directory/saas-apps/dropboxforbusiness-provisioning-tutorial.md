---
title: 'Zelfstudie: Dropbox for Business configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Dropbox for Business.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 04d17e17ef11696efd52f04ea83639f2a9b81fea
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938746"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Zelfstudie: Dropbox for Business configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Dropbox for Business en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Dropbox for Business.

> [!IMPORTANT]
> Microsoft en Dropbox schaffen de oude Dropbox-integratie af met ingang van 04/01/2021. Om onderbrekingen van de service te voorkomen, raden we u aan de nieuwe Dropbox-integratie, die groepen ondersteunt, te migreren. Als u wilt migreren naar de nieuwe Dropbox-integratie, kunt u een nieuw exemplaar van Dropbox voor het inrichten in uw Azure AD-tenant toevoegen en configureren met behulp van de onderstaande stappen. Wanneer u de nieuwe Dropbox-integratie hebt geconfigureerd, schakelt u inrichten voor de oude Dropbox-integratie uit om inrichtingsconflicten te voorkomen. Zie [Bijwerken naar de nieuwste Dropbox for Business-toepassing met behulp van Azure AD](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector) voor meer gedetailleerde stappen voor het migreren naar de nieuwe Dropbox-integratie.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Dropbox for Business-tenant](https://www.dropbox.com/business/pricing)
* Een gebruikersaccount in Dropbox for Business met beheerdersmachtigingen.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dropbox for Business toevoegen vanuit de galerie

Voordat u Dropbox for Business configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Dropbox for Business vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Dropbox for Business toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven in het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Dropbox voor Bedrijven** in het zoekvak in, selecteer **Dropbox voor Bedrijven** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Dropbox voor Bedrijven in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Gebruikers toewijzen aan Dropbox for Business

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Dropbox for Business. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Dropbox for Business toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Belangrijke tips voor het toewijzen van gebruikers aan Dropbox for Business

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan Dropbox for Business om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Dropbox for Business toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Automatische inrichting van gebruikers configureren voor Dropbox for Business 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Dropbox for Business te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Dropbox for Business, waarvoor u de instructies in de [Zelfstudie Eenmalige aanmelding voor Dropbox for Business](dropboxforbusiness-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Dropbox for Business in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Dropbox for Business** in de lijst met toepassingen.

    ![De koppeling Dropbox voor Bedrijven in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Klik onder de sectie **Referenties voor beheerder** op **Autoriseren**. Er wordt een dialoogvenster voor aanmelding bij Dropbox for Business geopend in een nieuw browservenster.

    ![Inrichten ](common/provisioning-oauth.png)

6. Meld u aan bij uw Dropbox for Business-tenant en controleer uw identiteit op het dialoogvenster **Aanmelden bij Dropbox for Business om te koppelen met Azure AD**.

    ![Aanmelden bij Dropbox for Business](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Klik na het voltooien van stap 5 en 6 op  **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Dropbox for Business. Als de verbinding mislukt, controleert u of het Dropbox for Business-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Token](common/provisioning-testconnection-oauth.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Dropbox for Business**.

    ![Gebruikerstoewijzingen voor Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Dropbox for Business worden gesynchroniseerd. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Dropbox te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikerskenmerken van Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Dropbox**.

    ![Groepstoewijzingen voor Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die worden gesynchroniseerd vanuit Azure AD naar Dropbox. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Dropbox te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Groepskenmerken van Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Dropbox.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die u aan Dropbox wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice in Dropbox worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen
 
* Dropbox biedt geen ondersteuning voor het tijdelijk intrekken van toegang van uitgenodigde gebruikers. Als de toegang van een uitgenodigde gebruiker wordt ingetrokken, wordt die gebruiker verwijderd.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

