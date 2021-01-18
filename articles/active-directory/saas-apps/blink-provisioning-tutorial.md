---
title: 'Zelfstudie: Blink configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Blink.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: d691807f673dcd6c8147c9ff18a95c6ce0c88ae6
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247426"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>Zelfstudie: Blink configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in Blink en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers aan Cisco Blink.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant
* [Een Blink-tenant](https://joinblink.com/pricing)
* Een gebruikersaccount in Blink met beheerdersmachtigingen

## <a name="assigning-users-to-blink"></a>Gebruikers toewijzen aan Blink

Azure Active Directory gebruikt een concept dat *toewijzingen* wordt genoemd om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepsleden gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische inrichting van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Blink. Als u dit eenmaal hebt besloten, kunt u deze gebruikers en/of groepen aan Blink toewijzen door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>Belangrijke tips voor het toewijzen van gebruikers aan Blink

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan Blink om de configuratie van de automatische gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen dan later nog worden toegewezen.

* Als u een gebruiker aan Blink toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-blink-for-provisioning"></a>Blink instellen voor inrichting

1. Dien een [Support Case](https://support.joinblink.com) in of e-mail **support van Blink** op support@joinblink.com om een SCIM-token aan te vragen.

2.  Kopieer het **SCIM Authentication Token**. Deze waarde moet later worden ingevoerd in het veld Token voor geheim op het tabblad Inrichten van de Blink-toepassing in de Azure-portal.

## <a name="add-blink-from-the-gallery"></a>Blink toevoegen vanuit de galerie

Voordat u Blink configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Blink vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Blink toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Ga naar de **[Azure-portal](https://portal.azure.com)** en selecteer **Azure Active Directory** in het navigatievenster aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** bovenin het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Blink** in het zoekvak, selecteer **Blink** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Blink in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>Automatische gebruikersinrichting voor Blink configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en groepen in Blink te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te schakelen voor Blink, waarvoor u de instructies in de [zelfstudie Eenmalige aanmelding voor Blink](./blink-tutorial.md) moet volgen. Eenmalige aanmelding kan onafhankelijk van automatische inrichting van gebruikers worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Blink in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Blink** in de lijst met toepassingen.

    ![De link naar Blink in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://api.joinblink.com/scim` in bij **Tenant-URL**. Voer in het veld **Token voor geheim** de waarde van het **SCIM Authentication Token** in dat eerder is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Blink. Als de verbinding mislukt, moet u controleren of uw Blink-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Blink**.

    ![Toewijzingen van Blink-gebruikers](media/blink-provisioning-tutorial/User_mappings.png)

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Blink worden gesynchroniseerd. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Blink te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |title|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |externalId|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|
   |urn:ietf:params:scim:schemas:extension:blink:2.0:User:company|Tekenreeks|
   urn:ietf:params:scim:schemas:extension:blink:2.0:User:description|Tekenreeks|
   urn:ietf:params:scim:schemas:extension:blink:2.0:User:location|Tekenreeks|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Blink.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan Blink wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers die zijn gedefinieerd in **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **Synchronisatiedetails** gebruiken om de voortgang te bewaken en koppelingen te volgen naar het rapport over de inrichtingsactiviteit, waarin alle acties worden beschreven die met de Azure AD-inrichtingsservice zijn uitgevoerd in Blink.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

* Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
* Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.  


## <a name="change-log"></a>Wijzigingenlogboek

* 01/14/2021 - Aangepaste extensiekenmerken **bedrijf**, **beschrijving** en **locatie** zijn toegevoegd.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
