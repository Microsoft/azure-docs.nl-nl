---
title: 'Zelfstudie: Robin configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten in Robin Powered, en de inrichting ervan ongedaan te maken.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: 83af1c3bc323546534613e6ff99c731010b103d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352130"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>Zelfstudie: Robin configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Robin en Azure AD (Azure Active Directory), om Azure AD te configureren voor de automatische inrichting van gebruikers en/of groepen in Robin, en het ongedaan maken ervan.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Robin-tenant](https://robinpowered.com/pricing/)
* Een gebruikersaccount in Robin met beheerdersmachtigingen.

## <a name="assigning-users-to-robin"></a>Gebruikers toewijzen aan Robin

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Robin. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan Robin door deze instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>Belangrijke tips voor het toewijzen van gebruikers aan Robin

* Het wordt aanbevolen om één Azure AD-gebruiker toe te wijzen aan Robin om de configuratie van de automatische inrichting van gebruikers te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker toewijst aan Robin, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-robin-for-provisioning"></a>Robin instellen voor inrichting

1. Meld u aan bij de [beheerconsole van Robin](https://dashboard.robinpowered.com/login). Ga naar **Beheren > Integraties > SCIM > Beheren**.

    ![Robin Powered-beheerconsole](media/robin-provisioning-tutorial/robin-admin.png)

2.  Genereer een nieuw organisatietoken. Als u dit token kwijtraakt, kunt u altijd een nieuw token maken zonder dat dit van invloed is op bestaande gebruikers.

    ![Robin Powered: SCIM toevoegen](media/robin-provisioning-tutorial/robin-token.png)

3.  Kopieer het **SCIM-verificatietoken**. Deze waarde wordt later ingevoerd in het veld Token voor geheim, op het tabblad Inrichten van de Robin-toepassing in de Azure-portal.



## <a name="add-robin-from-the-gallery"></a>Robin toevoegen vanuit de galerie

Voordat u Robin configureert voor de automatisch inrichting van gebruikers met Azure AD, moet u Robin vanuit de Azure AD-toepassingsgalerie toevoegen aan de lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Robin toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Robin** in het zoekvak, selecteer **Robin** in het resultatenpaneel, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Robin in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Automatische inrichting van gebruikers configureren voor Robin 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Robin te maken, bij te werken en uit te schakelen, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Robin, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor Robin](./robin-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Robin in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Robin** in de lijst met toepassingen.

    ![De Robin Powered-koppeling de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://api.robinpowered.com/v1.0/scim-2` in bij **Tenant-URL**. Voer de waarde van het **SCIM-verificatietoken** in die eerder in **Token voor geheim** is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Robin. Als de verbinding mislukt, controleert u of het Robin-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Robin**.

    ![Robin Powered-gebruikerstoewijzingen](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met Robin. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de overeenkomende gebruikersaccounts in Robin te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Robin Powered-gebruikerskenmerken](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Robin**.

    ![Robin Powered-groepstoewijzingen](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. Controleer in de sectie **Kenmerktoewijzing** de groepskenmerken die vanuit Azure AD worden gesynchroniseerd met Robin. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de overeenkomende groepen in Robin te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Robin Powered-groepskenmerken](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor Robin.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten in Robin, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit. Hierin worden alle acties beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in Robin.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.



## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)