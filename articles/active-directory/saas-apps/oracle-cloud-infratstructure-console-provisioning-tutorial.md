---
title: 'Zelfstudie: Oracle Cloud Infrastructure-console configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD naar de Oracle Cloud Infrastructure-console.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 94de0ca0a5393c891e567e558cbbadd0ca1f453b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607997"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Zelfstudie: Oracle Cloud Infrastructure-console configureren voor automatische inrichting van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel de Oracle Cloud Infrastructure-console als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op de [Oracle Cloud Infrastructure-console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken voor de Oracle Cloud Infrastructure-console
> * Gebruikers verwijderen in de Oracle Cloud Infrastructure-console wanneer deze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Oracle Cloud Infrastructure-console
> * Groepen en groepslidmaatschappen inrichten in de Oracle Cloud Infrastructure-console
> * [Eenmalige aanmelding](./oracle-cloud-tutorial.md) bij de Oracle Cloud Infrastructure-console (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een [tenant](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton) voor de Oracle Cloud Infrastructure-console.
* Een gebruikersaccount in de Oracle Cloud Infrastructure-console met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Oracle Cloud Infrastructure-console](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Stap 2. De Oracle Cloud Infrastructure-console configureren ter ondersteuning van het inrichten met Azure AD

1. Meld u aan bij de beheerportal van de Oracle Cloud Infrastructure-console. Navigeer in de linkerbovenhoek van het scherm naar **Identiteit > Federatie**.

    ![Oracle-beheerder](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Klik op de URL die wordt weergegeven op de pagina naast Oracle Identity Cloud Service-console.

    ![Oracle-URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Klik op **Id-provider toevoegen** om een nieuwe id-provider te maken. Sla de IdP-id op die moet worden gebruikt als onderdeel van de tenant-URL. Klik op het pluspictogram naast het tabblad **Toepassingen** om een OAuth-client te maken en de AppRole domeinbeheerder IDCS-identiteit te verlenen.

    ![Pictogram van Oracle Cloud](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Volg de onderstaande schermopnames om uw toepassing te configureren. Zodra de configuratie is voltooid, klikt u op **Opslaan**.

    ![Oracle-configuratie](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Uitgiftebeleid voor Oracle-tokens](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Vouw op het tabblad configuraties van uw toepassing de optie **Algemene informatie** uit om de client-id en het clientgeheim op te halen.

    ![Oracle-token genereren](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Als u een geheim token Base64 wilt genereren, moet u de client-id en het clientgeheim coderen in de indeling **client-id:clientgeheim**. Sla het geheime token op. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad Inrichten van uw Oracle Cloud Infrastructure-console-toepassing in de Azure Portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Stap 3. Een Oracle Cloud Infrastructure-console toevoegen vanuit de toepassingsgalerie van Azure AD

De Oracle Cloud Infrastructure-console toevoegen vanuit de toepassingsgalerie van Azure AD om het beheer van de inrichting van de Oracle Cloud Infrastructure-console te starten. Als u de Oracle Cloud Infrastructure-console eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Oracle Cloud Infrastructure-console, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Stap 5. Automatische gebruikersinrichting configureren voor de Oracle Cloud Infrastructure-console 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor de Oracle Cloud Infrastructure-console in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Oracle Cloud Infrastructure-console**.

    ![De link voor de Oracle Cloud Infrastructure-console in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie Inrichting gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **Beheerdersreferenties** de **Tenant-URL** in de indeling `https://<IdP ID>.identity.oraclecloud.com/admin/v1` in. Bijvoorbeeld `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Voer de waarde van de token voor het geheim in die eerder in **Token voor geheim** is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met de Oracle Cloud Infrastructure-console. Als de verbinding mislukt, moet u controleren of uw Oracle Cloud Infrastructure-console-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname met het dialoogvenster Beheerdersreferenties, waarin u uw Tenant U R L en Token voor geheim kunt invoeren.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Oracle Cloud Infrastructure-console**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met de Oracle Cloud Infrastructure-console worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in de Oracle Cloud Infrastructure-console te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van de Oracle Cloud Infrastructure-console het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    |Kenmerk|Type|
    |---|---|
    |displayName|Tekenreeks|
    |userName|Tekenreeks|
    |actief|Booleaans|
    |title|Tekenreeks|
    |emails[type eq "work"].value|Tekenreeks|
    |preferredLanguage|Tekenreeks|
    |name.givenName|Tekenreeks|
    |name.familyName|Tekenreeks|
    |addresses[type eq "work"].formatted|Tekenreeks|
    |addresses[type eq "work"].locality|Tekenreeks|
    |addresses[type eq "work"].region|Tekenreeks|
    |addresses[type eq "work"].postalCode|Tekenreeks|
    |addresses[type eq "work"].country|Tekenreeks|
    |addresses[type eq "work"].streetAddress|Tekenreeks|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Tekenreeks|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Tekenreeks|
    |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:bypassNotification|Booleaans|
    |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Booleaans|

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met de Oracle Cloud Infrastructure-console**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met de Oracle Cloud Infrastructure-console worden gesynchroniseerd. De kenmerken die als **Overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in de Oracle Cloud Infrastructure-console te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    | Kenmerk | Type |
    |--|--|
    | displayName | Tekenreeks |
    | externalId | Tekenreeks |
    | leden | Naslaginformatie |

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig de **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor de Oracle Cloud Infrastructure-console.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan de Oracle Cloud Infrastructure-console wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

* Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
* Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
