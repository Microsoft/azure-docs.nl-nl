---
title: 'Zelfstudie: RingCentral configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD voor RingCentral.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: f57114fc4cb76c500cc422966635273c3a923046
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181614"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Zelfstudie: RingCentral configureren voor automatische inrichting van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel RingCentral als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [RingCentral](https://www.ringcentral.com/office/plansandpricing.html) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in RingCentral
> * Gebruikers verwijderen uit RingCentral wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en RingCentral
> * [Eenmalige aanmelding](./ringcentral-tutorial.md) voor RingCentral (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* [Een RingCentral-tenant](https://www.ringcentral.com/office/plansandpricing.html)
* Een gebruikersaccount in RingCentral met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en RingCentral](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-ringcentral-to-support-provisioning-with-azure-ad"></a>Stap 2. RingCentral configureren om ondersteuning te bieden voor inrichting met Azure AD

1. Meld u aan bij uw [RingCentral-beheerconsole](https://login.ringcentral.com/sw.html). Navigeer naar **Hulpprogramma's > Directory-integratie**.

    ![RingCentral-beheerconsole](media/ringcentral-provisioning-tutorial/admin.png)

2.  Kies **SCIM** onder **Directory-provider selecteren**. (In de toekomst is er een optie met de naam Azure Active Directory). Klik op **SCIM-service inschakelen**.

    ![RingCentral SCIM toevoegen](media/ringcentral-provisioning-tutorial/scim.png)

3.  Neem contact op met het ondersteuningsteam van RingCentral via matthew.hunt@ringcentral.com voor een **SCIM-verificatietoken**. Deze waarde wordt ingevoerd in het veld Token voor geheim op het tabblad Inrichten van de RingCentral-toepassing in Azure Portal.

> [!NOTE]
> Als u licenties aan gebruikers wilt toewijzen, raadpleegt u [hier](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language) de videolink.

## <a name="step-3-add-ringcentral-from-the-azure-ad-application-gallery"></a>Stap 3. RingCentral toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg RingCentral toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor RingCentral. Als u RingCentral eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan RingCentral, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-ringcentral"></a>Stap 5. Automatische gebruikersinrichting configureren voor RingCentral 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlogIn te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor RingCentral in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **RingCentral** in de lijst met toepassingen.

    ![De RingCentral-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://platform.ringcentral.com/scim/v2` in bij **Tenant-URL**. Voer de waarde van het **SCIM-verificatietoken** in die eerder in **Token voor geheim** is opgehaald. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met RingCentral. Als de verbinding mislukt, moet u controleren of uw RingCentral-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname van de tekstvelden Tenant-URL en Token voor geheim met de optie Verbinding testen gemarkeerd.](./media/ringcentral-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met RingCentral**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met RingCentral worden gesynchroniseerd. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in RingCentral te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van RingCentral het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |externalId|Tekenreeks|
   |actief|Booleaans|
   |title|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |addresses[type eq "work"].country|Tekenreeks|
   |addresses[type eq "work"].region|Tekenreeks|
   |addresses[type eq "work"].locality|Tekenreeks|
   |addresses[type eq "work"].postalCode|Tekenreeks|
   |addresses[type eq "work"].streetAddress|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig in de sectie **Instellingen** **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor RingCentral.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u in RingCentral wilt inrichten door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="change-log"></a>Wijzigingenlogboek

* 10-09-2020 - ondersteuning voor kenmerken displayName en manager verwijderd.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)