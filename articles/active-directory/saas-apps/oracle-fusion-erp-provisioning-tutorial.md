---
title: 'Zelfstudie: Oracle Fusion ERP configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Oracle Fusion ERP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: da6e1a8ba31f8f4991bde4803191598a015a68b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358436"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Zelfstudie: Oracle Fusion ERP configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Oracle Fusion ERP en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Oracle Fusion ERP.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* Een [Oracle Fusion ERP-tenant](https://www.oracle.com/applications/erp/).
* Een gebruikersaccount in Oracle Fusion ERP met beheerdersmachtigingen.

## <a name="assign-users-to-oracle-fusion-erp"></a>Gebruikers toewijzen aan Oracle Fusion ERP 
Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Oracle Fusion ERP. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Oracle Fusion ERP toewijzen door de instructies hier te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Belangrijke tips voor het toewijzen van gebruikers aan Oracle Fusion ERP 

 * Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Oracle Fusion ERP om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Oracle Fusion ERP toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol Standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Oracle Fusion ERP instellen voor inrichting

Voordat u Oracle Fusion ERP configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in Oracle Fusion ERP.

1. Meld u aan bij de [beheerconsole van Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Klik op de Navigator in de linkerbovenhoek. Selecteer **Beveiligingsconsole** onder **Hulpprogramma's**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Schermopname van de Navigator-pagina in de beheerconsole van Oracle Fusion ERP. Hulpprogramma's en Beveiligingsconsole zijn gemarkeerd." border="false":::

3. Navigeer naar **Gebruikers**.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Schermopname van een deelvenster in de beheerconsole van Oracle Fusion ERP. Het item Gebruikers is gemarkeerd." border="false":::

4. Sla de gebruikersnaam en het wachtwoord op voor het gebruikersaccount van de beheerder die u gaat gebruiken om u aan te melden bij de beheerconsole van Oracle Fusion ERP. Deze waarden moeten worden ingevoerd in de velden **Gebruikersnaam van beheerder** en **Wachtwoord** op het tabblad Inrichten van uw Oracle Fusion ERP-toepassing in Azure Portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Oracle Fusion ERP toevoegen vanuit de galerie

Voordat u Oracle Fusion ERP configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Oracle Fusion ERP vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Oracle Fusion ERP toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **[Azure Active Directory](https://portal.azure.com)** in **Azure Portal** in het navigatiepaneel aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Oracle Fusion ERP** in selecteer **Oracle Fusion ERP** in het resultatenvenster.

    ![Oracle Fusion ERP in de lijst met resultaten](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Automatische gebruikersinrichting configureren voor Oracle Fusion ERP 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Oracle Fusion ERP te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Oracle Fusion ERP, waarvoor u de instructies in de [zelfstudie over eenmalige aanmelding voor Oracle Fusion ERP](oracle-fusion-erp-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

> [!NOTE]
> Voor meer informatie over het SCIM-eind punt van Oracle Fusion ERP raadpleegt u [REST API voor algemene functies in Oracle Applications Cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Oracle Fusion ERP in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Oracle Fusion ERP** in de lijst met toepassingen.

    ![Het koppeling naar Oracle Fusion ERP in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` in **Tenant-URL** in. Geef de gebruikersnaam en het wachtwoord voor de beheerder op die u eerder hebt opgehaald uit de velden **Gebruikersnaam van beheerder** en **Wachtwoord**. Klik op **Verbinding testen** om de verbinding tussen Azure AD en Oracle Fusion ERP te testen. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="Schermopname van de sectie Beheerdersreferenties. De knop Verbinding testen en velden voor een tenant-URL, gebruikersnaam van de beheerder en wachtwoord van de beheerder worden weergegeven." border="false":::

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Oracle Fusion ERP**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="Schermopname van de sectie Toewijzingen. Bij Naam is Azure Active Directory-gebruikers synchroniseren met Oracle Fusion ERP gemarkeerd." border="false":::

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Oracle Fusion ERP worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Oracle Fusion ERP te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="Schermopname van de pagina Kenmerktoewijzingen. Een tabel bevat Azure Active Directory- en Oracle Fusion ERP-kenmerken en de overeenkomende prioriteit." border="false":::

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Oracle Fusion ERP**.

    ![Oracle Fusion ERP-groepstoewijzingen](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Oracle Fusion ERP worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Oracle Fusion ERP te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Oracle Fusion ERP-groepskenmerken](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Oracle Fusion ERP.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Oracle Fusion ERP wilt toevoegen door de gewenste waarden in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Oracle Fusion ERP worden uitgevoerd.

    Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Oracle Fusion ERP ondersteunt alleen basisverificatie voor het SCIM-eindpunt.
* Oracle Fusion ERP biedt geen ondersteuning voor het inrichten van groepen.
* Rollen in Oracle Fusion ERP worden toegewezen aan groepen in Azure AD. Als u rollen wilt toewijzen aan gebruikers in Oracle Fusion ERP vanuit Azure AD, moet u gebruikers toewijzen aan de gewenste Azure AD-groepen die naar rollen zijn vernoemd in Oracle Fusion ERP.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
