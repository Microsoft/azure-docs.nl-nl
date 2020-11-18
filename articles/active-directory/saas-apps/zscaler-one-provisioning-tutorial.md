---
title: 'Zelfstudie: Zscaler One configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u automatisch gebruikersaccounts van Azure Active Directory inricht in Zscaler One, of de inrichting ervan ongedaan maakt.
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
ms.openlocfilehash: b8b6383c7808fd6c298d7776fc10572631bc6ddc
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359558"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler One configureren voor automatische inrichting van gebruikers

In deze zelfstudie worden de stappen weergegeven die u moet uitvoeren in Zscaler One en Azure AD (Azure Active Directory) om Azure AD te configureren om automatisch gebruikers en groepen in te richten in Zscaler One, of de inrichting ervan ongedaan te maken.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd op de Azure AD-service voor de inrichting van gebruikers. Raadpleeg [Inrichting en ongedaan maken van inrichting van gebruikers in SaaS-toepassingen (software-as-a-service) automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md) voor informatie over wat deze service doet, hoe het werkt en veelgestelde vragen.


## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u het volgende heeft:

* Een Azure AD-tenant.
* Een Zscaler One-tenant.
* Een gebruikersaccount in Zscaler One met beheerdersmachtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de Zscaler One SCIM API. Deze API is beschikbaar voor Zscaler One-ontwikkelaars voor accounts met het Enterprise-pakket.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Zscaler One toevoegen vanuit de Azure Marketplace

Voordat u Zscaler One configureert voor automatische inrichting van gebruikers met Azure AD, voegt u Zscaler One vanuit de Azure Marketplace toe aan de lijst met beheerde SaaS-toepassingen.

Volg deze stappen om Zscaler One toe te voegen vanuit de Marketplace.

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiemenu aan de linkerkant.

    ![Het pictogram van Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Zscaler One** in en selecteer **Zscaler One** in het resultatenpaneel. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Zscaler One in de lijst met resultaten](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Gebruikers toewijzen aan Zscaler One

Azure Active Directory gebruikt een concept met de naam *Toewijzingen*, om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische inrichting van gebruikers worden alleen de gebruikers of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, beslist u welke gebruikers of groepen in Azure AD toegang nodig hebben tot Zscaler One. Volg de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md) als u deze gebruikers of groepen wilt toewijzen aan Zscaler One.

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler One

* We raden u aan om één Azure AD-gebruiker toe te wijzen aan Zscaler One om de configuratie van automatische inrichting van gebruikers te testen. U kunt later aanvullende gebruikers of groepen toewijzen.

* Als u een gebruiker toewijst aan Zscaler One, selecteert u een geldige toepassingsspecifieke rol (indien beschikbaar) in het dialoogvenster Toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Automatische inrichting van gebruiker configureren in Zscaler One

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice. Gebruik dit om gebruikers of groepen in Zscaler One te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor Zscaler One. Volg de instructies in de zelfstudie [Eenmalige aanmelding voor Zscaler One](zscaler-One-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Zscaler One in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen** > **Zscaler One**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler One** in de lijst met toepassingen.

    ![De Zscaler One-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Zscaler One-inrichting](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Zscaler One-inrichtingsmodus](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Vul in de sectie **Beheerdersreferenties** de vakken **Tenant-URL** en **Token voor geheim** in met de instellingen van uw Zscaler One-account, zoals wordt beschreven in stap 6.

6. Ga naar **Beheer** > **Verificatie-instellingen** in de gebruikersinterface van de Zscaler One-portal om de tenant-URL en het token voor geheim te verkrijgen. Kies onder **Verificatietype** de optie **SAML**.

    ![Verificatie-instellingen voor Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecteer **SAML configureren** om de opties voor **SAML configureren** te openen.

    ![SAML configureren voor Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selecteer **Inrichting op basis van SCIM inschakelen** om de instellingen in **Basis-URL** en **Bearer-token** te openen. Sla de instellingen vervolgens op. Kopieer de instelling **Basis-URL** naar **Tenant-URL** in de Azure-portal. Kopieer de instelling **Bearer-token** naar **Token voor geheim** in de Azure-portal.

7. Nadat u de vakken uit stap 5 hebt ingevuld, selecteert u **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Zscaler One. Als de verbinding mislukt, controleert u of het Zscaler One-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Testverbinding met Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Voer in het vak **E-mailmelding** het e-mailadres in van een persoon of groep die de meldingen over inrichtingsfouten moet ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![E-mailmelding voor Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Zscaler One**.

    ![Zscaler One-gebruikerssynchronisatie](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met Zscaler One. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Zscaler One te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen op te slaan.

    ![Overeenkomende gebruikerskenmerken van Zscaler One](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Zscaler One**.

    ![Zscaler One-groepssynchronisatie](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler One. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Zscaler One te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen op te slaan.

    ![Overeenkomende groepskenmerken van Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Volg de instructies in [de zelfstudie over bereikfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

15. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor Zscaler One.

    ![Zscaler One-inrichtingsstatus](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers of groepen die u wilt inrichten in Zscaler One. Selecteer in de sectie **Instellingen** de gewenste waarden in **Bereik**.

    ![Bereik in Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Selecteer **Opslaan** als u klaar bent om in te richten.

    ![Opslaan in Zscaler One](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de initiële synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij latere synchronisaties. Ze vinden ongeveer elke 40 minuten plaats, zolang de Azure AD-inrichtingsservice actief is. 

U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit. In het rapport worden alle acties beschreven die met de Azure AD-inrichtingsservice worden uitgevoerd in Zscaler One.

Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichting van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png