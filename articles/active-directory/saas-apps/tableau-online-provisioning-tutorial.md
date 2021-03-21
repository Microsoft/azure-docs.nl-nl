---
title: 'Zelfstudie: Tableau Online configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Tableau Online.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359201"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Zelfstudie: Tableau Online configureren voor het automatisch inrichten van gebruikers

In deze zelfstudie worden de stappen gedemonstreerd die moeten worden uitgevoerd in Tableau Online en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en groepen voor Tableau Online.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie voor informatie over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar software-as-a-service (SaaS)-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u het volgende heeft:

*   Een Azure AD-tenant.
*   Een [Tableau Online-tenant](https://www.tableau.com/).
*   Een gebruikersaccount in Tableau Online met beheerdersmachtigingen.

> [!NOTE]
> De integratie van de Azure AD-inrichting is afhankelijk van de [Tableau Online-Rest-API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Deze API is beschikbaar voor Tableau Online-ontwikkelaars.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Tableau Online toevoegen vanuit Azure Marketplace
Voordat u Tableau Online configureert voor het automatisch inrichten van gebruikers met Azure AD, voegt u Tableau Online vanuit de Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Tableau Online toe te voegen vanuit de Marketplace.

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiemenu aan de linkerkant.

    ![Het pictogram van Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Tableau Online** in en selecteer **Tableau Online** in het deelvenster voor resultaten. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Tableau Online in de lijst met resultaten](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Gebruikers toewijzen aan Tableau Online

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, beslist u welke gebruikers of groepen in Azure AD toegang nodig hebben tot Tableau Online. Volg de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md) als u deze gebruikers of groepen wilt toewijzen aan Tableau Online.

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Belangrijke tips voor het toewijzen van gebruikers aan Tableau Online

*   We raden u aan om één Azure AD-gebruiker toe te wijzen aan Tableau Online om de configuratie van automatische inrichting van gebruikers te testen. U kunt later aanvullende gebruikers of groepen toewijzen.

*   Als u een gebruiker aan Tableau Online toewijst, selecteert u een geldige toepassingsspecifieke rol (indien beschikbaar) in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Automatische gebruikersinrichting configureren voor Tableau Online

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice. Gebruik dit om gebruikers of groepen in Tableau Online te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor Tableau Online. Volg de instructies in de [Zelfstudie voor eenmalige aanmelding voor Tableau Online](tableauonline-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Tableau Online in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen** > **Tableau Online**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Tableau Online** in de lijst met toepassingen.

    ![De link naar Tableau Online in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tableau Online inrichten](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Inrichtingsmodus van Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Voer in de sectie **Beheerdersreferenties** het domein, de gebruikersnaam van de beheerder, het beheerderswachtwoord en de inhouds-URL van uw Tableau Online-account in:

   * Vul in het vak **Domein** het subdomein in op basis van stap 6.

   * Vul in het vak **Gebruikersnaam van de beheerder** de gebruikersnaam van het beheerdersaccount in op uw Tableau Online-tenant. Een voorbeeld is admin@contoso.com.

   * Vul in het vak **Beheerderswachtwoord** het wachtwoord in van het beheerdersaccount dat overeenkomt met de gebruikersnaam van de beheerder.

   * Vul in het vak **Inhouds-URL** het subdomein in op basis van stap 6.

6. Nadat u zich hebt aangemeld bij uw beheerdersaccount voor Tableau Online, kunt u de waarden voor **Domein** en **Inhouds-URL** ophalen van de URL van de beheerpagina.

    * Het **Domein** voor uw Tableau Online-account kan vanuit dit deel van de URL worden gekopieerd:

        ![Domein van Tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * De **Inhouds-URL** voor uw Tableau Online-account kan vanuit dit deel worden gekopieerd. Het is een waarde die tijdens het instellen van het account is gedefinieerd. In dit voorbeeld is de waarde 'contoso':

        ![Inhouds-URL van Tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Uw **Domein** kan afwijken van wat hier wordt weergegeven.

7. Nadat u de vakken die in stap 5 zijn weergegeven, hebt ingevuld, selecteert u **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Tableau Online. Als de verbinding mislukt, moet u controleren of uw Tableau Online-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Verbinding van Tableau Online testen](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen over inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![E-mailadres voor meldingen van Tableau Online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Tableau**.

    ![Gebruikerssynchronisatie van Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Tableau Online worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Tableau Online te vinden voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen op te slaan.

    ![Overeenkomende gebruikerskenmerken van Tableau Online](./media/tableau-online-provisioning-tutorial/attribute.png)

12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Tableau Online**.

    ![Groepssynchronisatie van Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Tableau Online worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Tableau Online te vinden voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen op te slaan.

    ![Overeenkomende groepskenmerken van Tableau online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Volg de instructies in [de zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

15. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Tableau Online.

    ![Inrichtingsstatus van Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. De gebruikers of groepen die u wilt inrichten voor Tableau Online definiëren. Selecteer in de sectie **Instellingen** de waarden die u wilt in **Bereik**.

    ![Bereik voor Tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Opslaan voor Tableau Online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij latere synchronisaties. Ze vinden ongeveer elke 40 minuten plaats, zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en links naar het rapport over de inrichtingsactiviteit te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD-inrichtingsservice op Tableau Online.

Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="change-log"></a>Wijzigingenlogboek
* 09/30/2020 - Ondersteuning toegevoegd voor kenmerk 'authSetting' voor gebruikers.

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png