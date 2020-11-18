---
title: 'Zelfstudie: Signagelive configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Signagelive.
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
ms.openlocfilehash: 10ad06041e8136b5661b1b1ff487cd4d3b0f5153
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358402"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>Zelfstudie: Signagelive configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in Signagelive en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Signagelive.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Signagelive-tenant](https://signagelive.com/pricing/).
* Een gebruikersaccount in Signagelive met beheerdersmachtigingen.

## <a name="assigning-users-to-signagelive"></a>Gebruikers toewijzen aan Signagelive   

Azure Active Directory gebruikt een concept dat *toewijzingen* wordt genoemd om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Signagelive. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Signagelive toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Belangrijke tips voor het toewijzen van gebruikers aan Signagelive   

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan Signagelive om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen dan later worden toegewezen.

* Als u een gebruiker aan Signagelive toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het venster voor toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-signagelive--for-provisioning"></a>Signagelive instellen voor inrichting

Voordat u Signagelive configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in Signagelive.

  Neem contact op met [Signagelive](mailto:development@signagelive.com) om het geheime token aan te vragen dat nodig is voor het configureren van SCIM-inrichting.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive toevoegen vanuit de galerie

Als u Signagelive wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Signagelive vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Signagelive toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Signagelive** in het zoekvak in, selecteer **Signagelive** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Signagelive    in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Automatische gebruikersinrichting voor Signagelive configureren    

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Signagelive te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
>  U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Signagelive, waarvoor u de instructies in de [Zelfstudie eenmalige aanmelding voor Signagelive](Signagelive-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vullen elkaar aan.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Signagelive in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Signagelive**.

    ![De Signagelive-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie Referenties voor beheerder ` https://samlapi.signagelive.com/scim/v2` in bij **Tenant-URL**. Voer in het veld **Token voor geheim** de waarde voor het **bearer-token** in dat u hebt ontvangen van het Engineering Development-team. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Signagelive. Als de verbinding mislukt, controleert u of uw Signagelive-account wel beheerdersmachtigingen heeft. Probeer het daarna opnieuw.
    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Signagelive**.

    ![Schermopname van de sectie Toewijzingen met de optie Azure Active Directory-gebruikers synchroniseren met Signagelive gemarkeerd.](media/signagelive-provisioning-tutorial/usermapping.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Signagelive worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Signagelive te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Schermopname van de sectie Kenmerktoewijzingen met zeven toewijzingen weergegeven.](media/signagelive-provisioning-tutorial/userattribute.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groep synchroniseren met Signagelive**.

    ![Schermopname van de sectie Toewijzingen met de optie Azure Active Directory-groep synchroniseren met Signagelive gemarkeerd.](media/signagelive-provisioning-tutorial/groupmapping.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Signagelive worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepsaccounts in Signagelive te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Schermopname van de sectie Kenmerktoewijzingen met drie toewijzingen weergegeven.](media/signagelive-provisioning-tutorial/groupattribute.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Signagelive.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Signagelive wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De eerste synchronisatie duurt langer dan volgende synchronisaties. Zie [Hoe lang duurt het inrichten van gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) voor meer informatie over hoe lang het duurt om gebruikers en/of groepen in te richten. 

U kunt het gedeelte **Huidige status** gebruiken om de voortgang te controleren en links te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Signagelive worden uitgevoerd. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie. Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) als u de Azure AD-inrichtingslogboeken wilt lezen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
