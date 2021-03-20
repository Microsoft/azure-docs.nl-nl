---
title: 'Zelfstudie: Storegate configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357933"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Zelfstudie: Storegate configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in Storegate en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Storegate.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Storegate-tenant](https://www.storegate.com)
* Een gebruikersaccount in Storegate met beheerdersmachtigingen.

## <a name="assign-users-to-storegate"></a>Gebruikers toewijzen aan Storegate

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Storegate. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Storegate toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Belangrijke tips voor het toewijzen van gebruikers aan Storegate

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Storegate om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Storegate toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-storegate-for-provisioning"></a>Storegate instellen voor inrichting

Voordat u Storegate configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtingsgegevens ophalen van Storegate.

1. Meld u aan bij de [Storegate-beheerconsole](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) en navigeer naar de instellingen door te klikken op het pictogram Gebruikers in de rechterbovenhoek, en selecteer **Accountinstellingen**.

    ![Storegate - SCIM toevoegen](media/storegate-provisioning-tutorial/admin.png)

2. Ga in Instellingen naar **Team > Instellingen** en controleer of de schakeloptie is ingeschakeld in de sectie **Eenmalige aanmelding** .

    ![Storegate-instellingen](media/storegate-provisioning-tutorial/team.png)

    ![Storegate-wisselknop](media/storegate-provisioning-tutorial/sso.png)

3. Kopieer de waarden van **Tenant-URL** en **Token**. Deze waarden worden ingevoerd in de velden **Tenant-URL** en **Token voor geheim** op het tabblad Inrichten van uw Storegate-toepassing in Azure Portal. 

    ![Storegate - token maken](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Storegate toevoegen vanuit de galerie

Als u Storegate wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Storegate vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Storegate** in, selecteer **Storegate** in het resultatenvenster. 

    ![Storegate in de resultatenlijst](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor Storegate** om naar de aanmeldingspagina van Storegate te gaan. 

    ![Storegate - OIDC toevoegen](media/storegate-provisioning-tutorial/signup.png)

6.  Meld u aan bij de [Storegate-beheerconsole](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) en navigeer naar de instellingen door te klikken op het pictogram Gebruikers in de rechterbovenhoek, en selecteer **Accountinstellingen**.

    ![Storegate-aanmelding](media/storegate-provisioning-tutorial/admin.png)

7. Ga in Instellingen naar **Team > Instellingen** en klik op de schakeloptie in de sectie Eenmalige aanmelding. De stroom voor het geven van toestemming wordt dan gestart. Klik op **Activeren**.

    ![Storegate - Team](media/storegate-provisioning-tutorial/team.png)

    ![Storegate - eenmalige aanmelding](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate - activeren](media/storegate-provisioning-tutorial/activate.png)

8. Aangezien Storegate een OpenIDConnect-app is, kiest u ervoor om u bij Storegate aan te melden met uw Microsoft-werkaccount.

    ![Storegate - OIDC-aanmelding](media/storegate-provisioning-tutorial/login.png)

9. Wanneer de verificatie is voltooid, accepteert u toestemming op de toestemmingspagina. De toepassing wordt vervolgens automatisch toegevoegd aan uw tenant en u wordt omgeleid naar uw Storegate-account.

    ![Storegate - OIDC-toestemming](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Automatische gebruikersinrichting configureren voor Storegate 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Storegate te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!NOTE]
> Voor meer informatie over het SCIM-eindpunt van Storegate kunt u [hier](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/) terecht.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Storegate in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Storegate** in de lijst met toepassingen.

    ![De koppeling naar Storegate in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://dialpad.com/scim` in **Tenant-URL** in. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen in Storegate in **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Storegate. Als de verbinding mislukt, moet u controleren of uw Storegate-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Storegate**.

    ![Storegate-gebruikerstoewijzingen](media/storegate-provisioning-tutorial/usermappings.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Storegate worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Storegate te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Storegate-gebruikerskenmerken](media/storegate-provisioning-tutorial/userattributes.png)

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Storegate.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan Storegate wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Storegate worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
