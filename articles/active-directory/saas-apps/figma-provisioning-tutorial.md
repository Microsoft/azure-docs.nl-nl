---
title: 'Zelfstudie: Figma configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 789dafc61c89515f4b2ef64933262252d1232f16
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357025"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Zelfstudie: Figma configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Figma en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Figma.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in Openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Figma-tenant](https://www.figma.com/pricing/).
* Een gebruikersaccount in Figma met beheerdersmachtigingen.

## <a name="assign-users-to-figma"></a>Gebruikers toewijzen aan Figma.
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Figma. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Figma toewijzen door de instructies hier te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Belangrijke tips voor het toewijzen van gebruikers aan Figma

 * Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Figma om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Figma toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol Standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-figma-for-provisioning"></a>Figma instellen voor inrichting

Voordat u Figma configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtingsgegevens ophalen van Figma.

1. Meld u aan bij de [Beheerconsole van Figma](https://www.Figma.com/). Klik op het tandwielpictogram naast uw tenant.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Schermopname van de beheerconsole van Figma. Een Tenant met de naam A A D Scim Test is zichtbaar. Naast de tenant is een tandwielpictogram gemarkeerd." border="false":::

2. Ga naar **Algemeen > Logboek bijwerken in Instellingen**.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Schermopname van het tabblad Algemeen van de beheerconsole van Figma. Onder aanmelden en inrichten is Logboek bijwerken in instellingen gemarkeerd." border="false":::

3. Kopieer de **Tenant-id**. Deze waarde wordt gebruikt om de SCIM-eindpunt-URL te maken die moet worden ingevoerd in het veld **Tenant-URL** op het tabblad Inrichten van de Figma-toepassing in de Azure Portal.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Schermopname van de sectie S A M L S S O in de Figma-beheerconsole. Een label voor de Tenant-id en een aangrenzende link met de tekst Kopiëren zijn gemarkeerd." border="false":::

4. Schuif omlaag en klik op **API-token genereren**.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Schermopname van de sectie S C I M in de Figma-beheerconsole. Een link met het label een A P I-token genereren is gemarkeerd." border="false":::

5. Kopieer de waarde van **API-token**. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad Inrichten van uw Figma-toepassing in Azure Portal. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Schermopname van een pagina in de beheerconsole van Figma. Onder Uw A P I-token voor inrichten wordt een tijdelijke aanduiding voor het token gemarkeerd." border="false":::

## <a name="add-figma-from-the-gallery"></a>Voeg Figma toe vanuit de galerie

Als u Figma wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Figma vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenaan het paneel.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Figma** in het zoekvak in, selecteer **Figma** in het venster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Figma in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Automatische gebruikersinrichting voor Figma configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Figma te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Figma, waarvoor u de instructies in de [Zelfstudie eenmalige aanmelding voor Figma](figma-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Figma in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Figma** in de lijst met toepassingen.

    ![De Figma-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie Inrichting gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Selecteer in de sectie **Beheerdersreferenties** de invoer `https://www.figma.com/scim/v2/<TenantID>` in **Tenant-URL** waar **TenantID** de waarde is die u eerder hebt opgehaald van Figma. Voer de waarde van de **API-token** in voor **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Figma. Als de verbinding mislukt, moet u controleren of uw Figma-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Figma**.

    ![Figma-gebruikerstoewijzingen](media/Figma-provisioning-tutorial/figma05.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Figma worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Figma te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Figma-gebruikerskenmerken ophalen](media/Figma-provisioning-tutorial/figma06.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Figma.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Figma wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice in Figma worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)