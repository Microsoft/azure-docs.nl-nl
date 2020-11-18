---
title: 'Zelfstudie: Cisco ServiceNow configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD naar ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 1d6213d49c98f5e09f22e7310183315800d0c6f6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359779"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Zelfstudie: ServiceNow configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel ServiceNow als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [ServiceNow](https://www.servicenow.com/) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in ServiceNow
> * Gebruikers uit ServiceNow verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en ServiceNow
> * Groepen en groepslidmaatschappen inrichten in ServiceNow
> * [Eenmalige aanmelding](servicenow-tutorial.md) bij ServiceNow (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../users-groups-roles/directory-assign-admin-roles.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een [ServiceNow-exemplaar](https://www.servicenow.com/) van Calgary of hoger
* Een [ServiceNow Express-exemplaar](https://www.servicenow.com/) van Helsinki of hoger
* Een gebruikersaccount in ServiceNow met de beheerdersrol

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Stap 2. ServiceNow configureren ter ondersteuning van het inrichten met Azure AD

1. Identificeer de naam van uw ServiceNow-exemplaar. U kunt de naam van het exemplaar vinden in de URL die u gebruikt voor toegang tot ServiceNow. In het onderstaande voorbeeld is de naam van het exemplaar dev35214.

   ![ServiceNow-exemplaar](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Referenties voor een beheerder in ServiceNow verkrijgen. Navigeer naar het gebruikersprofiel in ServiceNow en controleer of de gebruiker de beheerdersrol heeft. 

   ![Beheerdersrol van ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Controleer of de volgende instellingen zijn **uitgeschakeld** in ServiceNow:

   1. Selecteer **Systeembeveiliging** > **Instellingen voor hoge beveiliging** > **Basisverificatie vereisen voor inkomende SCHEMA-aanvragen**.
   2. Selecteer **Systeemeigenschappen** > **Webservices** > **Standaard autorisatie vereisen voor binnenkomende SOAP-aanvragen**.
     
   > [!IMPORTANT]
   > Als deze instellingen zijn *ingeschakeld*, kan de inrichtings-engine niet communiceren met ServiceNow.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Stap 3. ServiceNow toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg ServiceNow toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor ServiceNow. Als u ServiceNow eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan ServiceNow, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Stap 5. Automatische gebruikersinrichting configureren voor ServiceNow 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor ServiceNow in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ServiceNow** in de lijst met toepassingen.

    ![De koppeling ServiceNow in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de beheeropties met de optie Inrichting gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer uw beheerdersreferenties en gebruikersnaam voor ServiceNow in de sectie **Beheerdersreferenties** in. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met ServiceNow. Als de verbinding mislukt, moet u controleren of uw ServiceNow-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname toont de Inrichtingspagina van de service, waar u Beheerdersreferenties kunt invoeren.](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met ServiceNow**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met ServiceNow worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in ServiceNow te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van ServiceNow het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met ServiceNow**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met ServiceNow worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in ServiceNow te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor ServiceNow.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan ServiceNow wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
* **InvalidLookupReference:** Bij het inrichten van bepaalde kenmerken, zoals Afdeling en Locatie in ServiceNow, moeten de waarden al bestaan in een referentietabel in ServiceNow. U kunt bijvoorbeeld twee locaties (Seattle, Los Angeles) en drie afdelingen (Sales, Finance, Marketing) hebben in de tabel **Tabelnaam invoegen** in ServiceNow. Als u een gebruiker wilt inrichten waarbij zijn afdeling ‘Sales’ is en de locatie ‘Seattle’ is, wordt hij met succes ingericht. Als u probeert een gebruiker in te richten met afdeling ‘Sales’ en locatie ‘LA’, wordt de gebruiker niet ingericht. De locatie LA moet worden toegevoegd aan de referentietabel in ServiceNow of het gebruikerskenmerk in Azure AD moet worden bijgewerkt zodat dit overeenkomt met de indeling in ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Controleer uw [Kenmerktoewijzingen](../app-provisioning/customize-application-attributes.md) om het overeenkomende kenmerk te identificeren. Deze waarde moet aanwezig zijn op de gebruiker of groep die u wilt inrichten. 
* Bekijk de [ServiceNow SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) om de vereisten of beperkingen te begrijpen (bijvoorbeeld de indeling om een landnummer op te geven voor een gebruiker)
* Inrichtingsaanvragen worden standaard verzonden naar https://{your-instance-name}.service-now.com/{table-name}. Als u een aangepaste tenant-URL nodig hebt, kunt u de volledige URL opgeven in het veld exemplaarnaam.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Deze fout wijst op een probleem in de communicatie met het ServiceNow-exemplaar. Controleer nogmaals of de volgende instellingen zijn *uitgeschakeld* in ServiceNow:
   
   1. Selecteer **Systeembeveiliging** > **Instellingen voor hoge beveiliging** > **Basisverificatie vereisen voor inkomende SCHEMA-aanvragen**.
   2. Selecteer **Systeemeigenschappen** > **Webservices** > **Standaard autorisatie vereisen voor binnenkomende SOAP-aanvragen**.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)