---
title: 'Zelf studie: StarLeaf configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op StarLeaf.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357928"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Zelf studie: StarLeaf configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in StarLeaf en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in StarLeaf.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een StarLeaf-Tenant](https://starleaf.com/).
* Een gebruikers account in StarLeaf met beheerders machtigingen.

## <a name="assign-users-to-starleaf"></a>Gebruikers toewijzen aan StarLeaf
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers en groepen in azure AD toegang nodig hebben tot StarLeaf. Vervolgens kunt u de gebruikers en groepen toewijzen aan StarLeaf door [deze instructies](../manage-apps/assign-user-or-group-access-portal.md)te volgen.

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Belang rijke tips voor het toewijzen van gebruikers aan StarLeaf

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan StarLeaf om de configuratie van automatische gebruikers inrichting te testen. U kunt later extra gebruikers en groepen toewijzen.

* Wanneer u een gebruiker toewijst aan StarLeaf, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol Standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-starleaf-for-provisioning"></a>StarLeaf instellen voor inrichting

Voordat u StarLeaf configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten configureren in StarLeaf:

1. Meld u aan bij de [StarLeaf-beheer console](https://portal.starleaf.com/#page=login). Navigeer naar **integraties**  >  **integratie toevoegen**.

    ![Scherm opname van de StarLeaf-beheer console met de integraties en het toevoegen van integratie opties.](media/starleaf-provisioning-tutorial/image00.png)

2. Selecteer het **type** dat moet worden Microsoft Azure Active Directory. Voer een passende naam in **naam** in. Klik op **Toepassen**.

    ![Scherm afbeelding van het dialoog venster integratie toevoegen met de tekst vakken type en naam.](media/starleaf-provisioning-tutorial/image01.png)

3.  De **scim basis-URL** en **toegangs token** waarden worden vervolgens weer gegeven. Deze waarden worden ingevoerd in de velden **Tenant-URL** en **geheim-token** op het tabblad inrichten van uw StarLeaf-toepassing in de Azure Portal. 

    ![Scherm afbeelding van het dialoog venster integratie bewerken met de tekst vakken type, naam en SCIM basis-URL.](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>StarLeaf toevoegen vanuit de galerie

Als u StarLeaf wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u StarLeaf van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om StarLeaf toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Ga naar de **[Azure-portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **StarLeaf** in het zoekvak en selecteer **StarLeaf** in het deel venster resultaten.
    ![StarLeaf in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Automatische gebruikers inrichting configureren voor StarLeaf

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in StarLeaf te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **StarLeaf**.

    ![De koppeling StarLeaf in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. In het gedeelte beheerders referenties voert u de **scim basis-URL** en **toegangs token** waarden in die respectievelijk eerder zijn opgehaald in de **Tenant-URL** en het **geheime token** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met StarLeaf. Als de verbinding mislukt, zorg er dan voor dat uw StarLeaf-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **een e-mail melding verzenden wanneer een fout** is opgetreden in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met StarLeaf**.

    ![Scherm afbeelding van de sectie toewijzingen waarin de optie Azure Active Directory gebruikers synchroniseren met StarLeaf wordt weer gegeven.](media/starleaf-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar StarLeaf in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in StarLeaf voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Scherm afbeelding van de sectie kenmerk toewijzingen waarin negen toewijzingen worden weer gegeven.](media/starleaf-provisioning-tutorial/userattribute.png)


10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Als u de Azure AD-inrichtings service voor **StarLeaf wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor StarLeaf door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op StarLeaf.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken

## <a name="connector-limitations"></a>Connectorbeperkingen

* StarLeaf biedt momenteel geen ondersteuning voor het inrichten van groepen. 
* StarLeaf vereist dat de waarden voor **e-mail** en **gebruikers naam** dezelfde bron waarde hebben.

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [controleren van Logboeken en het ophalen van rapporten over inrichtings activiteiten](../app-provisioning/check-status-user-account-provisioning.md).
