---
title: 'Zelfstudie: Wrike configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten in Wrike, of de inrichting van gebruikersaccounts ongedaan te maken.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 53b1db1a8c4da59055c0af5f448fa0c8a6933daf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95988090"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Zelfstudie: Wrike configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die u uitvoert in Wrike en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers of groepen voor Wrike.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar software-as-a-service (SaaS)-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in Openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Wrike-tenant](https://www.wrike.com/price/)
* Een gebruikersaccount in Wrike met beheerdersmachtigingen

## <a name="assign-users-to-wrike"></a>Gebruikers toewijzen aan Wrike
Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, beslist u welke gebruikers of groepen in Azure AD toegang nodig hebben tot Wrike. Vervolgens kunt u deze gebruikers of groepen aan Wrike toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Belangrijke tips voor het toewijzen van gebruikers aan Wrike

* We raden u aan om één Azure AD-gebruiker toe te wijzen aan Wrike om de configuratie van automatische inrichting van gebruikers te testen. Extra gebruikers of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Wrike toewijst, moet u een geldige toepassingsspecifieke rol toewijzen (indien beschikbaar) in het toewijzingsdialoogvenster. Gebruikers met de rol Standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-wrike-for-provisioning"></a>Wrike instellen voor inrichting

Voordat u Wrike configureert voor automatische inrichting van gebruikers met Azure AD, moet u inrichten met System for Cross-domain Identity Management (SCIM) inschakelen op Wrike.

1. Meld u aan bij de [Wrike-beheerconsole](https://www.Wrike.com/login/). Ga naar uw tenant-id. Selecteer **Apps en integraties**.

    ![Apps en integraties](media/Wrike-provisioning-tutorial/admin.png)

2.  Ga naar **Azure AD** en selecteer dit.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selecteer SCIM. Kopieer de **Basis-URL**.

    ![Basis-URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selecteer **API** > **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Er wordt een pop-upitem geopend. Voer het wachtwoord in dat u eerder hebt gemaakt om een account te creëren.

    ![Token maken in Wrike](media/Wrike-provisioning-tutorial/password.png)

6.  Kopieer het **Token voor geheim** en plak het in Azure AD. Selecteer **Opslaan** om de inrichtingsconfiguratie in Wrike te voltooien.

    ![Token voor permanente toegang](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Wrike toevoegen vanuit de galerie

Voordat u Wrike gaat configureren voor het automatisch inrichten van gebruikers met Azure AD, voegt u Wrike vanuit de Azure AD-toepassingsgalerie toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Wrike toe te voegen vanuit de Azure AD-toepassingsgalerie.

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiedeelvenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven in het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Wrike** in het zoekvak in, selecteer **Wrike** in het resultatenvenster en selecteer vervolgens **Toevoegen** om de toepassing toe te voegen.

    ![Wrike in de lijst met resultaten](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Automatische inrichting van gebruikers configureren voor Wrike 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers of groepen in Wrike te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> Als u eenmalige aanmelding op basis van SAML wilt inschakelen voor Wrike, volgt u de instructies in de [zelfstudie voor eenmalige aanmelding bij Wrike](wrike-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Wrike in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Alle toepassingen](common/enterprise-applications.png)

2. Selecteer **Wrike** in de lijst met toepassingen.

    ![De koppeling naar Wrike in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. In de sectie Referenties voor beheerder voert u de waarden van **Basis-URL** en **Token voor permanente toegang** in die eerder zijn opgehaald uit respectievelijk **Tenant-URL** en **Token voor geheim**. Selecteer **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Wrike. Als de verbinding mislukt, moet u controleren of uw Wrike-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

8. Selecteer **Opslaan**.

9. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Wrike**.

    ![Gebruikerstoewijzingen voor Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Wrike worden gesynchroniseerd. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Wrike te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

    ![Kenmerken van Wrike-gebruikers](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Volg de instructies in de [zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

12. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Wrike.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers of groepen die u in Wrike wilt inrichten door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

14. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij volgende synchronisaties. Voor meer informatie over hoe lang het duurt om gebruikers of groepen in te richten, zie [Hoe lang duurt het inrichten van gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

U kunt de sectie **Huidige status** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Wrike worden uitgevoerd. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie. Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) als u de Azure AD-inrichtingslogboeken wilt lezen.

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)