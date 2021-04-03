---
title: 'Zelf studie: Adobe Identity Management configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar Adobe Identity Management.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6ae05dc7-1265-44b4-a20c-512b5218b9d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: Zhchia
ms.openlocfilehash: 7b4dd01b74f81318338196a40949d339f13d16da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673296"
---
# <a name="tutorial-configure-adobe-identity-management-for-automatic-user-provisioning"></a>Zelf studie: Adobe Identity Management configureren voor het automatisch inrichten van gebruikers

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in Adobe Identity Management en Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt met Adobe Identity Management met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken met Adobe Identity Management
> * Gebruikers in Adobe Identity Management verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd blijven tussen Azure AD en Adobe Identity Management
> * Inrichtings groepen en groepslid maatschappen in Adobe Identity Management
> * Eenmalige aanmelding bij Adobe Identity Management (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van de inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een federatieve Directory in de [Adobe-beheer console](https://adminconsole.adobe.com/) met geverifieerde domeinen.

> [!NOTE]
> Als uw organisatie het hulp programma voor gebruikers synchronisatie of een UMAPI-integratie gebruikt, moet u de integratie eerst onderbreken. Voeg vervolgens automatische inrichting van Azure AD toe om gebruikers beheer te automatiseren vanuit Azure Portal. Als automatische inrichting van Azure AD is geconfigureerd en wordt uitgevoerd, kunt u het hulp programma voor gebruikers synchronisatie of UMAPI-integratie volledig verwijderen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Adobe Identity Management](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-adobe-identity-management-to-support-provisioning-with-azure-ad"></a>Stap 2. Adobe Identity Management configureren ter ondersteuning van het inrichten met Azure AD

1. Meld u aan bij de [Adobe-beheer console](https://adminconsole.adobe.com/). Navigeer naar **instellingen > Directory Details > synchronisatie**. 

2. Klik op **Sync toevoegen**.

    ![Toevoegen](media/adobe-identity-management-provisioning-tutorial/add-sync.png)

3. Selecteer **gebruikers synchroniseren van Microsoft Azure** en klik op **volgende**.

    ![Scherm afbeelding met de selectie ' gebruikers synchroniseren van Microsoft Azure Active Directory ' geselecteerd.](media/adobe-identity-management-provisioning-tutorial/sync-users.png)

4. Kopieer de Tenant- **URL** en het **geheime token** en sla deze op. Deze waarden worden ingevoerd in de velden **Tenant-URL** en **geheim-token** op het tabblad inrichten van uw Adobe Identity Management-toepassing in de Azure Portal.

    ![Synchroniseren](media/adobe-identity-management-provisioning-tutorial/token.png)

## <a name="step-3-add-adobe-identity-management-from-the-azure-ad-application-gallery"></a>Stap 3. Adobe Identity Management toevoegen vanuit de Azure AD-toepassings galerie

Voeg Adobe Identity Management toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting van Adobe Identity Management te starten. Als u Adobe Identity Management voor SSO eerder hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Adobe Identity Management, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-adobe-identity-management"></a>Stap 5. Automatische gebruikers inrichting configureren voor Adobe Identity Management 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-adobe-identity-management-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Adobe Identity Management in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Adobe Identity Management** in de lijst met toepassingen.

    ![De koppeling Adobe Identity Management in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. Typ in het gedeelte **beheerders referenties** de URL van uw Adobe Identity Management-Tenant en het geheime token dat u eerder hebt opgehaald uit stap 2. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Adobe Identity Management. Als de verbinding mislukt, zorg er dan voor dat uw Adobe Identity Management-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Adobe Identity Management**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Adobe Identity Management in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Adobe Identity Management voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de Adobe Identity Management-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |actief|Booleaans|
   |addresses[type eq "work"].country|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Adobe: 2.0: gebruiker: emailAliases|Tekenreeks|

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Adobe Identity Management**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Adobe Identity Management in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de groepen in Adobe Identity Management voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor Adobe Identity Management wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Adobe Identity Management door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

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