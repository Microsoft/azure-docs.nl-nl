---
title: 'Zelfstudie: BlueJeans configureren voor automatische gebruikersinrichting met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 204cdc689d5a117df428bb314a81a35081f7b13c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357637"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Zelfstudie: BlueJeans configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in BlueJeans en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor BlueJeans.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over het volgende:

* Een Azure AD-tenant
* Een BlueJeans-tenant waarop het abonnement [Mijn bedrijf](https://www.BlueJeans.com/pricing) of een beter abonnement is ingeschakeld
* Een gebruikersaccount in BlueJeans met beheerdersmachtigingen

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [BlueJeans-API](https://BlueJeans.github.io/developer), die beschikbaar is voor BlueJeans-teams met een Standard-abonnement of een beter abonnement.

## <a name="adding-bluejeans-from-the-gallery"></a>BlueJeans toevoegen vanuit de galerie

Voordat u BlueJeans configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u BlueJeans vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om BlueJeans toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven in het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **BlueJeans** in het zoekvak in, selecteer **BlueJeans** in het resultatenvenster en selecteer de knop **Toevoegen** om de toepassing toe te voegen.

    ![BlueJeans in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Gebruikers toewijzen aan BlueJeans

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot BlueJeans. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan BlueJeans toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Belangrijke tips voor het toewijzen van gebruikers aan BlueJeans

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan BlueJeans om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan BlueJeans toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Automatische gebruikersinrichting voor BlueJeans configureren

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlueJeans te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor BlueJeans, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor BlueJeans](bluejeans-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Om automatische gebruikersinrichting te configureren voor BlueJeans in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer **BlueJeans**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **BlueJeans** in de lijst met toepassingen.

    ![De koppeling BlueJeans in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de zijbalk van de bedrijfstoepassing BlueJeans met de optie Inrichten gemarkeerd.](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de pagina Inrichten met de secties Inrichtingsmodus en Referenties voor beheerder gemarkeerd.](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Voer in de sectie **Referenties voor beheerder** de **gebruikersnaam** en het **wachtwoord** van de beheerder van uw BlueJeans-account in. Voorbeelden van deze waarden zijn:

   * Vul in het vak **Gebruikersnaam van de beheerder** de gebruikersnaam van het beheerdersaccount in op uw BlueJeans-tenant. Bijvoorbeeld: admin@contoso.com.

   * Vul in het veld **Beheerderswachtwoord** het wachtwoord in dat overeenkomt met de gebruikersnaam van de beheerder.

6. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met BlueJeans. Als de verbinding mislukt, moet u controleren of uw BlueJeans-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname van de sectie Referenties voor beheerder met de optie Verbinding testen gemarkeerd.](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![Schermopname van het tekstvak E-mailadres voor meldingen.](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met BlueJeans**.

    ![Schermopname van de sectie Toewijzingen met de optie Azure Active Directory-gebruikers synchroniseren met BlueJeans gemarkeerd.](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met BlueJeans worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in BlueJeans te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Schermopname van de sectie Kenmerktoewijzingen met zeven toewijzingen weergegeven.](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor BlueJeans.

    ![Schermopname van de sectie Instellingen waar de optie Inrichtingsstatus is ingesteld op Aan.](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definieer de gebruikers en/of groepen die u aan BlueJeans wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Schermopname van de instelling Bereik met de optie Alleen toegewezen gebruikers en groepen synchroniseren gemarkeerd.](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Schermopname van de zijbalk van de bedrijfstoepassing BlueJeans met de optie Opslaan gemarkeerd.](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op BlueJeans worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Bluejeans staat het gebruik van gebruikersnamen van meer dan 30 tekens niet toe.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
