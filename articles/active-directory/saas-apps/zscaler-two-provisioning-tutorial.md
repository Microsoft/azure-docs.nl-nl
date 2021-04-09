---
title: 'Zelfstudie: Zscaler Two configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: In deze zelfstudie leert u hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en om de inrichting ongedaan te maken voor Zscaler Two.
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
ms.openlocfilehash: 1c63639321966cbb73f5c47524f408d17ba46c3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936553"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler Two configureren voor het automatisch inrichten van gebruikers

In deze zelfstudie leert u hoe u Azure Active Directory (Azure AD) configureert om gebruikers en/of groepen automatisch in te richten en/of om de inrichting ongedaan te maken voor Zscaler Two.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Raadpleeg [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md) (Het inrichten en ongedaan maken van het inrichten van gebruikers voor SaaS-toepassingen automatiseren met Azure Active Directory) voor belangrijke informatie over wat deze service doet en hoe deze werkt, en voor antwoorden op veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen uit te voeren die in deze zelfstudie zijn beschreven:

* Een Azure AD-tenant.
* Een Zscaler Two-tenant.
* Een gebruikersaccount in Zscaler Two met beheerdersmachtigingen.

> [!NOTE]
> De integratie voor inrichting met Azure AD steunt op de Zscaler Two SCIM-API, die beschikbaar is voor Enterprise-accounts.

## <a name="add-zscaler-two-from-the-gallery"></a>Voeg Zscaler Two toe vanuit de galerie

Voordat u Zscaler Two configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Zscaler Two vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**:

![Selecteer Azure Active Directory](common/select-azuread.png)

Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**:

![Bedrijfstoepassingen](common/enterprise-applications.png)

Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

![Selecteer Nieuwe toepassing](common/add-new-app.png)

Voer in het zoekvak **Zscaler Two** in. Selecteer **Zscaler Two** in de resultaten en selecteer vervolgens **Toevoegen**.

![Lijst met resultaten](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Gebruikers toewijzen aan Zscaler Two

Azure AD-gebruikers moeten toegang krijgen tot de geselecteerde apps voordat ze deze kunnen gebruiken. In de context van het automatisch inrichten van gebruikers worden alleen gebruikers of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Zscaler Two. Nadat u dat hebt besloten, kunt u deze gebruikers en groepen toewijzen aan Zscaler Two door de instructies in [Assign a user or group to an enterprise app](../manage-apps/assign-user-or-group-access-portal.md) (Een gebruiker of groep toewijzen aan een bedrijfs-app) te volgen.

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler Two

* We raden u aan om eerst één Azure AD-gebruiker toe te wijzen aan Zscaler Two om de configuratie van automatische inrichting van gebruikers te testen. U kunt later meer gebruikers en groepen toewijzen.

* Als u een gebruiker aan Zscaler Two toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaardtoegang** worden uitgesloten van het inrichten.

## <a name="set-up-automatic-user-provisioning"></a>Automatische inrichting van gebruikers instellen

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en groepen in Zscaler Two te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzingen in Azure AD.

> [!TIP]
> Mogelijk wilt u ook eenmalige aanmelding op basis van SAML inschakelen voor Zscaler Two. Volg in dat geval de instructies in de zelfstudie [Eenmalige aanmelding voor Zscaler Two](zscaler-two-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar de twee functies vormen een aanvulling op elkaar.

> [!NOTE]
> Wanneer gebruikers en groepen zijn ingericht of als de inrichting ongedaan is gemaakt, wordt u aangeraden het inrichten periodiek opnieuw te starten om ervoor te zorgen dat groepslidmaatschappen correct worden bijgewerkt. Als het inrichten opnieuw wordt gestart, wordt de service gedwongen om alle groepen opnieuw te evalueren en de lidmaatschappen bij te werken. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **Bedrijfstoepassingen** > **Alle toepassingen** > **Zscaler Two**:

    ![Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler Two** in de lijst met toepassingen:

    ![Lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichting**:

    ![Inrichten in Zscaler Two](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**:

    ![De inrichtingsmodus instellen](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. Voer in de sectie **Beheerdersreferenties** de **Tenant-URL** en **Geheim token** van uw Zscaler Two-account in, zoals wordt beschreven in de volgende stap.

6. U kunt de **Tenant-URL** en **Geheim token** ophalen via **Beheer** > **Verificatie-instellingen** in de Zscaler Two-portal. Selecteer vervolgens **SAML** onder **Verificatietype**:

    ![Verificatie-instellingen voor Zscaler Two](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Selecteer **SAML configureren** om het venster **SAML configureren** te openen:

    ![Het venster SAML configureren](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Selecteer **Inrichting op basis van SCIM inschakelen** en kopieer de **Basis-URL** en het **Bearer-token**. Sla de instellingen vervolgens op. Plak in de Azure Portal de **Basis-URL** in het vak **Tenant-URL** en het **Bearer-token** in het vak **Geheim token**.

7. Selecteer na het invoeren van de waarden in de vakken **Tenant-URL** en **Geheim token** de optie **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Zscaler Two. Als de verbinding mislukt, moet u controleren of uw Zscaler Two-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![De verbinding testen](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Selecteer **Een e-mail verzenden wanneer er een fout optreedt**:

    ![E-mailmeldingen instellen](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Selecteer **Opslaan**.

10. Selecteer onder het kopje **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met ZscalerTwo**:

    ![Azure AD-gebruikers synchroniseren](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Zscaler Two worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Zscaler Two te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

    ![Schermopname van de sectie Kenmerktoewijzingen met zeven toewijzingen weergegeven.](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer onder het kopje **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met ZscalerTwo**:

    ![Azure AD-groepen synchroniseren](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Zscaler Two worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Zscaler Two te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

    ![Schermopname van de sectie Kenmerktoewijzingen met drie toewijzingen weergegeven.](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Raadpleeg de instructies in [de zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

15. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Zscaler Two:

    ![Inrichtingsstatus](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers en/of groepen die u wilt inrichten voor Zscaler Two door onder **Scope** in de sectie **Instellingen** de gewenste waarden te kiezen:

    ![Bereikwaarden](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Selecteer **Opslaan** als u klaar bent voor het inrichten:

    ![Opslaan selecteren](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de voortgang bewaken in de sectie **Synchronisatiedetails**. U kunt ook koppelingen volgen naar een rapport met inrichtingsactiviteiten. Daarin worden alle acties beschreven die door de Azure AD-inrichtingsservice worden uitgevoerd voor Zscaler Two.

Zie [Reporting on automatic user account provisioning](../app-provisioning/check-status-user-account-provisioning.md) (Rapportage over automatische toewijzing van gebruikersaccounts) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png