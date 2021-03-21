---
title: 'Zelfstudie: Visitly configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten in Visitly, of de inrichting van gebruikersaccounts ongedaan te maken.
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
ms.openlocfilehash: ff3f3ab65df2d801b7c962de7cce645e9fc00b30
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358606"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Zelfstudie: Visitly configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die u uitvoert in Visitly en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers of groepen voor Visitly.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar software-as-a-service (SaaS)-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in Openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Visitly-tenant](https://www.visitly.io/pricing/)
* Een gebruikersaccount in Visitly met beheerdersmachtigingen

## <a name="assign-users-to-visitly"></a>Gebruikers toewijzen aan Visitly 

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, beslist u welke gebruikers of groepen in Azure AD toegang nodig hebben tot Visitly. Vervolgens kunt u deze gebruikers of groepen aan Visitly toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Belangrijke tips voor het toewijzen van gebruikers aan Visitly 

* We raden u aan om één Azure AD-gebruiker toe te wijzen aan Visitly om de configuratie van automatische inrichting van gebruikers te testen. Extra gebruikers of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan Visitly toewijst, moet u een geldige toepassingsspecifieke rol toewijzen (indien beschikbaar) in het toewijzingsdialoogvenster. Gebruikers met de rol Standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-visitly-for-provisioning"></a>Visitly instellen voor inrichting

Voordat u Visitly configureert voor automatische inrichting van gebruikers met Azure AD, moet u inrichten met System for Cross-domain Identity Management (SCIM) inschakelen op Visitly.

1. Aanmelden bij [Visitly](https://app.visitly.io/login). Selecteer **Integraties** > **Synchronisatie van de host**.

    ![Synchronisatie van de host](media/Visitly-provisioning-tutorial/login.png)

2. Selecteer de sectie **Azure AD**.

    ![Sectie Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Kopieer de **API-sleutel**. Deze waarden worden ingevoerd in het vak **Token voor geheim** op het tabblad **Inrichten** van uw Visitly-toepassing in de Azure Portal.

    ![API-sleutel](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Visitly uit de galerie toevoegen

Als u Visitly wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, voegt u Visitly vanuit de Azure AD-toepassingsgalerie toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Visitly toe te voegen vanuit de Azure AD-toepassingsgalerie.

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiedeelvenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Visitly** in het zoekvak in, selecteer **Visitly** in het venster met resultaten en selecteer vervolgens **Toevoegen** om de toepassing toe te voegen.

    ![Visitly in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Automatische gebruikersinrichting configureren voor Visitly 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers of groepen in Visitly te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> Als u eenmalige aanmelding op basis van SAML wilt inschakelen voor Visitly, volgt u de instructies in de [Zelfstudie voor eenmalige aanmelding bij Visitly](Visitly-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Visitly in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Alle toepassingen](common/enterprise-applications.png)

2. Selecteer **Visitly** in de lijst met toepassingen.

    ![De Visitly-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. In het gedeelte beheerdersreferenties voert u de waarden voor `https://api.visitly.io/v1/usersync/SCIM` en **API-sleutel** in die eerder zijn opgehaald uit respectievelijk **Tenant-URL** en **Token voor geheim**. Selecteer **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Visitly. Als de verbinding mislukt, moet u controleren of uw Visitly-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Visitly**.

    ![Visitly-gebruikerstoewijzingen](media/visitly-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die vanuit Azure AD met Visitly worden gesynchroniseerd in de sectie **Kenmerktoewijzingen**. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Visitly te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

    ![Gebruikerskenmerken van Visitly](media/visitly-provisioning-tutorial/userattribute.png)

10. Volg de instructies in de [zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

11. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Visitly.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers of groepen die u aan Visitly wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij volgende synchronisaties. Voor meer informatie over hoe lang het duurt om gebruikers of groepen in te richten, zie [Hoe lang duurt het inrichten van gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

U kunt het gedeelte **Huidige status** gebruiken om de voortgang te controleren en links te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op Visitly worden uitgevoerd. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie. Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) als u de Azure AD-inrichtingslogboeken wilt lezen.

## <a name="connector-limitations"></a>Connectorbeperkingen

Visitly biedt geen ondersteuning voor harde verwijderingen. Er wordt alleen gebruikgemaakt van voorlopig verwijderen.

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
