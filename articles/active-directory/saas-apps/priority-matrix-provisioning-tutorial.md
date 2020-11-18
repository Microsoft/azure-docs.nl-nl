---
title: 'Zelfstudie: Priority Matrix configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Priority Matrix.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: e79f21300325c6b451dd564bf2c69830f003f55c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357858"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Zelfstudie: Priority Matrix configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Priority Matrix en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Priority Matrix.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in Openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Priority Matrix-tenant](https://appfluence.com/pricing/)
* Een gebruikersaccount op Priority Matrix met beheerdersmachtigingen.

## <a name="assign-users-to-priority-matrix"></a>Gebruikers toewijzen aan Priority Matrix

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Priority Matrix. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Priority Matrix toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Belangrijke tips voor het toewijzen van gebruikers aan Priority Matrix

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Priority Matrix om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Priority Matrix toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-priority-matrix-for-provisioning"></a>Priority Matrix instellen voor inrichting

Voordat u Priority Matrix configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtingsgegevens ophalen uit Priority Matrix.

1. Meld u aan bij de [Priority Matrix-beheerconsole](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Klik op **OAuth-aanmeldingstoken** voor Priority Matrix

    ![Priority Matrix - SCIM toevoegen](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Klik op de knop **Nieuw token ophalen**. Kopieer de **tokentekenreeks**. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad Inrichten van uw Priority Matrix-toepassing in Azure Portal. 

    ![Priority Matrix - token maken](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Priority Matrix toevoegen vanuit de galerie

Als u Priority Matrix wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Priority Matrix vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven in het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. In het zoekvak voert u **Priority Matrix** in en selecteert u **Priority Matrix** in het resultatenvenster. 

    ![Priority Matrix in de lijst met resultaten](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor Priority Matrix** , waarmee u naar de aanmeldingspagina van Priority Matrix wordt omgeleid. 

    ![Priority Matrix - OIDC toevoegen](media/priority-matrix-provisioning-tutorial/signup.png)

6. Aangezien Priority Matrix een OpenIDConnect-app is, kiest u aanmelden bij Priority Matrix met uw Microsoft-werkaccount.

    ![Priority Matrix - OIDC-aanmelding](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Wanneer de verificatie is voltooid, accepteert u toestemming op de toestemmingspagina. De toepassing wordt vervolgens automatisch toegevoegd aan uw tenant en u wordt omgeleid naar uw Priority Matrix-account.

    ![Priority Matrix - OIDC-toestemming](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Automatische gebruikersinrichting configureren voor Priority Matrix 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Priority Matrix te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!NOTE]
> Raadpleeg [User provisioning and Priority Matrix](https://appfluence.com/help/article/user-provisioning/) (Gebruikersinrichting en Priority Matrix) voor meer informatie over het SCIM-eindpunt van Priority Matrix.

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Priority Matrix in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Priority Matrix** in de lijst met toepassingen.

    ![De koppeling naar Priority Matrix in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://sync.appfluence.com/scim/v2/` in **Tenant-URL** in. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen in Priority Matrix in **Token voor geheim**. Klik in Azure Portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Priority Matrix. Als de verbinding mislukt, moet u controleren of uw Priority Matrix-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Priority Matrix**.

    ![Priority Matrix-gebruikerstoewijzingen](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Priority Matrix worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Priority Matrix te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Priority Matrix-gebruikerskenmerken](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Priority Matrix.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan Priority Matrix wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Priority Matrix worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)


