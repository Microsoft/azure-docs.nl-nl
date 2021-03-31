---
title: 'Zelf studie: Netskope-gebruikers authenticatie configureren voor automatische gebruikers inrichting met Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts voor Netskope-gebruikers authenticatie.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 46766a7439185714648572f3f1b9d51ef96abba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357467"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>Zelf studie: Netskope-gebruikers authenticatie configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Netskope-gebruikers authenticatie en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen voor Netskope-gebruikers authenticatie.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Netskope-Tenant voor gebruikers authenticatie](https://www.netskope.com/)
* Een gebruikers account in Netskope-gebruikers verificatie met beheerders machtigingen.

## <a name="assigning-users-to-netskope-user-authentication"></a>Gebruikers toewijzen aan Netskope-gebruikers authenticatie

Azure Active Directory gebruikt een concept dat *toewijzingen* wordt genoemd om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Netskope-gebruikers authenticatie. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan Netskope-gebruikers verificatie door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>Belang rijke tips voor het toewijzen van gebruikers aan Netskope-gebruikers authenticatie

* Het is raadzaam dat er één Azure AD-gebruiker wordt toegewezen aan Netskope-gebruikers authenticatie om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

* Wanneer u een gebruiker toewijst aan Netskope-gebruikers authenticatie, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>Netskope instellen voor het inrichten van gebruikers verificatie

1. Meld u aan bij uw [Netskope-beheer console voor gebruikers verificatie](https://netskope.goskope.com/). Navigeer naar **instellingen voor Home->**.

    ![Beheer console voor Netskope-gebruikers authenticatie](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigeer naar **Hulpprogram ma's**. Ga in het menu **extra** naar **Directory-HULPPROGRAM ma's > scim-integratie**.

    ![Hulpprogram ma's voor Netskope-gebruikers authenticatie](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope-gebruikers verificatie SCIM toevoegen](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Schuif omlaag en klik op de knop **token toevoegen** . Geef in het dialoog venster **OAuth-client naam toevoegen** een **client naam** op en klik op de knop **Opslaan** .

    ![Token voor het toevoegen van Netskope-gebruikers authenticatie](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![CLient naam voor Netskope-gebruikers verificatie](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Kopieer de **URL** van de scim-server en het **token**. Deze waarden worden respectievelijk in de velden Tenant-URL en geheim vermeld op het tabblad inrichten van uw Netskope-gebruikers verificatie toepassing in de Azure Portal.

    ![Token voor het maken van Netskope-gebruikers authenticatie](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Netskope-gebruikers authenticatie toevoegen vanuit de galerie

Voordat u Netskope-gebruikers authenticatie configureert voor automatische gebruikers inrichting met Azure AD, moet u Netskope-gebruikers authenticatie vanuit de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Netskope-gebruikers verificatie toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Ga naar de **[Azure-portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak Netskope- **gebruikers verificatie** in, selecteer **Netskope gebruikers verificatie** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Gebruikers authenticatie Netskope in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Automatische gebruikers inrichting configureren voor Netskope-gebruikers authenticatie 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Netskope-gebruikers authenticatie te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor Netskope-gebruikers authenticatie door de instructies in de [zelf studie voor Netskope-gebruikers verificatie met eenmalige aanmelding](./netskope-cloud-security-tutorial.md)te volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, hoewel deze twee functies een aanvulling op elkaar vormen.

> [!NOTE]
> Raadpleeg voor meer informatie [over het scim](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)-eind punt van Netskope-gebruikers authenticatie.

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Netskope-gebruikers verificatie in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Netskope-gebruikersverificatie** in de lijst met toepassingen.

    ![De koppeling voor Netskope-gebruikers authenticatie in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de waarde voor de **scim-server-URL** die eerder is opgehaald in de Tenant- **URL**. Voer de **token** waarde in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Netskope-gebruikers authenticatie. Als de verbinding mislukt, zorgt u ervoor dat uw Netskope-gebruikers verificatie account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **synchronisatie Azure Active Directory gebruikers om gebruikers verificatie te Netskope**.

    ![Gebruikers toewijzingen voor Netskope-gebruikers authenticatie](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Netskope-gebruikers verificatie in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Netskope-gebruikers authenticatie voor update bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikers kenmerken van Netskope-gebruikers authenticatie](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren om gebruikers verificatie te Netskope**.

    ![Groeps toewijzingen voor Netskope-gebruikers authenticatie](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Netskope-gebruikers verificatie in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de groepen in Netskope-gebruikers authenticatie voor update bewerkingen te vergelijken. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van Netskope-gebruikers authenticatie groep](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor Netskope-gebruikers authenticatie wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Netskope gebruikers verificatie door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Netskope-gebruikers authenticatie.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)