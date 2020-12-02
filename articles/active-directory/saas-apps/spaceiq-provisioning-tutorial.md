---
title: 'Zelfstudie: SpaceIQ configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor SpaceIQ.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: a5bd3c39c293880e31ebe63f85b3814725f6027a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96347290"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Zelfstudie: SpaceIQ configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in SpaceIQ en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor SpaceIQ.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in Openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een SpaceIQ-tenant](https://spaceiq.com/)
* Een gebruikersaccount in SpaceIQ met beheerdersmachtigingen.

## <a name="assigning-users-to-spaceiq"></a>Gebruikers toewijzen aan SpaceIQ

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot SpaceIQ. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan SpaceIQ toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>Belangrijke tips voor het toewijzen van gebruikers aan SpaceIQ

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan SpaceIQ om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan SpaceIQ toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-spaceiq-for-provisioning"></a>SpaceIQ instellen voor inrichting

1. Meld u aan bij de [SpaceIQ-beheerconsole](https://main.spaceiq.com/login/). Navigeer naar **Instellingen** door deze te selecteren in de vervolgkeuzelijst in de rechterbovenhoek van het scherm.

    ![SpaceIQ-beheerconsole](media/spaceiq-provisioning-tutorial/admin.png)

2.  Selecteer op de pagina **Instellingen** **Integraties van derden**.

    ![SpaceIQ - SCIM toevoegen](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  Navigeer naar het tabblad **Inrichten en eenmalige aanmelding**. Zoek naar de **Azure**-tegel. Klik op **Activeren**.

    ![SpaceIQ - inrichten en eenmalige aanmelding](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ - Azure activeren ](media/spaceiq-provisioning-tutorial/azure.png)

3.  Kopieer de **Bearer-token voor SCIM**. Deze waarde wordt ingevoerd in het veld Token voor geheim op het tabblad Inrichten van uw SpaceIQ-toepassing in Azure Portal. Klik op **Activeren**

    ![SpaceIQ - token maken](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>SpaceIQ toevoegen vanuit de galerie

Voordat u SpaceIQ configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SpaceIQ vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om SpaceIQ toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven in het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **SpaceIQ** in het zoekvak in, selecteer **SpaceIQ** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![SpaceIQ in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>Automatische gebruikersinrichting voor SpaceIQ configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in SpaceIQ te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor SpaceIQ, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor SpaceIQ](./spaceiq-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Als u automatische gebruikersinrichting wilt configureren voor SpaceIQ in Azure AD, doet u het volgende:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SpaceIQ** in de lijst met toepassingen.

    ![De SpaceIQ-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://api.spaceiq.com/scim` in **Tenant-URL** in. Voer de waarde van het **SCIM-verificatietoken** in die eerder in **Token voor geheim** is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met SpaceIQ. Als de verbinding mislukt, moet u controleren of uw SpaceIQ-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met SpaceIQ**.

    ![SpaceIQ-gebruikerstoewijzingen](media/spaceiq-provisioning-tutorial/usermapping.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met SpaceIQ worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in SpaceIQ te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![SpaceIQ-gebruikerskenmerken](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor SpaceIQ.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u aan SpaceIQ wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op SpaceIQ worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)