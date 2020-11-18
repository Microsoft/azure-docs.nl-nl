---
title: 'Zelfstudie: Gebruikers inrichten voor Slack - Azure AD'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Slack.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 58fe99164c390d8a9435e5aa65e55fe7fca5d6db
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359473"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Zelfstudie: Slack configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in Slack en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in Slack, of de inrichting van gebruikersaccounts ongedaan te maken. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Slack
> * Gebruikers uit Slack verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Slack
> * Groepen en groepslidmaatschappen inrichten in Slack
> * [Eenmalige aanmelding](./slack-tutorial.md) bij Slack (aanbevolen)


## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtigingen](../users-groups-roles/directory-assign-admin-roles.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder).
* Een Slack-tenant waarvoor het [Plus-plan](https://aadsyncfabric.slack.com/pricing) of hoger is ingeschakeld.
* Een gebruikersaccount in Slack met teambeheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Slack](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Stap 2. Slack toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg Slack toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor Slack. Als u Slack eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Stap 3. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Slack, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven.

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Stap 4. Automatische gebruikersinrichting configureren voor Slack 

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor Slack en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in Slack te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Automatische gebruikersaccountinrichting configureren voor Slack in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Slack**.

    ![De Slack-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Klik onder de sectie **Referenties voor beheerder** op **Autoriseren**. Hiermee opent u een Slack-autorisatievenster in een nieuw browservenster.

    ![Schermopname van de knop Autoriseren in Referenties voor beheerders.](media/slack-provisioning-tutorial/authorization.png)


6. Meld u in het nieuwe venster aan bij Slack met het beheerdersaccount van uw team. Selecteer in het resulterende autorisatievenster het Slack-team waarvoor u het inrichten wilt inschakelen en selecteer vervolgens **Autoriseren**. Ga daarna terug de Azure-portal om de configuratie van de inrichting te voltooien.

    ![Autorisatievenster](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Klik in de Azure-portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met uw Slack-app. Als de verbinding mislukt, moet u controleren of uw Slack-account teambeheerdersmachtigingen heeft en probeer de stap Autoriseren nogmaals.

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met Slack**.

11. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Slack worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Slack te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |actief|Booleaans|
   |externalId|Tekenreeks|
   |displayName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |title|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |userName|Tekenreeks|
   |nickName|Tekenreeks|
   |addresses[type eq "untyped"].streetAddress|Tekenreeks|
   |addresses[type eq "untyped"].locality|Tekenreeks|
   |addresses[type eq "untyped"].region|Tekenreeks|
   |addresses[type eq "untyped"].postalCode|Tekenreeks|
   |addresses[type eq "untyped"].country|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |roles[primary eq "True"].value|Tekenreeks|
   |landinstelling|Tekenreeks|
   |name.honorificPrefix|Tekenreeks|
   |photos[type eq "photo"].value|Tekenreeks|
   |profileUrl|Tekenreeks|
   |timezone|Tekenreeks|
   |userType|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.department|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Naslaginformatie|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.division|Tekenreeks|

12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Slack**.

13. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Slack worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Slack te vinden voor updatebewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |leden|Naslaginformatie|

14. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Slack

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die u aan Slack wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-5-monitor-your-deployment"></a>Stap 5. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

* Houd rekening met het volgende gedrag bij het configureren van het kenmerk **displayName** van Slack:

  * Waarden zijn niet helemaal uniek (zo kunnen 2 gebruikers dezelfde weergavenaam hebben)

  * Ondersteunt niet-Engelse tekens, spaties en hoofdlettergebruik. 
  
  * Toegestane interpunctie omvat punten, onderstrepingstekens, afbreekstreepjes, apostrofs, haakjes (bijvoorbeeld **( [ { } ] )** ) en scheidingstekens (bijvoorbeeld **, / ;** ).
  
  * De eigenschap displayName kan geen @-teken bevatten. Als de weergavenaam een @ bevat, vindt u een gebeurtenis in de inrichtingslogboeken dat deze is overgeslagen, met de beschrijving "AttributeValidationFailed."

  * Wordt alleen bijgewerkt als deze twee instellingen zijn geconfigureerd in de werkruimte/organisatie van Slack: **Profile syncing is enabled** (Profielsynchronisatie is ingeschakeld) en **Users cannot change their display name** (Gebruikers hun weergavenaam niet kunnen wijzigen).

* Het kenmerk **userName** van Slack moet korter zijn dan 21 tekens en de waarde ervan moet uniek zijn.

* In Slack is alleen matching met de kenmerken **userName** en **email** toegestaan.  
  
* Veelvoorkomende foutcodes worden beschreven in de officiële documentatie over Slack: https://api.slack.com/scim#errors

## <a name="change-log"></a>Wijzigingenlogboek

* 06/16/2020: het kenmerk displayName kan alleen worden gewijzigd tijdens het maken van een nieuwe gebruiker.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)