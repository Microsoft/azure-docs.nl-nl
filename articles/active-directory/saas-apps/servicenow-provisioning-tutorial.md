---
title: 'Zelfstudie: Cisco ServiceNow configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar ServiceNow.
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
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539034"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Zelfstudie: ServiceNow configureren voor automatische gebruikersinrichting

In deze zelf studie worden de stappen beschreven die u kunt uitvoeren in zowel ServiceNow als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer Azure AD is geconfigureerd, worden gebruikers en groepen automatisch door de Azure AD-inrichtings service [ServiceNow](https://www.servicenow.com/) . 

Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in ServiceNow
> * Gebruikers in ServiceNow verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en ServiceNow
> * Groepen en groepslidmaatschappen inrichten in ServiceNow
> * [Eenmalige aanmelding](servicenow-tutorial.md) toestaan voor ServiceNow (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikers account in azure AD met [toestemming](../roles/permissions-reference.md) voor het configureren van inrichting (toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder)
* Een [ServiceNow-exemplaar](https://www.servicenow.com/) van Calgary of hoger
* Een [ServiceNow Express-exemplaar](https://www.servicenow.com/) van Helsinki of hoger
* Een gebruikersaccount in ServiceNow met de beheerdersrol

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1: plan uw inrichtings implementatie
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Stap 2: ServiceNow configureren voor ondersteuning bij het inrichten met Azure AD

1. Identificeer de naam van uw ServiceNow-exemplaar. U kunt de naam van het exemplaar vinden in de URL die u gebruikt voor toegang tot ServiceNow. In het volgende voor beeld is de naam van het exemplaar **dev35214**.

   ![Scherm opname van een ServiceNow-exemplaar.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Referenties voor een beheerder in ServiceNow verkrijgen. Ga naar het gebruikers profiel in ServiceNow en controleer of de gebruiker de rol Admin heeft. 

   ![Scherm afbeelding met een ServiceNow-beheerdersrol.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Stap 3: ServiceNow toevoegen vanuit de Azure AD-toepassings galerie

Voeg ServiceNow toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor ServiceNow. Als u eerder ServiceNow voor eenmalige aanmelding (SSO) hebt ingesteld, kunt u dezelfde toepassing gebruiken. We raden u echter aan om een afzonderlijke app te maken wanneer u de integratie test. [Meer informatie over het toevoegen van een toepassing uit de galerie](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4: definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing, of op basis van kenmerken van de gebruiker of groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de [stappen gebruiken om gebruikers en groepen toe te wijzen aan de toepassing](../manage-apps/assign-user-or-group-access-portal.md). Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u [een bereik filter gebruiken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Houd de volgende tips in acht:

* Wanneer u gebruikers en groepen toewijst aan ServiceNow, moet u een andere rol dan de standaard toegang selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [op kenmerken gebaseerd bereik filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Stap 5: automatische gebruikers inrichting configureren voor ServiceNow 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en groepen in TestApp te maken, bij te werken en uit te scha kelen. U kunt de configuratie baseren op toewijzingen van gebruikers en groepen in azure AD.

Automatische gebruikersinrichting configureren voor ServiceNow in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Schermopname van het deelvenster Ondernemingstoepassing.](common/enterprise-applications.png)

2. Selecteer **ServiceNow** in de lijst met toepassingen.

    ![Scherm opname van een lijst met toepassingen.](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Voer in de sectie **beheerders referenties** uw ServiceNow-beheerders referenties en de gebruikers naam in. Selecteer **verbinding testen** om te controleren of Azure AD verbinding kan maken met ServiceNow. Als de verbinding mislukt, zorg er dan voor dat uw ServiceNow-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm opname van de pagina voor het inrichten van de service, waar u beheerders referenties kunt invoeren.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Schakel vervolgens het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![Scherm afbeelding van de vakken voor de meldings-e-mail.](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met ServiceNow**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met ServiceNow worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in ServiceNow te vinden voor updatebewerkingen. 

   Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de SERVICENOW-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. 
   
   Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met ServiceNow**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met ServiceNow worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in ServiceNow te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

12. Raadpleeg de instructies in de [Zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

13. Als u de Azure AD-inrichtings service voor **ServiceNow wilt inschakelen, moet u de** **inrichtings status** wijzigen in in het gedeelte **instellingen** .

    ![Scherm opname waarin de inrichtings status wordt weer gegeven.](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en groepen die u wilt inrichten voor ServiceNow door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Scherm opname van de opties voor het inrichtings bereik.](common/provisioning-scope.png)

15. Selecteer **Opslaan** als u klaar bent om in te richten.

    ![Scherm afbeelding van de knop voor het opslaan van een inrichtings configuratie.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het duurt langer voordat de eerste cyclus is uitgevoerd. Volgende cycli komen ongeveer elke 40 minuten voor, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6: uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

- Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
- Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
- Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Meer [informatie over quarantaine statussen](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
* Wanneer u bepaalde kenmerken (zoals **afdeling** en **locatie**) in ServiceNow wilt inrichten, moeten de waarden al bestaan in een verwijzings tabel in ServiceNow. Als dat niet het geval is, krijgt u een **InvalidLookupReference** -fout. 

   U kunt bijvoorbeeld twee locaties hebben (Seattle, Los Angeles) en drie afdelingen (verkoop, financiën, marketing) in een bepaalde tabel in ServiceNow. Als u een gebruiker wilt inrichten waarvan de afdeling ' verkoop ' is en de locatie ' Seattle ' is, wordt die gebruiker met succes ingericht. Als u een gebruiker wilt inrichten waarvan de afdeling ' verkoop ' is en waarvan de locatie ' LA ' is, wordt de gebruiker niet ingericht. De locatie ' LA ' moet worden toegevoegd aan de verwijzings tabel in ServiceNow, of het gebruikers kenmerk in azure AD moet worden bijgewerkt zodat dit overeenkomt met de indeling in ServiceNow. 
* Als u een **EntryJoiningPropertyValueIsMissing** -fout krijgt, controleert u de [kenmerk toewijzingen](../app-provisioning/customize-application-attributes.md) om het overeenkomende kenmerk te identificeren. Deze waarde moet aanwezig zijn op de gebruiker of groep die u wilt inrichten. 
* Raadpleeg de [SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html)voor meer informatie over de vereisten of beperkingen (bijvoorbeeld de notatie voor het opgeven van een land code voor een gebruiker).
* Inrichtingsaanvragen worden standaard verzonden naar https://{your-instance-name}.service-now.com/{table-name}. Als u een aangepaste Tenant-URL nodig hebt, kunt u de volledige URL opgeven als de naam van het exemplaar.
* De **ServiceNowInstanceInvalid** -fout geeft aan dat er een probleem is met de communicatie met het ServiceNow-exemplaar. Dit is de tekst van de fout:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Als u verbindings problemen hebt, kiest u **Nee** voor de volgende instellingen in ServiceNow:
   
  - **Systeem beveiliging**  >  Instellingen voor hoge **beveiliging**  >  **Basis verificatie vereisen voor inkomende schema aanvragen**
  - **Systeem eigenschappen**  >  **Webservices**  >  **Basis autorisatie vereisen voor inkomende SOAP-aanvragen**

     ![Scherm afbeelding met de optie voor het machtigen van SOAP-aanvragen.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Als u het probleem nog steeds niet kunt oplossen, neemt u contact op met de ServiceNow-ondersteuning en vraagt u de SOAP-fout opsporing in te scha kelen voor probleem oplossing. 

* De Azure AD-inrichtings service werkt momenteel met bepaalde [IP-bereiken](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). Indien nodig kunt u andere IP-adresbereiken beperken en deze specifieke IP-bereiken toevoegen aan de acceptatie lijst van uw toepassing. Met deze techniek is verkeer stroom van de Azure AD-inrichtings service naar uw toepassing mogelijk.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Single sign-on to applications in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) (Eenmalige aanmelding bij toepassingen met Azure Active Directory)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
