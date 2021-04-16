---
title: 'Zelfstudie: G Suite configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD naar G Suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: Zhchia
ms.openlocfilehash: b8513f62b6f181a1490d136062c5de81db847ba7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533393"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Zelfstudie: G Suite configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel G Suite als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [G Suite](https://gsuite.google.com/) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in G Suite
> * Gebruikers uit G Suite verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en G Suite
> * Groepen en groepslidmaatschappen inrichten in G Suite
> * [Eenmalige aanmelding](./google-apps-tutorial.md) bij G Suite (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* [Een G Suite-tenant](https://gsuite.google.com/pricing.html)
* Een gebruikersaccount op een G Suite met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en G Suite](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Stap 2. G Suite configureren ter ondersteuning van het inrichten met Azure AD

Voordat u G Suite configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in G Suite.

1. Meld u aan bij de [beheerconsole van G Suite](https://admin.google.com/) met uw beheerdersaccount en selecteer vervolgens **Beveiliging**. Als u de koppeling niet ziet, is deze mogelijk verborgen in het menu **Meer besturingselementen** onder aan het scherm.

    ![G Suite-beveiliging](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. Selecteer op de pagina **Beveiliging** de optie **API-verwijzing**.

    ![G Suite-API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Selecteer **API-toegang inschakelen**.

    ![G Suite-API ingeschakeld](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Voor elke gebruiker die u wilt inrichten voor G Suite, **moet** de gebruikersnaam in Azure AD zijn gekoppeld aan een aangepast domein. Bijvoorbeeld: gebruikersnamen die eruitzien als bob@contoso.onmicrosoft.com worden niet geaccepteerd door G Suite. bob@contoso.com wordt daarentegen geaccepteerd. U kunt een bestaand gebruikersdomein wijzigen door de instructies [hier](../fundamentals/add-custom-domain.md) te volgen.

4. Nadat u de gewenste aangepaste domeinen hebt toegevoegd en geverifieerd met Azure AD, moet u ze opnieuw verifiëren met G Suite. Raadpleeg de volgende stappen om domeinen in G Suite te verifiëren:

    a. Selecteer in de [G Suite-beheerconsole](https://admin.google.com/) **Domeinen**.

    ![G Suite-domeinen](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Selecteer **Een domein of een domeinalias toevoegen**.

    ![G Suite domein toevoegen](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Selecteer **Nog een domein toevoegen** en typ de naam van het domein dat u wilt toevoegen.

    ![G Suite Nog een toevoegen](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Selecteer **Doorgaan en domeineigendom verifiëren**. Volg vervolgens de stappen om te controleren of u eigenaar bent van de domeinnaam. Zie [Uw site-eigendom verifiëren](https://support.google.com/webmasters/answer/35179) voor uitgebreide instructies voor het verifiëren van uw domein met Google.

    e. Herhaal de voorgaande stappen voor extra domeinen die u wilt toevoegen aan G Suite.

5. Bepaal vervolgens welk beheerdersaccount u wilt gebruiken voor het beheren van gebruikersinrichting in G Suite. Navigeer naar **Beheerdersrollen**.

    ![G Suite-beheer](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. Voor de **Beheerdersrol** van dat account bewerkt u de **Bevoegdheden** voor die rol. Zorg ervoor dat alle **Beheer-API-bevoegdheden** zijn ingeschakeld zodat dit account kan worden gebruikt voor het inrichten.

    ![G Suite-beheerdersbevoegdheden](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Stap 3. G Suite toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg G Suite toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor G Suite. Als u G Suite eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan G Suite, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Stap 5. Automatische gebruikersinrichting configureren voor G Suite 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!NOTE]
> Raadpleeg [Map-API](https://developers.google.com/admin-sdk/directory) voor meer informatie over het Map-API-eindpunt van G Suite.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor G Suite in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**. Gebruikers moeten zich aanmelden bij portal.azure.com en kunnen aad.portal.azure.com niet gebruiken

    ![De blade Bedrijfstoepassingen](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Blade Alle toepassingen](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Selecteer **G Suite** in de lijst met toepassingen.

    ![De koppeling G Suite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**. Klik op **Aan de slag**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

      ![Blade Aan de slag](./media/g-suite-provisioning-tutorial/get-started.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst 'Inrichtingsmodus' met de optie 'Automatisch' gemarkeerd.](common/provisioning-automatic.png)

5. Klik onder de sectie **Referenties voor beheerder** op **Autoriseren**. U wordt doorgestuurd naar een dialoogvenster voor Google-autorisatie in een nieuw browservenster.

      ![G Suite autoriseren](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Bevestig dat u Azure AD machtigingen wilt verlenen om wijzigingen aan te brengen in uw G Suite-tenant. Selecteer **Accepteren**.

     ![G Suite-tenant autoriseren](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. Klik in de Azure-portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met G Suite. Als de verbinding mislukt, moet u controleren of uw G Suite-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw. Probeer daarna opnieuw de stap **Autoriseren**.

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers inrichten**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met G Suite worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in G Suite te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van G Suite het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |primaryEmail|Tekenreeks|
   |relations.[type eq "manager"].value|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |onderbroken|Tekenreeks|
   |externalIds.[type eq "custom"].value|Tekenreeks|
   |externalIds.[type eq "organization"].value|Tekenreeks|
   |addresses.[type eq "work"].country|Tekenreeks|
   |addresses.[type eq "work"].streetAddress|Tekenreeks|
   |addresses.[type eq "work"].region|Tekenreeks|
   |addresses.[type eq "work"].locality|Tekenreeks|
   |addresses.[type eq "work"].postalCode|Tekenreeks|
   |emails.[type eq "work"].address|Tekenreeks|
   |organizations.[type eq "work"].department|Tekenreeks|
   |organizations.[type eq "work"].title|Tekenreeks|
   |phoneNumbers.[type eq "work"].value|Tekenreeks|
   |phoneNumbers.[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers.[type eq "work_fax"].value|Tekenreeks|
   |emails.[type eq "work"].address|Tekenreeks|
   |organizations.[type eq "work"].department|Tekenreeks|
   |organizations.[type eq "work"].title|Tekenreeks|
   |phoneNumbers.[type eq "work"].value|Tekenreeks|
   |phoneNumbers.[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers.[type eq "work_fax"].value|Tekenreeks|
   |addresses.[type eq "home"].country|Tekenreeks|
   |addresses.[type eq "home"].formatted|Tekenreeks|
   |addresses.[type eq "home"].locality|Tekenreeks|
   |addresses.[type eq "home"].postalCode|Tekenreeks|
   |addresses.[type eq "home"].region|Tekenreeks|
   |addresses.[type eq "home"].streetAddress|Tekenreeks|
   |addresses.[type eq "other"].country|Tekenreeks|
   |addresses.[type eq "other"].formatted|Tekenreeks|
   |addresses.[type eq "other"].locality|Tekenreeks|
   |addresses.[type eq "other"].postalCode|Tekenreeks|
   |addresses.[type eq "other"].region|Tekenreeks|
   |addresses.[type eq "other"].streetAddress|Tekenreeks|
   |addresses.[type eq "work"].formatted|Tekenreeks|
   |changePasswordAtNextLogin|Tekenreeks|
   |emails.[type eq "home"].address|Tekenreeks|
   |emails.[type eq "other"].address|Tekenreeks|
   |externalIds.[type eq "account"].value|Tekenreeks|
   |externalIds.[type eq "custom"].customType|Tekenreeks|
   |externalIds.[type eq "customer"].value|Tekenreeks|
   |externalIds.[type eq "login_id"].value|Tekenreeks|
   |externalIds.[type eq "network"].value|Tekenreeks|
   |gender.type|Tekenreeks|
   |GeneratedImmutableId|Tekenreeks|
   |Id|Tekenreeks|
   |ims.[type eq "home"].protocol|Tekenreeks|
   |ims.[type eq "other"].protocol|Tekenreeks|
   |ims.[type eq "work"].protocol|Tekenreeks|
   |includeInGlobalAddressList|Tekenreeks|
   |ipWhitelisted|Tekenreeks|
   |organizations.[type eq "school"].costCenter|Tekenreeks|
   |organizations.[type eq "school"].department|Tekenreeks|
   |organizations.[type eq "school"].domain|Tekenreeks|
   |organizations.[type eq "school"].fullTimeEquivalent|Tekenreeks|
   |organizations.[type eq "school"].location|Tekenreeks|
   |organizations.[type eq "school"].name|Tekenreeks|
   |organizations.[type eq "school"].symbol|Tekenreeks|
   |organizations.[type eq "school"].title|Tekenreeks|
   |organizations.[type eq "work"].costCenter|Tekenreeks|
   |organizations.[type eq "work"].domain|Tekenreeks|
   |organizations.[type eq "work"].fullTimeEquivalent|Tekenreeks|
   |organizations.[type eq "work"].location|Tekenreeks|
   |organizations.[type eq "work"].name|Tekenreeks|
   |organizations.[type eq "work"].symbol|Tekenreeks|
   |OrgUnitPath|Tekenreeks|
   |phoneNumbers.[type eq "home"].value|Tekenreeks|
   |phoneNumbers.[type eq "other"].value|Tekenreeks|
   |websites.[type eq "home"].value|Tekenreeks|
   |websites.[type eq "other"].value|Tekenreeks|
   |websites.[type eq "work"].value|Tekenreeks|
   

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen inrichten**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met G Suite worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in G Suite te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |e-mail|Tekenreeks|
      |Leden|Tekenreeks|
      |naam|Tekenreeks|
      |beschrijving|Tekenreeks|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig de **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor G Suite.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan G Suite wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

> [!NOTE]
> Als de gebruikers al een bestaand persoonlijk account/consumentenaccount hebben voor het e-mailadres van de Azure AD-gebruiker, kan dit problemen veroorzaken die kunnen worden opgelost door de Google Transfer Tool te gebruiken voordat de mapsynchronisatie wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="change-log"></a>Wijzigingenlogboek

* 17-10-2020: ondersteuning toegevoegd voor aanvullende gebruikers -en groepskenmerken van G Suite.
* 17-10-2020: bijgewerkte namen van G Suite-doelkenmerken zodat deze overeenkomen met wat [hier](https://developers.google.com/admin-sdk/directory) is gedefinieerd.
* 17-10-2020: bijgewerkte standaardkenmerktoewijzingen.
* 18-03-2021 - E-mail van manager is nu gesynchroniseerd in plaats van id voor alle nieuwe gebruikers. Voor alle bestaande gebruikers die zijn ingericht met een manager als een id, kunt u een herstart via [Microsoft Graph](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http&preserve-view=true) met bereik 'full' om ervoor te zorgen dat het e-mailbericht is ingericht. Deze wijziging is alleen van invloed op de GSuite-inrichtings job en niet op de oudere inrichtings job die begint met Goov2OutDelta. Opmerking: het e-mailadres van de manager wordt ingericht wanneer de gebruiker voor het eerst wordt gemaakt of wanneer de manager wordt gewijzigd. Het e-mailadres van de manager wordt niet ingericht als de manager het e-mailadres wijzigt. 

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
