---
title: 'Zelf studie: inhoud configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts vanuit Azure AD.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: Zhchia
ms.openlocfilehash: a473ecc21a5f8a0eb427f01ffba785732f0151e9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181045"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Zelf studie: inhoud configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel de inhoud als de Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer deze is geconfigureerd, worden gebruikers en groepen [automatisch door Azure](https://www.contentful.com/) AD ingericht en ongedaan gemaakt met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in een inhouds opmaakt
> * Gebruikers in een inhouds opheffen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en inhoud
> * Groepen en groepslid maatschappen in een inhouds oprichtende inrichten
> * [Eenmalige aanmelding](./contentful-tutorial.md) voor inhoud (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een inhouds eigen organisatie account met een abonnement dat ondersteuning biedt voor SCIM-inrichting. Neem contact met u op [support@contentful.com](mailto:support@contentful.com) Als u vragen hebt over het abonnement van uw organisatie.
 
## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en de inhoud](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-contentful-to-support-provisioning-with-azure-ad"></a>Stap 2. Inhoud configureren ter ondersteuning van inrichting met Azure AD

1. Een **Service gebruikers** account maken in een inhouds opgemaakte. Alle inrichtings machtigingen voor Azure worden via dit account geleverd. U kunt het beste de **eigenaar** kiezen als de rol van organisatie voor dit account.

2. Meld u aan bij de **service gebruiker** die u in de vorige stap hebt gemaakt.

3. Navigeer naar **linker schuif regelaar**  ->  **organisatie-instellingen**  ->  **toegangs Programma's**  ->  **Gebruikers inrichten**.

    ![Menu](media/contentful-provisioning-tutorial/access.png)

4. Kopieer de scim- **URL** en sla deze op. Deze waarde wordt ingevoerd op het tabblad inrichten van uw inhouds toepassing in de Azure Portal.

5. Klik op **persoonlijk toegangs token genereren**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. Geef in het modale venster uw persoonlijke toegangs token een zinvolle naam en klik op genereren.
    
7. De **scim-URL** en het **geheime token** worden gegenereerd. Kopieer deze waarden en sla deze op. Deze waarden worden ingevoerd op het tabblad inrichten van uw inhouds toepassing in de Azure Portal.

    ![toegang](media/contentful-provisioning-tutorial/token.png)


Neem contact op met [support@contentful.com](mailto:support@contentful.com) Als u vragen hebt over het configureren van inrichting op de content-out-beheer console.


## <a name="step-3-add-contentful-from-the-azure-ad-application-gallery"></a>Stap 3. Inhoud toevoegen vanuit de Azure AD-toepassings galerie

Voeg inhoud toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten. Als u eerder inhoud voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan inhoud, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-contentful"></a>Stap 5. Automatische gebruikers inrichting op inhoud configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlogIn te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Automatische gebruikers inrichting voor inhoud in azure AD configureren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Contentful** in de lijst met toepassingen.

    ![De koppeling naar de inhoud in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. Geef in het gedeelte **beheerders referenties** de URL van uw inhouds eigen Tenant en het geheime token op. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met inhoud. Als de verbinding mislukt, zorg er dan voor dat uw inhoudt account beheerders machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met inhoud**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar inhoud in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikers accounts in contental te vergelijken voor update bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de API voor inhoud filtering voor het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen op inhoud synchroniseren**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar inhoud in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in de richting van de update bewerkingen te vergelijken. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|Ondersteund voor filteren|
      |---|---|---|
      |displayName|Tekenreeks|&check;|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor **inhoud wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor inhoud door de gewenste waarden in het **bereik** in de sectie **instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)