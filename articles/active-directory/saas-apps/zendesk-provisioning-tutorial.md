---
title: 'Zelfstudie: Zendesk configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten in Zendesk, of de inrichting van gebruikersaccounts ongedaan te maken.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359711"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Zelfstudie: Zendesk configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen getoond die moeten worden uitgevoerd in Zendesk en Azure AD (Azure Active Directory) om Azure AD te configureren om gebruikers en groepen automatisch in te richten in Zendesk, of de inrichting van gebruikers en groepen ongedaan te maken.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd op de Azure AD-service voor de inrichting van gebruikers. Zie voor informatie over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar software-as-a-service (SaaS)-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u het volgende heeft:

* Een Azure AD-tenant.
* Een Zendesk-tenant met het Professional-abonnement of is beter ingeschakeld.
* Een gebruikersaccount in Zendesk met beheerdersmachtigingen.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Zendesk toevoegen vanuit Azure Marketplace

Voordat u Zendesk configureert voor automatische inrichting van gebruikers met Azure AD, voegt u Zendesk vanuit de Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Volg deze stappen om Zendesk toe te voegen vanuit de Marketplace.

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiemenu aan de linkerkant.

    ![Het pictogram van Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Zendesk** in en selecteer **Zendesk** in het resultatenpaneel. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Zendesk in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Gebruikers toewijzen aan Zendesk

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische inrichting van gebruikers worden alleen de gebruikers of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, beslist u welke gebruikers of groepen in Azure AD toegang nodig hebben tot Zendesk. Volg de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md) als u deze gebruikers of groepen wilt toewijzen aan Zendesk.

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zendesk

* Tegenwoordig worden Zendesk-rollen automatisch en dynamisch ingevuld in de gebruikersinterface van Azure Portal. Voordat u Zendesk-rollen aan gebruikers toewijst, moet u ervoor zorgen dat een initiële synchronisatie met Zendesk is voltooid om de nieuwste rollen in uw Zendesk-tenant op te halen.

* We raden u aan om één Azure AD-gebruiker toe te wijzen aan Zendesk om de initiële configuratie van automatische inrichting van gebruikers te testen. U kunt later aanvullende gebruikers of groepen toewijzen nadat de tests zijn voltooid.

* Als u een gebruiker aan Zendesk toewijst, selecteert u een geldige toepassingsspecifieke rol (indien beschikbaar) in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Automatische gebruikersinrichting configureren voor Zendesk 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice. Gebruik dit om gebruikers of groepen in Zendesk te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor Zendesk. Volg de instructies in de [Zelfstudie voor eenmalige aanmelding voor Zendesk](zendesk-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Zendesk in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Ondernemingstoepassingen** > **Alle toepassingen** > **Zendesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zendesk** in de lijst met toepassingen.

    ![De link Zendesk in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Zendesk-inrichting](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Zendesk-inrichtingsmodus](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Voer in de sectie **Beheerdersreferenties** de gebruikersnaam van de beheerder, het geheime token en domein van uw Zendesk-account in. Voorbeelden van deze waarden zijn:

   * Vul in het vak **Gebruikersnaam van de beheerder** de gebruikersnaam van het beheerdersaccount in op uw Zendesk-tenant. Een voorbeeld is admin@contoso.com.

   * Vul in het vak **Geheim token** het geheime token in, zoals beschreven in stap 6.

   * Vul in het vak **Domein** het subdomein van uw Zendesk-tenant in. Voor een account met een tenant-URL van `https://my-tenant.zendesk.com` is uw subdomein bijvoorbeeld **my-tenant**.

6. Het geheime token voor uw Zendesk-account bevindt zich in **Beheer** > **API** > **Instellingen**. Zorg ervoor dat **Tokentoegang** is ingesteld op **Ingeschakeld**.

    ![Zendesk-beheerdersinstellingen](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Geheim token voor Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Nadat u de vakken die in stap 5 zijn weergegeven, hebt ingevuld, selecteert u **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Zendesk. Als de verbinding mislukt, moet u controleren of uw Zendesk-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Verbinding testen voor Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen over inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![Zendesk-e-mailmelding](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Zendesk**.

    ![Zendesk-gebruikerssynchronisatie](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Controleer de gebruikerskenmerken die vanuit Azure AD met Zendesk worden gesynchroniseerd in de sectie **Kenmerktoewijzingen**. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Zendesk te vinden voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen op te slaan.

    ![Overeenkomende gebruikerskenmerken van Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Zendesk**.

    ![Synchronisatie van Zendesk-groep](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Controleer de groepskenmerken die vanuit Azure AD met Zendesk worden gesynchroniseerd in de sectie **Kenmerktoewijzingen**. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Zendesk te vinden voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen op te slaan.

    ![Overeenkomende groepskenmerken van Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Volg de instructies in [de zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

15. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Zendesk.

    ![Zendesk-inrichtingsstatus](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. De gebruikers of groepen die u wilt inrichten voor Zendesk definiëren. Selecteer in de sectie **Instellingen** de waarden die u wilt in **Bereik**.

    ![Zendesk-bereik](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Zendesk opslaan](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij latere synchronisaties. Ze vinden ongeveer elke 40 minuten plaats, zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en links naar het rapport over de inrichtingsactiviteit te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD-inrichtingsservice op Zendesk.

Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Zendesk biedt alleen ondersteuning voor het gebruik van groepen voor gebruikers met **Agent**-rollen. Raadpleeg de [documentatie van Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups) voor meer informatie.

* Wanneer een aangepaste rol wordt toegewezen aan een gebruiker of groep, wijst de automatische gebruikersinrichtingsservice van Azure AD ook de standaardrol **Agent** toe. Alleen aan Agents kan een aangepaste rol worden toegewezen. Raadpleeg de [documentatie van Zendesk-API](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests) voor meer informatie. 

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
