---
title: 'Zelfstudie: Bonusly configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Bonusly.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d8c3f64e5cb5269bfe7e555615f874ac3443c6eb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357824"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Zelfstudie: Bonusly configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Bonusly en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor Bonusly.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over het volgende:

* Een Azure AD-tenant
* Een [Bonusly-tenant](https://bonus.ly/pricing)
* Een gebruikersaccount in Bonusly met beheerdersmachtigingen

> [!NOTE]
> De integratie voor inrichting met Azure AD steunt op de [Bonusly Rest-API](https://konghq.com/solutions/gateway/), die beschikbaar is voor Bonusly-ontwikkelaars.

## <a name="adding-bonusly-from-the-gallery"></a>Bonusly toevoegen vanuit de galerie

Voordat u Bonusly configureert voor het automatisch inrichten van gebruikers met Azure AD, moet Bonusly vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Bonusly toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Bonusly** in het zoekvak, selecteer **Bonusly** in het venster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Bonusly in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Gebruikers toewijzen aan Bonusly

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD. 

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Bonusly. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Bonusly toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Belangrijke tips voor het toewijzen van gebruikers aan Bonusly

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan Bonusly om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Bonusly toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Automatische gebruikersinrichting voor Bonusly configureren

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Bonusly te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Bonusly, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor Bonusly](bonus-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Bonusly in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer **Bonusly**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Bonusly** in de lijst met toepassingen.

    ![De koppeling voor Bonusly in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Schermopname van het tabblad Inrichten van Bonusly. Onder Beheren is Inrichting gemarkeerd." border="false":::

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Schermopname van het lijstvak Inrichtingsmodus, met Automatisch geselecteerd en gemarkeerd." border="false":::

5. Voer in de sectie **Beheerdersreferenties** de **Token voor geheim** van uw Bonusly-account in, zoals wordt beschreven in stap 6.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="Schermopname van de sectie Beheerdersreferenties. Het vak Token voor geheim is leeg, maar het vak is gemarkeerd." border="false":::

6. Het **Token voor geheim** voor uw Bonusly-account bevindt zich in **Beheerder > Bedrijf > Integraties**. Klik in de sectie **Als u wilt coderen**, klik op **API > Nieuw API-toegangstoken maken** om een nieuw Token voor geheim te maken.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Schermopname van het Bonusly-menu. Onder Beheerder is Bedrijf gemarkeerd. Onder Bedrijf is Integraties gemarkeerd." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Schermopname van de sectie Als u wilt coderen van de Bonusly-website, met A P I gemarkeerd." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Schermopname van de Bonusly-website. Het tabblad Services is open. Onder uw A P I-toegangstokens is Nieuw A P I-toegangstoken maken gemarkeerd." border="false":::

7. Typ in het volgende scherm een naam voor het toegangstoken in het tekstvak en druk vervolgens op **API-sleutel maken**. Het nieuwe toegangstoken wordt een paar seconden weergegeven in een pop-up.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Schermopname van de pagina Nieuw toegangstoken van de Bonusly-website. Een niet-gelabeld vak bevat Mijn token en de knop A P I-sleutel maken is gemarkeerd." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Schermopname van de Bonusly-website. Er wordt een melding weergegeven met Nieuw toegangstoken gemaakt, gevolgd door een onleesbaar token." border="false":::

8. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Bonusly. Als de verbinding mislukt, moet u controleren of uw Bonusly-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Schermopname van de sectie Beheerdersreferenties van Azure Portal. De knop Tekstverbinding is gemarkeerd." border="false":::

9. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Schermopname waarop het vak E-mailadres voor meldingen leeg is. Er is een optie zichtbaar met het label Een e-mailmelding verzenden als een fout optreedt." border="false":::

10. Klik op **Opslaan**.

11. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Bonusly**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Schermopname van de sectie Toewijzingen. Bij Naam is Azure Active Directory-gebruikers met Bonusly gemarkeerd." border="false":::

12. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Bonusly worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Bonusly te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Schermopname van de pagina Kenmerktoewijzingen. Een tabel bevat Azure Active Directory-kenmerken, bijbehorende Bonusly-kenmerken en de overeenkomende status." border="false":::

13. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Bonusly.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Schermopname van de sectie Instellingen. De wisselknop van de Inrichtingsstatus is ingesteld op uit." border="false":::

15. Definieer de gebruikers en/of groepen die u aan Bonusly wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Schermopname van het lijstvak Bereik. Alleen toegewezen gebruikers en groepen synchroniseren is geselecteerd in het vak." border="false":::

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Schermopname van de pagina Inrichten van Bonusly met de knop Opslaan gemarkeerd." border="false":::

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Bonusly worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png