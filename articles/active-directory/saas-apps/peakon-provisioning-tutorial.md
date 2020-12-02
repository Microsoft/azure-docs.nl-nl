---
title: 'Zelfstudie: Peakon configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Peakon.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: c04bbd5459690262b484582e807569b965a0439b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349885"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Zelfstudie: Peakon configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in Peakon en Azure AD (Azure Active Directory) om automatisch gebruikers en/of groepen van Azure AD in te richten in Peakon, of de inrichting ervan ongedaan te maken.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al voldoet aan de volgende vereisten:

* Een Azure AD-tenant.
* [Een Peakon-tenant](https://peakon.com/us/pricing/).
* Een gebruikersaccount in Peakon met beheerdersmachtigingen.

## <a name="assigning-users-to-peakon"></a>Gebruikers toewijzen aan Peakon

Azure Active Directory gebruikt een concept dat *toewijzingen* wordt genoemd om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Peakon. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Peakon toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Belangrijke tips voor het toewijzen van gebruikers aan Peakon 

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan Peakon om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

* Als u een gebruiker toewijst aan Peakon, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-peakon-for-provisioning"></a>Peakon instellen voor inrichting

1.  Meld u aan bij de [beheerconsole van Peakon](https://app.Peakon.com/login). Klik op **Configuration**. 

    ![Beheerconsole van Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selecteer **Integrations**.
    
    ![Schermopname van de opties onder Configuration met de optie Integrations gemarkeerd.](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Schakel **Employee Provisioning** in.

    ![Schermopname van de sectie Employee Provisioning met de optie Enable gemarkeerd.](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Kopieer de waarden voor **SCIM 2.0 URL** en **OAuth Bearer Token**. Deze worden moet u later invoeren in de velden **Tenant-URL** en **Token voor geheim** op het tabblad Inrichten van uw Peakon-toepassing in de Azure-portal.

    ![Token maken voor Peakon](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Peakon toevoegen vanuit de galerie

Als u Peakon wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Peakon vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Ga naar de **[Azure-portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Peakon** in het zoekvak, selecteer **Peakon** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Peakon in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Automatische gebruikersinrichting voor Peakon configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Peakon te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Peakon, waarvoor u de instructies in de [Zelfstudie eenmalige aanmelding voor Peakon](peakon-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vullen elkaar aan.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Peakon in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Peakon** in de lijst met toepassingen.

    ![De link naar Peakon in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. In het gedeelte **Referenties voor beheerder** voert u de waarden voor **SCIM 2.0 URL** en **OAuth Bearer Token** in die eerder zijn opgehaald uit respectievelijk **Tenant-URL** en **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Peakon. Als de verbinding mislukt, controleert u of uw Peakon-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Peakon**.

    ![Toewijzingen van Peakon-gebruikers](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met Peakon. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Peakon te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van Peakon-gebruikers](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit, waarin alle acties worden beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in Peakon.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Alle aangepaste gebruikerskenmerken in Peakon moeten een uitbreiding zijn van de aangepaste SCIM-gebruikersextensie `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` van Peakon.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)