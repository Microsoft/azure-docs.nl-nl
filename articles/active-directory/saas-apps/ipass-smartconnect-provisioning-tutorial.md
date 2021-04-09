---
title: 'Zelfstudie: iPass SmartConnect configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor iPass SmartConnect.
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
ms.openlocfilehash: 405a7bc3b653ca7bca026d3318763a4922244e88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97093703"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Zelfstudie: iPass SmartConnect configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in iPass SmartConnect en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor iPass SmartConnect.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een iPass SmartConnect-tenant](https://www.ipass.com/buy-ipass/).
* Een gebruikersaccount in iPass SmartConnect met beheerdersmachtigingen.

## <a name="assigning-users-to-ipass-smartconnect"></a>Gebruikers toewijzen aan iPass SmartConnect

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot iPass SmartConnect. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan iPass SmartConnect toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Belangrijke tips voor het toewijzen van gebruikers aan iPass SmartConnect

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan iPass SmartConnect om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan iPass SmartConnect toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>IPass SmartConnect instellen voor inrichting

Voordat u iPass SmartConnect configureert voor automatische inrichting van gebruikers met Azure AD, moet u configuratie-informatie ophalen uit de beheerconsole van iPass SmartConnect:

1. U kunt het Bearer-token dat nodig is voor verificatie op basis van uw SCIM-eindpunt voor iPass SmartConnect, alleen ophalen wanneer u iPass SmartConnect voor het eerst instelt, omdat deze waarde alleen dan wordt verstrekt. 
2. Als u niet beschikt over het Bearer-token, neemt u contact op met het [ondersteuningsteam van iPass SmartConnect](mailto:help@ipass.com) om een nieuw Bearer-token op te halen.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>iPass SmartConnect toevoegen vanuit de galerie

Als u iPass SmartConnect wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u iPass SmartConnect vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om iPass SmartConnect toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **iPass SmartConnect** in het zoekvak in, selecteer **iPass SmartConnect** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![iPass SmartConnect in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Automatische gebruikersinrichting voor iPass SmartConnect configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in iPass SmartConnect te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
>  U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor iPass SmartConnect, waarvoor u de instructies in de [Zelfstudie 'Eenmalige aanmelding voor iPass SmartConnect'](ipasssmartconnect-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor iPass SmartConnect in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **iPass SmartConnect** in de lijst met toepassingen.

    ![De link iPass SmartConnect in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://openmobile.ipass.com/moservices/scim/v1` in bij **Tenant-URL**. Voer in **Token voor geheim** het eerder opgehaalde Bearer-token in. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met iPass SmartConnect. Als de verbinding mislukt, moet u controleren of uw iPass SmartConnect-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met iPass SmartConnect**.

    :::image type="content" source="media/ipass-smartconnect-provisioning-tutorial/usermapping.png" alt-text="Schermopname van de sectie Toewijzingen. Onder Naam is Azure Active Directory-gebruikers synchroniseren met iPass SmartConnect gemarkeerd." border="false":::

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met iPass SmartConnect worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in iPass SmartConnect te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    :::image type="content" source="media/ipass-smartconnect-provisioning-tutorial/userattribute.png" alt-text="Schermopname van de pagina Kenmerktoewijzingen. Een tabel bevat Azure Active Directory-kenmerken, iPass SmartConnect-kenmerken en de overeenkomende prioriteit." border="false":::


10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor iPass SmartConnect.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan iPass SmartConnect wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op iPass SmartConnect worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* iPass SmartConnect accepteert alleen gebruikersnamen waarvan de domeinen zijn geregistreerd in de beheerconsole van iPass SmartConnect.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
