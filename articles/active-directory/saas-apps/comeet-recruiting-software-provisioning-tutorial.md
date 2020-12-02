---
title: 'Zelfstudie: Comeet Recruiting Software configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Comeet Recruiting Software.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 288d1e6cec8ddcf7d4afe5a35f28a022c2a7be10
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006709"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Zelfstudie: Comeet Recruiting Software configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Comeet Recruiting Software en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Comeet Recruiting Software.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Comeet Recruiting Software-tenant](https://www.comeet.co/)
* Een gebruikersaccount in Comeet Recruiting Software met beheerdersmachtigingen.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Comeet Recruiting Software toevoegen vanuit de galerie

Voordat u Comeet Recruiting Software configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Comeet Recruiting Software vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Comeet Recruiting Software toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven in het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Comeet Recruiting Software** in het zoekvak in, selecteer **Comeet Recruiting Software** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Comeet Recruiting Software in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Gebruikers toewijzen aan Comeet Recruiting Software

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Comeet Recruiting Software. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Comeet Recruiting Software toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Belangrijke tips voor het toewijzen van gebruikers aan Comeet Recruiting Software

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Comeet Recruiting Software om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Comeet Recruiting Software toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Automatische gebruikersinrichting voor Comeet Recruiting Software configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Comeet Recruiting Software te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Comeet Recruiting Software, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor Comeet Recruiting Software](comeetrecruitingsoftware-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Comeet Recruiting Software in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen, **Comeet Recruiting Software**.

    ![De koppeling Comeet Recruiting Software in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **Referenties voor beheerder** de **tenant-URL** en **token voor geheim** van uw Comeet Recruiting Software-account in, zoals wordt beschreven in stap 6.

6. Ga in de [Comeet Recruiting Software-beheerconsole](https://app.comeet.co/), naar **Comeet > Settings > Authentication > Microsoft Azure** en kopieer de waarde van **Secret Token for your company** naar het veld **Token voor geheim** in Azure AD.

    ![Comeet Recruiting Software-inrichting](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Comeet Recruiting Software. Als de verbinding mislukt, moet u controleren of uw Comeet Recruiting Software-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Token](common/provisioning-testconnection-token.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Comeet**.

    ![Comeet Recruiting Software-gebruikerstoewijzingen](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Comeet Recruiting Software worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Comeet Recruiting Software te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Comeet Recruiting Software-groepskenmerken](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig de **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Comeet Recruiting Software.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Comeet Recruiting Software wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Comeet Recruiting Software worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Groepen worden momenteel niet ondersteund in Comeet Recruiting Software.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

