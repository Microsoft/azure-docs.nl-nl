---
title: 'Zelfstudie: Proxyclick configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Proxyclick.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: f7d2a6f01e891a7fb1c14cde552d66679e474139
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359167"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Zelfstudie: Proxyclick configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Proxyclick en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Proxyclick.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Proxyclick-tenant](https://www.proxyclick.com/pricing).
* Een gebruikersaccount in Proxyclick met beheerdersmachtigingen.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick toevoegen vanuit de galerie

Voordat u Proxyclick configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Proxyclick vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Proxyclick toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Proxyclick** in het zoekvak, selecteer **Proxyclick** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Proxyclick in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Gebruikers toewijzen aan Proxyclick

Azure Active Directory gebruikt een concept dat *toewijzingen* wordt genoemd om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Proxyclick. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Proxyclick toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Belangrijke tips voor het toewijzen van gebruikers aan Proxyclick

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan Proxyclick om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

* Als u een gebruiker aan Proxyclick toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Automatische gebruikersinrichting voor Proxyclick configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Proxyclick te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Proxyclick, waarvoor u de instructies in de [Zelfstudie eenmalige aanmelding voor Proxyclick](proxyclick-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vullen elkaar aan.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Proxyclick in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Proxyclick** in de lijst met toepassingen.

    ![De link Proxyclick in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Als u de **tenant-URL** en het **geheime token** van uw Proxyclick-account wilt ophalen, volgt u de procedure zoals die wordt beschreven in stap 6.

6. Meld u aan bij de [beheerconsole van Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Ga naar **Settings** > **Integrations** > **Browse Marketplace**.

    ![Proxyclick-instellingen](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick-integraties](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selecteer **Azure AD**. Klik op **Install now**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick installeren](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selecteer **User Provisioning** en klik op **Start integration**. 

    ![Proxyclick-gebruikers inrichten](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    De configuratie-interface met de juiste instellingen moet nu worden weergegeven onder **Settings** > **Integrations**. Selecteer **Settings** onder **Azure AD (User Provisioning)** .

    ![Proxyclick-integratie maken](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    U vindt de **tenant-URL** en het **geheime token** hier.

    ![Token voor maken van Proxyclick-integratie](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Proxyclick. Als de verbinding mislukt, moet u controleren of uw Proxyclick-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Proxyclick**.

    ![Toewijzingen van Proxyclick-gebruikers](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Proxyclick worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Proxyclick te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van Proxyclick-gebruikers](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Proxyclick.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

15. Definieer de gebruikers en/of groepen die u aan Proxyclick wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en links te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Proxyclick worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Proxyclick vereist dat de kenmerken **emails** en **userName** dezelfde bronwaarde hebben. Als een van beide kenmerken wordt gewijzigd, wordt de andere waarde automatisch aangepast.
* Proxyclick biedt geen ondersteuning voor het inrichten van groepen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

