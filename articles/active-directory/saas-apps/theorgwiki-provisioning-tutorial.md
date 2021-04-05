---
title: 'Zelfstudie: TheOrgWiki configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor TheOrgWiki.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357620"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Zelfstudie: TheOrgWiki configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in TheOrgWiki en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor TheOrgWiki.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een OrgWiki-tenant](https://www.theorgwiki.com/welcome/).
* Een gebruikersaccount in TheOrgWiki met beheerdersmachtigingen.

## <a name="assign-users-to-theorgwiki"></a>Gebruikers toewijzen aan TheOrgWiki

Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot TheOrgWiki. Eenmaal besloten, kunt u deze gebruikers en/of groepen aan TheOrgWiki toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Belangrijke tips voor het toewijzen van gebruikers aan TheOrgWiki

* Het wordt aanbevolen om een enkele Azure AD-gebruiker toe te wijzen aan TheOrgWiki om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan TheOrgWiki toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-theorgwiki-for-provisioning"></a>TheOrgWiki instellen voor inrichting

Voordat u TheOrgWiki configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen op TheOrgWiki.

1. Meld u aan bij de [TheOrgWiki-beheerconsole](https://www.theorgwiki.com/login/). Klik op **Beheerconsole**.

    ![Schermopname van Org wiki met de gebruikersavatar en de Beheerconsole gemarkeerd.](media/theorgwiki-provisioning-tutorial/login.png)

2. Klik in de beheerconsole op het **tabblad Instellingen**. 

    ![Schermopname van de beheerconsole van de The Org Wiki met het tabblad Instellingen gemarkeerd.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navigeer naar **Serviceaccounts**.

    ![Schermopname van de pagina Serviceaccounts in de Org Wiki-beheerconsole.](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klik op **+Serviceaccount**. Selecteer onder **Type serviceaccount** **Op tokens gebaseerd**. Klik op **Opslaan**.

    ![Schermopname van het dialoogvenster Nieuw serviceaccount met de opties Type serviceaccount, Op tokens gebaseerd en Opslaan gemarkeerd.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Kopieer de **Actieve tokens**. Deze waarde wordt ingevoerd in het veld Token voor geheim op het tabblad Inrichten van uw TheOrgWiki-toepassing in Azure Portal.
     
    ![Schermopname van het dialoogvenster Tokens beheren voor S C I M-inrichting.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>TheOrgWiki toevoegen vanuit de galerie

Als u TheOrgWiki wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u TheOrgWiki vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Ga naar **[Azure Portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **TheOrgWiki** in, selecteer **TheOrgWiki** in het resultatenvenster. 

    ![TheOrgWiki in de lijst met resultaten](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor TheOrgWiki** om naar de aanmeldingspagina van TheOrgWiki te gaan. 

    ![Schermopname van de aanmeldingspagina van TheOrgWiki met de URL gemarkeerd](media/theorgwiki-provisioning-tutorial/image00.png)

6.  Selecteer in de rechterbovenhoek **Aanmelden**.

    ![Schermopname van de rechterbovenhoek van de aanmelding pagina met de optie Aanmelden gemarkeerd.](media/theorgwiki-provisioning-tutorial/image02.png)

7. Aangezien TheOrgWiki een OpenIDConnect-app is, kiest u aanmelden bij TheOrgWiki met uw Microsoft-werkaccount.

    ![Schermopname van de aanmeldingspagina van TheOrgWiki met de optie Aanmelden met Microsoft gemarkeerd.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Na een geslaagde verificatie wordt de toepassing vervolgens automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw TheOrgWiki-account.

    ![SCIM toevoegen voor OrgWiki](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Automatische gebruikersinrichting configureren voor TheOrgWiki 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in TheOrgWiki te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor TheOrgWiki in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **TheOrgWiki** in de lijst met toepassingen.

    ![De OrgWiki-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` in **Tenant-URL** in. 

    Voorbeeld: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> De **Subdomeinwaarde** kan alleen worden ingesteld tijdens het eerste aanmeldingsproces voor TheOrgWiki.
 
6. Voer in het veld **Token voor geheim** de tokenwaarde in die u eerder hebt opgehaald uit TheOrgWiki. Klik in Azure Portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met TheOrgWiki. Als de verbinding mislukt, moet u controleren of uw TheOrgWiki-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met TheOrgWiki**.

    ![TheOrgWiki-gebruikerstoewijzingen](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met TheOrgWiki worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in TheOrgWiki te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikerskenmerken van TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor TheOrgWiki.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u aan TheOrgWiki wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij volgende synchronisaties. Voor meer informatie over hoe lang het duurt om gebruikers en/of groepen in te richten, zie [Hoe lang duurt het inrichten van gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

U kunt het gedeelte **Huidige status** gebruiken om de voortgang te controleren en koppelingen te volgen naar het activiteitenrapport van de inrichting, waarin alle acties worden beschreven die door de Azure AD-inrichtingsservice op TheOrgWiki worden uitgevoerd. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie. Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) als u de Azure AD-inrichtingslogboeken wilt lezen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md).