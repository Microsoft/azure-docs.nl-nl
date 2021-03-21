---
title: 'Zelfstudie: Cornerstone OnDemand configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Cornerstone OnDemand.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 59c599167089d222324ed880c18e68d763f5e468
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358453"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Zelfstudie: Cornerstone OnDemand configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen gedemonstreerd die moeten worden uitgevoerd in Cornerstone OnDemand en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers of groepen voor Cornerstone OnDemand.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie voor informatie over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar software-as-a-service (SaaS)-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u het volgende heeft:

* Een Azure AD-tenant.
* Een Cornerstone OnDemand-tenant.
* Een gebruikersaccount in Cornerstone OnDemand met beheerdersmachtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [Cornerstone OnDemand-webservice](https://www.cornerstoneondemand.com/). Deze service is beschikbaar voor Cornerstone OnDemand-teams.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Cornerstone OnDemand toevoegen uit de Azure Marketplace

Voordat u Cornerstone OnDemand configureert voor het automatisch inrichten van gebruikers met Azure AD, voegt u Cornerstone OnDemand vanuit de Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Cornerstone OnDemand toe te voegen vanuit de Marketplace.

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiemenu aan de linkerkant.

    ![Het pictogram van Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Cornerstone OnDemand** in en selecteer **Cornerstone OnDemand** in het resultatenvenster. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Cornerstone OnDemand in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Gebruikers toewijzen aan Cornerstone OnDemand

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, beslist u welke gebruikers of groepen in Azure AD toegang nodig hebben tot Cornerstone OnDemand. Volg de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md) als u deze gebruikers of groepen wilt toewijzen aan Cornerstone OnDemand.

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Belangrijke tips voor het toewijzen van gebruikers aan Cornerstone OnDemand

* We raden u aan om één Azure AD-gebruiker toe te wijzen aan Cornerstone OnDemand om de configuratie van automatische inrichting van gebruikers te testen. U kunt later aanvullende gebruikers of groepen toewijzen.

* Als u een gebruiker aan Cornerstone OnDemand toewijst, selecteert u een geldige toepassingsspecifieke rol, indien beschikbaar, in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Automatische gebruikersinrichting configureren voor Cornerstone OnDemand

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice. Gebruik dit om gebruikers of groepen in Cornerstone OnDemand te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

Ga als volgt te werk om het automatisch inrichten van gebruikers te configureren voor Cornerstone OnDemand in Azure AD.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen** > **Cornerstone OnDemand**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Cornerstone OnDemand** in de lijst met toepassingen.

    ![De Cornerstone OnDemand-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Cornerstone OnDemand inrichten](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Inrichtingsmodus van Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Voer de gebruikersnaam van de beheerder, het beheerderswachtwoord en het domein van uw Cornerstone OnDemand-account in de sectie **Beheerdersreferenties** in:

    * Vul in het vak **Gebruikersnaam van de beheerder** het domein of de gebruikersnaam van het beheerdersaccount in op uw Cornerstone OnDemand-tenant. Een voorbeeld is contoso\admin.

    * Vul in het vak **Beheerderswachtwoord** het wachtwoord in dat overeenkomt met de gebruikersnaam van de beheerder.

    * Vul in het vak **Domein** de webservice-URL in van de Cornerstone OnDemand-tenant. De service bevindt zich bijvoorbeeld op `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx` en voor Contoso is het domein `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Zie [deze pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf) voor meer informatie over het ophalen van de URL van de webservice.

6. Nadat u de vakken die in stap 5 zijn weergegeven, hebt ingevuld, selecteert u **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Cornerstone OnDemand. Als de verbinding mislukt, moet u controleren of uw Cornerstone OnDemand-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Een Cornerstone OnDemand-testverbinding](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen over inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![Emailadres voor meldingen van Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selecteer **Opslaan**.

9. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Cornerstone OnDemand**.

    ![Cornerstone OnDemand-synchronisatie](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Cornerstone OnDemand worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Cornerstone OnDemand te vinden voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen op te slaan.

    ![Cornerstone OnDemand-kenmerktoewijzingen](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Volg de instructies in [de zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

12. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Cornerstone OnDemand.

    ![Inrichtingsstatus van Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. De gebruikers of groepen die u wilt inrichten voor Cornerstone OnDemand definiëren. Selecteer in de sectie **Instellingen** de waarden die u wilt in **Bereik**.

    ![Bereik van Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Opslaan van Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij latere synchronisaties. Ze vinden ongeveer elke 40 minuten plaats, zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en links naar het rapport over de inrichtingsactiviteit te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD-inrichtingsservice op Cornerstone OnDemand.

Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

Het **Positie**-kenmerk van Cornerstone OnDemand verwacht een waarde die overeenkomt met de rollen op de portal van Cornerstone OnDemand. Als u een lijst met geldige **Positie**-waarden wilt ophalen, gaat u naar **Gebruikersrecord bewerken > Organisatiestructuur > Positie** in de Cornerstone OnDemand-portal.

![Gebruikersrecord bewerken van Cornerstone OnDemand-inrichting](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Positie van Cornerstone OnDemand-inrichting](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Positielijst van Cornerstone OnDemand-inrichting](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
