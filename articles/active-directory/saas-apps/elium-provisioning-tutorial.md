---
title: 'Zelfstudie: Elium configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Elium.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005500"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Zelfstudie: Elium configureren voor automatische gebruikersinrichting

In deze zelfstudie leert u hoe u Elium en Azure Active Directory (Azure AD) configureert om gebruikers of groepen automatisch in te richten of om de inrichting ongedaan te maken voor Elium.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd op de Azure AD-service voor de inrichting van gebruikers. Raadpleeg [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-toepassingen automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md) voor belangrijke informatie over wat deze service doet en hoe deze werkt, en voor veelgestelde vragen.
>
> Deze connector is momenteel beschikbaar in preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor algemene gebruiksvoorwaarden voor Azure-functies.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Elium-tenant](https://www.elium.com/pricing/)
* Een gebruikersaccount in Elium met beheerdersmachtigingen

## <a name="assigning-users-to-elium"></a>Gebruikers toewijzen aan Elium

Azure AD gebruikt een concept met de naam *Toewijzingen*, om te bepalen welke gebruikers toegang krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, beslist u welke gebruikers en groepen in Azure AD toegang nodig hebben tot Elium. Wijs deze gebruikers en groepen vervolgens toe aan Elium door de stappen te volgen in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Belangrijke tips voor het toewijzen van gebruikers aan Elium 

We raden u aan om één Azure AD-gebruiker toe te wijzen aan Elium om de configuratie van automatische inrichting van gebruikers te testen. U kunt later meer gebruikers en groepen toewijzen.

Als u een gebruiker aan Elium toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaardtoegang** worden uitgesloten van het inrichten.

## <a name="set-up-elium-for-provisioning"></a>Elium instellen voor inrichting

Voordat u Elium configureert voor automatische inrichting van gebruikers met Azure AD, moet u inrichten met System for Cross-domain Identity Management (SCIM) inschakelen op Elium. Volg deze stappen:

1. Meld u aan bij Elium en ga naar **Mijn profiel** > **Instellingen**.

    ![Menu-item instellingen in Elium](media/Elium-provisioning-tutorial/setting.png)

1. Selecteer **Beveiliging** in de linkerbenedenhoek onder **GEAVANCEERD**.

    ![Beveiligingslink in Elium](media/Elium-provisioning-tutorial/security.png)

1. Kopieer de waarden van **Tenant-URL** en **Token voor geheim**. U gebruikt deze waarden later in de bijbehorende velden op het tabblad **Inrichting** van uw Elium-toepassing in de Azure Portal.

    ![De velden Tenant-URL en Token voor geheim in Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Elium toevoegen vanuit de galerie

Als u Elium wilt configureren voor automatische inrichting van gebruikers met Azure AD, moet u Elium vanuit de Azure AD-toepassingsgalerie toevoegen aan de lijst met beheerde software-as-a-service (SaaS)-toepassingen. Volg deze stappen:

1. Ga naar [Azure Portal](https://portal.azure.com) en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![Menuopdracht van Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

     ![De blade Azure AD-bedrijfstoepassingen](common/enterprise-applications.png)

1. Om een nieuwe toepassing toe te voegen selecteert u **Nieuwe toepassing** bovenin het deelvenster.

    ![Nieuwe toepassingslink](common/add-new-app.png)

1. Typ **Elium** in het zoekvak, selecteer vervolgens **Elium** in de resultatenlijst en selecteer tot slot **Toevoegen** om de toepassing toe te voegen.

    ![Zoekvak van galerie](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Automatische gebruikersinrichting configureren voor Elium

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en groepen in Elium te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding in te schakelen voor Elium op basis van Security Assertion Markup Language (SAML) door de instructies in de [Zelfstudie eenmalige aanmelding voor Elium](Elium-tutorial.md)te volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar de twee functies vormen een aanvulling op elkaar.

Ga als volgt te werk om het automatisch inrichten van gebruikers te configureren voor Elium in Azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), selecteer **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Azure AD-bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Elium** in de lijst met toepassingen.

    ![Lijst met toepassingen op de blade bedrijfstoepassingen](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Het tabblad Inrichten op de blade Bedrijfstoepassingen](common/provisioning.png)

1. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Automatische instelling voor Inrichtingsmodus](common/provisioning-automatic.png)

1. Typ in de sectie **Beheerdersreferenties** **\<tenantURL\>/scim/v2** in het veld **Tenant-URL**. (De **tenantURL** is de waarde die eerder is opgehaald uit de Elium-beheerconsole.) Typ ook de waarde van **Token voor geheim** van Elium in het veld **Token voor geheim**. Selecteer tenslotte **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Elium. Als de verbinding mislukt, moet u controleren of uw Elium-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![De velden Tenant-URL en Token voor geheim in Beheerdersreferenties](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten ontvangen. Selecteer vervolgens het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

1. Klik op **Opslaan**.

1. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Elium**.

    ![Link synchroniseren voor het toewijzen van Azure AD-gebruikers aan Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Elium worden gesynchroniseerd. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Elium te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

    ![Kenmerktoewijzingen tussen Azure AD en Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Volg de instructies in de [zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

1. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Elium.

    ![De inrichtingsstatus is ingesteld op Aan](common/provisioning-toggle-on.png)

1. Definieer de gebruikers en groepen die u wilt inrichten voor Elium door de gewenste waarden te selecteren in de vervolgkeuzelijst **Bereik** in de sectie **Instellingen**.

    ![Keuzelijst Inrichtingsbereik](common/provisioning-scope.png)

1. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![De knop Opslaan van de inrichtingsconfiguratie](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Dit initiële synchronisatieproces duurt langer dan latere synchronisaties. Zie [Hoelang duurt het inrichten van gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) voor meer informatie over de tijd die nodig is voor het inrichten.

Gebruik de sectie **Huidige status** om de voortgang te bewaken en links naar het rapport over de inrichtingsactiviteit te volgen. In het rapport met inrichtingsactiviteit worden alle acties beschreven die met de Azure AD-inrichtingsservice worden uitgevoerd in Elium. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie. Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) als u de Azure AD-inrichtingslogboeken wilt lezen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
