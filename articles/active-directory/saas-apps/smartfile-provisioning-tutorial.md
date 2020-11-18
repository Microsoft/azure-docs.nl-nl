---
title: 'Zelfstudie: SmartFile configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor SmartFile.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fca18a58ccb8d4e2f10b5db606ad01a97c2d5bac
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359922"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Zelfstudie: SmartFile configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in SmartFile en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor SmartFile.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in Openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een SmartFile-tenant](https://www.SmartFile.com/pricing/).
* Een gebruikersaccount in SmartFile met beheerdersmachtigingen.

## <a name="assigning-users-to-smartfile"></a>Gebruikers toewijzen aan SmartFile

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot SmartFile. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan SmartFile toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Belangrijke tips voor het toewijzen van gebruikers aan SmartFile

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan SmartFile om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan SmartFile toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-smartfile-for-provisioning"></a>SmartFile instellen voor inrichting

Voordat u SmartFile configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen op SmartFile en aanvullende details verzamelen die nodig zijn.

1. Meld u aan bij de SmartFile-beheerconsole. Navigeer naar de rechterbovenhoek van de SmartFile-beheerconsole. Selecteer **Productcode**.

    ![SmartFile-beheerconsole](media/smartfile-provisioning-tutorial/login.png)

2. Als u een Bearer-token wilt genereren, kopieert u de **Productcode** en het **Productwachtwoord**. Plak deze in een kladblok met een dubbele punt ertussen.
    
     ![Schermopname van de Productcode-sectie met de tekstvakken van Productcode en Productwachtwoord.](media/smartfile-provisioning-tutorial/auth.png)

    ![Schermopname van de niet-versleutelde tekst met de Productcode en het Productwachtwoord gescheiden door een dubbele punt.](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>SmartFile toevoegen uit de galerie

Als u SmartFile wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u SmartFile vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om SmartFile toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenaan het paneel.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **SmartFile** in het zoekvak in, selecteer **SmartFile** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![SmartFile in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Automatische gebruikersinrichting voor SmartFile configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in SmartFile te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor SmartFile, waarvoor u de instructies in de [Zelfstudie eenmalige aanmelding voor SmartFile](SmartFile-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor SmartFile in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **SmartFile**.

    ![De SmartFile-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie Inrichting gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5.  Voer onder de sectie **Beheerdersreferenties** `https://<SmartFile sitename>.smartfile.com/ftp/scim` in **Tenant-URL** in. Een voorbeeld ziet eruit als `https://demo1test.smartfile.com/ftp/scim`. Voer de waarde van de **Bearer-token** (ProductCode:ProductWachtwoord) in die u eerder in **Token voor geheim** hebt opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met SmartFile. Als de verbinding mislukt, moet u controleren of uw SmartFile-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met SmartFile**.

    ![SmartFile-gebruikerstoewijzingen](media/smartfile-provisioning-tutorial/usermapping.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met SmartFile worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in SmartFile te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikerskenmerken van SmartFile configureren](media/smartfile-provisioning-tutorial/userattribute.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met SmartFile**.

    ![SmartFile-groepstoewijzingen](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met SmartFile worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in SmartFile te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![SmartFile-groepskenmerken](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor SmartFile.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan SmartFile wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op SmartFile worden uitgevoerd.

    Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken
    
## <a name="connector-limitations"></a>Connectorbeperkingen

* SmartFile biedt alleen ondersteuning voor definitieve verwijderingen. 

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

 [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
