---
title: 'Zelfstudie: Symantec Web Security Service (WSS) configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Symantec Web Security Service (WSS).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: d7e0db1b0bc1e7aef68ee06f3bdd5e5e0f83b73e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354696"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Zelfstudie: Symantec Web Security Service (WSS) configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Symantec Web Security Service (WSS) en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Symantec Web Security Service (WSS).

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Symantec Web Security Service-tenant (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Een gebruikersaccount in Symantec Web Security Service (WSS) met beheerdersmachtigingen.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Gebruikers toewijzen aan Symantec Web Security Service (WSS)

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Symantec Web Security Service (WSS). Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Symantec Web Security Service (WSS) toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Belangrijke tips voor het toewijzen van gebruikers aan Symantec Web Security Service (WSS)

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Symantec Web Security Service (WSS) om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Symantec Web Security Service (WSS) toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Symantec Web Security Service (WSS) instellen voor inrichting

Voordat u Symantec Web Security Service (WSS) configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in Symantec Web Security Service (WSS).

1. Meld u aan bij de [beheerconsole van Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Ga naar **Solutions** > **Service**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/service.png)

2. Ga naar **Account Maintenance** > **Integrations** > **New Integration**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selecteer **Third-Party Users & Groups Sync**. 

    ![Schermopname van de optie Third-Party Users & Groups Sync.](media/symantec-web-security-service/third-party-users.png)

4.  Kopieer de waarden van **SCIM-URL** en **Token**. Deze waarden worden ingevoerd in de velden **Tenant-URL** en **Token voor geheim** op het tabblad Inrichten van uw Symantec Web Security Service-toepassing (WSS) in Azure Portal.

    ![Schermopname van het dialoogvenster New Integration met de tekstvakken SCIM-URL en Token gemarkeerd.](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security Service (WSS) toevoegen vanuit de galerie

Als u Symantec Web Security Service (WSS) wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Symantec Web Security Service (WSS) vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Symantec Web Security Service (WSS) toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het paneel.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Symantec Web Security Service (WSS)** in het zoekvak in, selecteer **Symantec Web Security Service (WSS)** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

    ![Symantec Web Security Service (WSS) toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Automatische inrichting van gebruikers configureren voor Symantec Web Security Service (WSS)

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Symantec Web Security Service (WSS) te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor Symantec Web Security-service (WSS). Volg hiertoe de instructies in de [zelfstudie over eenmalige aanmelding voor Symantec Web Security Service (WSS)](symantec-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Symantec Web Security Service (WSS) in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Symantec Web Security Service (WSS)** in de lijst met toepassingen.

    ![De koppeling Symantec Web Security Service (WSS) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. In het gedeelte Referenties voor beheerder voert u de waarden voor **SCIM-URL** en **Toegangstoken** in die eerder die eerder zijn opgehaald uit respectievelijk **Tenant-URL** en **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Symantec Web Security Service. Als de verbinding mislukt, moet u controleren of uw Symantec Web Security Service-account (WSS) beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Symantec Web Security Service (WSS)** .

    ![Schermopname van de sectie Toewijzingen met de optie Azure Active Directory-gebruikers synchroniseren met Symantec Web Security Service (WSS) gemarkeerd.](media/symantec-web-security-service/usermapping.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Symantec Web Security Service (WSS) worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Symantec Web Security Service (WSS)e te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Schermopname van de sectie Kenmerktoewijzingen waarin 16 overeenkomende eigenschappen worden weergegeven.](media/symantec-web-security-service/userattribute.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Symantec Web Security Service**.

    ![Schermopname van de sectie Toewijzingen met de optie Azure Active Directory-groepen synchroniseren met Symantec Web Security Service (WSS) gemarkeerd.](media/symantec-web-security-service/groupmapping.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Symantec Web Security Service (WSS) worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Symantec Web Security Service (WSS)e te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Schermopname van de sectie Kenmerktoewijzingen waarin drie overeenkomende eigenschappen worden weergegeven.](media/symantec-web-security-service/groupattribute.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Symantec Web Security Service.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Symantec Web Security Service wilt toevoegen door de gewenste waarden in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij volgende synchronisaties. Zie [Hoe lang duurt het inrichten van gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) voor meer informatie over hoe lang het duurt om gebruikers en/of groepen in te richten.

U kunt het gedeelte **Huidige status** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice in Symantec Web Security Service worden uitgevoerd. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie. Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) als u de Azure AD-inrichtingslogboeken wilt lezen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
