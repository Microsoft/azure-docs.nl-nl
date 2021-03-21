---
title: 'Zelfstudie: SAP Cloud Platform Identity Authentication configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor SAP Cloud Platform Identity Authentication.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 419f25ee3df471bc2fc4526254f5677b8bd71856
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96342726"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Zelfstudie: SAP Cloud Platform Identity Authentication configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in SAP Cloud Platform Identity Authentication en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor SAP Cloud Platform Identity Authentication.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een SAP Cloud Platform Identity Authentication-tenant](https://cloudplatform.sap.com/pricing.html)
* Een gebruikersaccount in SAP Cloud Platform Identity Authentication met beheerdersmachtigingen.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Gebruikers toewijzen aan SAP Cloud Platform Identity Authentication

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot SAP Cloud Platform Identity Authentication. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan SAP Cloud Platform Identity Authentication toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Belangrijke tips voor het toewijzen van gebruikers aan SAP Cloud Platform Identity Authentication

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan SAP Cloud Platform Identity Authentication om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan SAP Cloud Platform Identity Authentication toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>SAP Cloud Platform Identity Authentication instellen voor inrichting

1. Meld u aan bij uw [SAP Cloud Platform Identity Authentication-beheerconsole](https://sapmsftintegration.accounts.ondemand.com/admin). Navigeer naar **Gebruikers & Autorisaties > Beheerders**.

    ![SAP Cloud Platform Identity Authentication-beheerconsole](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Druk op de knop **+Toevoegen** in het deelvenster aan de linkerkant om een nieuwe beheerder aan de lijst toe te voegen. Kies **Systeem toevoegen** en voer de naam van het systeem in.   

> [!NOTE]
> De beheerdersgebruiker in SAP Cloud Platform Identity Authentication moet van het type **Systeem** zijn. Het maken van een normale beheerdersgebruiker kan leiden tot *ongeoorloofde* fouten tijdens het inrichten.   

3.  Onder Autorisaties configureren selecteert u de wisselknop voor **Gebruikers beheren** en **Groepen beheren**.

    ![SAP Cloud Platform Identity Authentication - SCIM toevoegen](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. U ontvangt een e-mail bericht om uw account te activeren en een wachtwoord in te stellen voor **SAP Cloud Platform Identity Authentication Service**.

4.  Kopieer de **Gebruikers-id** en het **Wachtwoord**. Deze waarden worden respectievelijk ingevoerd in de velden Gebruikersnaam beheerder en Wachtwoord beheerder op het tabblad Inrichten van uw SAP Cloud Platform Identity Authentication-toepassing in de Azure Portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Cloud Platform Identity Authentication uit de galerie toevoegen

Voordat u SAP Cloud Platform Identity Authentication configureert voor automatische gebruikersinrichting met Azure AD, moet u SAP Cloud Platform Identity Authentication toevoegen vanuit de Azure AD-toepassingsgalerie aan uw lijst met beheerde SaaS-toepassingen.

**Als u SAP Cloud Platform Identity Authentication uit de galerie van de Azure AD-toepassing wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **SAP Cloud Platform Identity Authentication** in, selecteer **SAP Cloud Platform-identiteitsverificatie** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![SAP Cloud Platform-identiteitsverificatie in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Automatische gebruikersinrichting configureren voor SAP Cloud Platform Identity Authentication 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in SAP Cloud Platform Identity Authentication, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding voor SAP Cloud Platform Identity Authentication in te schakelen, gevolgd door de instructies in de [Zelfstudie voor eenmalige aanmelding voor SAP-Cloud Platform Identity Authentication](./sap-hana-cloud-platform-identity-authentication-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor SAP Cloud Platform Identity Authentication in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SAP Cloud Platform-identiteitsverificatie** in de lijst met toepassingen.

    ![De koppeling SAP Cloud Platform-identiteitsverificatie in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://<tenantID>.accounts.ondemand.com/service/scim ` in bij **Tenant-URL**. Voer de waarden van de **Gebruikers-id** en het **Wachtwoord** in die eerder zijn opgehaald in respectievelijk **Gebruikersnaam van beheerder** en **Wachtwoord van beheerder**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met SAP Cloud Platform Identity Authentication. Als de verbinding mislukt, controleert u of uw SAP Cloud Platform Identity Authentication-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met SAP Cloud Platform Identity Authentication**.

    ![Gebruikerstoewijzingen van SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met SAP Cloud Platform Identity Authentication. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in SAP Cloud Platform Identity Authentication te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikerskenmerken van SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor SAP Cloud Platform Identity Authentication.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt toevoegen aan SAP Cloud Platform Identity Authentication, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op SAP Cloud Platform Identity Authentication worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Het SCIM-eind punt van SAP Cloud Platform Identity Authentication vereist dat bepaalde kenmerken een specifieke indeling hebben. U kunt meer informatie over deze kenmerken en hun specifieke indeling [hier](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#) vinden.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)