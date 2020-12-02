---
title: 'Zelfstudie: RFPIO configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor RFPIO.
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
ms.openlocfilehash: ff859e7d77fd19cd006cf45a6faa737297fdb9a1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349636"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Zelfstudie: RFPIO configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in RFPIO en Azure AD (Azure Active Directory) om automatisch gebruikers en/of groepen van Azure AD in te richten in RFPIO, of de inrichting ervan ongedaan te maken.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een RFPIO-tenant](https://www.rfpio.com/product/).
* Een gebruikersaccount in RFPIO met beheerdersmachtigingen.

## <a name="assigning-users-to-rfpio"></a>Gebruikers toewijzen aan RFPIO

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot RFPIO. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan RFPIO toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Belangrijke tips voor het toewijzen van gebruikers aan RFPIO

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan RFPIO om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan RFPIO toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-rfpio-for-provisioning"></a>RFPIO instellen voor inrichting

Voordat u RFPIO configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in RFPIO.

1.  Meld u aan bij de beheerconsole van RFPIO. Klik linksonder in de beheerconsole op **Tenant**.

    ![Beheerconsole van RFPIO](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Klik op **Organisatie-instellingen**.
    
    ![RFPIO-beheerder](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Ga naar **USER MANAGEMENT** > **SECURITY** > **SCIM**.

    ![SCIM toevoegen voor RFPIO](media/rfpio-provisioning-tutorial/scim.png)

4.  Zorg ervoor dat **Auto User Provisioning** (automatische inrichting van gebruikers) is ingeschakeld. Klik op **GENERATE SCIM API TOKEN**.

    ![Schermopname van de sectie SCIM met de optie GENERATE SCIM API TOKEN gemarkeerd.](media/rfpio-provisioning-tutorial/generate.png)

5.  Sla het **SCIM-API-token** op. Dit token wordt om beveiligingsredenen niet opnieuw weergegeven. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad Inrichten van de RFPIO-toepassing in de Azure-portal.

    ![Schermopname van de sectie SCIM met het waarschuwingsvenster dat wordt weer gegeven nadat u SUBMIT hebt geselecteerd.](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>RFPIO toevoegen vanuit de galerie

Als u RFPIO wilt configureren voor automatische inrichting van gebruikers met Azure AD, moet u RFPIO vanuit de Azure AD-toepassingsgalerie toevoegen aan de lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om RFPIO toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven in het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **RFPIO** in het zoekvak in, selecteer **RFPIO** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![RFPIO in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Automatische gebruikersinrichting voor RFPIO configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in RFPIO, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor RFPIO, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor RFPIO](rfpio-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor RFPIO in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **RFPIO** in de lijst met toepassingen.

    ![De RFPIO-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` in bij **Tenant-URL**. Een voorbeeldwaarde is `https://Azure-test1.rfpio.com/rfpserver/scim/v2`. Voer de waarde van het **SCIM-API-token** in die eerder in **Token voor geheim** is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met RFPIO. Als de verbinding mislukt, controleert u of het RFPIO-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met RFPIO**.

    ![RFPIO-gebruikerstoewijzingen](media/rfpio-provisioning-tutorial/usermapping.png)

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met RFPIO. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in RFPIO te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![RFPIO-gebruikerskenmerken](media/rfpio-provisioning-tutorial/userattributes.png)

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor RFPIO.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt toevoegen aan RFPIO, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit, waarin alle acties worden beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in RFPIO.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* RFPIO biedt momenteel geen ondersteuning voor het inrichten van groepen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
