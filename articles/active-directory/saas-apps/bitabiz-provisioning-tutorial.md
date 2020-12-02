---
title: 'Zelfstudie: BitaBIZ configureren voor automatische gebruikersinrichting met Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor BitaBIZ.
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
ms.openlocfilehash: 8eccc3be7da201ee1e2af046c6b515871ef05adc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350769"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Zelfstudie: BitaBIZ configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in BitaBIZ en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor BitaBIZ.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een BitaBIZ-tenant](https://bitabiz.dk/en/price/).
* Een gebruikersaccount in BitaBIZ met beheerdersmachtigingen.

## <a name="assigning-users-to-bitabiz"></a>Gebruikers toewijzen aan BitaBIZ

Azure Active Directory gebruikt een concept met de naam *Toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot BitaBIZ. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan BitaBIZ toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Belangrijke tips voor het toewijzen van gebruikers aan BitaBIZ

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan BitaBIZ om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan BitaBIZ toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-bitabiz-for-provisioning"></a>BitaBIZ instellen voor inrichting

Voordat u BitaBIZ configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen op BitaBIZ.

1. Meld u aan bij de [Beheerconsole van BitaBIZ](https://www.bitabiz.com/login?lang=en). Klik op **BEHEERDER INSTELLEN**.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/setup-admin.png" alt-text="Schermopname van de BitaBIZ-beheerconsole, met 'Beheerder installeren' gemarkeerd." border="false":::

2.  Ga naar **INTEGRATIE**.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/integration.png" alt-text="Schermopname van de BitaBIZ-beheerconsole, met 'Integratie' gemarkeerd." border="false":::

2.  Ga naar **Microsoft Azure AD inrichten**.  Selecteer **Inschakelen** in 'Gebruikers automatisch inrichten'. Kopieer de waarden voor **eindpunt-URL van de SCIM-inrichting** en **Bearer-token**. Deze worden ingevoerd in de velden Tenant-URL en Geheim token op het tabblad 'Inrichten' van uw BitaBIZ-toepassing in de Azure Portal.

    ![BitaBIZ SCIM toevoegen](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>BitaBIZ toevoegen vanuit de galerie

Als u BitaBIZ wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u BitaBIZ vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om BitaBIZ toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **BitaBIZ** in het zoekvak in, selecteer **BitaBIZ** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![BitaBIZ in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Automatische gebruikersinrichting voor BitaBIZ configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BitaBIZ te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor BitaBIZ, waarvoor u de instructies in de [Zelfstudie 'Eenmalige aanmelding voor BitaBIZ'](BitaBIZ-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, ook al vullen deze twee functies elkaar aan

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Om automatische gebruikersinrichting te configureren voor BitaBIZ in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **BitaBIZ** in de lijst met toepassingen.

    ![De koppeling naar BitaBIZ in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie 'Inrichting' gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst 'Inrichtingsmodus' met de optie 'Automatisch' gemarkeerd.](common/provisioning-automatic.png)

5. In het gedeelte 'Referenties voor beheerders' voert u de **SCIM-eindpunt-URL** en **Bearer-token** in die eerder zijn opgehaald uit respectievelijk Tenant-URL en Geheim Token. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met BitaBIZ. Als de verbinding mislukt, moet u controleren of uw BitaBIZ-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met BitaBIZ**.

    ![BitaBIZ-gebruikerstoewijzingen](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met BitaBIZ worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in BitaBIZ te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![BitaBIZ-gebruikerskenmerken](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om de Azure AD-inrichtingsservice in te schakelen voor BitaBIZ, wijzigt u de **Inrichtingsstatus** naar **Ingeschakeld** in de sectie **Instellingen**.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan BitaBIZ wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te bewaken en links te volgen naar het activiteitenrapport van de inrichting. In het activiteitenrapport worden alle acties beschreven die door de Azure AD-inrichtingsservice op BitaBIZ worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* BitaBIZ vereist **Gebruikersnaam**, **E-mailadres**, **Voornaam** en **Achternaam** als verplichte kenmerken. 
* BitaBIZ biedt momenteel geen ondersteuning voor definitieve verwijderingen.

## <a name="additional-resources"></a>Aanvullende resources

* [De inrichting van gebruikersaccounts voor bedrijfstoepassingen beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md).
