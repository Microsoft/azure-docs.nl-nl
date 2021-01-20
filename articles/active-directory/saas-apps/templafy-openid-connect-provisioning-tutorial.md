---
title: 'Zelf studie: Configure Templafy OpenID Connect Connect for automatic user Provisioning with Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts naar Templafy OpenID Connect Connect.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: f9e462d66e438e42c40b16ba9b818ddd330e9e5a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98612061"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>Zelf studie: Templafy OpenID Connect Connect configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Templafy OpenID Connect Connect en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen naar Templafy OpenID Connect Connect.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Templafy-tenant](https://www.templafy.com/pricing/).
* Een gebruikersaccount in Templafy met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Templafy OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="assigning-users-to-templafy-openid-connect"></a>Gebruikers toewijzen aan Templafy OpenID Connect Connect

Azure Active Directory gebruikt een concept dat *toewijzingen* wordt genoemd om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Templafy OpenID Connect Connect. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan Templafy OpenID Connect Connect door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>Belang rijke tips voor het toewijzen van gebruikers aan Templafy OpenID Connect Connect

* Het is raadzaam dat er één Azure AD-gebruiker wordt toegewezen aan Templafy OpenID connect verbinding maken om de configuratie van automatische gebruikers inrichting te testen. U kunt later meer gebruikers en/of groepen toewijzen.

* Wanneer u een gebruiker toewijst aan Templafy OpenID Connect Connect, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>Stap 2. Templafy OpenID connect-verbinding configureren voor ondersteuning bij het inrichten met Azure AD

Voordat u Templafy OpenID Connect configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM Provisioning inschakelen op Templafy OpenID Connect Connect.

1. Meld u aan bij de Templafy-beheerconsole. Klik op **Beheer**.

    ![Templafy-beheerconsole](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. Klik op **Verificatiemethode**.

    ![Schermopname van sectie Templafy-beheer met de optie Verificatiemethode gemarkeerd.](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. Kopieer de **scim API-sleutel** waarde. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw Templafy OpenID Connect Connect-toepassing in de Azure Portal.

    ![Een scherm afbeelding van de SCIM API-sleutel.](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>Stap 3. Templafy OpenID connect-verbinding toevoegen vanuit de galerie

Als u Templafy OpenID Connect Connect wilt configureren voor automatische gebruikers inrichten met Azure AD, moet u Templafy OpenID Connect toevoegen vanuit de Azure AD-toepassings galerie naar uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Templafy OpenID Connect Connect toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Ga naar de **[Azure-portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Templafy OpenID Connect Connect** in, selecteer **Templafy OpenID Connect Connect** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Templafy OpenID Connect Connect in de lijst met resultaten](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>Stap 4. Automatische gebruikers inrichting configureren voor Templafy OpenID Connect Connect 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen te maken, bij te werken en uit te scha kelen in Templafy OpenID connect verbinding maken op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van OpenID Connect voor Templafy in te scha kelen, gevolgd door de instructies in de [Templafy-zelf studie voor eenmalige aanmelding](templafy-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Templafy OpenID Connect Connect in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Templafy OpenID Connect Connect**.

    ![De koppeling Templafy OpenID Connect Connect in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://scim.templafy.com/scim` in bij **Tenant-URL**. Voer de **scim API-sleutel** waarde in die eerder is opgehaald in het **geheime token**. Klik in Azure Portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Templafy. Als de verbinding mislukt, moet u controleren of uw Templafy-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Templafy OpenID connect verbinding maken**.

    ![Templafy OpenID Connect Connect-gebruikers toewijzingen](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Templafy OpenID Connect Connect in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Templafy OpenID Connect Connect for update-bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Templafy OpenID Connect Connect-gebruikers kenmerken](media/templafy-openid-connect-provisioning-tutorial/user-attribute.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Templafy**.

    ![Templafy OpenID connect verbinding maken met groeps toewijzingen](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Templafy OpenID Connect Connect in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de groepen in Templafy OpenID Connect Connect for update-bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van Templafy OpenID Connect Connect-groep](media/templafy-openid-connect-provisioning-tutorial/group-attribute.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor Templafy OpenID Connect **Connect wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Templafy OpenID Connect Connect door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. Hiermee worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Templafy OpenID Connect Connect.

## <a name="step-5-monitor-your-deployment"></a>Stap 5. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

* Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
* Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
