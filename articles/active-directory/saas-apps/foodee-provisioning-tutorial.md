---
title: 'Zelfstudie: Foodee configureren voor automatische inrichting van gebruikers met behulp van Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten in Foodee, of de inrichting van gebruikersaccounts ongedaan te maken.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358674"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Zelfstudie: Foodee configureren voor automatische gebruikersinrichting

In dit artikel leert u hoe u Azure Active Directory (Azure AD) in Foodee en Azure AD configureert om gebruikers of groepen automatisch in te richten of om de inrichting ongedaan te maken voor Foodee.

> [!NOTE]
> In dit artikel wordt een connector beschreven die is gebouwd op de Azure AD-service voor de inrichting van gebruikers. Zie [Gebruikersinrichtingen en het ongedaan maken van inrichtingen automatiseren voor Saas-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md) voor informatie over wat deze service doet en hoe deze werkt, en voor antwoorden op veelgestelde vragen.
>
> Deze connector is momenteel beschikbaar in preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet aan de volgende vereisten worden voldaan:

* Een Azure AD-tenant
* [Een Foodee-tenant](https://www.food.ee/about/)
* Een gebruikersaccount in Foodee met beheerdersmachtigingen

## <a name="assign-users-to-foodee"></a>Gebruikers toewijzen aan Foodee 

Azure AD gebruikt een concept met de naam *Toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers of groepen in Azure AD toegang nodig hebben tot Foodee. Nadat u deze beslissing hebt genomen, kunt u deze gebruikers en groepen toewijzen aan Foodee door de instructies te volgen in [Een gebruiker of groep toewijzen aan een bedrijfstoepassing](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Belangrijke tips voor het toewijzen van gebruikers aan Foodee 

Denk aan de volgende tips wanneer u gebruikers toewijst:

* We raden u aan om één Azure AD-gebruiker toe te wijzen aan Foodee om de configuratie van automatische inrichting van gebruikers te testen. U kunt later aanvullende gebruikers of groepen toewijzen.

* Als u een gebruiker aan Foodee toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het **Toewijzingsdeelvenster**. Gebruikers met de rol *Standaardtoegang* worden uitgesloten van het inrichten.

## <a name="set-up-foodee-for-provisioning"></a>Foodee instellen voor inrichting

Voordat u Foodee configureert voor automatische inrichting van gebruikers met Azure AD, moet u inrichten met System for Cross-domain Identity Management (SCIM) inschakelen op Foodee.

1. Meld u aan bij [Foodee](https://www.food.ee/login/) en selecteer vervolgens uw tenant-id.

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Schermopname van het menu in de Foodee Enterprise Portal. Een tijdelijke aanduiding voor de tenant-id wordt weergegeven in het menu." border="false":::

1. Selecteer **Eenmalige aanmelding** onder **Enterprise Portal**.

    ![Het menu aan de linkerzijde van het deelvenster binnen de Foodee Enterprise Portal](media/Foodee-provisioning-tutorial/scim.png)

1. Kopieer de waarde in het vak **API-token** voor later gebruik. U gaat deze invoeren in het vak **Geheime token** op het tabblad **Inrichten** van uw Foodee-toepassing in de Azure Portal.

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Schermopname van een pagina in de Foodee Enterprise Portal. Een API-tokenwaarde is gemarkeerd." border="false":::

## <a name="add-foodee-from-the-gallery"></a>Foodee toevoegen vanuit de galerie

Als u Foodee wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Foodee vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

Doe het volgende om Foodee toe te voegen vanuit de Azure AD-toepassingsgalerie:

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**.

    ![De Azure Active Directory-opdracht](common/select-azuread.png)

1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Om een nieuwe toepassing toe te voegen selecteert u **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

1. Voer **Foodee** in het zoekvak in, selecteer vervolgens **Foodee** in het resultatenvenster en selecteer tot slot **Toevoegen** om de toepassing toe te voegen.

    ![Foodee in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Automatische gebruikersinrichting configureren voor Foodee 

In deze sectie configureert u de Azure AD-inrichtingsservice om gebruikers of groepen in Foodee te maken, bij te werken en uit te schakelen. U doet dit op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt ook eenmalige aanmelding op basis van SAML inschakelen voor Foodee. U volgt dan de instructies in de [Zelfstudie voor eenmalige aanmelding bij Foodee](Foodee-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

U kunt de automatische gebruikersinrichting voor Foodee in Azure AD configureren door de volgende handelingen uit te voeren:

1. Selecteer, in de [Azure Portal](https://portal.azure.com), **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Foodee** in de lijst **Toepassingen**.

    ![De Foodee-link in de lijst Toepassingen](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie 'Inrichting' gemarkeerd.](common/provisioning.png)

1. Selecteer **Automatisch** in de vervolgkeuzelijst **Inrichtingsmodus**.

    ![Schermopname van de vervolgkeuzelijst 'Inrichtingsmodus' met de optie 'Automatisch' gemarkeerd.](common/provisioning-automatic.png)

1. Ga als volgt te werk onder **Referenties voor beheerders**:

   a. Voer in het vak **Tenant-URL** de waarde **https:\//concierge.food.ee/scim/v2** in die u eerder hebt opgehaald.

   b. Voer in het vak **Geheim token** de waarde van de **API-token** in die u eerder hebt opgehaald.
   
   c. Selecteer **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Foodee. Als de verbinding mislukt, zorg er dan voor dat uw Foodee-account beheerdersmachtigingen heeft en probeer het opnieuw.

    ![De link 'Verbinding testen'](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen. Zet vervolgens een vinkje in het selectievakje **Verzend een e-mailmelding als een fout optreedt**.

    ![Het tekstvak van de e-mailmelding](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer, in de sectie **Toewijzingen**, de optie **Azure Active Directory-gebruikers synchroniseren met Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="Schermopname van de sectie 'Toewijzingen'. Onder 'Naam' is 'Synchroniseer Azure Active Directory-gebruikers met Foodee' gemarkeerd." border="false":::

1. Controleer, in **Kenmerktoewijzingen**, de gebruikerskenmerken die vanuit Azure AD met Foodee worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de *Gebruikersaccounts* in Foodee te vinden voor updatebewerkingen. 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="Schermopname van de pagina 'Kenmerktoewijzingen'. Een tabel bevat Azure Active Directory-kenmerken, Foodee-kenmerken en de overeenkomende prioriteit." border="false":::

1. Klik op **Opslaan** om uw wijzigingen door te voeren.
1. Selecteer, in de sectie **Toewijzingen**, de optie **Azure Active Directory-groepen synchroniseren met Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="Schermopname van de sectie 'Toewijzingen'. Onder 'Naam' is 'Synchroniseer Azure Active Directory-groepen met Foodee' gemarkeerd." border="false":::

1. Controleer, in **Kenmerktoewijzingen**, de gebruikerskenmerken die vanuit Azure AD met Foodee worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de *Groepsaccounts* in Foodee te vinden voor updatebewerkingen.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="Schermopname van de pagina 'Kenmerktoewijzingen'. Een tabel bevat Azure Active Directory-kenmerken, Foodee-kenmerken en de overeenkomende prioriteit." border="false":::

1. Klik op **Opslaan** om uw wijzigingen door te voeren.
1. De filters voor het bereik configureren. Raadpleeg de instructies in de [Zelfstudie over filters voor bereik](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u filters voor bereik wilt configureren.

1. Wijzig de **Inrichtingsstatus** naar **Ingeschakeld** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Foodee.

    ![De schakelaar van de inrichtingsstatus](common/provisioning-toggle-on.png)

1. Definieer onder **Instellingen** in de vervolgkeuzelijst **Bereik** de gebruikers of groepen die u wilt inrichten naar Foodee.

    ![De vervolgkeuzelijst voor het inrichtingsbereik](common/provisioning-scope.png)

1. Klik op **Opslaan** als u klaar bent voor het inrichten.

    ![De knop 'Opslaan' van de inrichtingsconfiguratie](common/provisioning-configuration-save.png)

Met de voorgaande bewerking wordt de eerste synchronisatie gestart van de gebruikers of groepen die u hebt gedefinieerd in de vervolgkeuzelijst **Bereik**. Het uitvoeren van de eerste synchronisatie duurt langer dan bij volgende synchronisaties. Zie [Hoelang duurt het inrichten van gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) voor meer informatie.

U kunt de sectie **Huidige status** gebruiken om de voortgang te bewaken en links naar het rapport over de inrichtingsactiviteit te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD-inrichtingsservice op Foodee. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie. Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) als u de Azure AD-inrichtingslogboeken wilt lezen.

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
