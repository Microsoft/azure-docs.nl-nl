---
title: 'Zelfstudie: Soloinsight-CloudGate SSO configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Soloinsight-CloudGate SSO.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 5c42102c0704d7212411d6b86f4210ad8ecd885c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96347494"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Zelfstudie: Soloinsight-CloudGate SSO configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in Soloinsight-CloudGate SSO en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Soloinsight-CloudGate SSO.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Soloinsight-CloudGate SSO-tenant](https://www.soloinsight.com/)
* Een gebruikersaccount in Soloinsight-CloudGate SSO met beheerdersmachtigingen.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Gebruikers toewijzen aan Soloinsight-CloudGate SSO

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Soloinsight-CloudGate SSO. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Soloinsight-CloudGate SSO toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Belangrijke tips voor het toewijzen van gebruikers aan Soloinsight-CloudGate SSO

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan Soloinsight-CloudGate SSO om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Soloinsight-CloudGate SSO toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het venster voor toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Soloinsight-CloudGate SSO instellen voor inrichting

1. Meld u aan bij de [beheerconsole van Soloinsight-CloudGate SS](https://soloinsight.sigateway.com/login). Ga naar **Beheer > Systeeminstellingen**.

    ![Beheerconsole van Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Ga naar **Algemeen**.

    ![SCIM toevoegen voor Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Schuif omlaag naar het einde van de pagina om de waarden voor **Tenant-URL** en **Token voor geheim** op te halen. Kopieer de waarde van **Token voor geheim**. Deze waarde wordt ingevoerd in het veld Token voor geheim op het tabblad Inrichten van de Soloinsight-CloudGate SSO-toepassing in de Azure-portal.

    ![Token maken voor Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO toevoegen vanuit de galerie

Voordat u Soloinsight-CloudGate SSO configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Soloinsight-CloudGate SSO vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Soloinsight-CloudGate SSO toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Soloinsight-CloudGate SSO** in het zoekvak in, selecteer **Soloinsight-CloudGate SSO** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Soloinsight-CloudGate SSO in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Automatische gebruikersinrichting voor Soloinsight-CloudGate SSO configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in Soloinsight-CloudGate SSO, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Soloinsight-CloudGate SSO, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor Soloinsight-CloudGate SSO](./soloinsight-cloudgate-sso-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Soloinsight-CloudGate SSO in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Soloinsight-CloudGate SSO**.

    ![De koppeling voor Soloinsight-CloudGate SSO in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://sigateway.com/scim/v2/sync/serviceproviderconfig` in bij **Tenant-URL**. Voer de waarde van het **SCIM-verificatietoken** in die eerder in **Token voor geheim** is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Soloinsight-CloudGate SSO. Als de verbinding mislukt, moet u controleren of uw Soloinsight-CloudGate SSO-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Soloinsight-CloudGate SSO**.

    ![Soloinsight-CloudGate SSO-gebruikerstoewijzingen](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Soloinsight-CloudGate SSO worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Soloinsight-CloudGate SSO te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Soloinsight-CloudGate SSO-gebruikerskenmerken](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikersgroepen synchroniseren met Soloinsight-CloudGate SSO**.

    ![Soloinsight-CloudGate SSO-groepstoewijzingen](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Soloinsight-CloudGate SSO worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Soloinsight-CloudGate SSO te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Soloinsight-CloudGate SSO-groepskenmerken](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Soloinsight-CloudGate SSO.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Soloinsight-CloudGate SSO wilt toevoegen door de gewenste waarden in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Soloinsight-CloudGate SSO worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)