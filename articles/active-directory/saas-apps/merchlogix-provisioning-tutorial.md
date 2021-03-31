---
title: 'Zelfstudie: MerchLogix configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor MerchLogix.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 4d0a52f06a751fba57a00615e2d57485ff740d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359694"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Zelfstudie: MerchLogix configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in MerchLogix en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor MerchLogix.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* Een MerchLogix-tenant
* Een technische contactpersoon bij MerchLogix die de SCIM-eindpunt-URL en het token voor het geheim kan verstrekken dat vereist is voor het inrichten van gebruikers

## <a name="adding-merchlogix-from-the-gallery"></a>MerchLogix toevoegen vanuit de galerie

Voordat u MerchLogix configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u MerchLogix vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om MerchLogix toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. In **[Azure Portal](https://portal.azure.com)** klikt u in het navigatievenster aan de linkerkant op het pictogram **Azure Active Directory**. 

    ![De knop Azure Active Directory][1]

2. Navigeer naar **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![De sectie Bedrijfstoepassingen][2]

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing][3]

4. Typ **MerchLogix** in het zoekvak.

5. Selecteer in het resultatenvenster **MerchLogix** en klik vervolgens op de knop **Toevoegen** om MerchLogix toe te voegen aan uw lijst met SaaS-toepassingen.

    ![Schermopname van de sectie Toevoegen uit de galerie met het tekstvak Voer een naam in gemarkeerd.][4]

## <a name="assigning-users-to-merchlogix"></a>Gebruikers toewijzen aan MerchLogix

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD. 

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot MerchLogix. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan MerchLogix toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Belangrijke tips voor het toewijzen van gebruikers aan MerchLogix

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan MerchLogix om uw eerste configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen als de tests goede resultaten laten zien.

* Als u een gebruiker aan MerchLogix toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Automatische gebruikersinrichting voor MerchLogix configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in MerchLogix te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor MerchLogix, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor MerchLogix](merchlogix-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor MerchLogix in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en blader naar **Azure Active Directory > Bedrijfsapps > Alle toepassingen**.

2. Selecteer MerchLogix in de lijst met SaaS-toepassingen.

3. Selecteer het tabblad **Inrichten**.

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de sectie MerchLogix - Inrichting met de optie Inrichten gemarkeerd, met de inrichtingsmodus ingesteld op Automatisch en met de optie Verbinding testen gemarkeerd.](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. In de sectie **Referenties voor beheerder** doet u het volgende:

    * Voer in het veld **Tenant-URL** de SCIM-eindpunt-URL in die u hebt gekregen van uw technische contactpersoon bij MerchLogix.

    * Voer in het veld **Token voor geheim** het token voor het geheim in dat u hebt gekregen van uw technische contactpersoon bij MerchLogix.

6. Klik bij het invullen van de velden die worden weergegeven in stap 5 op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met MerchLogix. Als de verbinding mislukt, moet u controleren of uw MerchLogix-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

7. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

8. Klik op **Opslaan**.

9. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met MerchLogix**.

10. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met MerchLogix worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in MerchLogix te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

11. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met MerchLogix**.

12. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met MerchLogix worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in MerchLogix te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor MerchLogix.

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt het gedeelte **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op MerchLogix worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
